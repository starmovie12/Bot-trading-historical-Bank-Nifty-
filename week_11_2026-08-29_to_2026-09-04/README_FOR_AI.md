# README_FOR_AI: read this before building or testing a strategy on this data

Data: Indian index options (NIFTY, BANKNIFTY, SENSEX) from the Upstox API, downloaded by `updl`.
Window of this folder: 2026-08-29 to 2026-09-04. Holdout starts 2026-10-05 (days on or after it live in `store/holdout/`
and `ai_ready/holdout/`).

## Files (see README_EXPORT.txt for the full list)
- `<INDEX>/options_1m/<expiry>.csv` and `ai_ready/<INDEX>_options_1m.parquet`: one row = one minute of one option
  contract. Columns: time (IST, candle START), strike, option_type (CE/PE), open, high, low, close, volume, oi,
  instrument_key (+ trading_date, underlying, expiry in Parquet).
- `<INDEX>/spot_1m.csv` (index, no volume), `<INDEX>/futures_1m.csv`, `<INDEX>/contracts.csv` (lot_size).
- `<INDEX>/recorder_quotes/` (only for days the Recorder ran): live bid, ask and their quantities per snapshot;
  `spread_report.csv` summarises the spreads by moneyness, days to expiry and hour.
- Python: `from updl.layout import load_ai_ready; df = load_ai_ready(folder, "NIFTY")` loads development days
  only; `include_holdout=True` is for the single final test.
- `store/` all tables, incl. derived `iv_greeks_1m` (IV, delta, gamma, theta, vega), `chain_metrics_1m` (PCR,
  OI walls, max pain, ATM IV, 25-delta skew, near-minus-next term spread, gamma flip PROXY) and
  `iv_daily_DERIVED` (ATM IV known at 10:00 and 14:00). Every
  derived row has `available_from_ts`: never use a value before that time. (Weekly batch folders carry `store/`;
  an `export --layout` folder does not: use the Parquet store in the updl workdir.)
- `xcheck_csv/`: exchange-side PCR / max pain / OI / FII-DII; values of day D are usable only from the next session.
- `strategy_ready_days.csv`: which index-days have complete price data (PRICE_READY) and also events and
  recorded bid/ask (FULL_READY). Test only on READY days, or say clearly how you treat the others.

## Units and timing
- volume and oi are in UNITS (quantity), not lots: lots = units / lot_size.
- Times are IST (+05:30). A candle stamped 09:15 covers 09:15:00-09:15:59 and is known at 09:16.
- Index F&O trades until 15:40 from 3 Aug 2026 (15:30 before); the index itself stops at 15:30.
- An option minute exists only if that contract traded in that minute: missing minutes on thin strikes are normal.
  Never fill them silently; if you carry a price forward, mark it.
- Expiry-day contracts and weekly expiry rules changed over time (lot sizes per contract are in contracts.csv).

## Known limits (do not pretend otherwise)
- No tick data and no historical bid/ask: 1 minute is the smallest candle. Fills at the candle close are
  optimistic. Use the full charges (brokerage, STT, exchange, GST, stamp) AND a pessimistic slippage, larger for
  cheap out-of-the-money options and on expiry day. Real spreads exist only for days the Recorder ran
  (`recorder/` files, SPREAD = READY).
- IV / greeks are computed from the last traded price (Black-76): noisy on thin strikes and stale prices.
- Index constituents, event calendar and corporate actions are owner files; when missing, checks that need them
  are not testable.

## How to test honestly
1. Build and tune ONLY on development days. Do not open the holdout while building.
2. Use walk-forward testing (tune on a past block, test on the next block, roll forward).
3. Prefer few parameters; report the number of trades, the worst drawdown and results per month.
4. Run the holdout ONCE at the very end. If it fails, do not re-tune on it.
5. Paper-trade live before any real money. This data and any result from it is not financial advice.
