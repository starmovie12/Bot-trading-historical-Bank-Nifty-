UPDL BATCH FOLDER - kya kahan hai (what is where)
================================================

Har index ka alag folder: NIFTY/, BANKNIFTY/, SENSEX/
  spot_1m.csv             index ka 1-minute candle: time, open, high, low, close
  futures_1m.csv          index futures 1-minute: time, expiry, instrument_key, OHLC, volume, oi
  contracts.csv           is window ke saare option contracts: expiry, strike, CE/PE, lot_size, pehla/aakhri din
  recorder_quotes/        Recorder ke din (live bid/ask har snapshot; <date>_ltp.csv = spot, future, VIX)
  options_1m/<expiry>.csv har option ka har minute (LONG format: ek line = ek contract ka ek minute):
                          time, strike, option_type (CE/PE), open, high, low, close, volume, oi, instrument_key
                          40 MB se badi file `<expiry>_from_<date>.csv` mein aage chalti hai.
ai_ready/                 wahi options table Parquet mein (pandas: pd.read_parquet). holdout/ folder ALAG hai:
                          strategy banate waqt use mat kholo; sirf aakhri ek test ke liye.
chain_15min/              har 15 minute ki option chain (spot, CE/PE close, OI, PCR, OI walls, max pain)
xcheck_csv/               exchange ka PCR / max pain / OI / FII-DII (Upstox se; din D ka data agle din se use karo)
store/                    poora Parquet store (sab tables, derived IV/greeks bhi; sirf hafte wale folder mein)
health.csv, day_status.csv, holes.csv, strategy_ready_days.csv   kaunsa din poora hai, kya missing hai
README_FOR_AI.md          AI ko strategy banwane se pehle yeh file do
raw_index.csv             raw/ folder ke hash-naam -> index, item, instrument_key

Rules
  * time = IST (+05:30), candle ka START minute. 09:15 wali candle 09:15:00-09:15:59 ki hai.
  * Khali cell = data nahi tha. Kabhi 0 se nahi bhara.
  * volume aur oi = units (quantity), lots NAHI. Lots = units / lot_size (contracts.csv).
  * Upstox par TICK data nahi hai: 1 minute sabse chhoti candle hai. Usse chhota (aur bid/ask) sirf
    Recorder (bash termux_run.sh record) aage ke liye save karta hai.
  * Option ka minute tabhi hota hai jab us minute trade hua; patle strikes par minutes chhoot-te hain (normal).
