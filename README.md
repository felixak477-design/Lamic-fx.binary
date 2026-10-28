# Deriv Higher/Lower Hedge Lab

Android-friendly, single-page GitHub Pages project for **demo-first** testing of mirrored Higher/Lower contracts on Deriv.

## Default configuration

- Market: Volatility 25 (1s) Index (`1HZ25V`)
- Higher contract: `CALL`, relative barrier `+35`
- Lower contract: `PUT`, relative barrier `-35`
- Stake: $2 per contract
- Duration: 10 ticks
- Filter: strong trend only
- Max pairs/session: 3
- Session loss stop: $8
- One pair open at a time
- Demo/virtual account only; real accounts are blocked by the authorization check

## Important contract mechanics

These are terminal-price Higher/Lower contracts, not Touch or No Touch contracts. The Higher contract wins only if the exit spot is strictly above its barrier; the Lower contract wins only if the exit spot is strictly below its barrier. If the final spot is between the mirrored barriers, both can lose. Narrower barriers do **not** eliminate risk.

The trend filter is a simple heuristic over recent ticks, not a predictive model or guarantee. It may block trades while collecting ticks and can misclassify conditions.

## Deploy from an Android phone

1. Download and extract the ZIP.
2. In GitHub, create a **new repository** (for example `deriv-higher-lower-hedge`). Do not replace or delete files in your existing projects.
3. Upload `index.html` and `README.md` from this folder to the repository root and commit.
4. Open the repository's **Settings → Pages**. Select **Deploy from a branch**, choose `main` and `/ (root)`, then Save.
5. Open the Pages URL after GitHub finishes publishing.

You can also test locally by opening `index.html` in a browser, although browser restrictions may vary.

## Deriv connection setup

1. In your Deriv account, create/register an application and obtain its App ID.
2. Create an API token for a **demo/virtual account only**, with trading scope if required.
3. Enter the App ID and demo token in the app and tick the demo confirmation.
4. Connect. The app checks the authorization response for a virtual account, subscribes to live ticks, and fetches fresh quotes for both contracts.
5. Let the tick stream collect enough observations for the trend filter.
6. Review both quoted costs/payouts and use **Buy one pair**. Confirmation is enabled by default.

Never put your token in GitHub files, public messages, screenshots, or a URL. The page does not intentionally store the token in local storage or send it to any service other than Deriv. Disconnect to clear it from the input. Use a dedicated demo token and revoke it if exposed.

## Risk controls included

- Demo-account check (`is_virtual` or virtual login ID)
- Stake capped at $2 per contract
- Barrier distance constrained to 1–500 relative units
- Maximum 3 pairs per session
- Session loss stop capped at $8
- No martingale or automatic stake escalation
- Fresh quote age limit (15 seconds)
- Strong-trend filter (can be disabled for analysis/testing)
- One pair at a time
- Stops after partial purchase and logs that you must check Deriv Positions manually
- Live API errors and settlement updates in the activity log

## Execution limitation

Deriv does not provide an atomic two-contract purchase in this workflow. The app sends the two buy requests back-to-back, but one can succeed while the other fails. If that happens, it stops further purchases and tells you to check the Positions tab. Do not assume both legs succeeded.

The session P/L is accumulated from contract settlement messages received while the page is connected. It is not a substitute for checking Deriv account history; if you disconnect or the browser suspends, this in-page counter may miss updates.

## Technical notes

- Uses the Deriv WebSocket API endpoint `wss://ws.derivws.com/websockets/v3?app_id=...`.
- Uses `CALL` for Higher and `PUT` for Lower with relative barriers.
- Requests both quotes with `basis: "stake"`, duration in ticks, and account currency.
- Validates both quotes before allowing purchase.
- This is an educational prototype. API behavior, contract availability, symbols, account authorization, and barrier validation can vary. If Deriv rejects a proposal, no purchase should be made for that pair.

## Files

- `index.html` — complete responsive application.
- `README.md` — setup, usage, limitations, and safety notes.
