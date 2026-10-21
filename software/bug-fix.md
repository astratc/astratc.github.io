# Sample 2: Bug fix, address-poisoning detection in Wallet Health Card

**Service:** Bug fix, $35, 48h · **Repo:** [astratc/wallet-health-card](https://github.com/astratc/wallet-health-card) · **Fix commit:** [`b1b82b8`](https://github.com/astratc/wallet-health-card/commit/b1b82b818bbfc5ccfdc3fa5ca3b9dfcf9131a2a7) · **Bug:** finding 1 from the [code review](code-review.md)

## Summary
The scanner was built to raise a high-severity alert when you've sent funds to an address-poisoning look-alike. That alert could never fire. Worse, when your real contact sent you something, the tool reported *them* as the fake. We reproduced the bug, found the root cause, fixed it with a small change to one function and added a regression test that fails before the fix and passes after it. The live demo is unchanged and still serves 200.

## Reproduce (before the fix)
A wallet pays its friend `0xabcd…5678`. A poisoner sends a zero-value transfer from the look-alike `0xabcdff…5678`. The wallet then copies the wrong address and pays the look-alike 25 USDC. The friend also sends a refund.
```js
detectPoisoning(ME, [
  tr(ME, FAKE,   "25000000", "0xoops",   "2026-10-21T10:00Z"),  // paid the fake
  tr(FAKE, ME,   "0",        "0xpoison", "2026-10-20T10:00Z"),  // poisoning transfer
  tr(FRIEND, ME, "10000000", "0xrefund", "2026-10-19T12:00Z"),  // genuine refund
  tr(ME, FRIEND, "50000000", "0xreal1",  "2026-10-19T10:00Z")], []);
```
**Expected:** a high "funds sent to look-alike" hit for `0xoops` and a medium poisoning-attempt hit for `0xpoison`.
**Actual:** no `sentToFake` hit at all, plus a hit on `0xrefund` claiming the *friend* is the fake that mimics the poisoner.

## Root cause
`detectPoisoning` built a set of "real" counterparties from **every** address the wallet had paid:
```js
for (const t of transfers) if (lc(t.from?.hash) === me && lc(t.to?.hash) !== me) real.add(lc(t.to.hash));
```
Once you've paid the poisoner, the poisoner is in `real`. Two things then go wrong:
1. Outgoing transfers to an address in `real` are skipped (`if (!other || real.has(other) && from === me) continue;`), so the payment to the fake is never examined. `sentToFake` is unreachable, and so is the -30 score penalty.
2. An inbound transfer from the genuine friend is compared with every `real` address. The fake is now one of them and looks like the friend, so the friend is reported as `fake` and the poisoner as `mimics`.

The existing test only covered the simple case (a poisoning attempt with no payment), so it passed.

## Fix
- Record when each paid address was **first** paid. A paid address is *genuine* only if no look-alike of it was paid earlier. A poisoner can only mimic a contact you already have, so the later look-alike is the fake.
- Skip only genuine addresses. Any other counterparty that looks like a genuine contact is a hit, and `sentToFake` is true when the wallet was the sender.
- Also check native-coin payments to look-alikes, not only token transfers.
- Clean-up from the same review: removed a stale comment and an unused import in the test (ESLint `no-unused-vars`).

Diff: [`docs/core.js`, `test/run.mjs`](https://github.com/astratc/wallet-health-card/commit/b1b82b818bbfc5ccfdc3fa5ca3b9dfcf9131a2a7), +32/-15 lines.

## Regression test
Added to `test/run.mjs`, using the scenario above plus a native ETH payment to the fake:
```js
assert.ok(hits.every(h => h.fake === FAKE && h.mimics === FRIEND));               // friend never labelled fake
assert.deepEqual(hits.filter(h => h.sentToFake).map(h => h.tx).sort(), ["0xnative", "0xoops"]);
assert.deepEqual(hits.filter(h => !h.sentToFake).map(h => h.tx), ["0xpoison"]);
```
| Run | Result |
|---|---|
| New test against the old `core.js` | **fails**: `AssertionError: genuine contact must never be reported as the fake` |
| New test against the fixed `core.js` | pass |
| Existing suite (grade, score, findings) | unchanged: B, 74/100, 7 findings |
| Live demo https://astratc.github.io/wallet-health-card/ after deploy | HTTP 200, and the served `core.js` contains the fix |

## Known limits
Look-alike matching stays at "same first 4 and last 4 hex characters", the common poisoning pattern. If a wallet paid the look-alike *before* ever paying the real contact, the earlier address is treated as genuine. This is rare, because poisoners copy existing contacts.

*Fix delivered as a commit to our own repo. For a client repo we deliver the same thing as a fork and pull request, or as a patch file.*
