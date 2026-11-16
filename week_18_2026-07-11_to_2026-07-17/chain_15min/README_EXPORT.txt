UPDL EXPORT - option chain at time T (CSV)
=========================================

Yeh files Upstox ke 1-minute candles se banayi gayi hain (expired + live contracts).
Upstox purani option chain (bid/ask/IV/greeks) nahi deta, isliye chain yahan candles se
"time T par jo pata tha" ke hisaab se dobara banayi gayi hai. Koi value future se nahi li gayi.

Files
-----
chain_<INDEX>_<DATE>.csv    har time, har expiry, har strike ki ek line
summary_<INDEX>_<DATE>.csv  har time, har expiry ki ek line (PCR, OI walls, max pain)

chain_*.csv columns
-------------------
time            IST time T (HH:MM). Sirf woh candles jo T tak band ho chuki thi.
expiry_date     contract expiry (YYYY-MM-DD)
strike          strike price
spot_asof       index ka last 1-minute close jo T tak pata tha (khali = abhi tak nahi)
spot_age_min    spot value kitne minute purani hai (index 15:30 par band hota hai, F&O 15:40 tak)
spot_is_filled  true = spot purani candle se aage laaya gaya
ce_close        CE ka last traded close (T ya usse pehle)
ce_vol_1m       CE volume us minute ka jo T par khatam hua (khali = us minute trade nahi hua)
ce_vol_cum      CE ka din bhar ka volume T tak (units/quantity, lots nahi)
ce_oi           CE open interest, last known (units/quantity, lots nahi)
ce_age_min      last candle kitne minute purani hai (0 = bilkul taaza)
ce_is_filled    true = value purani candle se aage laayi gayi (carried), false = taaza
pe_*            wahi sab PE ke liye
Khali cell = data nahi tha. Kabhi bhi 0 se nahi bhara gaya.

summary_*.csv columns
---------------------
time, expiry_date, spot_asof, spot_age_min, spot_is_filled
pcr             put OI / call OI (sirf downloaded strikes ka band, poori exchange chain nahi)
call_oi_wall    spot ke upar sabse zyada CE OI wala strike
put_oi_wall     spot ke neeche sabse zyada PE OI wala strike
max_pain        woh strike jahan option buyers ka total intrinsic value sabse kam ho (band ke andar)
n_strikes       kitne strikes the
n_oi_known      kitne CE+PE OI values pata thi
n_oi_carried    kitni values purani candle se aage laayi gayi (zyada = kam bharosa)

Important
---------
* Bid/ask, IV aur greeks history Upstox se nahi milti. Woh sirf Recorder (python -m updl record)
  market time mein live save karta hai.
* Volume aur OI Upstox units (quantity) mein deta hai, lots mein nahi. Lots = units / lot size.
* Yeh data sirf apne use ke liye hai. Kisi ko mat do.
