# Trader BotView

<p align="center">
  <img src="assets/icon-192.png" alt="Trader BotView logo" width="88">
</p>

<p align="center"><b>One Futures grid, twelve values, one tap to copy.</b></p>

<p align="center">
  <a href="https://moonspiid.github.io/trader-botview/"><b>Open the app</b></a>
</p>

A Futures grid bot is not something you eyeball. It needs a lower bound, an upper bound, a number of levels, a margin per grid, a leverage, a stop: twelve numbers that have to stay consistent with each other. One wrong step and the bot opens three orders instead of twenty, or gets liquidated on a wick.

BotView computes those twelve values from the live market, puts them on a single phone screen, and gives each one a **Copy** button.

## Contents

- [What the tool does](#what-the-tool-does)
- [The twelve values](#the-twelve-values)
- [Quick start](#quick-start)
- [Grid count](#grid-count)
- [How the grid is computed](#how-the-grid-is-computed)
- [AI auto mode](#ai-auto-mode)
- [Languages](#languages)
- [Install on mobile](#install-on-mobile)
- [Good to know](#good-to-know)
- [Support the project](#support-the-project)
- [Contributors](#contributors)
- [License](#license)

## What the tool does

- **One screen, no scrolling** — header, mode, pair, four indicators, then the twelve grid cells. It all fits a phone, even at 390 × 844, with nothing to scroll.
- **No pair list** — in manual mode you type the symbol (`BTC`, `BTCUSDT`, `ethusdt`), it gets normalised and checked against the Binance filters. In auto mode the scan picks the pair.
- **Bilingual labels** — every cell shows the local term followed by the English term in grey (`Paire Pair`, `Marge/grille Margin/grid`, `Borne basse Lower bound`). A trader pasting those values into an English Binance interface finds the right field immediately, even when their interface is in French.
- **Twelve copyable values** — each cell is a button: one tap copies the raw value, ready to paste into Binance, and the cell turns green with "Copied". The grid count stays editable: type it, use the − / + buttons, or go back to the automatic value.
- **Futures maths** — isolated margin mode, leverage, margin per grid, quantity per grid rounded down to `stepSize`, bounds rounded to `tickSize`, TP and SL, range width and net profit per grid after fees.
- **Language picked by itself** — on first load the page derives the language from the time zone (French in Algeria, Morocco, Tunisia, Senegal, France, Québec) then from the browser language, falling back to English. The globe button in the header switches and remembers the choice.
- **Refresh button** — the ↻ in the header re-runs the analysis on the current pair and settings, or rescans in auto mode, without touching your capital, leverage or grid count.
- **Installable** — install button, `manifest`, service worker and cache: the app opens full screen and starts offline.
- **Zero dependencies** — a single HTML page, no framework, no build step, no keys, no order ever sent.

## The twelve values

| Cell | What you do with it |
| --- | --- |
| **Pair** | The contract symbol, to paste into the Binance field |
| **Leverage** | The leverage to set before starting the grid |
| **Margin** | The margin mode, `ISOLATED` by default |
| **Lower bound** | Lower limit of the grid |
| **Upper bound** | Upper limit of the grid |
| **Grids** | Number of levels, editable from 2 to 150 |
| **Grid mode** | `ARITHMETIC` (fixed step) or `GEOMETRIC` (percentage step) |
| **Margin/grid** | Capital committed per level |
| **Total margin** | Total capital locked by the bot |
| **Qty/grid** | Size of each order, rounded to `stepSize` |
| **TP** | Take profit, placed on the upper bound |
| **SL** | Safety stop below the lower bound |

Values are copied without thousand separators and with a decimal point (`55847.77`), which is exactly what Binance expects in a numeric field.

## Grid count

The **Grids** field is a real input, not a frozen value:

- **Automatic** (default): the count follows the range width and volatility, between 2 and 60 levels. An `auto` tag shows in the cell.
- **Manual**: type a number, or tap **−** / **+**. The tag becomes clickable to return to the computed value.
- **Small capital**: 2 or 3 grids are enough to start. With 20 $ at 3×, two grids clear the minimum notional; the rest (margin per grid, quantity, TP, SL) recomputes on every keystroke.
- **Live capital**: every digit typed into the Capital field immediately recomputes the total margin, the margin per grid and the grid count, without leaving the field. Total margin equals the capital minus a fee reserve of 0.05 % of the notional, and margin per grid is that amount ÷ grids, with no hidden rounding.
- **Ceiling forced by capital**: the grid count can never exceed what the funds support. The limit is `notional ÷ (minimum notional per grid × 1.001)`, where the minimum per grid is the stricter of the two thresholds Binance imposes: 5 $ of notional, and `minimum quantity × price` — 0.001 BTC, i.e. 60 $ at 60 000 $.
- **Margin check, in order**: the capital first goes into a fee reserve (0.05 % of the notional, i.e. 1.50 $ on 1 000 $ at 3×), then the tool looks for the smallest number of grids it can finance. If two grids do not fit at the requested leverage, leverage is raised just enough, up to the 10× maximum — 30 $ on BTCUSDT goes from 3× to 5×, 20 $ to 7×, 13 $ to 10×, and the Leverage field follows so the copyable cell tells the truth.
- **Blocking final validation**: before display, nine checks run over the plan — at least 2 grids, leverage between 1 and 10, positive quantity above the contract minimum, notional per grid above the minimum, quantity aligned to the step, total margin within the capital, reserve covered, price strictly inside the bounds. One failure and nothing is displayed.
- **Numbered refusal when it is physically impossible**: 12 $ on BTCUSDT cannot pay for two 60 $ orders at 10×, so the tool answers "not enough funds: 13 $ minimum for 2 grids at 10×" instead of showing a quantity Binance would reject.
- **Bounds tightened when capital caps the count**: the range follows market structure, but if the capital only finances 4 grids over a 15 % range, the step would jump to 3.9 %. The range is then brought back to 4 × 1.8 times the target step, centred on the price, to keep the grid usable.
- **No impossible quantity**: quantity is rounded down to the contract step, then checked. If it drops below the minimum quantity or its notional falls under 5 $, the tool refuses to produce the plan and states the capital needed: "not enough funds: 42 $ minimum for 2 grids at 3×". That message beats an order rejected by Binance.
- **Futures data blocked**: if the Futures API is unreachable, the status pill turns amber and a tooltip warns that spot filters are in use — minimum quantities there are more permissive, check them before starting the bot.

The grid count is kept when you change pair, capital or timeframe, until you go back to `auto`.

## Quick start

1. Open **[the app](https://moonspiid.github.io/trader-botview/)**.
2. **Manual**: type the pair, pick the timeframe, set capital and leverage, then **Analyse**.
   **AI auto**: type nothing, tap **Scan**. The scan picks the pair and fills everything.
3. Read the bottom line: net profit per grid and range width.
4. Copy the twelve values into the Binance Futures grid form.
5. Test on [testnet.binancefuture.com](https://testnet.binancefuture.com) before any real money.

## How the grid is computed

- **Bounds** — the highest and lowest prices actually touched over the timeframe window: 42 candles at 4 h (7 days), 72 at 1 h (3 days), 96 at 15 min (24 h), 30 at 1 d (1 month). The lower bound is that window's low, the upper bound its high — the two extremes a trader sees on their chart, not a theoretical band.
- **Margin around the price** — if the price sits against an extreme (range breakout, new high), the range is extended by 1.2 ATR on that side, at least 2 %, so the price is never stuck at the edge.
- **Consistency with the grid count** — the structure range is kept as long as the step stays between 0.6 and 1.8 times the target step. Beyond that it is tightened around the price, to avoid 4 % steps with three grids.
- **Grid count** — target step at 55 % of ATR, clamped between 0.5 % and 2.6 % of price: the count follows from the range divided by that step, then between 2 and 60 levels. The count is reduced while the margin per grid does not cover the contract's minimum notional.
- **Geometric mode** — chosen when the range exceeds 12 % or when the price exceeds 5 000, arithmetic otherwise.
- **Leverage and margin** — notional = invested capital × leverage; total margin = capital − fee reserve (0.05 % of notional); margin per grid = invested capital ÷ grid count; quantity = notional / grids / price, rounded down to `stepSize`.
- **Grid count versus capital** — in automatic mode the number of levels is capped by what the capital can finance: 100 $ on BTCUSDT at 3× gives 4 grids, 1 000 $ gives 11. It goes back up as soon as the capital rises, without leaving the field.
- **Stop** — 0.9 ATR below the lower bound, clamped between 1.2 % and 6 %.
- **Net profit** — grid step minus 0.04 % round-trip fees (maker on both sides).

Prices and quantities are rounded to the contract's `tickSize` and `stepSize`, read from `exchangeInfo`. If the Futures API is not reachable from your country, the tool falls back to spot market filters and funding shows "—".

## AI auto mode

The scanner fetches the full 24 h ticker, keeps USDT pairs above 15 M volume, drops leveraged tokens, then analyses the 14 most liquid on the chosen timeframe. Each one gets a score out of 100:

| Criterion | Weight |
| --- | --- |
| Low ADX (ranging market) | up to 34 |
| ATR between 1 % and 4 % | up to 26 |
| 24 h volume | up to 22 |
| Bollinger width | up to 8 |
| Extreme 24 h change | up to −10 |

The best score becomes the displayed configuration. The pair field turns read-only: the scan decides, not you.

## Languages

There is no language list to pick from: the page configures itself.

1. **Remembered choice** — if you already switched language, that one comes back.
2. **Time zone** — `Africa/Algiers`, `Africa/Casablanca`, `Africa/Tunis`, `Africa/Dakar`, `Europe/Paris`, `Europe/Brussels`, `America/Montreal` and their neighbours give French. An English browser in Algeria therefore gets French, not English.
3. **Browser language** — if the time zone is not recognised, `navigator.languages` decides between French and English, and nothing else.
4. **Fallback** — English.

No network call: detection uses the time zone and the browser, not an IP database.

The local language and English work together: the interface is in the detected language, every cell label carries its English equivalent in grey, and any string missing from a translation falls back to English rather than to a technical key.

The interface switches between two languages only: French and English. A Spanish, Portuguese, Arabic or Japanese time zone lands on English — no third language is offered. The **globe** button in the header shows the active language (`FR` or `EN`) and toggles between them on tap, with the language name as confirmation. The choice is stored and takes priority over detection. Numbers follow the language: `59 752,01` in French, `59,752.01` in English.

## Install on mobile

1. Open [the page](https://moonspiid.github.io/trader-botview/) in the phone browser.
2. Tap **⤓** in the top right corner.
3. **Android / desktop**: pick *Install app* in the browser menu. **iPhone / iPad**: **Share**, then **Add to Home Screen**.
4. The BotView icon opens full screen, without an address bar, and restarts from cache when the network is gone.

The `?mode=auto` shortcut opens the scanner directly.

## Good to know

- The tool does not connect to your account, asks for no key and sends no order. It produces numbers, you paste them.
- A grid does not protect against a breakout: if the price leaves the range and hits the SL, the bot must be stopped. High leverage brings liquidation closer, especially with a wide range.
- Funding is only shown when the Futures API answers from your country; prices and filters come from `data-api.binance.vision`, which stays reachable where `api.binance.com` is blocked.
- Binance public data can be blocked or delayed; a grid calculation remains an estimate, not a promise of return.
- A bot can lose the whole committed capital. Past performance does not predict future results.

## Support the project

The tool is free, with no account, no ads and no tracking, and there is no paid tier behind it. If it saves you time, a donation pays for the hours behind it and for the next features.

**Donate in USDT →** a single crypto address, no card, no account, no payment processor in between.

<p align="center">
  <img src="https://raw.githubusercontent.com/moonspiid/ai-agent-multi-model/master/donate-usdt-qr.png" alt="QR code of the EVM donation address" width="200">
  <br>
  <sub>Scan with any wallet</sub>
</p>

```
0xA2700896E16BE762467D458F2B45260f8FAC9F3e
```

It is a regular EVM address: **the same address works on every EVM network**. Send USDT on whichever suits you:

| Network | Token |
| --- | --- |
| BNB Smart Chain (BEP20) | USDT |
| Ethereum (ERC20) | USDT |
| Polygon | USDT |
| Arbitrum One | USDT |
| Base | USDT |
| Optimism | USDT |

Two rules: send **USDT only** (another token sent to this address cannot be recovered), and keep a little of the network's coin (BNB, ETH, POL) in your wallet to pay fees. A non-EVM network such as Tron (TRC20) uses another address format: nothing sent there arrives here.

## Contributors

| Contributor | Role |
| --- | --- |
| [moonspiid](https://github.com/moonspiid) · [X](https://x.com/moonspiid) | Author — grid engine, interface, documentation |

Issues and pull requests are welcome on [GitHub](https://github.com/moonspiid/trader-botview).

## License

[MIT](LICENSE) © 2026 moonspiid — use it, change it, publish it.
