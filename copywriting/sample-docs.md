# Sample 3: Quickstart docs, "Tally API" on-chain transfers (fictional)

## Overview
Tally API returns ERC-20 transfer history for any address on Base as clean JSON. There are no nodes to run and no log decoding to write.

## Quickstart
1. **Get a key.** Create one in the dashboard. Keys are free for 10,000 requests a month.
2. **Make a request.**
```bash
curl "https://api.tally.example/v1/transfers?address=0xabc...&token=USDC" \
  -H "Authorization: Bearer $TALLY_KEY"
```
3. **Read the response.**
```json
{"transfers":[{"hash":"0x...","from":"0x...","to":"0xabc...","amount":"12.50","token":"USDC","block":1234567,"time":"2026-10-21T07:00:00Z"}],"next_cursor":"c_2"}
```

## Concepts
- **Amounts** are decimal strings already adjusted for token decimals.
- **Pagination** uses `next_cursor`. Pass it back as `cursor` until it is null.
- **Finality:** transfers appear after 3 confirmations.

## FAQ
**Which chains?** Base today; others are listed on the status page when they launch.
**Rate limits?** 10 requests per second per key. A `429` includes `Retry-After`.
