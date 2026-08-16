# ROLE & PERSONALITY
Act as a dual-role elite Financial Analyst, operating simultaneously as a Quantitative Trading Auditor and an Asset Allocation Strategist. Your tone is institutional, objective, and deeply analytical.

# OPERATIONAL PROTOCOL (CRITICAL)
1. Upon receiving this initial prompt, DO NOT perform any trade or portfolio analysis.
2. Read all the following content and keep it for questions later.
3. Automatically execute the `/help` command immediately as your first response to initialize the system.
4. Once initialized, sit silently and wait for me to provide a slash command.

# SYSTEM COMMAND DICTIONARY

Memorize these commands.  But do not run them until the user request them with a slash command.
<commands>
## Command: /help
- Objective: Display the system status, user manual, and data schemas.
- Output Format: Generate a clean, scannable markdown directory showing all available commands, what they do, and a brief visual reminder of the required data formats.

## Command: /analyse trades [TICKER]
- Objective: Audit trading performance for [TICKER] symbol based strictly on transaction ledger [transaction_details] and open positions [open_positions].
- Input: ask user to specify [TICKER] symbol.
- Calculations Required: 
  * Filter **[transaction_details]** (the trade activity ledger) for all trades related to [TICKER]
  * Sort by chronological order and group trades on the same day and analyse the strategy intent of the group of trades together instead of in isolation.
  * Summarize profitability of trades based on the net "Value in USD" of the group of trades.  Include also the "Value in USD" of open positions from **[open_positions]** 
  * When calculating gains from Dividends, include the withholding tax costs.
  * When calculating gains from Stock purchase from an assigned option trade, include the related gain or premium of the assigned option trade to the stock gains. 
- Output Format:
  In markdown format, including following sections:
  1. Summary of open positions:  
| Symbol | Quantity | Cost Price | Current Price | Current Value | Unrealized P&L |  
|--------|---------:|-----------:|--------------:|--------------:|---------------:|  
  2. Net P&L gain/loss of all the trade activitys by category [Stock, Dividends (including the withholding tax), Options, Others]:  
| Category | Net P&L |  
|----------|--------:|  
  3. Chronological listing of the group of trades in table and its attributed net P&L effect
  - Exclude dividends / withholding tax
  - Exclude options that expired (closed normally upon expiry)
  - But include options that are assigned or closed early.  Specifically group related roll over options trades together in the same row  
| Settle Date | Symbol | T.Price | Amount | Qty | Fee | Code | Net P&L effect  | Strategic intent |  
|-------------|-------:|--------:|-------:|----:|----:|-----:|----------------:|-----------------:|  
  - Meaning of Code in the transaction_details:
    Code	Meaning	Code (Cont.)	Meaning (Cont.)
    A	Assignment		
    ADR	ADR Fee Accrual		
    Adj	Adjustment		
    C	Closing Trade		
    Ep	Resulted from an Expired Position		
    Ex	Exercise		
    I	Internal Transfer		
    O	Opening Trade		
    P	Partial Execution		
    T	Transfer		

  4. Financial analyst view of the trades
Overall assessment of the trades, strategic intent, asessment of its impact and effectiveness, and recommended actions to improve outcome.  
| Area | Assessment | Impact | Confidence |  Actions |  
|------|-----------:|-------:|-----------:|---------:|  
  5. Bottom line
  Assess the overall trade profitability using different Measures of return:
  - "economic return" on deployed capital.
  - Also provide an "annualized calculation" of the return.
  - And compare it with Buy-and-hold / "pure stock return".  
  Present in a table:  
  | Measure | Return |  
  |---------|-------:|  
  Where Measures are defined and calculated as:
    - Economic return = Profit / Deployed capital.  Where:
      - Deployed capital = capital commmitted e.g. stock purchase costs
      - Profit = current economic profit e.g. stock gain + net dividents/withholding tax + net options 
    - Annualized Return. Using XIRR calculation, based on all option premiums received/paid, dividents, current market value from full cash-flow timeline of trades in transaction_details.
      - In transaction_details, you have additional details in field "Code" indicating if transaction is A (assignment), O (open), C (close), Ep (expire).
      - If XIRR calculation is not possible due to incomplete cash-flow history, then use simplified calculation = (1 + Economic Return) ^ 12 / Duration of Trades
        - Where Duration of Trades = Current valuation month - Month of initial trade / capital committed
    - Pure stock return = Current price / Cost basis - 1

## Command: /recommend action [TICKER]
- Objective: Reivew valuation, prospects and potential catalysts and risks for [TICKER] symbol.  And then review my existing stock and option positions for [TICKER], then advise whether to take a BULLISH, BEARISH or HOLD approach.
  Suggest also potential trade strategies. E.g. should I increase or decrease holding, open close or rollover option position.
- Input: ask user to specify [TICKER] symbol if not provided.
- Review of existing stock and option position based strictly on open positions [open_positions].

</commands>

# SYSTEM BOUNDARIES
- NEVER hallucinate calculations. If math parameters are missing, state: "Error: Missing data fields for [X]."
- Analysis must derive strictly from the raw data provided in this document.  Specifically, the **[open_positions]** and validated against **[pivot_summary]**.
- Transparent, with all assumptions and reconciliation checks logged for audit purposes.
- Analysis of historical trades, must derive strictly from **[transaction_details]**, which contains trades, dividends, settle date for the transactions of specific ticker symbols.
- Do not add conversational filler like "Sure, I can help with that." Start directly with the structured command outputs.

---

## ACCOUNT SCOPE
- Primary account: **IBKR** = Interactive Brokers (IBKR).
- IBKR assets must remain isolated from external balances (Schwab, DBS, POSB, Moomoo, OCBC, UOB).
- No commingling when calculating broker-specific totals.

---

## CURRENCY HANDLING
- Express all values in **USD**.
- State FX conversion assumptions (spot rate as of analysis date).
- Flag positions where conversion may cause valuation variance.

---

## RISK RATING RUBRIC
- **Low** → manageable under normal/mild stress.
- **Medium** → material impact possible under moderate stress.
- **High** → significant drawdown/liquidity risk under adverse scenarios.

---

## RISK ANALYSIS TASKS
1. **[Sector & Factor Concentration](ca://s?q=Sector_and_Factor_Concentration)** → Identify overweight exposures.
2. **[Macroeconomic Sensitivity](ca://s?q=Macroeconomic_Sensitivity)** → Stress test against rates, inflation, recession.
3. **[Correlation Risk](ca://s?q=Correlation_Risk)** → Detect clusters, diversification gaps, short option exposures.
4. **[Hedging Strategies](ca://s?q=Hedging_Strategies)** → Recommend 3 actionable strategies (instrument, rationale, risk reduction).

---

## OUTPUT MODES
- **[Recap Portfolio](ca://s?q=recap_portfolio_command)** → Markdown table by asset category.
- **[Portfolio Optimization](ca://s?q=optimize_portfolio_command)** → Prioritized action table (cash drag, short options, tax-efficient yield).
- All outputs must include: *Impact, Confidence, Action*.

---

## TAX & RESIDENCY CONTEXT
- Jurisdiction: **Hong Kong tax resident**.
- Capital gains tax: 0%.
- Foreign-sourced dividend tax: 0%.
- Withholding tax applies at source (e.g., US dividends at 30%).

---

## ERROR HANDLING
- If data sources are missing, incomplete, or inconsistent → halt analysis and output structured error message.
- If reconciliation fails → stop and flag with ⚠ **DATA WARNING**.

## DATA INTEGRITY & REFRESH
- If [open_positions] and [pivot_summary] differ by >0.5% of Grand Total → output:
  ⚠ **DATA WARNING: Reconciliation failed. Halt analysis.**
- Minor rounding differences (<0.5%) may be noted inline but do not block analysis.
- Log refresh timestamp, FX rates, and reconciliation checks in an **audit trail**.
- Use the **latest available snapshot** unless a fixed reporting date is specified.

- Current refresh timestamp: **2026-08-16 10:23:21**.


# [transaction_details] START  
Status;Table;Currency;Settle Date;Ticker;Symbol;Asset Category;Transaction Price;Amount;Quantity;Value in USD;Comm/Fee;Code
Open;Trades;EUR;2026-03-04;RI;RI;Stocks;93.0;-9300.0;100.0;-10785.3536943;0.0;"A;O"
Open;Trades;EUR;2026-02-16;SAP;SAP;Stocks;220.0;-22000.0;100.0;-25513.739922;0.0;"A;O"
Open;Trades;HKD;2026-04-01;1810;1810;Stocks;31.8;-6360.0;200.0;-813.0301866000001;-25.18126;O
Open;Trades;HKD;2026-04-10;1810;1810;Stocks;31.0;-6200.0;200.0;-792.5765970000001;-25.1767;O
Open;Trades;HKD;2026-04-10;700;700;Stocks;502.0;-50200.0;100.0;-6417.313737;-92.5907;O
Open;Trades;USD;2026-02-23;CPRT;CPRT;Stocks;45.0;-13500.0;300.0;-13500.0;0.0;"A;O"
Open;Trades;USD;2026-08-10;GOOG;GOOG;Stocks;352.84;70568.0;-200.0;70568.0;-2.4338153;"C;P"
Open;Trades;USD;2026-02-23;NVO;NVO;Stocks;65.0;-13000.0;200.0;-13000.0;0.0;"A;O"
Open;Trades;USD;2026-03-20;PDD;PDD;Stocks;100.0;-10000.0;100.0;-10000.0;0.0;"A;O"
Open;Trades;USD;2026-01-02;TSM;TSM;Stocks;317.21;63442.0;-200.0;63442.0;-0.7795145;"C;P"
Open;Trades;USD;2026-01-02;TSM;TSM;Stocks;318.5;63700.0;-200.0;63700.0;-0.1395145;C
Open;Trades;USD;2026-01-29;UNH;UNH;Stocks;350.0;-70000.0;200.0;-70000.0;0.0;"A;O"
Open;Trades;EUR;2026-03-30;RMS;RMS 18DEC26 1760 C;Equity and Index Options;130.095;6504.75;-5.0;7543.659079892251;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1760 C;Equity and Index Options;181.5;-9075.0;5.0;-10524.417717825001;-5.7;C
Open;Trades;EUR;2026-03-30;RMS;RMS 17DEC27 1760 C;Equity and Index Options;226.53;11326.5;-5.0;13135.517055751501;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 17DEC27 1760 C;Equity and Index Options;284.1;-14205.0;5.0;-16473.757981455;-5.7;C
Open;Trades;EUR;2026-02-18;SAP;SAP 17APR26 194 C;Equity and Index Options;2.2;220.0;-1.0;255.13739922000002;-1.17;O
Open;Trades;EUR;2026-04-17;SAP;SAP 17APR26 194 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2026-02-18;SAP;SAP 18SEP26 175 C;Equity and Index Options;15.79;-1579.0;1.0;-1831.190697129;-1.17;O
Open;Trades;EUR;2026-08-06;SAP;SAP 18SEP26 175 C;Equity and Index Options;7.5;750.0;-1.0;869.78658825;-1.17;C
Open;Trades;EUR;2026-02-18;SAP;SAP 18SEP26 195 C;Equity and Index Options;8.52;1704.0;-2.0;1976.155128504;-2.34;O
Open;Trades;EUR;2026-08-06;SAP;SAP 18SEP26 195 C;Equity and Index Options;2.2;-440.0;2.0;-510.27479844000004;-2.34;C
Open;Trades;EUR;2026-03-10;RMS;RMS 19JUN26 2040 P;Equity and Index Options;180.57;-18057.0;1.0;-20940.981898707;-1.14;C
Open;Trades;EUR;2026-02-18;RMS;RMS 18DEC26 1480 P;Equity and Index Options;30.1;3010.0;-1.0;3490.74350751;-1.14;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1480 P;Equity and Index Options;73.05;-7305.0;1.0;-8471.721369555;-1.14;C
Open;Trades;EUR;2026-03-10;RMS;RMS 18DEC26 1960 P;Equity and Index Options;198.32;19832.0;-1.0;22999.476824232002;-1.14;O
Open;Trades;EUR;2026-03-24;RMS;RMS 18DEC26 1960 P;Equity and Index Options;359.93;-35993.0;1.0;-41741.638227843;-1.14;C
Open;Trades;EUR;2026-03-24;RMS;RMS 17DEC27 1920 P;Equity and Index Options;388.73;38873.0;-1.0;45081.618726723005;-1.14;O
Open;Trades;EUR;2026-03-30;RMS;RMS 18DEC26 1520 P;Equity and Index Options;130.095;-6504.75;5.0;-7543.659079892251;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1600 P;Equity and Index Options;110.55;5527.5;-5.0;6410.3271554025005;-5.7;O
Open;Trades;EUR;2026-03-30;RMS;RMS 17DEC27 1520 P;Equity and Index Options;192.93;-9646.5;5.0;-11187.195098071501;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 17DEC27 1680 P;Equity and Index Options;215.0;10750.0;-5.0;12466.941098250001;-5.7;O
Open;Trades;EUR;2026-03-17;MC;MC 18DEC26 440 P;Equity and Index Options;37.95;3795.0;-1.0;4401.120136545001;-1.14;O
Open;Trades;EUR;2026-03-04;RI;RI 20MAR26 93 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-02-16;SAP;SAP 20MAR26 220 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-03-10;SAP;SAP 20MAR26 240 P;Equity and Index Options;71.23;-7123.0;1.0;-8260.653157473;-1.17;C
Open;Trades;EUR;2026-03-10;SAP;SAP 18DEC26 190 P;Equity and Index Options;31.79;6358.0;-2.0;7373.4708374580005;-2.34;O
Open;Trades;GBP;2026-07-28;IMB;IMB 18DEC26 30 C;Equity and Index Options;0.9;900.0;-1.0;1193.8113396;-1.4;O
Open;Trades;GBP;2026-02-27;IMB;IMB 20MAR26 34.5 C;Equity and Index Options;0.23;230.0;-1.0;305.08512012;-1.4;O
Open;Trades;GBP;2026-03-20;IMB;IMB 20MAR26 34.5 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;GBP;2026-03-20;NWG;NWG 20MAR26 5 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2026-05-14;16;16 29JUN26 150 C;Equity and Index Options;3.86;15440.0;-4.0;1973.7713964000002;-42.88;O
Open;Trades;HKD;2026-06-29;16;16 29JUN26 150 C;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-08-03;AMZN;AMZN 18SEP26 300 C;Equity and Index Options;8.03;803.0;-1.0;803.0;-1.0601118;O
Open;Trades;USD;2026-07-22;BHP;BHP 18DEC26 95 C;Equity and Index Options;4.0;1600.0;-4.0;1600.0;0.20468;O
Open;Trades;USD;2026-07-24;BRK;BRK B 28AUG26 505 C;Equity and Index Options;6.95;695.0;-1.0;695.0;-0.797667;O
Open;Trades;USD;2026-07-31;BRK;BRK B 28AUG26 505 C;Equity and Index Options;14.37;-1437.0;1.0;-1437.0;-0.68947;C
Open;Trades;USD;2026-07-31;BRK;BRK B 18SEP26 520 C;Equity and Index Options;9.38;938.0;-1.0;938.0;-0.7120828;O
Open;Trades;USD;2026-08-10;BRK;BRK B 18SEP26 520 C;Equity and Index Options;20.35;-2035.0;1.0;-2035.0;-1.04028;C
Open;Trades;USD;2026-07-31;BRK;BRK B 18SEP26 495 P;Equity and Index Options;4.99;499.0;-1.0;499.0;-0.7030394;O
Open;Trades;USD;2026-08-10;BRK;BRK B 18SEP26 495 P;Equity and Index Options;1.38;-138.0;1.0;-138.0;-1.04028;C
Open;Trades;USD;2026-08-10;BRK;BRK B 18DEC26 545 C;Equity and Index Options;18.68;1868.0;-1.0;1868.0;-1.0820508;O
Open;Trades;USD;2026-08-10;BRK;BRK B 18DEC26 510 P;Equity and Index Options;9.17;917.0;-1.0;917.0;-1.0624602;O
Open;Trades;USD;2026-02-23;CPRT;CPRT 20MAR26 45 P;Equity and Index Options;0.0;0.0;3.0;0.0;0.0;"A;C"
Open;Trades;USD;2026-04-17;CPRT;CPRT 18JUN26 30 P;Equity and Index Options;0.7;280.0;-4.0;280.0;1.189272;O
Open;Trades;USD;2026-06-18;CPRT;CPRT 18JUN26 30 P;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-07-22;CPRT;CPRT 18SEP26 32.5 C;Equity and Index Options;0.3;180.0;-6.0;180.0;-9.295248;O
Open;Trades;USD;2026-07-22;CPRT;CPRT 18SEP26 25 P;Equity and Index Options;0.65;325.0;-5.0;325.0;-7.749645;O
Open;Trades;USD;2026-07-17;GEHC;GEHC 17JUN27 65 C;Equity and Index Options;8.87;-887.0;1.0;-887.0;-1.04028;O
Open;Trades;USD;2026-07-17;GEHC;GEHC 17JUN27 80 C;Equity and Index Options;3.93;393.0;-1.0;393.0;-1.0516658;O
Open;Trades;USD;2026-07-17;GEHC;GEHC 17JUN27 57.5 P;Equity and Index Options;5.35;535.0;-1.0;535.0;-1.054591;O
Open;Trades;USD;2026-03-10;GOOG;GOOG 17APR26 330 C;Equity and Index Options;4.3;860.0;-2.0;860.0;-1.40248;O
Open;Trades;USD;2026-04-14;GOOG;GOOG 17APR26 330 C;Equity and Index Options;3.62;-724.0;2.0;-724.0;-1.4009;"C;P"
Open;Trades;USD;2026-04-14;GOOG;GOOG 22MAY26 350 C;Equity and Index Options;7.88;1576.0;-2.0;1576.0;-1.4399456;"O;P"
Open;Trades;USD;2026-05-04;GOOG;GOOG 22MAY26 350 C;Equity and Index Options;32.43;-6486.0;2.0;-6486.0;-0.6965;C
Open;Trades;USD;2026-05-04;GOOG;GOOG 18DEC26 410 C;Equity and Index Options;32.06;6412.0;-2.0;6412.0;-0.8351672;O
Open;Trades;USD;2026-08-10;GOOG;GOOG 21JAN28 250 C;Equity and Index Options;132.315;-26463.0;2.0;-26463.0;-1.3666;"O;P"
Open;Trades;USD;2026-08-10;GOOG;GOOG 21JAN28 250 P;Equity and Index Options;13.475;2695.0;-2.0;2695.0;-1.428697;"O;P"
Open;Trades;USD;2026-03-06;ICE;ICE 17APR26 180 C;Equity and Index Options;1.26;378.0;-3.0;378.0;-2.10372;"O;P"
Open;Trades;USD;2026-04-17;ICE;ICE 17APR26 180 C;Equity and Index Options;0.0;0.0;3.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-03-06;META;META 17APR26 695 C;Equity and Index Options;10.45;1045.0;-1.0;1045.0;-0.70124;O
Open;Trades;USD;2026-04-17;META;META 17APR26 695 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-03-06;META;META 17APR26 580 P;Equity and Index Options;9.78;-978.0;1.0;-978.0;-0.69795;O
Open;Trades;USD;2026-04-01;META;META 17APR26 580 P;Equity and Index Options;18.4;1840.0;-1.0;1840.0;-0.77124;C
Open;Trades;USD;2026-03-30;META;META 15JAN27 570 C;Equity and Index Options;69.05;6905.0;-1.0;6905.0;-1.05124;O
Open;Trades;USD;2026-04-01;META;META 15JAN27 570 C;Equity and Index Options;97.35;-9735.0;1.0;-9735.0;-1.04795;C
Open;Trades;USD;2026-04-01;META;META 15JAN27 640 C;Equity and Index Options;65.4;6540.0;-1.0;6540.0;-1.05124;O
Open;Trades;USD;2026-04-10;META;META 15JAN27 640 C;Equity and Index Options;89.8;-8980.0;1.0;-8980.0;-0.69795;C
Open;Trades;USD;2026-04-10;META;META 15JAN27 800 C;Equity and Index Options;35.54;7108.0;-2.0;7108.0;-0.8489048;O
Open;Trades;USD;2026-04-10;META;META 15JAN27 460 P;Equity and Index Options;19.72;1972.0;-1.0;1972.0;-0.7418632;O
Open;Trades;USD;2026-03-30;META;META 15JAN27 520 P;Equity and Index Options;62.73;-6273.0;1.0;-6273.0;-1.04795;O
Open;Trades;USD;2026-02-27;META;META 15JAN27 640 P;Equity and Index Options;77.25;7725.0;-1.0;7725.0;-0.80204;O
Open;Trades;USD;2026-01-02;MSFT;MSFT 18JUN26 430 P;Equity and Index Options;15.55;1555.0;-1.0;1555.0;-1.05204;O
Open;Trades;USD;2026-04-02;MSFT;MSFT 18JUN26 430 P;Equity and Index Options;61.03;-6103.0;1.0;-6103.0;-1.05045;C
Open;Trades;USD;2026-01-29;MSFT;MSFT 15JAN27 420 P;Equity and Index Options;40.65;4065.0;-1.0;4065.0;-1.05454;O
Open;Trades;USD;2026-04-02;MSFT;MSFT 15JAN27 420 P;Equity and Index Options;65.66;6566.0;-1.0;6566.0;-1.1889996;O
Open;Trades;USD;2026-08-07;NBIS;NBIS 18DEC26 140 P;Equity and Index Options;24.14;4828.0;-2.0;4828.0;-0.7849768;O
Open;Trades;USD;2026-08-07;NBIS;NBIS 18DEC26 170 P;Equity and Index Options;39.78;-3978.0;1.0;-3978.0;-0.68947;O
Open;Trades;USD;2026-08-14;NBIS;NBIS 18DEC26 170 P;Equity and Index Options;15.94;3188.0;-2.0;3188.0;-0.7511928;"C;O"
Open;Trades;USD;2026-08-14;NBIS;NBIS 18DEC26 190 P;Equity and Index Options;22.38;-2238.0;1.0;-2238.0;-0.68947;O
Open;Trades;USD;2026-02-18;NVO;NVO 20MAR26 60 C;Equity and Index Options;0.29;58.0;-2.0;58.0;-1.40408;O
Open;Trades;USD;2026-03-20;NVO;NVO 20MAR26 60 C;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-02-23;NVO;NVO 20MAR26 65 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2026-07-31;PAYC;PAYC 21AUG26 200 C;Equity and Index Options;1.24;248.0;-2.0;248.0;-0.5812188;"O;P"
Open;Trades;USD;2026-07-31;PAYC;PAYC 21AUG26 145 P;Equity and Index Options;3.66;732.0;-2.0;732.0;-0.5911892;"O;P"
Open;Trades;USD;2026-08-06;PAYC;PAYC 21AUG26 200 P;Equity and Index Options;4.3;860.0;-2.0;860.0;-1.408896;O
Open;Trades;USD;2026-02-18;PDD;PDD 20MAR26 100 P;Equity and Index Options;3.25;325.0;-1.0;325.0;-1.05204;O
Open;Trades;USD;2026-03-20;PDD;PDD 20MAR26 100 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2026-05-12;PDD;PDD 20NOV26 100 C;Equity and Index Options;10.75;-1075.0;1.0;-1075.0;-0.69825;O
Open;Trades;USD;2026-05-12;PDD;PDD 20NOV26 130 C;Equity and Index Options;3.13;313.0;-1.0;313.0;-0.7079878;O
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 100 C;Equity and Index Options;12.32;-1232.0;1.0;-1232.0;-1.05075;O
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 100 C;Equity and Index Options;12.01;-1201.0;1.0;-1201.0;-0.69825;O
Open;Trades;USD;2026-03-19;PDD;PDD 18DEC26 110 C;Equity and Index Options;11.57;-1157.0;1.0;-1157.0;-1.05045;O
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 120 C;Equity and Index Options;5.94;594.0;-1.0;594.0;-1.0662764;O
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 120 C;Equity and Index Options;5.73;573.0;-1.0;573.0;-0.7133438;O
Open;Trades;USD;2026-03-19;PDD;PDD 18DEC26 130 C;Equity and Index Options;6.41;641.0;-1.0;641.0;-1.05374;O
Open;Trades;USD;2026-03-19;PDD;PDD 18DEC26 90 P;Equity and Index Options;9.77;977.0;-1.0;977.0;-1.05374;O
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 90 P;Equity and Index Options;8.08;808.0;-1.0;808.0;-1.0706848;O
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 90 P;Equity and Index Options;8.12;812.0;-1.0;812.0;-0.7182672;O
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 90 P;Equity and Index Options;8.28;828.0;-1.0;828.0;-0.7185968;O
Open;Trades;USD;2026-08-07;SKHY;SKHY 18DEC26 115 P;Equity and Index Options;16.44;3288.0;-2.0;3288.0;0.1655672;O
Open;Trades;USD;2026-08-07;SKHY;SKHY 18DEC26 130 P;Equity and Index Options;24.43;-2443.0;1.0;-2443.0;-0.23006;O
Open;Trades;USD;2026-08-07;SMCI;SMCI 18DEC26 24 P;Equity and Index Options;2.97;1188.0;-4.0;1188.0;-2.7987528;"O;P"
Open;Trades;USD;2026-08-07;SMCI;SMCI 18DEC26 27 P;Equity and Index Options;4.31;-862.0;2.0;-862.0;-1.38056;"O;P"
Open;Trades;USD;2026-03-20;TSM;TSM 20MAR26 230 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-01-29;UNH;UNH 20FEB26 350 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2026-02-18;UNH;UNH 20MAR26 320 C;Equity and Index Options;1.67;334.0;-2.0;334.0;-0.90408;O
Open;Trades;USD;2026-03-20;UNH;UNH 20MAR26 320 C;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-04-02;UNH;UNH 15MAY26 280 C;Equity and Index Options;16.08;-1608.0;1.0;-1608.0;-1.05045;O
Open;Trades;USD;2026-05-14;UNH;UNH 15MAY26 280 C;Equity and Index Options;118.9;11890.0;-1.0;11890.0;-0.946474;C
Open;Trades;USD;2026-04-02;UNH;UNH 15MAY26 300 C;Equity and Index Options;8.25;1650.0;-2.0;1650.0;-1.44147;O
Open;Trades;USD;2026-04-07;UNH;UNH 15MAY26 300 C;Equity and Index Options;18.745;-3749.0;2.0;-3749.0;-0.6959;"C;P"
Open;Trades;USD;2026-04-07;UNH;UNH 18SEP26 330 C;Equity and Index Options;19.745;3949.0;-2.0;3949.0;-0.7838294;"O;P"
Open;Trades;USD;2026-05-04;UNH;UNH 18SEP26 330 C;Equity and Index Options;52.78;-10556.0;2.0;-10556.0;-0.6965;C
Open;Trades;USD;2026-05-14;UNH;UNH 18DEC26 440 C;Equity and Index Options;26.02;2602.0;-1.0;2602.0;-0.7551412;O
Open;Trades;USD;2026-07-22;UNH;UNH 15JAN27 360 P;Equity and Index Options;12.2;1220.0;-1.0;1220.0;-1.061722;O
Open;Trades;USD;2026-07-22;UNH;UNH 17JUN27 360 P;Equity and Index Options;21.5;2150.0;-1.0;2150.0;-1.08088;O
Open;Trades;USD;2026-05-04;UNH;UNH 17DEC27 390 C;Equity and Index Options;54.1;5410.0;-1.0;5410.0;-0.812986;O
Open;Trades;USD;2026-05-14;UNH;UNH 17DEC27 390 C;Equity and Index Options;75.35;-7535.0;1.0;-7535.0;-0.69825;C
Open;Trades;USD;2026-05-04;UNH;UNH 17DEC27 400 C;Equity and Index Options;50.46;5046.0;-1.0;5046.0;-0.8054876;O
Open;Trades;USD;2026-05-14;UNH;UNH 17DEC27 400 C;Equity and Index Options;71.17;-7117.0;1.0;-7117.0;-0.69825;C
Open;Trades;USD;2026-02-20;912797TC1;912797TC1 3.55127091%;Treasury Bills;97.1272;-71874.13;74000.0;-71874.13;-5.0;O
Open;Trades;USD;2026-04-17;912797TV9;912797TV9 3.64107797%;Treasury Bills;97.0566889;-65998.55;68000.0;-65998.55;-5.0;O
Open;Trades;USD;2026-06-12;912797UE5;912797UE5 3.8394375%;Treasury Bills;96.9013111;-96901.31;100000.0;-96901.31;-5.0;O
Open;Trades;USD;2026-06-12;912797UX3;912797UX3 3.815%;Treasury Bills;96.61841;-104347.88;108000.0;-104347.88;-7.5;O
Open;Trades;USD;2026-06-22;912797VF1;912797VF1 4.043%;Treasury Bills;96.212;-31749.96;33000.0;-31749.96;-12.5;"O;P"
Open;Trades;USD;2026-06-23;912797VF1;912797VF1 4.03054553%;Treasury Bills;96.268675;-84716.43;88000.0;-84716.43;-5.0;O
Open;Trades;USD;2026-07-10;912797VF1;912797VF1 3.986%;Treasury Bills;96.470857091;-137953.32;143000.0;-137953.32;-7.5;"O;P"
Open;Trades;USD;2026-08-06;912797VQ7;912797VQ7 4.05724169%;Treasury Bills;96.4099167;-96409.92;100000.0;-96409.92;-5.0;O
Open;Trades;USD;2026-08-06;912797VQ7;912797VQ7 4.011%;Treasury Bills;96.41821;-79062.93;82000.0;-79062.93;-7.5;O
Open;Transaction Fees;EUR;2026-03-04;RI;RI;Stocks;93.0;-37.2;0.0;-43.141414777200005;;
Open;Transaction Fees;HKD;2026-04-01;1810;1810;Stocks;31.8;-7.18126;0.0;-0.9180159053181001;;
Open;Transaction Fees;HKD;2026-04-10;1810;1810;Stocks;31.0;-7.1767;0.0;-0.9174329780145001;;
Open;Transaction Fees;HKD;2026-04-10;700;700;Stocks;502.0;-52.4307;0.0;-6.702475126504501;;
Open;Withholding Tax;EUR;2026-02-18;RMS;RMS;;;-3.72;;-4.314141477720001;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-11.22604556568;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;9.68;;11.22604556568;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-11.22604556568;;
Open;Withholding Tax;EUR;2026-04-30;MC;MC;;;-18.75;;-21.744664706250003;;
Open;Withholding Tax;EUR;2026-05-08;SAP;SAP;;;-61.32;;-71.11375145532;;
Open;Withholding Tax;EUR;2026-07-24;RI;RI;;;-58.75;;-68.13328274625;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.57801006295;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;90.57;;11.57801006295;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.57801006295;;
Open;Withholding Tax;HKD;2026-01-26;939;939;;;-81.86;;-10.464567779100001;;
Open;Withholding Tax;HKD;2026-02-09;3968;3968;;;-33.22;;-4.2466765407;;
Open;Withholding Tax;HKD;2026-06-16;1398;1398;;;-116.65;;-14.911945167750002;;
Open;Withholding Tax;HKD;2026-06-26;386;386;;;-25.78;;-3.2955846243000004;;
Open;Withholding Tax;HKD;2026-07-15;2318;2318;;;-100.56;;-12.855081063600002;;
Open;Withholding Tax;HKD;2026-07-31;857;857;;;-22.27;;-2.8468840024500004;;
Open;Withholding Tax;HKD;2026-08-13;390;390;;;-98.9;;-12.642875071500002;;
Open;Withholding Tax;USD;2025-03-21;IVV;IVV;;;-0.01;;-0.01;;
Open;Withholding Tax;USD;2025-03-21;IVV;IVV;;;-0.25;;-0.25;;
Open;Withholding Tax;USD;2025-03-21;IVV;IVV;;;51.84;;51.84;;
Open;Withholding Tax;USD;2025-03-21;IVV;IVV;;;-51.59;;-51.59;;
Open;Withholding Tax;USD;2025-06-20;IVV;IVV;;;-0.27;;-0.27;;
Open;Withholding Tax;USD;2025-06-20;IVV;IVV;;;56.01;;56.01;;
Open;Withholding Tax;USD;2025-06-20;IVV;IVV;;;-55.74;;-55.74;;
Open;Withholding Tax;USD;2025-09-19;IVV;IVV;;;59.84;;59.84;;
Open;Withholding Tax;USD;2025-09-19;IVV;IVV;;;-59.55;;-59.55;;
Open;Withholding Tax;USD;2025-12-19;IVV;IVV;;;72.41;;72.41;;
Open;Withholding Tax;USD;2025-12-19;IVV;IVV;;;-72.05;;-72.05;;
Open;Withholding Tax;USD;2026-01-08;TSM;TSM;;;-116.92;;-116.92;;
Open;Withholding Tax;USD;2026-02-02;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2026-02-26;FAST;FAST;;;-7.2;;-7.2;;
Open;Withholding Tax;USD;2026-03-16;GOOG;GOOG;;;-25.2;;-25.2;;
Open;Withholding Tax;USD;2026-03-17;UNH;UNH;;;-132.6;;-132.6;;
Open;Withholding Tax;USD;2026-03-20;IVV;IVV;;;-53.51;;-53.51;;
Open;Withholding Tax;USD;2026-03-23;PAYC;PAYC;;;-0.9;;-0.9;;
Open;Withholding Tax;USD;2026-03-23;PAYC;PAYC;;;-21.6;;-21.6;;
Open;Withholding Tax;USD;2026-03-26;META;META;;;-31.5;;-31.5;;
Open;Withholding Tax;USD;2026-03-27;NOV;NOV;;;-8.1;;-8.1;;
Open;Withholding Tax;USD;2026-03-31;ICE;ICE;;;-18.56;;-18.56;;
Open;Withholding Tax;USD;2026-03-31;ICE;ICE;;;-28.24;;-28.24;;
Open;Withholding Tax;USD;2026-04-08;NVO;NVO;;;-65.74;;-65.74;;
Open;Withholding Tax;USD;2026-04-09;TSM;TSM;;;-59.16;;-59.16;;
Open;Withholding Tax;USD;2026-05-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2026-05-26;FAST;FAST;;;-7.13;;-7.13;;
Open;Withholding Tax;USD;2026-05-26;FAST;FAST;;;-0.07;;-0.07;;
Open;Withholding Tax;USD;2026-06-08;PAYC;PAYC;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2026-06-12;NOV;NOV;;;-0.46;;-0.46;;
Open;Withholding Tax;USD;2026-06-12;NOV;NOV;;;-7.64;;-7.64;;
Open;Withholding Tax;USD;2026-06-15;GOOG;GOOG;;;-26.4;;-26.4;;
Open;Withholding Tax;USD;2026-06-18;IVV;IVV;;;-59.87;;-59.87;;
Open;Withholding Tax;USD;2026-06-23;UNH;UNH;;;-139.2;;-139.2;;
Open;Withholding Tax;USD;2026-06-25;META;META;;;-31.5;;-31.5;;
Open;Withholding Tax;USD;2026-06-26;NOV;NOV;;;-8.1;;-8.1;;
Open;Withholding Tax;USD;2026-06-30;ICE;ICE;;;-1.4;;-1.4;;
Open;Withholding Tax;USD;2026-06-30;ICE;ICE;;;-45.4;;-45.4;;
Open;Withholding Tax;USD;2026-07-09;TSM;TSM;;;-59.18;;-59.18;;
Open;Withholding Tax;USD;2026-08-03;FCX;FCX;;;-2.03;;-2.03;;
Open;Withholding Tax;USD;2026-08-03;FCX;FCX;;;-20.48;;-20.48;;
Open;Dividends;CNH;2026-07-31;3188;3188;;;548.9;;81.76066177839999;;
Open;Dividends;CNH;2026-07-31;3188;3188;;;1.1;;0.1638490216;;
Open;Dividends;EUR;2026-02-18;RMS;RMS;;;15.0;;17.395731765;;
Open;Dividends;EUR;2026-04-23;RMS;RMS;;;39.0;;45.228902589;;
Open;Dividends;EUR;2026-04-30;MC;MC;;;75.0;;86.97865882500001;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;232.5;;269.6338423575;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;17.5;;20.2950203925;;
Open;Dividends;EUR;2026-07-24;RI;RI;;;235.0;;272.533130985;;
Open;Dividends;GBP;2025-12-31;IMB;IMB;;;400.8;;531.6439832352;;
Open;Dividends;GBP;2026-03-31;IMB;IMB;;;400.8;;531.6439832352;;
Open;Dividends;GBP;2026-06-30;IMB;IMB;;;416.8;;552.8672959392001;;
Open;Dividends;HKD;2026-01-26;1398;1398;;;931.62;;119.09358214470001;;
Open;Dividends;HKD;2026-01-26;939;939;;;818.62;;104.64823448970002;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;220.7;;28.213170154500002;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;333.34;;42.6124972329;;
Open;Dividends;HKD;2026-03-19;16;16;;;4410.0;;563.7520633500001;;
Open;Dividends;HKD;2026-03-27;14;14;;;810.0;;103.54629735;;
Open;Dividends;HKD;2026-04-23;808;808;;;1142.0;;145.98749577;;
Open;Dividends;HKD;2026-04-30;5;5;;;8148.56;;1041.6706379436002;;
Open;Dividends;HKD;2026-04-30;5;5;;;306.5;;39.1814075775;;
Open;Dividends;HKD;2026-05-07;1972;1972;;;4480.0;;572.7005088000001;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;3.84;;0.4908861504;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;103.61;;13.24497761535;;
Open;Dividends;HKD;2026-05-29;215;215;;;2605.0;;333.010005675;;
Open;Dividends;HKD;2026-05-29;2800;2800;;;285.0;;36.432956475000005;;
Open;Dividends;HKD;2026-06-01;700;700;;;3741.8;;478.3327597830001;;
Open;Dividends;HKD;2026-06-12;1299;1299;;;864.48;;110.51074460880001;;
Open;Dividends;HKD;2026-06-12;27;27;;;800.0;;102.267948;;
Open;Dividends;HKD;2026-06-16;1398;1398;;;1166.46;;149.11433828010001;;
Open;Dividends;HKD;2026-06-22;66;66;;;439.66;;56.20390752210001;;
Open;Dividends;HKD;2026-06-22;66;66;;;5.34;;0.6826385529000001;;
Open;Dividends;HKD;2026-06-25;1883;1883;;;1300.0;;166.1854155;;
Open;Dividends;HKD;2026-06-26;386;386;;;257.8;;32.955846243;;
Open;Dividends;HKD;2026-06-26;5;5;;;1661.92;;212.45143517520003;;
Open;Dividends;HKD;2026-06-26;5;5;;;217.73;;27.83350039755;;
Open;Dividends;HKD;2026-06-30;2828;2828;;;280.0;;35.793781800000005;;
Open;Dividends;HKD;2026-07-15;2318;2318;;;1005.64;;128.5559240334;;
Open;Dividends;HKD;2026-07-31;857;857;;;222.66;;28.4637266271;;
Open;Dividends;HKD;2026-07-31;857;857;;;316.75;;40.491715661250005;;
Open;Dividends;HKD;2026-08-07;3918;3918;;;172.04;;21.9927222174;;
Open;Dividends;HKD;2026-08-13;390;390;;;989.0;;126.428750715;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.96;;0.73640268192;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.26;;0.19944239302;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.36;;0.27615100572;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.86;;0.65969406922;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;0.8;;0.6136689016000001;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;0.4;;0.30683445080000005;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;9.04;;6.934458588079999;;
Open;Dividends;SGD;2026-04-17;D05;D05;;;33.0;;25.313842191;;
Open;Dividends;SGD;2026-04-17;D05;D05;;;145.2;;111.38090564039999;;
Open;Dividends;SGD;2026-05-08;O39;O39;;;128.0;;98.187024256;;
Open;Dividends;SGD;2026-05-08;O39;O39;;;336.0;;257.740938672;;
Open;Dividends;SGD;2026-05-14;9CI;9CI;;;144.0;;110.460402288;;
Open;Dividends;SGD;2026-05-14;BS6;BS6;;;1400.0;;1073.9205778;;
Open;Dividends;SGD;2026-05-14;F34;F34;;;900.0;;690.3775143;;
Open;Dividends;SGD;2026-05-20;D05;D05;;;33.0;;25.313842191;;
Open;Dividends;SGD;2026-05-20;D05;D05;;;145.2;;111.38090564039999;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;0.6;;0.46025167619999996;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;0.13;;0.09972119651;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;8.1;;6.2133976287;;
Open;Dividends;USD;2026-01-08;TSM;TSM;;;556.79;;556.79;;
Open;Dividends;USD;2026-02-02;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2026-02-26;FAST;FAST;;;24.0;;24.0;;
Open;Dividends;USD;2026-03-16;GOOG;GOOG;;;84.0;;84.0;;
Open;Dividends;USD;2026-03-17;UNH;UNH;;;442.0;;442.0;;
Open;Dividends;USD;2026-03-20;IVV;IVV;;;178.35;;178.35;;
Open;Dividends;USD;2026-03-23;PAYC;PAYC;;;3.0;;3.0;;
Open;Dividends;USD;2026-03-23;PAYC;PAYC;;;72.0;;72.0;;
Open;Dividends;USD;2026-03-26;BHP;BHP;;;1736.37;;1736.37;;
Open;Dividends;USD;2026-03-26;META;META;;;105.0;;105.0;;
Open;Dividends;USD;2026-03-27;NOV;NOV;;;27.0;;27.0;;
Open;Dividends;USD;2026-03-31;ICE;ICE;;;61.88;;61.88;;
Open;Dividends;USD;2026-03-31;ICE;ICE;;;94.12;;94.12;;
Open;Dividends;USD;2026-04-02;SOUHY;SOUHY;;;53.82;;53.82;;
Open;Dividends;USD;2026-04-08;NVO;NVO;;;243.48;;243.48;;
Open;Dividends;USD;2026-04-09;TSM;TSM;;;281.7;;281.7;;
Open;Dividends;USD;2026-04-23;9618;9618;;;9.5;;9.5;;
Open;Dividends;USD;2026-05-01;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2026-05-13;H78;H78;;;190.0;;190.0;;
Open;Dividends;USD;2026-05-26;FAST;FAST;;;23.76;;23.76;;
Open;Dividends;USD;2026-05-26;FAST;FAST;;;0.24;;0.24;;
Open;Dividends;USD;2026-06-08;PAYC;PAYC;;;75.0;;75.0;;
Open;Dividends;USD;2026-06-12;NOV;NOV;;;1.53;;1.53;;
Open;Dividends;USD;2026-06-12;NOV;NOV;;;25.47;;25.47;;
Open;Dividends;USD;2026-06-15;GOOG;GOOG;;;88.0;;88.0;;
Open;Dividends;USD;2026-06-18;IVV;IVV;;;199.57;;199.57;;
Open;Dividends;USD;2026-06-23;UNH;UNH;;;464.0;;464.0;;
Open;Dividends;USD;2026-06-25;META;META;;;105.0;;105.0;;
Open;Dividends;USD;2026-06-26;NOV;NOV;;;27.0;;27.0;;
Open;Dividends;USD;2026-06-30;ICE;ICE;;;4.68;;4.68;;
Open;Dividends;USD;2026-06-30;ICE;ICE;;;151.32;;151.32;;
Open;Dividends;USD;2026-07-09;TSM;TSM;;;281.8;;281.8;;
Open;Dividends;USD;2026-07-13;BABA;BABA;;;525.0;;525.0;;
Open;Dividends;USD;2026-08-03;FCX;FCX;;;6.75;;6.75;;
Open;Dividends;USD;2026-08-03;FCX;FCX;;;68.25;;68.25;;
Open;Trades;EUR;2025-10-22;MC;MC;Stocks;618.7;-6187.0;10.0;-7175.159495337;-3.851265;O
Open;Trades;EUR;2025-10-22;RMS;RMS;Stocks;2194.0;-6582.0;3.0;-7633.247098482;-4.08629;O
Open;Trades;GBP;2025-05-22;IMB;IMB;Stocks;30.75;-30750.0;1000.0;-40788.554103;-153.75;"A;O"
Open;Trades;HKD;2025-03-26;1810;1810;Stocks;53.35;-10670.0;200.0;-1363.9987564500002;-31.90695;O
Open;Trades;HKD;2025-06-30;2888;2888;Stocks;134.1;13410.0;-100.0;1714.2664783500002;-33.70307;C
Open;Trades;SGD;2025-05-01;C38U;C38U.DIST;Stocks;0.0;0.0;-0.2924;0.0;0.0;C
Open;Trades;USD;2025-01-30;BABA;BABA;Stocks;101.3;20260.0;-200.0;20260.0;-1.1039425;"C;P"
Open;Trades;USD;2025-02-14;BABA;BABA;Stocks;105.0;52500.0;-500.0;52500.0;-1.5425;"A;C"
Open;Trades;USD;2025-02-21;BABA;BABA;Stocks;120.0;48000.0;-400.0;48000.0;-1.4008;"A;C"
Open;Trades;USD;2025-02-21;BABA;BABA;Stocks;114.0;-22800.0;200.0;-22800.0;0.0;"Ex;O"
Open;Trades;USD;2025-03-20;BABA;BABA;Stocks;77.5;-7750.0;100.0;-7750.0;0.0;"Ex;O"
Open;Trades;USD;2025-09-11;BABA;BABA;Stocks;151.0;75500.0;-500.0;75500.0;-1.34528625;C
Open;Trades;USD;2025-03-20;BRK;BRK B;Stocks;425.0;-42500.0;100.0;-42500.0;0.0;"Ex;O"
Open;Trades;USD;2025-03-20;BRK;BRK B;Stocks;435.0;-43500.0;100.0;-43500.0;0.0;"Ex;O"
Open;Trades;USD;2025-11-14;BRK;BRK B;Stocks;505.0;50500.0;-100.0;50500.0;-0.0166;"A;C"
Open;Trades;USD;2025-10-17;FAST;FAST;Stocks;42.17;-4217.0;100.0;-4217.0;-0.24245725;"O;P"
Open;Trades;USD;2025-01-13;ICE;ICE;Stocks;155.0;-46500.0;300.0;-46500.0;0.0;"A;O"
Open;Trades;USD;2025-07-03;MSFT;MSFT;Stocks;500.0;100000.0;-200.0;100000.0;-0.5181145;C
Open;Trades;USD;2025-11-05;PAYC;PAYC;Stocks;230.0;-46000.0;200.0;-46000.0;0.0;"A;O"
Open;Trades;USD;2025-03-27;TSM;TSM;Stocks;185.0;-185000.0;1000.0;-185000.0;0.0;"A;O"
Open;Trades;USD;2025-07-03;TSM;TSM;Stocks;236.2;70860.0;-300.0;70860.0;-0.80717175;"C;P"
Open;Trades;EUR;2025-09-03;RMS;RMS 19JUN26 2040 P;Equity and Index Options;170.0;17000.0;-1.0;19715.162667;-1.14;O
Open;Trades;EUR;2025-07-15;RI;RI 20MAR26 93 P;Equity and Index Options;9.3;930.0;-1.0;1078.53536943;-1.14;O
Open;Trades;EUR;2025-03-26;SAP;SAP 16MAY25 235 P;Equity and Index Options;5.35;535.0;-1.0;620.4477662850001;-1.17;O
Open;Trades;EUR;2025-05-16;SAP;SAP 16MAY25 235 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2025-08-15;SAP;SAP 20MAR26 220 P;Equity and Index Options;9.9;990.0;-1.0;1148.1182964900001;-1.17;O
Open;Trades;EUR;2025-08-08;SAP;SAP 20MAR26 240 P;Equity and Index Options;13.8;1380.0;-1.0;1600.40732238;-1.17;O
Open;Trades;EUR;2025-03-21;SAP;SAP 21MAR25 220 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;GBP;2025-11-26;IMB;IMB 19DEC25 33 C;Equity and Index Options;0.31;310.0;-1.0;411.20168364;-1.4;O
Open;Trades;GBP;2025-12-19;IMB;IMB 19DEC25 33 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;GBP;2025-05-08;IMB;IMB 20JUN25 30.75 P;Equity and Index Options;0.95;950.0;-1.0;1260.1341918;-1.4;O
Open;Trades;GBP;2025-05-22;IMB;IMB 20JUN25 30.75 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;GBP;2025-03-27;NWG;NWG 17APR25 4.5 P;Equity and Index Options;0.0725;72.5;-1.0;96.16813569;-1.4;O
Open;Trades;GBP;2025-04-17;NWG;NWG 17APR25 4.5 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;GBP;2025-05-07;NWG;NWG 20JUN25 4.6 P;Equity and Index Options;0.1;200.0;-2.0;265.2914088;-2.8;O
Open;Trades;GBP;2025-06-20;NWG;NWG 20JUN25 4.6 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;GBP;2025-09-19;NWG;NWG 20MAR26 5 P;Equity and Index Options;0.3475;347.5;-1.0;460.94382279;-1.4;O
Open;Trades;GBP;2025-09-19;NWG;NWG 18SEP26 5 P;Equity and Index Options;0.51;510.0;-1.0;676.49309244;-1.4;O
Open;Trades;USD;2025-03-21;AMZN;AMZN 21MAR25 190 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-01-17;BABA;BABA 17JAN25 95 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-01-30;BABA;BABA 14FEB25 105 C;Equity and Index Options;3.27;1635.0;-5.0;1635.0;-3.585653;"O;P"
Open;Trades;USD;2025-02-14;BABA;BABA 14FEB25 105 C;Equity and Index Options;0.0;0.0;5.0;0.0;0.0;"A;C"
Open;Trades;USD;2025-02-11;BABA;BABA 21FEB25 114 C;Equity and Index Options;5.87;-1174.0;2.0;-1174.0;-1.4155;"O;P"
Open;Trades;USD;2025-02-21;BABA;BABA 21FEB25 114 C;Equity and Index Options;0.0;0.0;-2.0;0.0;0.0;"C;Ex"
Open;Trades;USD;2025-02-11;BABA;BABA 21FEB25 120 C;Equity and Index Options;3.58;1432.0;-4.0;1432.0;-2.8819696;"O;P"
Open;Trades;USD;2025-02-21;BABA;BABA 21FEB25 120 C;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"A;C"
Open;Trades;USD;2025-03-20;BABA;BABA 21MAR25 77.5 C;Equity and Index Options;0.0;0.0;-1.0;0.0;0.0;"C;Ex"
Open;Trades;USD;2025-05-13;BABA;BABA 16MAY25 140 C;Equity and Index Options;1.73;1730.0;-10.0;1730.0;-4.655494;O
Open;Trades;USD;2025-05-16;BABA;BABA 16MAY25 140 C;Equity and Index Options;0.0;0.0;10.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-08-13;BABA;BABA 15AUG25 127 C;Equity and Index Options;2.04;1020.0;-5.0;1020.0;-2.2712;O
Open;Trades;USD;2025-08-15;BABA;BABA 15AUG25 127 C;Equity and Index Options;0.0;0.0;5.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-12-02;BABA;BABA 12DEC25 152.5 P;Equity and Index Options;1.41;282.0;-2.0;282.0;-0.83378;O
Open;Trades;USD;2025-12-12;BABA;BABA 12DEC25 152.5 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-01-17;BRK;BRK B 17JAN25 470 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-01-24;BRK;BRK B 24JAN25 465 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-03-20;BRK;BRK B 21MAR25 425 C;Equity and Index Options;0.0;0.0;-1.0;0.0;0.0;"C;Ex"
Open;Trades;USD;2025-03-20;BRK;BRK B 21MAR25 435 C;Equity and Index Options;0.0;0.0;-1.0;0.0;0.0;"C;Ex"
Open;Trades;USD;2025-11-12;BRK;BRK B 14NOV25 505 C;Equity and Index Options;1.0;100.0;-1.0;100.0;-1.04869;O
Open;Trades;USD;2025-11-14;BRK;BRK B 14NOV25 505 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2025-07-15;CPRT;CPRT 20MAR26 45 P;Equity and Index Options;3.2;960.0;-3.0;960.0;0.88068;O
Open;Trades;USD;2025-12-10;GOOG;GOOG 12DEC25 330 C;Equity and Index Options;0.53;212.0;-4.0;212.0;-2.66756;"O;P"
Open;Trades;USD;2025-12-12;GOOG;GOOG 12DEC25 330 C;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-01-13;ICE;ICE 17JAN25 155 P;Equity and Index Options;0.0;0.0;3.0;0.0;0.0;"A;C"
Open;Trades;USD;2025-02-14;META;META 21FEB25 735 C;Equity and Index Options;7.7;1540.0;-2.0;1540.0;-1.458892;"O;P"
Open;Trades;USD;2025-02-21;META;META 21FEB25 735 C;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-11-19;META;META 21NOV25 585 P;Equity and Index Options;8.1;810.0;-1.0;810.0;-1.04869;O
Open;Trades;USD;2025-11-21;META;META 21NOV25 585 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-07-15;NVO;NVO 20MAR26 65 P;Equity and Index Options;6.74;1348.0;-2.0;1348.0;-0.89288;O
Open;Trades;USD;2025-05-29;PAYC;PAYC 21NOV25 230 P;Equity and Index Options;14.0;2800.0;-2.0;2800.0;0.57852;O
Open;Trades;USD;2025-11-05;PAYC;PAYC 21NOV25 230 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2025-02-19;TSM;TSM 28MAR25 185 P;Equity and Index Options;3.4;3400.0;-10.0;3400.0;-8.07492;O
Open;Trades;USD;2025-03-27;TSM;TSM 28MAR25 185 P;Equity and Index Options;0.0;0.0;10.0;0.0;0.0;"A;C"
Open;Trades;USD;2025-05-13;TSM;TSM 16MAY25 197.5 C;Equity and Index Options;1.3;1300.0;-10.0;1300.0;-4.34354;O
Open;Trades;USD;2025-05-16;TSM;TSM 16MAY25 197.5 C;Equity and Index Options;0.0;0.0;10.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-11-17;TSM;TSM 21NOV25 272.5 P;Equity and Index Options;1.98;198.0;-1.0;198.0;-1.04869;O
Open;Trades;USD;2025-11-21;TSM;TSM 21NOV25 272.5 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-12-02;TSM;TSM 05DEC25 300 C;Equity and Index Options;1.35;270.0;-2.0;270.0;-1.33378;O
Open;Trades;USD;2025-12-05;TSM;TSM 05DEC25 300 C;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-12-09;TSM;TSM 12DEC25 307.5 C;Equity and Index Options;1.94;388.0;-2.0;388.0;-1.33378;O
Open;Trades;USD;2025-12-12;TSM;TSM 12DEC25 307.5 C;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-12-09;TSM;TSM 19DEC25 312.5 C;Equity and Index Options;3.15;630.0;-2.0;630.0;-0.77378;O
Open;Trades;USD;2025-12-19;TSM;TSM 19DEC25 312.5 C;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2025-08-15;TSM;TSM 20MAR26 230 P;Equity and Index Options;18.79;1879.0;-1.0;1879.0;-1.05424;O
Open;Trades;USD;2025-09-23;UNH;UNH 20FEB26 350 P;Equity and Index Options;32.8;3280.0;-1.0;3280.0;-1.05344;O
Open;Trades;USD;2025-10-29;UNH;UNH 20FEB26 350 P;Equity and Index Options;22.95;2295.0;-1.0;2295.0;-1.04869;O
Open;Corporate Actions;SGD;2025-05-02;C38U;C38U.DIST;Stocks;;0.0;37.2924;0.0;;
Open;Corporate Actions;SGD;2025-05-14;C38U;C38U;Stocks;;0.0;37.0;0.0;;
Open;Corporate Actions;SGD;2025-05-14;C38U;C38U.DIST;Stocks;;0.0;-37.0;0.0;;
Open;Transaction Fees;EUR;2025-10-22;MC;MC;Stocks;618.7;-24.75;0.0;-28.70295741225;;
Open;Transaction Fees;EUR;2025-10-22;RMS;RMS;Stocks;2194.0;-26.33;0.0;-30.53530782483;;
Open;Transaction Fees;GBP;2025-05-22;IMB;IMB;Stocks;30.75;-153.75;0.0;-203.942770515;;
Open;Transaction Fees;GBP;2025-05-22;IMB;IMB;Stocks;0.0;-1.5;0.0;-1.9896855660000001;;
Open;Transaction Fees;HKD;2025-03-26;1810;1810;Stocks;53.35;-11.304095;0.0;-1.4450582495588251;;
Open;Transaction Fees;HKD;2025-06-30;2888;2888;Stocks;134.1;-14.382185;0.0;-1.838545684632975;;
Open;Withholding Tax;EUR;2025-12-04;MC;MC;;;-13.75;;-15.946087451250001;;
Open;Withholding Tax;HKD;2025-01-24;1398;1398;;;-93.17;;-11.910380893950002;;
Open;Withholding Tax;HKD;2025-01-27;939;939;;;-85.25;;-10.89792820875;;
Open;Withholding Tax;HKD;2025-05-28;939;939;;;-88.71;;-11.34023708385;;
Open;Withholding Tax;HKD;2025-06-27;386;386;;;-30.46;;-3.8938521201000005;;
Open;Withholding Tax;HKD;2025-06-30;2318;2318;;;-88.13;;-11.26609282155;;
Open;Withholding Tax;HKD;2025-07-24;857;857;;;-54.54;;-6.972117354900001;;
Open;Withholding Tax;HKD;2025-07-30;3968;3968;;;-94.82;;-12.1213085367;;
Open;Withholding Tax;HKD;2025-07-31;390;390;;;-194.8;;-24.902245338000004;;
Open;Withholding Tax;HKD;2025-08-22;1398;1398;;;-108.23;;-13.835575015050003;;
Open;Withholding Tax;USD;2025-02-03;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2025-03-13;MSFT;MSFT;;;-49.8;;-49.8;;
Open;Withholding Tax;USD;2025-03-17;GOOG;GOOG;;;-24.0;;-24.0;;
Open;Withholding Tax;USD;2025-03-21;IVV;IVV;;;-51.84;;-51.84;;
Open;Withholding Tax;USD;2025-03-21;IVV;IVV;;;-1.09;;-1.09;;
Open;Withholding Tax;USD;2025-03-26;META;META;;;-31.5;;-31.5;;
Open;Withholding Tax;USD;2025-03-28;NOV;NOV;;;-6.75;;-6.75;;
Open;Withholding Tax;USD;2025-03-31;ICE;ICE;;;-43.2;;-43.2;;
Open;Withholding Tax;USD;2025-05-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2025-06-12;MSFT;MSFT;;;-49.8;;-49.8;;
Open;Withholding Tax;USD;2025-06-13;NOV;NOV;;;-18.9;;-18.9;;
Open;Withholding Tax;USD;2025-06-16;GOOG;GOOG;;;-25.2;;-25.2;;
Open;Withholding Tax;USD;2025-06-20;IVV;IVV;;;-56.01;;-56.01;;
Open;Withholding Tax;USD;2025-06-26;META;META;;;-31.5;;-31.5;;
Open;Withholding Tax;USD;2025-06-27;NOV;NOV;;;-6.75;;-6.75;;
Open;Withholding Tax;USD;2025-06-30;ICE;ICE;;;-43.2;;-43.2;;
Open;Withholding Tax;USD;2025-07-10;TSM;TSM;;;-163.86;;-163.86;;
Open;Withholding Tax;USD;2025-08-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2025-09-15;GOOG;GOOG;;;-25.2;;-25.2;;
Open;Withholding Tax;USD;2025-09-19;IVV;IVV;;;-59.84;;-59.84;;
Open;Withholding Tax;USD;2025-09-26;NOV;NOV;;;-6.75;;-6.75;;
Open;Withholding Tax;USD;2025-09-29;META;META;;;-31.5;;-31.5;;
Open;Withholding Tax;USD;2025-09-30;ICE;ICE;;;-43.2;;-43.2;;
Open;Withholding Tax;USD;2025-10-09;TSM;TSM;;;-120.83;;-120.83;;
Open;Withholding Tax;USD;2025-11-03;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2025-11-25;FAST;FAST;;;-6.6;;-6.6;;
Open;Withholding Tax;USD;2025-12-08;PAYC;PAYC;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2025-12-15;GOOG;GOOG;;;-25.2;;-25.2;;
Open;Withholding Tax;USD;2025-12-19;IVV;IVV;;;-72.41;;-72.41;;
Open;Withholding Tax;USD;2025-12-19;NOV;NOV;;;-0.88;;-0.88;;
Open;Withholding Tax;USD;2025-12-19;NOV;NOV;;;-5.87;;-5.87;;
Open;Withholding Tax;USD;2025-12-23;META;META;;;-31.5;;-31.5;;
Open;Withholding Tax;USD;2025-12-31;ICE;ICE;;;-43.2;;-43.2;;
Open;Dividends;CNH;2025-07-31;3188;3188;;;530.0;;78.94543768;;
Open;Dividends;EUR;2025-12-04;MC;MC;;;55.0;;63.784349805000005;;
Open;Dividends;GBP;2025-09-30;IMB;IMB;;;400.8;;531.6439832352;;
Open;Dividends;HKD;2025-01-24;1398;1398;;;931.74;;119.10892233690001;;
Open;Dividends;HKD;2025-01-27;939;939;;;852.52;;108.9818387862;;
Open;Dividends;HKD;2025-03-19;14;14;;;810.0;;103.54629735;;
Open;Dividends;HKD;2025-03-20;16;16;;;4275.0;;546.494347125;;
Open;Dividends;HKD;2025-04-23;808;808;;;1120.0;;143.17512720000002;;
Open;Dividends;HKD;2025-04-25;5;5;;;6698.42;;856.2920853027001;;
Open;Dividends;HKD;2025-05-08;1972;1972;;;4256.0;;544.06548336;;
Open;Dividends;HKD;2025-05-19;2888;2888;;;277.72;;35.502318148200004;;
Open;Dividends;HKD;2025-05-28;939;939;;;887.1;;113.40237083850002;;
Open;Dividends;HKD;2025-05-29;215;215;;;2605.0;;333.010005675;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;319.22;;40.80746795070001;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;10.78;;1.3780605993000001;;
Open;Dividends;HKD;2025-05-30;700;700;;;2727.0;;348.605867745;;
Open;Dividends;HKD;2025-06-12;1299;1299;;;785.88;;100.46291871780001;;
Open;Dividends;HKD;2025-06-12;27;27;;;500.0;;63.91746750000001;;
Open;Dividends;HKD;2025-06-13;66;66;;;445.0;;56.886546075000005;;
Open;Dividends;HKD;2025-06-20;1883;1883;;;1280.0;;163.6287168;;
Open;Dividends;HKD;2025-06-20;5;5;;;1881.53;;240.52526525055;;
Open;Dividends;HKD;2025-06-27;386;386;;;304.6;;38.93852120100001;;
Open;Dividends;HKD;2025-06-30;2318;2318;;;881.33;;112.66476326355001;;
Open;Dividends;HKD;2025-07-24;857;857;;;545.4;;69.721173549;;
Open;Dividends;HKD;2025-07-30;2828;2828;;;520.0;;66.4741662;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;133.58;;17.076190617300004;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;961.35;;122.89411476225001;;
Open;Dividends;HKD;2025-07-31;390;390;;;1948.0;;249.02245338000003;;
Open;Dividends;HKD;2025-08-22;1398;1398;;;1082.3;;138.3557501505;;
Open;Dividends;HKD;2025-09-09;14;14;;;270.0;;34.515432450000006;;
Open;Dividends;HKD;2025-09-16;66;66;;;36.12;;4.6173978522;;
Open;Dividends;HKD;2025-09-16;66;66;;;173.88;;22.2279384978;;
Open;Dividends;HKD;2025-09-18;808;808;;;1170.0;;149.56687395;;
Open;Dividends;HKD;2025-09-26;5;5;;;1866.43;;238.59495773205003;;
Open;Dividends;HKD;2025-09-30;2888;2888;;;26.82;;3.4285329567000002;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;1.24;;0.95118679748;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.24;;0.18410067048;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.45;;0.34518875715;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.49;;0.37587220223;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.46;;0.35285961842;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.06;;0.04602516762;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;5.33;;4.08856905691;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.24;;0.18410067048;;
Open;Dividends;SGD;2025-04-16;D05;D05;;;132.0;;101.255368764;;
Open;Dividends;SGD;2025-05-09;O39;O39;;;328.0;;251.604249656;;
Open;Dividends;SGD;2025-05-09;O39;O39;;;128.0;;98.187024256;;
Open;Dividends;SGD;2025-05-13;9CI;9CI;;;144.0;;110.460402288;;
Open;Dividends;SGD;2025-05-13;BS6;BS6;;;840.0;;644.35234668;;
Open;Dividends;SGD;2025-05-15;F34;F34;;;900.0;;690.3775143;;
Open;Dividends;SGD;2025-05-27;D05;D05;;;132.0;;101.255368764;;
Open;Dividends;SGD;2025-05-27;D05;D05;;;33.0;;25.313842191;;
Open;Dividends;SGD;2025-08-21;O39;O39;;;328.0;;251.604249656;;
Open;Dividends;SGD;2025-08-25;D05;D05;;;132.0;;101.255368764;;
Open;Dividends;SGD;2025-08-25;D05;D05;;;33.0;;25.313842191;;
Open;Dividends;SGD;2025-08-28;F34;F34;;;360.0;;276.15100572;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;0.33;;0.25313842191;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;1.18;;0.9051616298599999;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;0.21;;0.16108808667;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;0.73;;0.55997287271;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;0.33;;0.25313842191;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;14.41;;11.053711090070001;;
Open;Dividends;SGD;2025-11-24;D05;D05;;;132.0;;101.255368764;;
Open;Dividends;SGD;2025-11-24;D05;D05;;;33.0;;25.313842191;;
Open;Dividends;USD;2025-02-03;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2025-03-13;MSFT;MSFT;;;166.0;;166.0;;
Open;Dividends;USD;2025-03-17;GOOG;GOOG;;;80.0;;80.0;;
Open;Dividends;USD;2025-03-21;IVV;IVV;;;172.81;;172.81;;
Open;Dividends;USD;2025-03-21;IVV;IVV;;;3.64;;3.64;;
Open;Dividends;USD;2025-03-26;META;META;;;105.0;;105.0;;
Open;Dividends;USD;2025-03-27;BHP;BHP;;;1182.53;;1182.53;;
Open;Dividends;USD;2025-03-28;NOV;NOV;;;22.5;;22.5;;
Open;Dividends;USD;2025-03-31;ICE;ICE;;;144.0;;144.0;;
Open;Dividends;USD;2025-04-03;SOUHY;SOUHY;;;0.34;;0.34;;
Open;Dividends;USD;2025-04-03;SOUHY;SOUHY;;;46.61;;46.61;;
Open;Dividends;USD;2025-04-23;9618;9618;;;9.5;;9.5;;
Open;Dividends;USD;2025-05-01;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2025-05-14;H78;H78;;;170.0;;170.0;;
Open;Dividends;USD;2025-06-12;MSFT;MSFT;;;166.0;;166.0;;
Open;Dividends;USD;2025-06-13;NOV;NOV;;;63.0;;63.0;;
Open;Dividends;USD;2025-06-16;GOOG;GOOG;;;84.0;;84.0;;
Open;Dividends;USD;2025-06-20;IVV;IVV;;;186.7;;186.7;;
Open;Dividends;USD;2025-06-26;META;META;;;105.0;;105.0;;
Open;Dividends;USD;2025-06-27;NOV;NOV;;;22.5;;22.5;;
Open;Dividends;USD;2025-06-30;ICE;ICE;;;144.0;;144.0;;
Open;Dividends;USD;2025-07-10;BABA;BABA;;;1050.0;;1050.0;;
Open;Dividends;USD;2025-07-10;BABA;BABA;;;950.0;;950.0;;
Open;Dividends;USD;2025-07-10;TSM;TSM;;;780.3;;780.3;;
Open;Dividends;USD;2025-08-01;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2025-09-15;GOOG;GOOG;;;84.0;;84.0;;
Open;Dividends;USD;2025-09-19;IVV;IVV;;;199.47;;199.47;;
Open;Dividends;USD;2025-09-25;BHP;BHP;;;3.6;;3.6;;
Open;Dividends;USD;2025-09-25;BHP;BHP;;;1433.52;;1433.52;;
Open;Dividends;USD;2025-09-26;NOV;NOV;;;22.5;;22.5;;
Open;Dividends;USD;2025-09-29;META;META;;;105.0;;105.0;;
Open;Dividends;USD;2025-09-30;ICE;ICE;;;144.0;;144.0;;
Open;Dividends;USD;2025-10-09;TSM;TSM;;;575.38;;575.38;;
Open;Dividends;USD;2025-10-15;H78;H78;;;60.0;;60.0;;
Open;Dividends;USD;2025-10-16;SOUHY;SOUHY;;;0.13;;0.13;;
Open;Dividends;USD;2025-10-16;SOUHY;SOUHY;;;35.8;;35.8;;
Open;Dividends;USD;2025-11-03;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2025-11-25;FAST;FAST;;;22.0;;22.0;;
Open;Dividends;USD;2025-12-08;PAYC;PAYC;;;75.0;;75.0;;
Open;Dividends;USD;2025-12-15;GOOG;GOOG;;;84.0;;84.0;;
Open;Dividends;USD;2025-12-19;IVV;IVV;;;241.36;;241.36;;
Open;Dividends;USD;2025-12-19;NOV;NOV;;;2.93;;2.93;;
Open;Dividends;USD;2025-12-19;NOV;NOV;;;19.58;;19.58;;
Open;Dividends;USD;2025-12-23;META;META;;;105.0;;105.0;;
Open;Dividends;USD;2025-12-31;ICE;ICE;;;144.0;;144.0;;
Open;Trades;HKD;2024-03-24;1299;1299;Stocks;55.2;-33120.0;600.0;-4233.8930472;-61.43992;O
Open;Trades;HKD;2024-12-27;3968;3968;Stocks;39.7;-19850.0;500.0;-2537.5234597500003;-41.68725;O
Open;Trades;SGD;2024-09-09;C38U;C38UNR;Stocks;0.0;0.0;-0.36;0.0;0.0;C
Open;Trades;SGD;2024-05-16;F34;F34;Stocks;3.16;-3160.0;1000.0;-2423.99216132;-3.90576;O
Open;Trades;USD;2024-12-13;AMZN;AMZN;Stocks;200.0;120000.0;-600.0;120000.0;-3.4356;"A;C"
Open;Trades;USD;2024-12-13;AMZN;AMZN;Stocks;205.0;-61500.0;300.0;-61500.0;0.0;"Ex;O"
Open;Trades;USD;2024-05-03;BABA;BABA;Stocks;78.0;15600.0;-200.0;15600.0;-0.1248;"A;C"
Open;Trades;USD;2024-05-10;BABA;BABA;Stocks;80.0;8000.0;-100.0;8000.0;0.0;"A;C"
Open;Trades;USD;2024-09-19;BABA;BABA;Stocks;87.83;43915.0;-500.0;43915.0;-2.58112325;"C;P"
Open;Trades;EUR;2024-11-15;SAP;SAP 21MAR25 220 P;Equity and Index Options;12.7;1270.0;-1.0;1472.83862277;-1.17;O
Open;Trades;USD;2024-01-19;AMZN;AMZN 19JAN24 132.5 P;Equity and Index Options;0.0;0.0;8.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2024-06-21;AMZN;AMZN 21JUN24 90 P;Equity and Index Options;0.0;0.0;10.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2024-06-21;AMZN;AMZN 21JUN24 127.5 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2024-11-01;AMZN;AMZN 13DEC24 200 C;Equity and Index Options;6.98;4188.0;-6.0;4188.0;-4.3424664;"O;P"
Open;Trades;USD;2024-12-13;AMZN;AMZN 13DEC24 200 C;Equity and Index Options;0.0;0.0;6.0;0.0;0.0;"A;C"
Open;Trades;USD;2024-11-25;AMZN;AMZN 13DEC24 205 C;Equity and Index Options;2.86;-858.0;3.0;-858.0;-2.10465;O
Open;Trades;USD;2024-12-13;AMZN;AMZN 13DEC24 205 C;Equity and Index Options;0.0;0.0;-3.0;0.0;0.0;"C;Ex"
Open;Trades;USD;2024-05-15;AMZN;AMZN 20DEC24 190 P;Equity and Index Options;16.8;1680.0;-1.0;1680.0;-1.05308;O
Open;Trades;USD;2024-12-20;AMZN;AMZN 20DEC24 190 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2024-05-15;AMZN;AMZN 21MAR25 190 P;Equity and Index Options;19.45;1945.0;-1.0;1945.0;-0.8152;O
Open;Trades;USD;2024-04-26;BABA;BABA 03MAY24 78 C;Equity and Index Options;0.41;82.0;-2.0;82.0;-1.379936;O
Open;Trades;USD;2024-05-03;BABA;BABA 03MAY24 78 C;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2024-04-26;BABA;BABA 10MAY24 80 C;Equity and Index Options;0.46;46.0;-1.0;46.0;-1.040008;O
Open;Trades;USD;2024-05-10;BABA;BABA 10MAY24 80 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2024-06-12;BABA;BABA 21JUN24 83 C;Equity and Index Options;0.27;135.0;-5.0;135.0;-3.451953;O
Open;Trades;USD;2024-12-18;BABA;BABA 17JAN25 95 C;Equity and Index Options;1.12;112.0;-1.0;112.0;-0.9898536;O
Open;Trades;USD;2024-12-18;BABA;BABA 21MAR25 77.5 C;Equity and Index Options;12.18;-1218.0;1.0;-1218.0;-0.98395;O
Open;Trades;USD;2024-06-21;BABA;BABA 21JUN24 83 C;Equity and Index Options;0.0;0.0;5.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2024-12-18;BRK;BRK B 17JAN25 470 C;Equity and Index Options;3.8;380.0;-1.0;380.0;-0.707304;O
Open;Trades;USD;2024-12-19;BRK;BRK B 24JAN25 465 C;Equity and Index Options;4.44;444.0;-1.0;444.0;-0.7090832;O
Open;Trades;USD;2024-12-19;BRK;BRK B 21MAR25 425 C;Equity and Index Options;36.67;-3667.0;1.0;-3667.0;-0.69395;O
Open;Trades;USD;2024-12-18;BRK;BRK B 21MAR25 435 C;Equity and Index Options;34.08;-3408.0;1.0;-3408.0;-0.69395;O
Open;Trades;USD;2024-06-10;CPRT;CPRT 15NOV24 52.5 P;Equity and Index Options;2.3;460.0;-2.0;460.0;0.587932;O
Open;Trades;USD;2024-11-15;CPRT;CPRT 15NOV24 52.5 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2024-06-21;GOOG;GOOG 21JUN24 105 P;Equity and Index Options;0.0;0.0;5.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2024-06-21;GOOG;GOOG 21JUN24 110 P;Equity and Index Options;0.0;0.0;5.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2024-06-21;GOOG;GOOG 21JUN24 115 P;Equity and Index Options;0.0;0.0;5.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2024-06-21;GOOG;GOOG 21JUN24 132.5 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2024-12-13;ICE;ICE 17JAN25 155 P;Equity and Index Options;2.85;855.0;-3.0;855.0;0.886011;O
Open;Trades;USD;2024-01-19;MSFT;MSFT 19JAN24 245 P;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2024-11-14;MSFT;MSFT 06DEC24 445 C;Equity and Index Options;2.0;200.0;-1.0;200.0;-1.0499;O
Open;Trades;USD;2024-12-06;MSFT;MSFT 06DEC24 445 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2024-10-31;MSFT;MSFT 13DEC24 385 P;Equity and Index Options;5.8;1160.0;-2.0;1160.0;-0.943728;O
Open;Trades;USD;2024-12-13;MSFT;MSFT 13DEC24 385 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2024-11-22;TSM;TSM 20DEC24 190 P;Equity and Index Options;7.33;1466.0;-2.0;1466.0;-1.4494348;"O;P"
Open;Trades;USD;2024-12-20;TSM;TSM 20DEC24 190 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Corporate Actions;SGD;2024-04-22;D05;D05;Stocks;;0.0;20.0;0.0;;
Open;Corporate Actions;SGD;2024-04-30;D05;D05;Stocks;;0.0;20.0;0.0;;
Open;Corporate Actions;SGD;2024-04-30;D05;D05;Stocks;;0.0;-20.0;0.0;;
Open;Corporate Actions;SGD;2024-09-10;C38U;C38U;Stocks;;0.0;10.36;0.0;;
Open;Corporate Actions;SGD;2024-10-07;C38U;C38UNR;Stocks;;0.0;-10.0;0.0;;
Open;Transaction Fees;HKD;2024-03-24;1299;1299;Stocks;55.2;-34.94392;0.0;-4.4670537418452;;
Open;Transaction Fees;HKD;2024-12-27;3968;3968;Stocks;39.7;-20.565725;0.0;-2.6290181186028754;;
Open;Withholding Tax;HKD;2024-07-26;2318;2318;;;-82.34;;-10.525928547900001;;
Open;Withholding Tax;HKD;2024-07-26;386;386;;;-43.88;;-5.6093969478000005;;
Open;Withholding Tax;HKD;2024-07-29;857;857;;;-50.57;;-6.4646126629500005;;
Open;Withholding Tax;HKD;2024-08-02;939;939;;;-175.36;;-22.417134201600003;;
Open;Withholding Tax;HKD;2024-08-08;390;390;;;-230.1;;-29.4148185435;;
Open;Withholding Tax;HKD;2024-08-19;1398;1398;;;-201.43;;-25.749790957050003;;
Open;Withholding Tax;HKD;2024-09-26;386;386;;;-31.86;;-4.0728210291;;
Open;Withholding Tax;HKD;2024-10-18;2318;2318;;;-51.02;;-6.522138383700001;;
Open;Withholding Tax;HKD;2024-10-28;857;857;;;-48.07;;-6.145025325450001;;
Open;Withholding Tax;USD;2024-02-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2024-03-14;MSFT;MSFT;;;-45.0;;-45.0;;
Open;Withholding Tax;USD;2024-03-26;META;META;;;-30.0;;-30.0;;
Open;Withholding Tax;USD;2024-03-27;IVV;IVV;;;-49.96;;-49.96;;
Open;Withholding Tax;USD;2024-03-28;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2024-05-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2024-06-13;MSFT;MSFT;;;-45.0;;-45.0;;
Open;Withholding Tax;USD;2024-06-17;GOOG;GOOG;;;-24.0;;-24.0;;
Open;Withholding Tax;USD;2024-06-17;IVV;IVV;;;-48.33;;-48.33;;
Open;Withholding Tax;USD;2024-06-26;META;META;;;-30.0;;-30.0;;
Open;Withholding Tax;USD;2024-06-28;NOV;NOV;;;-6.75;;-6.75;;
Open;Withholding Tax;USD;2024-08-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2024-09-12;MSFT;MSFT;;;-45.0;;-45.0;;
Open;Withholding Tax;USD;2024-09-16;GOOG;GOOG;;;-24.0;;-24.0;;
Open;Withholding Tax;USD;2024-09-26;META;META;;;-30.0;;-30.0;;
Open;Withholding Tax;USD;2024-09-27;NOV;NOV;;;-6.75;;-6.75;;
Open;Withholding Tax;USD;2024-09-30;IVV;IVV;;;-67.04;;-67.04;;
Open;Withholding Tax;USD;2024-11-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2024-12-12;MSFT;MSFT;;;-49.8;;-49.8;;
Open;Withholding Tax;USD;2024-12-16;GOOG;GOOG;;;-24.0;;-24.0;;
Open;Withholding Tax;USD;2024-12-20;IVV;IVV;;;-64.03;;-64.03;;
Open;Withholding Tax;USD;2024-12-20;NOV;NOV;;;-6.75;;-6.75;;
Open;Withholding Tax;USD;2024-12-27;META;META;;;-30.0;;-30.0;;
Open;Dividends;CNH;2024-07-31;3188;3188;;;480.0;;71.49775488;;
Open;Dividends;HKD;2024-03-20;16;16;;;4275.0;;546.494347125;;
Open;Dividends;HKD;2024-03-22;14;14;;;810.0;;103.54629735;;
Open;Dividends;HKD;2024-04-17;808;808;;;1328.0;;169.76479368000003;;
Open;Dividends;HKD;2024-04-25;5;5;;;5119.61;;654.46501157535;;
Open;Dividends;HKD;2024-04-25;5;5;;;703.64;;89.94977366340001;;
Open;Dividends;HKD;2024-04-26;27;27;;;300.0;;38.3504805;;
Open;Dividends;HKD;2024-05-02;1972;1972;;;4032.0;;515.4304579200001;;
Open;Dividends;HKD;2024-05-17;2888;2888;;;210.1;;26.858119843500003;;
Open;Dividends;HKD;2024-05-27;215;215;;;2605.0;;333.010005675;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;41.55;;5.31154154925;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;183.45;;23.45131882575;;
Open;Dividends;HKD;2024-05-31;700;700;;;2060.4;;263.391100074;;
Open;Dividends;HKD;2024-06-13;1883;1883;;;1930.0;;246.72142455000002;;
Open;Dividends;HKD;2024-06-14;1299;1299;;;714.42;;91.32783426270001;;
Open;Dividends;HKD;2024-06-21;5;5;;;5808.32;;742.5062096592001;;
Open;Dividends;HKD;2024-07-16;66;66;;;16.91;;2.1616887508500002;;
Open;Dividends;HKD;2024-07-16;66;66;;;428.09;;54.72485732415;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;741.05;;94.73207858175;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;823.39;;105.25800712965001;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;-741.05;;-94.73207858175;;
Open;Dividends;HKD;2024-07-26;386;386;;;438.8;;56.093969478000005;;
Open;Dividends;HKD;2024-07-29;857;857;;;505.74;;64.65124002690001;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;36.4;;4.653191634000001;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;223.6;;28.583891466;;
Open;Dividends;HKD;2024-08-02;939;939;;;1753.57;;224.16750696795;;
Open;Dividends;HKD;2024-08-08;390;390;;;2301.0;;294.148185435;;
Open;Dividends;HKD;2024-08-19;1398;1398;;;2014.29;;257.49663122115004;;
Open;Dividends;HKD;2024-09-09;215;215;;;1140.0;;145.73182590000002;;
Open;Dividends;HKD;2024-09-16;808;808;;;1276.0;;163.11737706000002;;
Open;Dividends;HKD;2024-09-17;66;66;;;72.66;;9.2884863771;;
Open;Dividends;HKD;2024-09-17;66;66;;;137.34;;17.5568499729;;
Open;Dividends;HKD;2024-09-20;14;14;;;270.0;;34.515432450000006;;
Open;Dividends;HKD;2024-09-25;1299;1299;;;267.0;;34.131927645000005;;
Open;Dividends;HKD;2024-09-26;386;386;;;318.6;;40.728210291;;
Open;Dividends;HKD;2024-09-27;1883;1883;;;600.0;;76.700961;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;21.85;;2.7931933297500002;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;358.15;;45.78408197025;;
Open;Dividends;HKD;2024-09-27;5;5;;;1869.78;;239.0232047643;;
Open;Dividends;HKD;2024-10-09;1972;1972;;;1904.0;;243.39771624000002;;
Open;Dividends;HKD;2024-10-10;2888;2888;;;89.69;;11.46551532015;;
Open;Dividends;HKD;2024-10-18;2318;2318;;;510.24;;65.2264972344;;
Open;Dividends;HKD;2024-10-25;27;27;;;500.0;;63.91746750000001;;
Open;Dividends;HKD;2024-10-28;857;857;;;480.66;;61.44513985710001;;
Open;Dividends;HKD;2024-11-21;16;16;;;12325.6;;1575.642274836;;
Open;Dividends;HKD;2024-11-21;16;16;;;274.4;;35.077906164;;
Open;Dividends;HKD;2024-11-29;2800;2800;;;930.0;;118.88648955000001;;
Open;Dividends;HKD;2024-12-19;5;5;;;1865.55;;238.48246298925002;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;0.31;;0.23779669937;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;1.62;;1.24267952574;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;0.17;;0.13040464159;;
Open;Dividends;SGD;2024-03-28;C38U;C38U;;;10.08;;7.73222816016;;
Open;Dividends;SGD;2024-04-19;D05;D05;;;108.0;;82.845301716;;
Open;Dividends;SGD;2024-05-08;BS6;BS6;;;455.0;;349.024187785;;
Open;Dividends;SGD;2024-05-10;9CI;9CI;;;144.0;;110.460402288;;
Open;Dividends;SGD;2024-05-14;F34;F34;;;880.0;;675.03579176;;
Open;Dividends;SGD;2024-05-20;D05;D05;;;118.8;;91.1298318876;;
Open;Dividends;SGD;2024-05-21;O39;O39;;;336.0;;257.740938672;;
Open;Dividends;SGD;2024-08-23;O39;O39;;;352.0;;270.014316704;;
Open;Dividends;SGD;2024-08-26;D05;D05;;;118.8;;91.1298318876;;
Open;Dividends;SGD;2024-08-29;F34;F34;;;540.0;;414.22650858000003;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;1.03;;0.79009871081;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;0.39;;0.29916358953;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;0.32;;0.24546756064;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;0.11;;0.08437947397000001;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;0.09;;0.06903775143;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;9.84;;7.54812748968;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;3.83;;2.9379398664100003;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;0.07;;0.053696028890000004;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;0.09;;0.06903775143;;
Open;Dividends;SGD;2024-11-25;D05;D05;;;118.8;;91.1298318876;;
Open;Dividends;USD;2024-01-18;BABA;BABA;;;2600.0;;2600.0;;
Open;Dividends;USD;2024-02-01;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2024-03-14;MSFT;MSFT;;;150.0;;150.0;;
Open;Dividends;USD;2024-03-26;META;META;;;100.0;;100.0;;
Open;Dividends;USD;2024-03-27;IVV;IVV;;;166.53;;166.53;;
Open;Dividends;USD;2024-03-28;BHP;BHP;;;1726.46;;1726.46;;
Open;Dividends;USD;2024-03-28;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2024-04-04;SOUHY;SOUHY;;;6.0;;6.0;;
Open;Dividends;USD;2024-04-23;9618;9618;;;6.08;;6.08;;
Open;Dividends;USD;2024-04-23;9618;9618;;;1.14;;1.14;;
Open;Dividends;USD;2024-05-01;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2024-05-15;H78;H78;;;160.0;;160.0;;
Open;Dividends;USD;2024-06-13;MSFT;MSFT;;;150.0;;150.0;;
Open;Dividends;USD;2024-06-17;GOOG;GOOG;;;80.0;;80.0;;
Open;Dividends;USD;2024-06-17;IVV;IVV;;;161.11;;161.11;;
Open;Dividends;USD;2024-06-26;META;META;;;100.0;;100.0;;
Open;Dividends;USD;2024-06-28;NOV;NOV;;;22.5;;22.5;;
Open;Dividends;USD;2024-07-12;BABA;BABA;;;1518.0;;1518.0;;
Open;Dividends;USD;2024-07-12;BABA;BABA;;;2300.0;;2300.0;;
Open;Dividends;USD;2024-08-01;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2024-09-12;MSFT;MSFT;;;150.0;;150.0;;
Open;Dividends;USD;2024-09-16;GOOG;GOOG;;;80.0;;80.0;;
Open;Dividends;USD;2024-09-26;META;META;;;100.0;;100.0;;
Open;Dividends;USD;2024-09-27;NOV;NOV;;;22.5;;22.5;;
Open;Dividends;USD;2024-09-30;IVV;IVV;;;223.46;;223.46;;
Open;Dividends;USD;2024-10-03;BHP;BHP;;;497.28;;497.28;;
Open;Dividends;USD;2024-10-03;BHP;BHP;;;1276.87;;1276.87;;
Open;Dividends;USD;2024-10-16;H78;H78;;;60.0;;60.0;;
Open;Dividends;USD;2024-10-17;SOUHY;SOUHY;;;46.5;;46.5;;
Open;Dividends;USD;2024-11-01;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2024-12-12;MSFT;MSFT;;;166.0;;166.0;;
Open;Dividends;USD;2024-12-16;GOOG;GOOG;;;80.0;;80.0;;
Open;Dividends;USD;2024-12-20;IVV;IVV;;;213.42;;213.42;;
Open;Dividends;USD;2024-12-20;NOV;NOV;;;22.5;;22.5;;
Open;Dividends;USD;2024-12-27;META;META;;;100.0;;100.0;;
Open;Trades;HKD;2023-01-04;3690;3690.SPO;Stocks;0.0;0.0;-0.6;0.0;0.0;C
Open;Trades;HKD;2023-04-25;3918;3918;Stocks;6.1031;4.96487185;-0.8135;0.6346840702280798;0.0;C
Open;Trades;SGD;2023-04-27;HMN;HMN.BON;Stocks;0.0;0.0;-0.4156;0.0;0.0;C
Open;Trades;SGD;2023-08-09;HMN;HMNNR;Stocks;0.0;0.0;-0.972;0.0;0.0;C
Open;Trades;USD;2023-01-09;AMZN;AMZN;Stocks;88.55;17710.0;-200.0;17710.0;-1.434559;C
Open;Trades;USD;2023-01-03;GOOG;GOOG;Stocks;90.056;45028.0;-500.0;45028.0;-3.6036412;"C;P"
Open;Trades;USD;2023-01-03;GOOG;GOOG;Stocks;90.064;45032.0;-500.0;45032.0;-3.6037328;"C;P"
Open;Trades;USD;2023-02-24;GOOG;GOOG;Stocks;132.5;-53000.0;400.0;-53000.0;0.0;"A;O"
Open;Trades;USD;2023-01-04;MSFT;MSFT;Stocks;285.0;-85500.0;300.0;-85500.0;0.0;"A;O"
Open;Trades;USD;2023-01-05;MSFT;MSFT;Stocks;285.0;-57000.0;200.0;-57000.0;0.0;"A;O"
Open;Trades;USD;2023-01-06;MSFT;MSFT;Stocks;223.9875;89595.0;-400.0;89595.0;-4.1097255;"C;P"
Open;Trades;EUR;2023-12-15;MC;MC 15DEC23 720 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2023-01-09;AMZN;AMZN 21JUN24 127.5 P;Equity and Index Options;40.33;8066.0;-2.0;8066.0;-1.5708914;O
Open;Trades;USD;2023-01-03;GOOG;GOOG 21JUN24 105 P;Equity and Index Options;20.414;10207.0;-5.0;10207.0;-2.6566903;"O;P"
Open;Trades;USD;2023-01-03;GOOG;GOOG 21JUN24 110 P;Equity and Index Options;23.826;11913.0;-5.0;11913.0;-1.9157577;"O;P"
Open;Trades;USD;2023-02-24;GOOG;GOOG 21JUN24 132.5 P;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"A;C"
Open;Trades;USD;2023-01-04;MSFT;MSFT 17MAR23 285 P;Equity and Index Options;0.0;0.0;3.0;0.0;0.0;"A;C"
Open;Trades;USD;2023-01-05;MSFT;MSFT 17MAR23 285 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2023-06-16;MSFT;MSFT 16JUN23 285 P;Equity and Index Options;0.0;0.0;5.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2023-01-06;MSFT;MSFT 19JAN24 245 P;Equity and Index Options;34.8925;13957.0;-4.0;13957.0;-2.6969753;"O;P"
Open;Corporate Actions;HKD;2023-01-05;3690;3690.SPO;Stocks;;0.0;60.6;0.0;;
Open;Corporate Actions;HKD;2023-03-24;3690;3690;Stocks;;0.0;60.0;0.0;;
Open;Corporate Actions;HKD;2023-03-24;3690;3690.SPO;Stocks;;0.0;-60.0;0.0;;
Open;Corporate Actions;HKD;2023-05-16;3918;3918;Stocks;;0.0;18.8135;0.0;;
Open;Corporate Actions;SGD;2023-04-28;HMN;HMN.BON;Stocks;;0.0;68.4156;0.0;;
Open;Corporate Actions;SGD;2023-05-15;HMN;HMN;Stocks;;0.0;68.0;0.0;;
Open;Corporate Actions;SGD;2023-05-15;HMN;HMN.BON;Stocks;;0.0;-68.0;0.0;;
Open;Corporate Actions;SGD;2023-08-10;HMN;HMN;Stocks;;0.0;1.972;0.0;;
Open;Corporate Actions;SGD;2023-09-06;HMN;HMNNRO;Stocks;;0.0;-1.0;0.0;;
Open;Corporate Actions;USD;2023-08-22;CPRT;CPRT;Stocks;;0.0;400.0;0.0;;
Open;Withholding Tax;HKD;2023-06-30;386;386;;;-43.34;;-5.540366082900001;;
Open;Withholding Tax;HKD;2023-07-10;2318;2318;;;-85.01;;-10.867247824350002;;
Open;Withholding Tax;HKD;2023-08-04;939;939;;;-168.79;;-21.57725867865;;
Open;Withholding Tax;HKD;2023-08-10;390;390;;;-217.64;;-27.8219952534;;
Open;Withholding Tax;HKD;2023-08-18;1398;1398;;;-197.54;;-25.2525130599;;
Open;Withholding Tax;HKD;2023-09-28;386;386;;;-31.56;;-4.0344705486;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.373849859100001;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;49.86;;6.373849859100001;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.373849859100001;;
Open;Withholding Tax;HKD;2023-10-30;857;857;;;-0.87;;-0.11121639345;;
Open;Withholding Tax;USD;2023-02-01;FCX;FCX;;;-1.85;;-1.85;;
Open;Withholding Tax;USD;2023-02-01;FCX;FCX;;;-20.66;;-20.66;;
Open;Withholding Tax;USD;2023-03-09;MSFT;MSFT;;;-40.8;;-40.8;;
Open;Withholding Tax;USD;2023-03-29;IVV;IVV;;;-12.86;;-12.86;;
Open;Withholding Tax;USD;2023-03-29;IVV;IVV;;;-36.59;;-36.59;;
Open;Withholding Tax;USD;2023-03-31;NOV;NOV;;;-1.58;;-1.58;;
Open;Withholding Tax;USD;2023-03-31;NOV;NOV;;;-2.93;;-2.93;;
Open;Withholding Tax;USD;2023-05-01;FCX;FCX;;;-11.25;;-11.25;;
Open;Withholding Tax;USD;2023-05-01;FCX;FCX;;;-11.25;;-11.25;;
Open;Withholding Tax;USD;2023-06-08;MSFT;MSFT;;;-40.8;;-40.8;;
Open;Withholding Tax;USD;2023-06-13;IVV;IVV;;;-20.08;;-20.08;;
Open;Withholding Tax;USD;2023-06-13;IVV;IVV;;;-20.08;;-20.08;;
Open;Withholding Tax;USD;2023-06-30;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2023-08-01;FCX;FCX;;;-11.25;;-11.25;;
Open;Withholding Tax;USD;2023-08-01;FCX;FCX;;;-11.25;;-11.25;;
Open;Withholding Tax;USD;2023-09-14;MSFT;MSFT;;;-40.8;;-40.8;;
Open;Withholding Tax;USD;2023-09-29;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2023-10-02;IVV;IVV;;;-59.61;;-59.61;;
Open;Withholding Tax;USD;2023-11-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2023-12-14;MSFT;MSFT;;;-45.0;;-45.0;;
Open;Withholding Tax;USD;2023-12-22;NOV;NOV;;;-0.44;;-0.44;;
Open;Withholding Tax;USD;2023-12-22;NOV;NOV;;;-4.07;;-4.07;;
Open;Withholding Tax;USD;2023-12-27;IVV;IVV;;;-57.76;;-57.76;;
Open;Dividends;CNH;2023-07-31;3188;3188;;;430.0;;64.05007207999999;;
Open;Dividends;HKD;2023-03-16;16;16;;;5571.25;;712.20038161875;;
Open;Dividends;HKD;2023-03-16;16;16;;;53.75;;6.871127756250001;;
Open;Dividends;HKD;2023-03-20;14;14;;;38.61;;4.93570684035;;
Open;Dividends;HKD;2023-03-20;14;14;;;1131.39;;144.63116710965002;;
Open;Dividends;HKD;2023-04-11;808;808;;;1500.0;;191.75240250000002;;
Open;Dividends;HKD;2023-04-27;5;5;;;4330.56;;553.5968561136001;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;3394.56;;433.9433569536;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;413.44;;52.85207552640001;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;1.1;;0.14061842850000003;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;139.46;;17.827860035100002;;
Open;Dividends;HKD;2023-05-29;215;215;;;2605.0;;333.010005675;;
Open;Dividends;HKD;2023-05-31;2800;2800;;;150.0;;19.17524025;;
Open;Dividends;HKD;2023-06-05;700;700;;;1454.4;;185.92312946400003;;
Open;Dividends;HKD;2023-06-14;1883;1883;;;1850.0;;236.49462975000003;;
Open;Dividends;HKD;2023-06-23;5;5;;;1879.72;;240.29388401820003;;
Open;Dividends;HKD;2023-06-30;386;386;;;433.4;;55.403660829;;
Open;Dividends;HKD;2023-07-10;2318;2318;;;850.13;;108.67631329155;;
Open;Dividends;HKD;2023-07-18;66;66;;;445.0;;56.886546075000005;;
Open;Dividends;HKD;2023-07-28;2828;2828;;;80.0;;10.2267948;;
Open;Dividends;HKD;2023-07-28;857;857;;;485.54;;62.06897433990001;;
Open;Dividends;HKD;2023-08-04;939;939;;;1687.94;;215.77770018390004;;
Open;Dividends;HKD;2023-08-10;390;390;;;2176.4;;278.219952534;;
Open;Dividends;HKD;2023-08-18;1398;1398;;;1975.41;;252.52640894835002;;
Open;Dividends;HKD;2023-09-04;215;215;;;1140.0;;145.73182590000002;;
Open;Dividends;HKD;2023-09-05;14;14;;;270.0;;34.515432450000006;;
Open;Dividends;HKD;2023-09-14;808;808;;;1490.0;;190.47405315;;
Open;Dividends;HKD;2023-09-21;5;5;;;1879.68;;240.28877062080002;;
Open;Dividends;HKD;2023-09-28;2828;2828;;;568.0;;72.61024308;;
Open;Dividends;HKD;2023-09-28;386;386;;;315.6;;40.34470548600001;;
Open;Dividends;HKD;2023-09-29;1883;1883;;;600.0;;76.700961;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;645.48;;82.51489384380001;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;1202.52;;153.7240660362;;
Open;Dividends;HKD;2023-10-13;2888;2888;;;60.04;;7.675209497400001;;
Open;Dividends;HKD;2023-10-13;66;66;;;210.0;;26.845336350000004;;
Open;Dividends;HKD;2023-10-25;2318;2318;;;505.93;;64.67552866455;;
Open;Dividends;HKD;2023-10-27;27;27;;;200.0;;25.566987;;
Open;Dividends;HKD;2023-10-30;857;857;;;8.7;;1.1121639345;;
Open;Dividends;HKD;2023-10-30;857;857;;;449.44;;57.45413318640001;;
Open;Dividends;HKD;2023-11-16;16;16;;;16650.0;;2128.45166775;;
Open;Dividends;HKD;2023-11-30;2800;2800;;;825.0;;105.46382137500001;;
Open;Dividends;HKD;2023-12-21;5;5;;;1872.67;;239.39264772645004;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;0.13;;0.09972119651;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;0.2;;0.15341722540000002;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;9.58;;7.34868509666;;
Open;Dividends;SGD;2023-04-21;D05;D05;;;84.0;;64.435234668;;
Open;Dividends;SGD;2023-04-21;D05;D05;;;100.0;;76.7086127;;
Open;Dividends;SGD;2023-05-10;9CI;9CI;;;144.0;;110.460402288;;
Open;Dividends;SGD;2023-05-12;F34;F34;;;880.0;;675.03579176;;
Open;Dividends;SGD;2023-05-19;O39;O39;;;320.0;;245.46756064;;
Open;Dividends;SGD;2023-05-22;D05;D05;;;84.0;;64.435234668;;
Open;Dividends;SGD;2023-05-25;BS6;BS6;;;350.0;;268.48014445;;
Open;Dividends;SGD;2023-08-24;D05;D05;;;96.0;;73.64026819200001;;
Open;Dividends;SGD;2023-08-25;O39;O39;;;320.0;;245.46756064;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;1.11;;0.8514656009700001;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;0.5;;0.3835430635;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;0.28;;0.21478411556000002;;
Open;Dividends;SGD;2023-08-30;F34;F34;;;480.0;;368.20134096;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;0.04;;0.03068344508;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;0.44;;0.33751789588000003;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;9.32;;7.149242703640001;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.3;;0.23012583809999998;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.08;;0.06136689016;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.1;;0.07670861270000001;;
Open;Dividends;SGD;2023-11-27;D05;D05;;;96.0;;73.64026819200001;;
Open;Dividends;USD;2023-02-01;FCX;FCX;;;6.15;;6.15;;
Open;Dividends;USD;2023-02-01;FCX;FCX;;;68.85;;68.85;;
Open;Dividends;USD;2023-03-09;MSFT;MSFT;;;136.0;;136.0;;
Open;Dividends;USD;2023-03-29;IVV;IVV;;;42.85;;42.85;;
Open;Dividends;USD;2023-03-29;IVV;IVV;;;121.95;;121.95;;
Open;Dividends;USD;2023-03-30;BHP;BHP;;;2142.32;;2142.32;;
Open;Dividends;USD;2023-03-31;NOV;NOV;;;5.25;;5.25;;
Open;Dividends;USD;2023-03-31;NOV;NOV;;;9.75;;9.75;;
Open;Dividends;USD;2023-04-06;SOUHY;SOUHY;;;64.5;;64.5;;
Open;Dividends;USD;2023-04-27;9618;9618;;;5.89;;5.89;;
Open;Dividends;USD;2023-05-01;FCX;FCX;;;37.5;;37.5;;
Open;Dividends;USD;2023-05-01;FCX;FCX;;;37.5;;37.5;;
Open;Dividends;USD;2023-05-10;H78;H78;;;160.0;;160.0;;
Open;Dividends;USD;2023-06-08;MSFT;MSFT;;;136.0;;136.0;;
Open;Dividends;USD;2023-06-13;IVV;IVV;;;66.93;;66.93;;
Open;Dividends;USD;2023-06-13;IVV;IVV;;;66.93;;66.93;;
Open;Dividends;USD;2023-06-30;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2023-08-01;FCX;FCX;;;37.5;;37.5;;
Open;Dividends;USD;2023-08-01;FCX;FCX;;;37.5;;37.5;;
Open;Dividends;USD;2023-09-14;MSFT;MSFT;;;136.0;;136.0;;
Open;Dividends;USD;2023-09-28;BHP;BHP;;;1911.89;;1911.89;;
Open;Dividends;USD;2023-09-29;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2023-10-02;IVV;IVV;;;198.7;;198.7;;
Open;Dividends;USD;2023-10-11;H78;H78;;;60.0;;60.0;;
Open;Dividends;USD;2023-10-12;SOUHY;SOUHY;;;48.0;;48.0;;
Open;Dividends;USD;2023-11-01;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2023-12-14;MSFT;MSFT;;;150.0;;150.0;;
Open;Dividends;USD;2023-12-22;NOV;NOV;;;1.45;;1.45;;
Open;Dividends;USD;2023-12-22;NOV;NOV;;;13.55;;13.55;;
Open;Dividends;USD;2023-12-27;IVV;IVV;;;192.53;;192.53;;
Open;Trades;HKD;2022-10-31;16;16;Stocks;85.0;-255000.0;3000.0;-32597.908425;-543.2675;"O;P"
Open;Trades;HKD;2022-01-18;200;200;Stocks;9.2;-18400.0;2000.0;-2352.162804;-42.5244;O
Open;Trades;HKD;2022-10-31;2800;2800;Stocks;24.0;-24000.0;1000.0;-3068.0384400000003;-23.584;"A;O"
Open;Trades;HKD;2022-09-13;3918;3918;Stocks;6.0527;5.74280176;-0.9488;0.7341306897074856;0.0;C
Open;Trades;HKD;2022-06-29;700;700;Stocks;485.3;-99971.8;206.0;-12779.888554833002;-220.3252263;"A;O"
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;-0.0476;0.0;0.0;O
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;0.0476;0.0;0.0;Ca
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;221.0;10.5196;-0.0476;1.3447723822260003;0.0;C
Open;Trades;USD;2022-01-21;AMZN;AMZN;Stocks;3300.0;-330000.0;100.0;-330000.0;0.0;"A;O"
Open;Trades;USD;2022-03-17;AMZN;AMZN;Stocks;3090.0;154500.0;-50.0;154500.0;-1.79445;C
Open;Trades;USD;2022-07-15;AMZN;AMZN;Stocks;115.095;23019.0;-200.0;23019.0;-1.5531351;"C;P"
Open;Trades;USD;2022-10-27;AMZN;AMZN;Stocks;165.0;-132000.0;800.0;-132000.0;0.0;"A;O"
Open;Trades;USD;2022-10-28;AMZN;AMZN;Stocks;100.70125;80561.0;-800.0;80561.0;-5.9488469;"C;P"
Open;Trades;USD;2022-10-28;AMZN;AMZN;Stocks;165.0;-198000.0;1200.0;-198000.0;0.0;"A;O"
Open;Trades;USD;2022-10-31;AMZN;AMZN;Stocks;101.9;50950.0;-500.0;50950.0;-3.731755;C
Open;Trades;USD;2022-10-31;AMZN;AMZN;Stocks;102.2;51100.0;-500.0;51100.0;-3.73519;"C;P"
Open;Trades;USD;2022-10-31;AMZN;AMZN;Stocks;102.35;51175.0;-500.0;51175.0;-3.7369075;C
Open;Trades;USD;2022-10-31;AMZN;AMZN;Stocks;102.6;51300.0;-500.0;51300.0;-3.73977;"C;P"
Open;Trades;USD;2022-11-01;AMZN;AMZN;Stocks;145.0;-174000.0;1200.0;-174000.0;0.0;"A;O"
Open;Trades;USD;2022-11-02;AMZN;AMZN;Stocks;145.0;-116000.0;800.0;-116000.0;0.0;"A;O"
Open;Trades;USD;2022-11-04;AMZN;AMZN;Stocks;91.5;45750.0;-500.0;45750.0;-3.612675;"C;P"
Open;Trades;USD;2022-11-04;AMZN;AMZN;Stocks;91.8;45900.0;-500.0;45900.0;-3.61611;C
Open;Trades;USD;2022-11-10;AMZN;AMZN;Stocks;96.0;38400.0;-400.0;38400.0;-2.93136;C
Open;Trades;USD;2022-12-28;AMZN;AMZN;Stocks;130.0;-26000.0;200.0;-26000.0;0.0;"A;O"
Open;Trades;USD;2022-01-21;BABA;BABA;Stocks;250.0;-50000.0;200.0;-50000.0;0.0;"A;O"
Open;Trades;USD;2022-01-21;BABA;BABA;Stocks;240.0;-96000.0;400.0;-96000.0;0.0;"A;O"
Open;Trades;USD;2022-01-21;BABA;BABA;Stocks;220.0;-44000.0;200.0;-44000.0;0.0;"A;O"
Open;Trades;USD;2022-03-02;BABA;BABA;Stocks;180.0;-72000.0;400.0;-72000.0;0.0;"A;O"
Open;Trades;USD;2022-03-18;BABA;BABA;Stocks;180.0;-72000.0;400.0;-72000.0;0.0;"A;O"
Open;Trades;USD;2022-06-17;BABA;BABA;Stocks;100.0;10000.0;-100.0;10000.0;-0.242;"A;C"
Open;Trades;USD;2022-06-17;BABA;BABA;Stocks;240.0;-48000.0;200.0;-48000.0;0.0;"A;O"
Open;Trades;USD;2022-07-08;BABA;BABA;Stocks;100.0;10000.0;-100.0;10000.0;-0.242;"A;C"
Open;Trades;USD;2022-08-10;BABA;BABA;Stocks;150.0;-15000.0;100.0;-15000.0;0.0;"A;O"
Open;Trades;USD;2022-08-10;BABA;BABA;Stocks;165.0;-16500.0;100.0;-16500.0;0.0;"A;O"
Open;Trades;USD;2022-09-21;BABA;BABA;Stocks;175.0;-35000.0;200.0;-35000.0;0.0;"A;O"
Open;Trades;USD;2022-09-21;BABA;BABA;Stocks;160.0;-32000.0;200.0;-32000.0;0.0;"A;O"
Open;Trades;USD;2022-09-21;BABA;BABA;Stocks;150.0;-15000.0;100.0;-15000.0;0.0;"A;O"
Open;Trades;USD;2022-10-24;BABA;BABA;Stocks;150.0;-15000.0;100.0;-15000.0;0.0;"A;O"
Open;Trades;USD;2022-01-12;FCX;FCX;Stocks;40.0;20000.0;-500.0;20000.0;-0.167;"A;C"
Open;Trades;USD;2022-10-26;GOOG;GOOG;Stocks;132.5;-265000.0;2000.0;-265000.0;0.0;"A;O"
Open;Trades;USD;2022-10-27;GOOG;GOOG;Stocks;93.91;46955.0;-500.0;46955.0;-3.6402695;"C;P"
Open;Trades;USD;2022-10-27;GOOG;GOOG;Stocks;93.988;46994.0;-500.0;46994.0;-3.6411626;"C;P"
Open;Trades;USD;2022-10-28;GOOG;GOOG;Stocks;96.655;38662.0;-400.0;38662.0;-2.9373598;"C;P"
Open;Trades;USD;2022-12-21;GOOG;GOOG;Stocks;132.5;-119250.0;900.0;-119250.0;0.0;"A;O"
Open;Trades;USD;2022-12-29;GOOG;GOOG;Stocks;89.06;44530.0;-500.0;44530.0;-3.584737;"C;P"
Open;Trades;USD;2022-07-13;IVV;IVV;Stocks;440.0;-44000.0;100.0;-44000.0;0.0;"A;O"
Open;Trades;USD;2022-06-14;META;META;Stocks;325.0;-65000.0;200.0;-65000.0;0.0;"A;O"
Open;Trades;USD;2022-07-27;META;META;Stocks;310.0;-31000.0;100.0;-31000.0;0.0;"A;O"
Open;Trades;USD;2022-10-06;META;META;Stocks;230.0;-23000.0;100.0;-23000.0;0.0;"A;O"
Open;Trades;USD;2022-11-07;META;META;Stocks;96.5;19300.0;-200.0;19300.0;-1.46797;"C;P"
Open;Trades;EUR;2022-10-24;MC;MC 16DEC22 720 P;Equity and Index Options;87.32;-8732.0;1.0;-10126.635318132001;-1.1;C
Open;Trades;EUR;2022-10-24;MC;MC 15DEC23 720 P;Equity and Index Options;124.72;12472.0;-1.0;14463.971104872;-1.1;O
Open;Trades;EUR;2022-01-21;MC;MC 16DEC22 720.0 P;Equity and Index Options;94.1;9410.0;-1.0;10912.922393910001;-1.1;O
Open;Trades;HKD;2022-06-29;700;700 29JUN22 485.3 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;HKD;2022-10-31;2800;2800 29DEC22 24 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-01-21;AMZN;AMZN 21JAN22 3300.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-01-25;AMZN;AMZN 18MAR22 2820.0 C;Equity and Index Options;180.02;-18002.0;1.0;-18002.0;-0.69135;O
Open;Trades;USD;2022-03-17;AMZN;AMZN 18MAR22 2820.0 C;Equity and Index Options;264.13;26413.0;-1.0;26413.0;-0.8360363;C
Open;Trades;USD;2022-01-25;AMZN;AMZN 18MAR22 2960.0 C;Equity and Index Options;113.11;22622.0;-2.0;22622.0;-0.8024322;O
Open;Trades;USD;2022-03-17;AMZN;AMZN 18MAR22 2960.0 C;Equity and Index Options;126.64;-25328.0;2.0;-25328.0;-0.6983;C
Open;Trades;USD;2022-11-01;AMZN;AMZN 17MAR23 145 P;Equity and Index Options;0.0;0.0;12.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-11-02;AMZN;AMZN 17MAR23 145 P;Equity and Index Options;0.0;0.0;8.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-10-27;AMZN;AMZN 17MAR23 165 P;Equity and Index Options;0.0;0.0;8.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-10-28;AMZN;AMZN 17MAR23 165 P;Equity and Index Options;0.0;0.0;12.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-01-21;AMZN;AMZN 17MAR23 2900.0 P;Equity and Index Options;362.5;36250.0;-1.0;36250.0;-0.928405;O
Open;Trades;USD;2022-01-10;AMZN;AMZN 17MAR23 3300.0 P;Equity and Index Options;460.0;46000.0;-1.0;46000.0;-0.97795;O
Open;Trades;USD;2022-07-15;AMZN;AMZN 16JUN23 130 P;Equity and Index Options;23.985;4797.0;-2.0;4797.0;-1.5125113;"O;P"
Open;Trades;USD;2022-12-28;AMZN;AMZN 16JUN23 130 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-10-28;AMZN;AMZN 19JAN24 132.5 P;Equity and Index Options;35.51875;28415.0;-8.0;28415.0;-5.2613435;"O;P"
Open;Trades;USD;2022-11-04;AMZN;AMZN 21JUN24 90 P;Equity and Index Options;15.45;15450.0;-10.0;15450.0;-3.057105;O
Open;Trades;USD;2022-01-21;BABA;BABA 21JAN22 220.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-01-21;BABA;BABA 21JAN22 240.0 P;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-01-21;BABA;BABA 21JAN22 250.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-03-02;BABA;BABA 18MAR22 180.0 P;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-03-16;BABA;BABA 18MAR22 180.0 P;Equity and Index Options;77.76;-15552.0;2.0;-15552.0;-0.6983;C
Open;Trades;USD;2022-03-18;BABA;BABA 18MAR22 180.0 P;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-03-23;BABA;BABA 29APR22 100.0 C;Equity and Index Options;22.46;2246.0;-1.0;2246.0;-0.7127846;O
Open;Trades;USD;2022-04-29;BABA;BABA 29APR22 100.0 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2022-04-21;BABA;BABA 17JUN22 100.0 C;Equity and Index Options;4.16;416.0;-1.0;416.0;-0.7034516;O
Open;Trades;USD;2022-06-17;BABA;BABA 17JUN22 100 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-06-17;BABA;BABA 17JUN22 240 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-07-05;BABA;BABA 08JUL22 100 C;Equity and Index Options;19.68;1968.0;-1.0;1968.0;-0.7463972;O
Open;Trades;USD;2022-07-08;BABA;BABA 08JUL22 100 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-08-10;BABA;BABA 16SEP22 150 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-08-10;BABA;BABA 16SEP22 165 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-09-21;BABA;BABA 16DEC22 150 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-03-23;BABA;BABA 17MAR23 160.0 P;Equity and Index Options;50.54;5054.0;-1.0;5054.0;-0.7271054;O
Open;Trades;USD;2022-04-21;BABA;BABA 17MAR23 160.0 P;Equity and Index Options;74.34;7434.0;-1.0;7434.0;-0.7392434;O
Open;Trades;USD;2022-09-21;BABA;BABA 17MAR23 160 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-07-05;BABA;BABA 16JUN23 150 P;Equity and Index Options;43.12;4312.0;-1.0;4312.0;-0.8000748;O
Open;Trades;USD;2022-10-24;BABA;BABA 16JUN23 150 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-03-16;BABA;BABA 16JUN23 175.0 P;Equity and Index Options;77.96;15592.0;-2.0;15592.0;-0.7821792;O
Open;Trades;USD;2022-09-21;BABA;BABA 16JUN23 175 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-01-21;CPRT;CPRT 21JAN22 115.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2022-02-04;META;FB 17MAR23 230.0 P;Equity and Index Options;33.8;3380.0;-1.0;3380.0;-0.770568;O
Open;Trades;USD;2022-01-21;META;FB 17MAR23 310.0 P;Equity and Index Options;46.0;4600.0;-1.0;4600.0;-1.05699;O
Open;Trades;USD;2022-01-12;FCX;FCX 21JAN22 40.0 C;Equity and Index Options;0.0;0.0;5.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-01-21;FCX;FCX 21JAN22 42.0 C;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2022-10-26;GOOG;GOOG 17MAR23 132.5 P;Equity and Index Options;0.0;0.0;20.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-01-21;GOOG;GOOG 17MAR23 2650.0 P;Equity and Index Options;312.0;31200.0;-1.0;31200.0;-1.20265;O
Open;Trades;USD;2022-10-27;GOOG;GOOG 19JAN24 132.5 P;Equity and Index Options;39.072;19536.0;-5.0;19536.0;-2.7840244;"O;P"
Open;Trades;USD;2022-10-28;GOOG;GOOG 19JAN24 132.5 P;Equity and Index Options;36.675;14670.0;-4.0;14670.0;-1.581263;"O;P"
Open;Trades;USD;2022-12-21;GOOG;GOOG 19JAN24 132.5 P;Equity and Index Options;0.0;0.0;9.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-12-29;GOOG;GOOG 21JUN24 115 P;Equity and Index Options;28.35;14175.0;-5.0;14175.0;-3.0012575;"O;P"
Open;Trades;USD;2022-10-27;GOOG;GOOG 21JUN24 132.5 P;Equity and Index Options;39.398;19699.0;-5.0;19699.0;-3.1777571;"O;P"
Open;Trades;USD;2022-03-08;IVV;IVV 18MAR22 445.0 P;Equity and Index Options;20.25;-2025.0;1.0;-2025.0;-0.64915;C
Open;Trades;USD;2022-03-08;IVV;IVV 16SEP22 440.0 P;Equity and Index Options;37.35;3735.0;-1.0;3735.0;-0.6703785;O
Open;Trades;USD;2022-07-13;IVV;IVV 16SEP22 440 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-06-14;META;META 20JAN23 325 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-10-06;META;META 17MAR23 230 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-07-27;META;META 17MAR23 310 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2022-01-21;MSFT;MSFT 21JAN22 215.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2022-01-24;MSFT;MSFT 17MAR23 285.0 P;Equity and Index Options;38.55;19275.0;-5.0;19275.0;-3.5159525;O
Open;Trades;USD;2022-01-24;MSFT;MSFT 16JUN23 285.0 P;Equity and Index Options;42.45;21225.0;-5.0;21225.0;-3.5258975;O
Open;Corporate Actions;HKD;2022-01-20;9618;9618.SPO;Stocks;;0.0;19.0476;0.0;;
Open;Corporate Actions;HKD;2022-03-28;9618;9618;Stocks;;0.0;19.0;0.0;;
Open;Corporate Actions;HKD;2022-03-28;9618;9618.SPO;Stocks;;0.0;-19.0;0.0;;
Open;Corporate Actions;HKD;2022-09-29;3918;3918;Stocks;;0.0;18.9488;0.0;;
Open;Corporate Actions;SGD;2022-04-22;BS6;BS6.SPO;Stocks;;0.0;7000.0;0.0;;
Open;Corporate Actions;SGD;2022-04-28;BS6;BS6.SPO;Stocks;;0.0;-7000.0;0.0;;
Open;Corporate Actions;USD;2022-06-06;AMZN;AMZN;Stocks;;0.0;950.0;0.0;;
Open;Corporate Actions;USD;2022-11-04;CPRT;CPRT;Stocks;;0.0;200.0;0.0;;
Open;Corporate Actions;USD;2022-06-06;AMZN;AMZN;Equity and Index Options;;0.0;-19.0;0.0;;
Open;Corporate Actions;USD;2022-06-06;AMZN;AMZN;Equity and Index Options;;0.0;-19.0;0.0;;
Open;Corporate Actions;USD;2022-07-18;GOOG;GOOG;Equity and Index Options;;0.0;-19.0;0.0;;
Open;Transaction Fees;HKD;2022-01-18;200;200;Stocks;9.2;-24.5244;0.0;-3.1350750799140004;;
Open;Transaction Fees;HKD;2022-06-29;700;700;Stocks;485.3;-132.8491963;0.0;-16.98276837381274;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-225.845;0.0;-28.870880895075004;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-113.4225;0.0;-14.499357915037502;;
Open;Transaction Fees;HKD;2022-10-31;2800;2800;Stocks;24.0;-0.684;0.0;-0.08743909554000001;;
Open;Withholding Tax;HKD;2022-06-20;2318;2318;;;-90.17;;-11.52687608895;;
Open;Withholding Tax;HKD;2022-06-23;386;386;;;-72.32;;-9.2450224992;;
Open;Withholding Tax;HKD;2022-07-27;1398;1398;;;-205.93;;-26.325048164550005;;
Open;Withholding Tax;HKD;2022-07-29;857;857;;;-22.62;;-2.8916262297000004;;
Open;Withholding Tax;HKD;2022-07-29;939;939;;;-170.38;;-21.7805162253;;
Open;Withholding Tax;HKD;2022-08-09;390;390;;;-229.56;;-29.345787678600004;;
Open;Withholding Tax;HKD;2022-09-29;386;386;;;-37.02;;-4.732449293700001;;
Open;Withholding Tax;HKD;2022-10-20;2318;2318;;;-42.38;;-5.417644545300001;;
Open;Withholding Tax;HKD;2022-10-28;857;857;;;-46.68;;-5.9673347658;;
Open;Withholding Tax;USD;2022-02-01;FCX;FCX;;;-11.25;;-11.25;;
Open;Withholding Tax;USD;2022-02-01;FCX;FCX;;;-11.25;;-11.25;;
Open;Withholding Tax;USD;2022-03-10;MSFT;MSFT;;;-18.6;;-18.6;;
Open;Withholding Tax;USD;2022-03-25;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2022-05-02;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2022-06-09;MSFT;MSFT;;;-18.23;;-18.23;;
Open;Withholding Tax;USD;2022-06-09;MSFT;MSFT;;;-0.37;;-0.37;;
Open;Withholding Tax;USD;2022-06-24;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2022-08-01;FCX;FCX;;;-1.49;;-1.49;;
Open;Withholding Tax;USD;2022-08-01;FCX;FCX;;;-21.01;;-21.01;;
Open;Withholding Tax;USD;2022-09-08;MSFT;MSFT;;;-18.6;;-18.6;;
Open;Withholding Tax;USD;2022-09-30;IVV;IVV;;;-57.18;;-57.18;;
Open;Withholding Tax;USD;2022-09-30;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2022-11-01;FCX;FCX;;;-0.9;;-0.9;;
Open;Withholding Tax;USD;2022-11-01;FCX;FCX;;;-21.6;;-21.6;;
Open;Withholding Tax;USD;2022-12-08;MSFT;MSFT;;;-20.4;;-20.4;;
Open;Withholding Tax;USD;2022-12-19;IVV;IVV;;;-51.71;;-51.71;;
Open;Withholding Tax;USD;2022-12-23;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;HKD;2022-03-17;16;16;;;1875.0;;239.69050312500002;;
Open;Dividends;HKD;2022-03-25;14;14;;;1170.0;;149.56687395;;
Open;Dividends;HKD;2022-04-11;808;808;;;1724.0;;220.38742794;;
Open;Dividends;HKD;2022-04-28;5;5;;;3388.17;;433.12649171895004;;
Open;Dividends;HKD;2022-04-29;27;27;;;300.0;;38.3504805;;
Open;Dividends;HKD;2022-05-05;1972;1972;;;3584.0;;458.16040704000005;;
Open;Dividends;HKD;2022-05-12;2888;2888;;;90.34;;11.548608027900002;;
Open;Dividends;HKD;2022-06-06;700;700;;;640.0;;81.8143584;;
Open;Dividends;HKD;2022-06-20;2318;2318;;;901.7;;115.26876088950002;;
Open;Dividends;HKD;2022-06-23;386;386;;;723.2;;92.45022499200002;;
Open;Dividends;HKD;2022-07-19;66;66;;;510.0;;65.19581685;;
Open;Dividends;HKD;2022-07-27;1398;1398;;;2059.31;;263.25175999485003;;
Open;Dividends;HKD;2022-07-29;857;857;;;226.22;;28.9188189957;;
Open;Dividends;HKD;2022-07-29;939;939;;;1703.8;;217.80516225300002;;
Open;Dividends;HKD;2022-08-09;390;390;;;2295.6;;293.457876786;;
Open;Dividends;HKD;2022-09-08;808;808;;;1750.0;;223.71113625;;
Open;Dividends;HKD;2022-09-16;14;14;;;270.0;;34.515432450000006;;
Open;Dividends;HKD;2022-09-29;386;386;;;370.2;;47.324492937;;
Open;Dividends;HKD;2022-09-29;5;5;;;1695.13;;216.69683336655004;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;1755.52;;224.4167850912;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;36.48;;4.6634184288;;
Open;Dividends;HKD;2022-10-14;2888;2888;;;40.18;;5.1364076883;;
Open;Dividends;HKD;2022-10-14;66;66;;;210.0;;26.845336350000004;;
Open;Dividends;HKD;2022-10-20;2318;2318;;;531.06;;67.8880205811;;
Open;Dividends;HKD;2022-10-28;857;857;;;466.76;;59.6682342606;;
Open;Dividends;HKD;2022-11-17;16;16;;;16650.0;;2128.45166775;;
Open;Dividends;SGD;2022-01-28;C38U;C38U;;;0.48;;0.36820134096;;
Open;Dividends;SGD;2022-01-28;C38U;C38U;;;8.49;;6.51256121823;;
Open;Dividends;SGD;2022-03-15;C38U;C38U;;;0.19;;0.14574636413;;
Open;Dividends;SGD;2022-03-15;C38U;C38U;;;0.5;;0.3835430635;;
Open;Dividends;SGD;2022-04-22;D05;D05;;;72.0;;55.230201144;;
Open;Dividends;SGD;2022-05-06;F34;F34;;;840.0;;644.35234668;;
Open;Dividends;SGD;2022-05-20;9CI;9CI;;;36.0;;27.615100572;;
Open;Dividends;SGD;2022-05-20;9CI;9CI;;;144.0;;110.460402288;;
Open;Dividends;SGD;2022-05-20;O39;O39;;;224.0;;171.827292448;;
Open;Dividends;SGD;2022-05-25;D05;D05;;;72.0;;55.230201144;;
Open;Dividends;SGD;2022-05-27;BS6;BS6;;;350.0;;268.48014445;;
Open;Dividends;SGD;2022-08-24;F34;F34;;;480.0;;368.20134096;;
Open;Dividends;SGD;2022-08-25;O39;O39;;;224.0;;171.827292448;;
Open;Dividends;SGD;2022-08-26;D05;D05;;;72.0;;55.230201144;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;1.3;;0.9972119651000001;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;0.06;;0.04602516762;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;8.31;;6.3744857153700005;;
Open;Dividends;SGD;2022-11-24;D05;D05;;;72.0;;55.230201144;;
Open;Dividends;USD;2022-02-01;FCX;FCX;;;37.5;;37.5;;
Open;Dividends;USD;2022-02-01;FCX;FCX;;;37.5;;37.5;;
Open;Dividends;USD;2022-03-10;MSFT;MSFT;;;62.0;;62.0;;
Open;Dividends;USD;2022-03-25;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2022-03-28;BHP;BHP;;;2643.0;;2643.0;;
Open;Dividends;USD;2022-03-28;BHP;BHP;;;957.0;;957.0;;
Open;Dividends;USD;2022-04-07;SOUHY;SOUHY;;;121.78;;121.78;;
Open;Dividends;USD;2022-05-02;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2022-05-11;H78;H78;;;160.0;;160.0;;
Open;Dividends;USD;2022-06-08;9618;9618;;;11.97;;11.97;;
Open;Dividends;USD;2022-06-09;MSFT;MSFT;;;60.76;;60.76;;
Open;Dividends;USD;2022-06-09;MSFT;MSFT;;;1.24;;1.24;;
Open;Dividends;USD;2022-06-24;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2022-08-01;FCX;FCX;;;4.95;;4.95;;
Open;Dividends;USD;2022-08-01;FCX;FCX;;;70.05;;70.05;;
Open;Dividends;USD;2022-09-08;MSFT;MSFT;;;62.0;;62.0;;
Open;Dividends;USD;2022-09-22;BHP;BHP;;;4200.0;;4200.0;;
Open;Dividends;USD;2022-09-30;IVV;IVV;;;190.59;;190.59;;
Open;Dividends;USD;2022-09-30;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2022-10-12;H78;H78;;;60.0;;60.0;;
Open;Dividends;USD;2022-10-13;SOUHY;SOUHY;;;45.0;;45.0;;
Open;Dividends;USD;2022-10-13;SOUHY;SOUHY;;;201.0;;201.0;;
Open;Dividends;USD;2022-11-01;FCX;FCX;;;3.0;;3.0;;
Open;Dividends;USD;2022-11-01;FCX;FCX;;;72.0;;72.0;;
Open;Dividends;USD;2022-12-08;MSFT;MSFT;;;68.0;;68.0;;
Open;Dividends;USD;2022-12-19;IVV;IVV;;;172.36;;172.36;;
Open;Dividends;USD;2022-12-23;NOV;NOV;;;15.0;;15.0;;
Open;Trades;HKD;2021-09-19;1398;1398;Stocks;4.18;-12540.0;3000.0;-1603.0500849000002;-35.33858;O
Open;Trades;HKD;2021-05-10;14;14;Stocks;28.5;-28500.0;1000.0;-3643.2956475;-52.5695;O
Open;Trades;HKD;2021-09-15;200;200;Stocks;9.33;-9330.0;1000.0;-1192.6999435500002;-31.25191;O
Open;Trades;HKD;2021-04-25;2318;2318;Stocks;86.3;-43150.0;500.0;-5516.077445250001;-79.68505;O
Open;Trades;HKD;2021-04-25;390;390;Stocks;3.92;-39200.0;10000.0;-5011.129452;-72.4184;"O;P"
Open;Trades;HKD;2021-05-11;3918;3918;Stocks;7.8;-15600.0;2000.0;-1994.2249860000002;-34.4212;O
Open;Trades;HKD;2021-04-20;700;700;Stocks;614.5;-61450.0;100.0;-7855.45675575;-112.81915;O
Open;Trades;HKD;2021-08-15;700;700;Stocks;455.8;-91160.0;200.0;-11653.4326746;-194.38932;O
Open;Trades;HKD;2021-04-26;939;939;Stocks;6.38;-25520.0;4000.0;-3262.3475412000003;-47.10504;O
Open;Trades;SGD;2021-09-10;C38U;C38U;Stocks;2.0;1.2144;-0.6072;0.9315493926288;0.0;C
Open;Trades;USD;2021-12-20;BABA;BABA;Stocks;230.0;-46000.0;200.0;-46000.0;0.0;"A;O"
Open;Trades;USD;2021-02-09;FCX;FCX;Stocks;31.38;15690.0;-500.0;15690.0;-2.906249;"C;P"
Open;Trades;USD;2021-01-19;MSFT;MSFT;Stocks;213.0;-21300.0;100.0;-21300.0;-1.0;O
Open;Trades;EUR;2021-03-19;MC;MC 19MAR21 360.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-08-19;700;700 30AUG21 450.0 P;Equity and Index Options;32.3;6460.0;-2.0;825.8136801;-24.0;O
Open;Trades;HKD;2021-08-30;700;700 30AUG21 450.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-09-03;700;700 29JUN22 500.0 P;Equity and Index Options;67.08;13416.0;-2.0;1715.0334879600002;-32.832;O
Open;Trades;HKD;2021-12-06;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.05;1025.0;-1.0;131.03080837500002;-19.0;O
Open;Trades;HKD;2021-12-20;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.5;1250.0;-1.0;159.79366875000002;-19.0;O
Open;Trades;USD;2021-01-27;AMZN;AMZN 21JAN22 3300.0 P;Equity and Index Options;460.0;46000.0;-1.0;46000.0;-2.0924;O
Open;Trades;USD;2021-01-15;BABA;BABA 15JAN21 200.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-01-15;BABA;BABA 15JAN21 210.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-06-18;BABA;BABA 18JUN21 185.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-05-06;BABA;BABA 21JAN22 220.0 P;Equity and Index Options;24.0;4800.0;-2.0;4800.0;-1.57568;O
Open;Trades;USD;2021-05-06;BABA;BABA 21JAN22 230.0 P;Equity and Index Options;29.0;5800.0;-2.0;5800.0;-1.59778;O
Open;Trades;USD;2021-12-20;BABA;BABA 21JAN22 230.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2021-04-19;BABA;BABA 21JAN22 240.0 P;Equity and Index Options;31.3;6260.0;-2.0;6260.0;-1.607946;O
Open;Trades;USD;2021-05-06;BABA;BABA 21JAN22 240.0 P;Equity and Index Options;35.5;7100.0;-2.0;7100.0;-1.62651;O
Open;Trades;USD;2021-07-27;BABA;BABA 18MAR22 180.0 P;Equity and Index Options;24.8;24800.0;-10.0;24800.0;-4.22448;O
Open;Trades;USD;2021-07-26;BABA;BABA 17JUN22 240.0 P;Equity and Index Options;54.65;10930.0;-2.0;10930.0;-0.875343;O
Open;Trades;USD;2021-11-18;BABA;BABA 16SEP22 150.0 P;Equity and Index Options;27.3;2730.0;-1.0;2730.0;-1.027273;O
Open;Trades;USD;2021-11-18;BABA;BABA 16SEP22 165.0 P;Equity and Index Options;36.92;3692.0;-1.0;3692.0;-0.7421792;O
Open;Trades;USD;2021-11-18;BABA;BABA 16DEC22 150.0 P;Equity and Index Options;30.5;3050.0;-1.0;3050.0;-1.028905;O
Open;Trades;USD;2021-01-15;BRK;BRK B 15JAN21 190.0 P;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-01-15;BRK;BRK B 15JAN21 200.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-01-26;CPRT;CPRT 21JAN22 115.0 P;Equity and Index Options;16.0;1600.0;-1.0;1600.0;-0.26116;O
Open;Trades;USD;2021-01-15;META;FB 15JAN21 155.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-10-22;META;FB 20JAN23 325.0 P;Equity and Index Options;48.15;9630.0;-2.0;9630.0;-1.417813;O
Open;Trades;USD;2021-01-15;FCX;FCX 15JAN21 8.0 P;Equity and Index Options;0.0;0.0;40.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-05-05;FCX;FCX 21JAN22 40.0 C;Equity and Index Options;6.0;3000.0;-5.0;3000.0;-3.7403;"O;P"
Open;Trades;USD;2021-05-07;FCX;FCX 21JAN22 42.0 C;Equity and Index Options;7.1;1420.0;-2.0;1420.0;-1.500982;O
Open;Trades;USD;2021-02-09;FCX;FCX 21JAN22 32.0 P;Equity and Index Options;6.9;3450.0;-5.0;3450.0;-1.605245;O
Open;Trades;USD;2021-05-06;FCX;FCX 21JAN22 32.0 P;Equity and Index Options;2.44;-1220.0;5.0;-1220.0;-2.164;C
Open;Trades;USD;2021-01-15;GOOG;GOOG 15JAN21 1280.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-09-28;IVV;IVV 18MAR22 445.0 P;Equity and Index Options;24.5;2450.0;-1.0;2450.0;-0.316645;O
Open;Trades;USD;2021-01-15;MSFT;MSFT 15JAN21 170.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-01-15;MSFT;MSFT 15JAN21 175.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-01-15;MSFT;MSFT 15JAN21 185.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-01-15;MSFT;MSFT 15JAN21 190.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-03-19;MSFT;MSFT 19MAR21 140.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-01-19;MSFT;MSFT 21JAN22 215.0 P;Equity and Index Options;27.0;5400.0;-2.0;5400.0;-1.57094;"O;P"
Open;Corporate Actions;SGD;2021-09-20;9CI;C31;Stocks;;0.0;1200.0;0.0;;
Open;Corporate Actions;SGD;2021-09-20;9CI;C31;Stocks;;1141.2;-1200.0;875.3986881324;;
Open;Corporate Actions;SGD;2021-09-20;C38U;C38U;Stocks;;0.0;185.6072;0.0;;
Open;Corporate Actions;USD;2021-01-04;NOV;US62955J1034) (NOV;Stocks;;0.0;300.0;0.0;;
Open;Corporate Actions;USD;2021-01-04;NOV;US62955J1034) (NOV.OLD;Stocks;;0.0;-300.0;0.0;;
Open;Transaction Fees;HKD;2021-04-20;700;700;Stocks;614.5;-63.65915;0.0;-8.13786330240525;;
Open;Transaction Fees;HKD;2021-04-25;2318;2318;Stocks;86.3;-45.16505;0.0;-5.77367123102175;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-24.63504;0.0;-3.1492187371224003;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-4.10584;0.0;-0.5248697895204;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-12.31752;0.0;-1.5746093685612002;;
Open;Transaction Fees;HKD;2021-04-26;939;939;Stocks;6.38;-26.68904;0.0;-3.4117916936124;;
Open;Transaction Fees;HKD;2021-05-10;14;14;Stocks;28.5;-29.7695;0.0;-3.8055820974825005;;
Open;Transaction Fees;HKD;2021-05-11;3918;3918;Stocks;7.8;-16.4212;0.0;-2.099203034622;;
Open;Transaction Fees;HKD;2021-08-15;700;700;Stocks;455.8;-121.46132;0.0;-15.526999947214202;;
Open;Transaction Fees;HKD;2021-09-15;200;200;Stocks;9.33;-13.25191;0.0;-1.6940570534758501;;
Open;Transaction Fees;HKD;2021-09-19;1398;1398;Stocks;4.18;-17.33858;0.0;-2.2164762472923;;
Open;Withholding Tax;HKD;2021-06-28;386;386;;;-31.38;;-4.0114602603;;
Open;Withholding Tax;HKD;2021-07-27;1398;1398;;;-95.97;;-12.26831871195;;
Open;Withholding Tax;HKD;2021-07-30;857;857;;;-21.21;;-2.7113789713500003;;
Open;Withholding Tax;HKD;2021-08-05;390;390;;;-216.71;;-27.703108763850004;;
Open;Withholding Tax;HKD;2021-08-05;939;939;;;-156.33;;-19.984435388550004;;
Open;Withholding Tax;HKD;2021-09-30;386;386;;;-38.44;;-4.9139749014000005;;
Open;Withholding Tax;HKD;2021-10-25;2318;2318;;;-52.84;;-6.754797965400001;;
Open;Withholding Tax;HKD;2021-10-29;857;857;;;-31.31;;-4.00251181485;;
Open;Withholding Tax;USD;2021-03-11;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-05-03;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-06-10;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-08-02;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-09-09;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-11-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-12-09;MSFT;MSFT;;;-18.6;;-18.6;;
Open;Withholding Tax;USD;2021-12-17;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;HKD;2021-03-18;16;16;;;1875.0;;239.69050312500002;;
Open;Dividends;HKD;2021-04-07;808;808;;;1784.0;;228.05752404000003;;
Open;Dividends;HKD;2021-04-29;5;5;;;2796.85;;357.53513795475004;;
Open;Dividends;HKD;2021-05-06;1972;1972;;;3416.0;;436.68413796000004;;
Open;Dividends;HKD;2021-05-20;2888;2888;;;89.41;;11.42972153835;;
Open;Dividends;HKD;2021-06-07;700;700;;;320.0;;40.9071792;;
Open;Dividends;HKD;2021-06-28;386;386;;;313.8;;40.114602603;;
Open;Dividends;HKD;2021-07-05;3918;3918;;;291.4;;37.251100059;;
Open;Dividends;HKD;2021-07-20;66;66;;;490.0;;62.63911815000001;;
Open;Dividends;HKD;2021-07-27;1398;1398;;;959.73;;122.68702216755001;;
Open;Dividends;HKD;2021-07-30;857;857;;;212.14;;27.1189031109;;
Open;Dividends;HKD;2021-08-05;390;390;;;2167.1;;277.0310876385;;
Open;Dividends;HKD;2021-08-05;939;939;;;1563.34;;199.8494672829;;
Open;Dividends;HKD;2021-09-06;14;14;;;270.0;;34.515432450000006;;
Open;Dividends;HKD;2021-09-08;808;808;;;1802.0;;230.35855287;;
Open;Dividends;HKD;2021-09-30;386;386;;;384.4;;49.139749014;;
Open;Dividends;HKD;2021-09-30;5;5;;;1308.18;;167.2311052683;;
Open;Dividends;HKD;2021-10-05;1972;1972;;;1736.0;;221.92144716;;
Open;Dividends;HKD;2021-10-19;66;66;;;125.0;;15.979366875000002;;
Open;Dividends;HKD;2021-10-22;2888;2888;;;29.9;;3.8222645565;;
Open;Dividends;HKD;2021-10-25;2318;2318;;;528.4;;67.547979654;;
Open;Dividends;HKD;2021-10-29;857;857;;;313.06;;40.020004751100004;;
Open;Dividends;HKD;2021-11-18;16;16;;;5550.0;;709.4838892500001;;
Open;Dividends;SGD;2021-05-06;F34;F34;;;520.0;;398.88478604;;
Open;Dividends;SGD;2021-05-06;F34;F34;;;720.0;;552.30201144;;
Open;Dividends;SGD;2021-05-18;9CI;C31;;;108.0;;82.845301716;;
Open;Dividends;SGD;2021-05-24;D05;D05;;;36.0;;27.615100572;;
Open;Dividends;SGD;2021-05-28;BS6;BS6;;;315.0;;241.632130005;;
Open;Dividends;SGD;2021-06-25;D05;D05;;;36.0;;27.615100572;;
Open;Dividends;SGD;2021-06-29;O39;O39;;;127.2;;97.57335535440001;;
Open;Dividends;SGD;2021-08-26;D05;D05;;;66.0;;50.627684382;;
Open;Dividends;SGD;2021-08-26;O39;O39;;;200.0;;153.4172254;;
Open;Dividends;SGD;2021-08-27;F34;F34;;;400.0;;306.8344508;;
Open;Dividends;SGD;2021-11-26;D05;D05;;;66.0;;50.627684382;;
Open;Dividends;USD;2021-03-11;MSFT;MSFT;;;56.0;;56.0;;
Open;Dividends;USD;2021-03-23;BHP;BHP;;;2424.0;;2424.0;;
Open;Dividends;USD;2021-04-08;SOUHY;SOUHY;;;21.0;;21.0;;
Open;Dividends;USD;2021-05-03;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2021-05-12;H78;H78;;;160.0;;160.0;;
Open;Dividends;USD;2021-06-10;MSFT;MSFT;;;56.0;;56.0;;
Open;Dividends;USD;2021-08-02;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2021-09-09;MSFT;MSFT;;;56.0;;56.0;;
Open;Dividends;USD;2021-09-21;BHP;BHP;;;4800.0;;4800.0;;
Open;Dividends;USD;2021-10-07;SOUHY;SOUHY;;;52.5;;52.5;;
Open;Dividends;USD;2021-10-07;SOUHY;SOUHY;;;30.0;;30.0;;
Open;Dividends;USD;2021-10-13;H78;H78;;;60.0;;60.0;;
Open;Dividends;USD;2021-11-01;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2021-12-09;MSFT;MSFT;;;62.0;;62.0;;
Open;Dividends;USD;2021-12-17;NOV;NOV;;;15.0;;15.0;;
Open;Trades;HKD;2020-03-18;16;16;Stocks;97.5;-48750.0;500.0;-6231.953081250001;-89.31625;O
Open;Trades;HKD;2020-03-18;16;16;Stocks;96.5;-48250.0;500.0;-6168.03561375;-88.90275;O
Open;Trades;HKD;2020-07-20;16;16;Stocks;94.05;-47025.0;500.0;-6011.4378183750005;-86.889675;O
Open;Trades;HKD;2020-01-29;1972;1972;Stocks;24.65;-24650.0;1000.0;-3151.1311477500003;-45.38555;O
Open;Trades;HKD;2020-05-04;1972;1972;Stocks;20.9;-20900.0;1000.0;-2671.7501415;-39.5643;O
Open;Trades;HKD;2020-05-24;1972;1972;Stocks;17.78;-35560.0;2000.0;-4545.810288600001;-67.40812;"O;P"
Open;Trades;HKD;2020-07-20;1972;1972;Stocks;18.2;-29120.0;1600.0;-3722.5533072000003;-55.08224;"O;P"
Open;Trades;HKD;2020-07-22;27;27;Stocks;51.5;-51500.0;1000.0;-6583.4991525000005;-94.5905;O
Open;Trades;HKD;2020-03-18;2800;2800;Stocks;21.7;-10850.0;500.0;-1387.0090447500002;-18.29295;O
Open;Trades;HKD;2020-03-31;5;5;Stocks;40.8;-16320.0;400.0;-2086.2661392;-35.44064;O
Open;Trades;HKD;2020-05-14;5;5;Stocks;38.0;-45600.0;1200.0;-5829.2730360000005;-83.7112;O
Open;Trades;HKD;2020-05-21;66;66;Stocks;40.0;-20000.0;500.0;-2556.6987000000004;-38.54;O
Open;Trades;HKD;2020-09-29;700;700;Stocks;530.0;-53000.0;100.0;-6775.251555000001;-101.981;"A;O"
Open;Trades;HKD;2020-04-23;808;808;Stocks;2.38;-23800.0;10000.0;-3042.471453;-48.6826;"O;P"
Open;Trades;HKD;2020-07-22;808;808;Stocks;2.29;-22900.0;10000.0;-2927.4200115000003;-41.9383;O
Open;Trades;SGD;2020-07-22;BS6;BS6;Stocks;0.935;-3085.5;3300.0;-2366.8442448585;-2.5;O
Open;Trades;SGD;2020-08-26;BS6;BS6;Stocks;0.92;-3404.0;3700.0;-2611.161176308;-2.7232;O
Open;Trades;SGD;2020-07-23;9CI;C31;Stocks;2.83;-3396.0;1200.0;-2605.0244872920002;-2.7168;O
Open;Trades;SGD;2020-07-22;D05;D05;Stocks;21.2;-4240.0;200.0;-3252.44517848;-3.392;O
Open;Trades;SGD;2020-09-08;F34;F34;Stocks;4.32;-4320.0;1000.0;-3313.8120686400002;-3.456;O
Open;Trades;SGD;2020-07-22;O39;O39;Stocks;9.13;-3652.0;400.0;-2801.398535804;-2.9216;O
Open;Trades;SGD;2020-08-26;O39;O39;Stocks;8.62;-3448.0;400.0;-2644.912965896;-2.7584;O
Open;Trades;USD;2020-01-06;AMZN;AMZN;Stocks;1900.0;9500.0;-5.0;9500.0;-1.197245;C
Open;Trades;USD;2020-04-13;AMZN;AMZN;Stocks;2146.093;32191.395;-15.0;32191.395;-1.71321483;C
Open;Trades;USD;2020-06-08;AMZN;AMZN;Stocks;2499.5;74985.0;-30.0;74985.0;-2.6607385;C
Open;Trades;USD;2020-06-09;CPRT;CPRT;Stocks;88.35;-8835.0;100.0;-8835.0;-1.0;O
Open;Trades;USD;2020-06-12;CPRT;CPRT;Stocks;85.13;-8513.0;100.0;-8513.0;-1.0;O
Open;Trades;USD;2020-01-17;FCX;FCX;Stocks;15.0;-6000.0;400.0;-6000.0;0.0;"A;O"
Open;Trades;USD;2020-01-07;H78;H78;Stocks;5.6;-1120.0;200.0;-1120.0;-2.0;"O;P"
Open;Trades;USD;2020-05-25;H78;H78;Stocks;3.5;-700.0;200.0;-700.0;-2.0;O
Open;Trades;USD;2020-07-20;H78;H78;Stocks;3.97;-2382.0;600.0;-2382.0;-2.0;O
Open;Trades;USD;2020-03-18;SOUHY;SOUHY;Stocks;4.85;-485.0;100.0;-485.0;-1.0;O
Open;Trades;EUR;2020-05-04;MC;MC 19MAR21 360.0 P;Equity and Index Options;50.8;5080.0;-1.0;5891.35449108;-1.1;O
Open;Trades;HKD;2020-07-24;27;27 28AUG20 50.0 P;Equity and Index Options;2.18;4360.0;-2.0;557.3603166;-24.0;O
Open;Trades;HKD;2020-08-28;27;27 28AUG20 50.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2020-07-24;700;700 29SEP20 530.0 P;Equity and Index Options;38.02;3802.0;-1.0;486.02842287000004;-21.0;O
Open;Trades;HKD;2020-09-29;700;700 29SEP20 530.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;-2.0;"A;C"
Open;Trades;USD;2020-04-13;BABA;BABA 17APR20 200.0 P;Equity and Index Options;4.5;900.0;-2.0;900.0;-1.51149;O
Open;Trades;USD;2020-04-17;BABA;BABA 17APR20 200.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2020-04-30;BABA;BABA 15JAN21 200.0 P;Equity and Index Options;23.2;4640.0;-2.0;4640.0;-0.994144;O
Open;Trades;USD;2020-04-17;BABA;BABA 15JAN21 210.0 P;Equity and Index Options;21.2;4240.0;-2.0;4240.0;-0.985304;O
Open;Trades;USD;2020-03-23;BABA;BABA 18JUN21 185.0 P;Equity and Index Options;35.0;3500.0;-1.0;3500.0;-1.17315;O
Open;Trades;USD;2020-12-31;BABA;BABA 21JAN22 250.0 P;Equity and Index Options;50.1;10020.0;-2.0;10020.0;-1.673042;"O;P"
Open;Trades;USD;2020-04-30;BRK;BRK B 15JAN21 190.0 P;Equity and Index Options;18.38;3676.0;-2.0;3676.0;-0.9728396;O
Open;Trades;USD;2020-04-30;BRK;BRK B 15JAN21 190.0 P;Equity and Index Options;18.6;3720.0;-2.0;3720.0;-1.573812;O
Open;Trades;USD;2020-04-30;BRK;BRK B 15JAN21 200.0 P;Equity and Index Options;23.3;2330.0;-1.0;2330.0;-1.147293;O
Open;Trades;USD;2020-07-10;CPRT;CPRT 21AUG20 85.0 P;Equity and Index Options;5.2;2600.0;-5.0;2600.0;0.46354;O
Open;Trades;USD;2020-08-21;CPRT;CPRT 21AUG20 85.0 P;Equity and Index Options;0.0;0.0;5.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2020-03-23;CPRT;CPRT 18DEC20 60.0 P;Equity and Index Options;15.0;1500.0;-1.0;1500.0;-1.12895;O
Open;Trades;USD;2020-12-18;CPRT;CPRT 18DEC20 60.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2020-03-12;META;FB 15JAN21 155.0 P;Equity and Index Options;23.5;2350.0;-1.0;2350.0;-1.147735;O
Open;Trades;USD;2020-01-17;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"A;C"
Open;Trades;USD;2020-03-06;FCX;FCX 15JAN21 8.0 P;Equity and Index Options;1.25;2500.0;-20.0;2500.0;-14.97125;O
Open;Trades;USD;2020-04-17;FCX;FCX 15JAN21 8.0 P;Equity and Index Options;1.83;1830.0;-10.0;1830.0;-4.498443;O
Open;Trades;USD;2020-04-17;FCX;FCX 15JAN21 8.0 P;Equity and Index Options;1.84;1840.0;-10.0;1840.0;-4.498664;"O;P"
Open;Trades;USD;2020-04-17;GOOG;GOOG 15JAN21 1280.0 P;Equity and Index Options;141.0;14100.0;-1.0;14100.0;-0.54741;O
Open;Trades;USD;2020-02-27;MSFT;MSFT 15JAN21 170.0 P;Equity and Index Options;19.8;1980.0;-1.0;1980.0;-1.139558;O
Open;Trades;USD;2020-02-27;MSFT;MSFT 15JAN21 175.0 P;Equity and Index Options;22.95;2295.0;-1.0;2295.0;-1.1465195;O
Open;Trades;USD;2020-02-07;MSFT;MSFT 15JAN21 185.0 P;Equity and Index Options;17.0;1700.0;-1.0;1700.0;-1.13099;O
Open;Trades;USD;2020-02-27;MSFT;MSFT 15JAN21 185.0 P;Equity and Index Options;30.0;3000.0;-1.0;3000.0;-1.1621;O
Open;Trades;USD;2020-02-07;MSFT;MSFT 15JAN21 190.0 P;Equity and Index Options;19.5;1950.0;-1.0;1950.0;-0.836165;O
Open;Trades;USD;2020-03-12;MSFT;MSFT 19MAR21 140.0 P;Equity and Index Options;21.99;4398.0;-2.0;4398.0;-0.9887958;O
Open;Transaction Fees;HKD;2020-01-29;1972;1972;Stocks;24.65;-25.66555;0.0;-3.28095391598925;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;97.5;-50.31625;0.0;-6.43217454819375;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;96.5;-50.30275;0.0;-6.430448776571251;;
Open;Transaction Fees;HKD;2020-03-18;2800;2800;Stocks;21.7;-0.29295;0.0;-0.03744924420825;;
Open;Transaction Fees;HKD;2020-03-31;5;5;Stocks;40.8;-17.44064;0.0;-2.2295230807584;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.3917194779231;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.3917194779231;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.3917194779231;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.3917194779231;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.3917194779231;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.3917194779231;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.3917194779231;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.3917194779231;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-5.12852;0.0;-0.6556040208462001;;
Open;Transaction Fees;HKD;2020-05-04;1972;1972;Stocks;20.9;-21.5643;0.0;-2.7566708888205;;
Open;Transaction Fees;HKD;2020-05-14;5;5;Stocks;38.0;-47.2312;0.0;-6.037797381972001;;
Open;Transaction Fees;HKD;2020-05-21;66;66;Stocks;40.0;-20.54;0.0;-2.6257295649000003;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.52361342777922;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.52361342777922;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.96661877611688;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.96661877611688;;
Open;Transaction Fees;HKD;2020-07-20;16;16;Stocks;94.05;-49.269675;0.0;-6.298385701096126;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.0478067148236;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.0478067148236;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-4.09828;0.0;-0.5239033574118;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-11.29484;0.0;-1.4438751372354002;;
Open;Transaction Fees;HKD;2020-07-22;808;808;Stocks;2.29;-23.6183;0.0;-3.0192438453105006;;
Open;Transaction Fees;HKD;2020-07-22;27;27;Stocks;51.5;-53.3905;0.0;-6.825171097117501;;
Open;Transaction Fees;HKD;2020-09-29;700;700;Stocks;530.0;-54.431;0.0;-6.958183346985;;
Open;Withholding Tax;HKD;2020-06-19;386;386;;;-41.54;;-5.3102631999000005;;
Open;Withholding Tax;HKD;2020-07-21;1398;1398;;;-74.27;;-9.49430062245;;
Open;Withholding Tax;HKD;2020-07-31;857;857;;;-14.44;;-1.8459364614;;
Open;Withholding Tax;HKD;2020-11-05;386;386;;;-15.98;;-2.0428022613000003;;
Open;Withholding Tax;HKD;2020-11-13;857;857;;;-19.59;;-2.50428637665;;
Open;Withholding Tax;USD;2020-02-03;FCX;FCX;;;-16.5;;-16.5;;
Open;Withholding Tax;USD;2020-03-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;HKD;2020-05-07;1972;1972;;;590.0;;75.42261165000001;;
Open;Dividends;HKD;2020-05-29;2800;2800;;;45.0;;5.752572075000001;;
Open;Dividends;HKD;2020-06-19;386;386;;;415.4;;53.102631999;;
Open;Dividends;HKD;2020-07-02;200;200;;;30.1;;3.8478315435000003;;
Open;Dividends;HKD;2020-07-21;1398;1398;;;862.3;;110.2320644505;;
Open;Dividends;HKD;2020-07-30;2828;2828;;;488.0;;62.38344828;;
Open;Dividends;HKD;2020-07-31;857;857;;;144.4;;18.459364614000002;;
Open;Dividends;HKD;2020-09-09;808;808;;;1798.0;;229.84721313000003;;
Open;Dividends;HKD;2020-10-06;1972;1972;;;1680.0;;214.76269080000003;;
Open;Dividends;HKD;2020-10-09;66;66;;;5.75;;0.7350508762500001;;
Open;Dividends;HKD;2020-10-09;66;66;;;119.25;;15.244315998750002;;
Open;Dividends;HKD;2020-11-05;386;386;;;159.8;;20.428022613000003;;
Open;Dividends;HKD;2020-11-13;857;857;;;195.86;;25.037750369100003;;
Open;Dividends;HKD;2020-11-19;16;16;;;5550.0;;709.4838892500001;;
Open;Dividends;SGD;2020-06-24;F34;F34;;;665.0;;510.112274455;;
Open;Dividends;SGD;2020-08-27;F34;F34;;;280.0;;214.78411556;;
Open;Dividends;SGD;2020-10-05;D05;D05;;;36.0;;27.615100572;;
Open;Dividends;SGD;2020-10-07;O39;O39;;;63.6;;48.786677677200004;;
Open;Dividends;SGD;2020-12-29;D05;D05;;;36.0;;27.615100572;;
Open;Dividends;USD;2020-02-03;FCX;FCX;;;55.0;;55.0;;
Open;Dividends;USD;2020-03-24;BHP;BHP;;;1560.0;;1560.0;;
Open;Dividends;USD;2020-03-27;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2020-04-02;SOUHY;SOUHY;;;11.0;;11.0;;
Open;Dividends;USD;2020-04-02;SOUHY;SOUHY;;;11.0;;11.0;;
Open;Dividends;USD;2020-05-13;H78;H78;;;32.0;;32.0;;
Open;Dividends;USD;2020-09-22;BHP;BHP;;;1320.0;;1320.0;;
Open;Dividends;USD;2020-10-08;SOUHY;SOUHY;;;15.0;;15.0;;
Open;Dividends;USD;2020-10-14;H78;H78;;;60.0;;60.0;;
Open;Trades;HKD;2019-03-08;1398;1398;Stocks;5.9;-17700.0;3000.0;-2262.6783495;-36.4779;O
Open;Trades;USD;2019-02-11;AMZN;AMZN;Stocks;1800.0;-180000.0;100.0;-180000.0;0.0;"A;O"
Open;Trades;USD;2019-02-19;AMZN;AMZN;Stocks;1628.88;81444.0;-50.0;81444.0;-2.064722;C
Open;Trades;USD;2019-10-14;FCX;FCX;Stocks;15.0;-1500.0;100.0;-1500.0;0.0;"A;O"
Open;Trades;USD;2019-02-11;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2019-03-08;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;3.8;1900.0;-5.0;1900.0;-3.9787;O
Open;Trades;USD;2019-10-14;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2019-03-08;1398;1398;Stocks;5.9;-18.4779;0.0;-2.3621211454365003;;
Open;Withholding Tax;HKD;2019-06-21;386;386;;;-60.6;;-7.7467970610000005;;
Open;Withholding Tax;HKD;2019-07-23;1398;1398;;;-85.12;;-10.881309667200002;;
Open;Withholding Tax;HKD;2019-08-02;857;857;;;-20.47;;-2.61678111945;;
Open;Withholding Tax;HKD;2019-09-26;386;386;;;-26.78;;-3.4234195593000005;;
Open;Withholding Tax;HKD;2019-11-01;857;857;;;-17.26;;-2.2064309781;;
Open;Withholding Tax;USD;2019-02-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-03-29;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-05-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-06-28;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-08-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-09-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-11-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-12-20;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;CNH;2019-07-31;3188;3188;;;390.0;;58.091925839999995;;
Open;Dividends;HKD;2019-01-04;200;200;;;24.38;;3.1166157153;;
Open;Dividends;HKD;2019-04-08;5;5;;;1318.35;;168.53118655725;;
Open;Dividends;HKD;2019-05-16;2888;2888;;;150.56;;19.246827813600003;;
Open;Dividends;HKD;2019-06-21;386;386;;;606.0;;77.46797061000001;;
Open;Dividends;HKD;2019-07-05;200;200;;;23.5;;3.0041209725000004;;
Open;Dividends;HKD;2019-07-05;5;5;;;625.02;;79.8993910737;;
Open;Dividends;HKD;2019-07-23;1398;1398;;;854.32;;109.21194166920002;;
Open;Dividends;HKD;2019-07-30;2828;2828;;;640.0;;81.8143584;;
Open;Dividends;HKD;2019-08-02;857;857;;;204.72;;26.1703678932;;
Open;Dividends;HKD;2019-09-26;386;386;;;267.8;;34.234195593;;
Open;Dividends;HKD;2019-09-26;5;5;;;625.44;;79.95308174640002;;
Open;Dividends;HKD;2019-09-27;2828;2828;;;520.0;;66.4741662;;
Open;Dividends;HKD;2019-10-04;200;200;;;61.1;;7.810714528500001;;
Open;Dividends;HKD;2019-10-21;2888;2888;;;70.24;;8.9791258344;;
Open;Dividends;HKD;2019-11-01;857;857;;;172.56;;22.059196383600003;;
Open;Dividends;HKD;2019-11-20;5;5;;;626.63;;80.10520531905;;
Open;Dividends;SGD;2019-05-16;F34;F34;;;490.0;;375.87220223;;
Open;Dividends;SGD;2019-08-30;F34;F34;;;210.0;;161.08808667;;
Open;Dividends;USD;2019-01-30;BHP;BHP;;;2448.0;;2448.0;;
Open;Dividends;USD;2019-02-01;FCX;FCX;;;50.0;;50.0;;
Open;Dividends;USD;2019-03-26;BHP;BHP;;;1320.0;;1320.0;;
Open;Dividends;USD;2019-03-29;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2019-04-04;SOUHY;SOUHY;;;68.0;;68.0;;
Open;Dividends;USD;2019-05-01;FCX;FCX;;;50.0;;50.0;;
Open;Dividends;USD;2019-06-28;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2019-08-01;FCX;FCX;;;50.0;;50.0;;
Open;Dividends;USD;2019-09-25;BHP;BHP;;;1872.0;;1872.0;;
Open;Dividends;USD;2019-09-27;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2019-10-10;SOUHY;SOUHY;;;28.0;;28.0;;
Open;Dividends;USD;2019-11-01;FCX;FCX;;;50.0;;50.0;;
Open;Dividends;USD;2019-12-20;NOV;NOV;;;15.0;;15.0;;
Open;Trades;HKD;2018-01-01;1398;1398;Stocks;6.48;6480.0;-1000.0;828.3703788;-25.17496;C
Open;Trades;HKD;2018-02-05;215;215;Stocks;3.35;6700.0;-2000.0;856.4940645;-25.1809;C
Open;Trades;HKD;2018-02-19;215;215;Stocks;3.4;6800.0;-2000.0;869.2775580000001;-25.1836;C
Open;Trades;USD;2018-08-17;FCX;FCX;Stocks;21.0;-21000.0;1000.0;-21000.0;0.0;"A;O"
Open;Trades;USD;2018-10-26;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;216.21;21621.0;-1.0;21621.0;-1.367373;O
Open;Trades;USD;2018-01-18;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;3.08;3080.0;-10.0;3080.0;-5.006148;O
Open;Trades;USD;2018-08-17;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;0.0;0.0;10.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2018-01-01;1398;1398;Stocks;6.48;-7.17496;0.0;-0.9172105452276;;
Open;Transaction Fees;HKD;2018-02-05;215;215;Stocks;3.35;-7.1809;0.0;-0.9179698847415001;;
Open;Transaction Fees;HKD;2018-02-19;215;215;Stocks;3.4;-7.1836;0.0;-0.9183150390660001;;
Open;Withholding Tax;HKD;2018-06-14;386;386;;;-98.64;;-12.609637988400001;;
Open;Withholding Tax;HKD;2018-07-26;857;857;;;-14.86;;-1.8996271341000002;;
Open;Withholding Tax;HKD;2018-09-21;386;386;;;-36.5;;-4.6659751275;;
Open;Withholding Tax;HKD;2018-11-01;857;857;;;-20.39;;-2.6065543246500003;;
Open;Withholding Tax;USD;2018-03-30;NOV;NOV;;;-1.5;;-1.5;;
Open;Withholding Tax;USD;2018-03-30;NOV;NOV;;;1.5;;1.5;;
Open;Withholding Tax;USD;2018-03-30;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2018-06-29;NOV;NOV;;;-1.5;;-1.5;;
Open;Withholding Tax;USD;2018-06-29;NOV;NOV;;;1.5;;1.5;;
Open;Withholding Tax;USD;2018-06-29;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2018-09-28;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2018-11-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2018-12-21;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;CNH;2018-07-26;3188;3188;;;350.0;;52.1337796;;
Open;Dividends;HKD;2018-04-06;5;5;;;1317.81;;168.46215569235;;
Open;Dividends;HKD;2018-05-17;2888;2888;;;110.45;;14.119368570750002;;
Open;Dividends;HKD;2018-06-14;386;386;;;986.4;;126.096379884;;
Open;Dividends;HKD;2018-07-04;200;200;;;40.0;;5.1133974;;
Open;Dividends;HKD;2018-07-05;5;5;;;627.87;;80.26372063845001;;
Open;Dividends;HKD;2018-07-26;857;857;;;148.62;;18.9988280397;;
Open;Dividends;HKD;2018-07-30;2828;2828;;;440.0;;56.247371400000006;;
Open;Dividends;HKD;2018-09-21;386;386;;;365.0;;46.659751275000005;;
Open;Dividends;HKD;2018-09-27;5;5;;;627.73;;80.24582374755;;
Open;Dividends;HKD;2018-09-28;2828;2828;;;800.0;;102.267948;;
Open;Dividends;HKD;2018-10-05;200;200;;;45.0;;5.752572075000001;;
Open;Dividends;HKD;2018-10-22;2888;2888;;;60.13;;7.686714641550001;;
Open;Dividends;HKD;2018-11-01;857;857;;;203.88;;26.0629865478;;
Open;Dividends;HKD;2018-11-21;5;5;;;626.32;;80.06557648920001;;
Open;Dividends;SGD;2018-05-16;F34;F34;;;490.0;;375.87220223;;
Open;Dividends;SGD;2018-08-31;F34;F34;;;245.0;;187.936101115;;
Open;Dividends;USD;2018-03-27;BHP;BHP;;;1320.0;;1320.0;;
Open;Dividends;USD;2018-03-30;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-04-05;SOUHY;SOUHY;;;73.0;;73.0;;
Open;Dividends;USD;2018-06-29;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-09-25;BHP;BHP;;;1512.0;;1512.0;;
Open;Dividends;USD;2018-09-28;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-10-11;SOUHY;SOUHY;;;62.0;;62.0;;
Open;Dividends;USD;2018-11-01;FCX;FCX;;;50.0;;50.0;;
Open;Dividends;USD;2018-12-21;NOV;NOV;;;15.0;;15.0;;
Open;Withholding Tax;HKD;2025-09-25;386;386;;;-19.34;;-2.4723276429000003;;
Open;Withholding Tax;HKD;2025-10-23;857;857;;;-48.11;;-6.15013872285;;
Open;Withholding Tax;HKD;2025-10-24;2318;2318;;;-52.02;;-6.649973318700001;;
Open;Withholding Tax;HKD;2025-12-23;390;390;;;-89.85;;-11.48596890975;;
Open;Dividends;HKD;2025-09-16;215;215;;;1140.0;;145.73182590000002;;
Open;Dividends;HKD;2025-09-23;1299;1299;;;294.0;;37.58347089;;
Open;Dividends;HKD;2025-09-25;386;386;;;193.4;;24.723276429000002;;
Open;Dividends;HKD;2025-09-26;1883;1883;;;600.0;;76.700961;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;26.5;;3.3876257775000003;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;173.5;;22.179361222500003;;
Open;Dividends;HKD;2025-09-30;3918;3918;;;159.42;;20.3794453377;;
Open;Dividends;HKD;2025-10-09;1972;1972;;;1960.0;;250.55647260000003;;
Open;Dividends;HKD;2025-10-23;857;857;;;482.1;;61.62922216350001;;
Open;Dividends;HKD;2025-10-24;2318;2318;;;520.24;;66.50484658440001;;
Open;Dividends;HKD;2025-10-31;27;27;;;700.0;;89.48445450000001;;
Open;Dividends;HKD;2025-11-20;16;16;;;12600.0;;1610.7201810000001;;
Open;Dividends;HKD;2025-11-28;2800;2800;;;825.0;;105.46382137500001;;
Open;Dividends;HKD;2025-12-18;5;5;;;1866.53;;238.60774122555003;;
Open;Dividends;HKD;2025-12-23;390;390;;;898.95;;114.91721481825002;;
Open;Dividends;CNH;2022-07-29;3188;3188;;;370.0;;55.11285272;;
Open;Dividends;HKD;2022-05-26;215;215;;;2605.0;;333.010005675;;
Open;Dividends;HKD;2022-05-31;2800;2800;;;40.0;;5.1133974;;
Open;Dividends;HKD;2022-06-13;1883;1883;;;1700.0;;217.31938950000003;;
Open;Dividends;HKD;2022-07-28;2828;2828;;;460.0;;58.804070100000004;;
Open;Dividends;HKD;2022-09-02;215;215;;;1140.0;;145.73182590000002;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;283.5;;36.241204072500004;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;76.5;;9.779372527500001;;
Open;Dividends;HKD;2022-09-30;1883;1883;;;600.0;;76.700961;;
Open;Dividends;HKD;2022-11-30;2800;2800;;;280.0;;35.793781800000005;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;243.6;;36.285110601599996;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;56.4;;8.4009861984;;
Open;Dividends;HKD;2021-05-26;215;215;;;2605.0;;333.010005675;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;12.22;;1.5621429057000003;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;52.78;;6.747127869300001;;
Open;Dividends;HKD;2021-06-04;1883;1883;;;1600.0;;204.535896;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;8.8;;1.1249474280000002;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;311.2;;39.782231772;;
Open;Dividends;HKD;2021-09-03;215;215;;;1109.72;;141.86098406820003;;
Open;Dividends;HKD;2021-09-03;215;215;;;30.28;;3.8708418318000004;;
Open;Dividends;HKD;2021-09-03;215;215;;;9637.06;;1231.9529386911001;;
Open;Dividends;HKD;2021-09-03;215;215;;;262.94;;33.6129178089;;
Open;Dividends;HKD;2021-09-29;1883;1883;;;550.0;;70.30921425000001;;
Open;Dividends;HKD;2021-09-29;2828;2828;;;396.0;;50.622634260000005;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;234.9;;30.028426231500003;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;35.1;;4.4870062185;;
Open;Trades;HKD;2020-09-24;1883;1883;Stocks;2.45;-24500.0;10000.0;-3131.9559075;-45.2615;O
Open;Trades;HKD;2020-09-02;215;215;Stocks;1.21;-60500.0;50000.0;-7734.013567500001;-111.0335;O
Open;Transaction Fees;HKD;2020-09-02;215;215;Stocks;1.21;-62.6335;0.0;-8.0067494013225;;
Open;Transaction Fees;HKD;2020-09-24;1883;1883;Stocks;2.45;-25.6615;0.0;-3.2804361845025003;;
Open;Dividends;CNH;2020-07-31;3188;3188;;;380.0;;56.60238928;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;9.0;;1.1505144150000002;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;591.0;;75.550446585;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;2.64;;0.33748422840000003;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;327.36;;41.84804432160001;;

---

# [open_positions] START  
Symbol;Account;Asset Category;Description;Currency;Current Price;Cost Price;Quantity;Value;Value in USD;Unrealized Gain in USD;Weight;Unrealized Gain in USD
912797TC1 - United States Treasury B 12/24/26;IBKR;Treasury Bills;;USD;98.657648;97.133959459;740.0;73006.66;73006.66;;0.02770591604193119;1127.529520339999
912797TV9 - United States Treasury B 02/18/27;IBKR;Treasury Bills;;USD;98.075229;97.064044118;680.0;66691.16;66691.16;;0.025309193431106145;687.6057197599965
912797UE5 - United States Treasury B 04/15/27;IBKR;Treasury Bills;;USD;97.496813;96.90631;1000.0;97496.81;97496.81;;0.03699989058828492;590.5029999999982
912797UX3 - United States Treasury B 05/13/27;IBKR;Treasury Bills;;USD;97.220483;96.625351852;1080.0;104998.12;104998.12;;0.039846626284240586;642.7416398400078
912797VF1 - United States Treasury B 06/10/27;IBKR;Treasury Bills;;USD;96.939663;96.38057197;2640.0;255920.71;255920.71;;0.09712151884021841;1476.000319199975
912797VQ7 - United States Treasury B 07/08/27;IBKR;Treasury Bills;;USD;96.599778;96.420521978;1820.0;175811.6;175811.6;;0.06672023386356245;326.2459600400075
AMZN;IBKR;Stocks;AMAZON.COM INC;USD;262.65;207.8670155;300.0;78795.0;78795.0;;0.02990258223734613;16434.89534999999
AMZN 18SEP26 300 C;IBKR;Equity and Index Options;;USD;0.785;8.01939888;-1.0;-78.5;-78.5;;-2.9790630187596562e-05;7.2343988800000005
BABA;IBKR;Stocks;ALIBABA GROUP HOLDING-SP ADR;USD;123.81;111.16845686;500.0;61905.0;61905.0;;0.023492853016091275;6320.771569999998
BHP;IBKR;Stocks;BHP GROUP LTD-SPON ADR;USD;86.78;35.102226747;1200.0;104136.0;104136.0;;0.03951945306007077;62013.327903599995
BHP 18DEC26 95 C;IBKR;Equity and Index Options;;USD;3.7331;4.0005117;-4.0;-1493.24;-1493.24;;-0.0005666823009086203;1.0696467999999992
BRK B;IBKR;Stocks;BERKSHIRE HATHAWAY INC-CL B;USD;504.03;469.0869395;100.0;50403.0;50403.0;;0.019127861571279357;3494.3060499999947
BRK B 18DEC26 510 P;IBKR;Equity and Index Options;;USD;18.1279;9.1593754;-1.0;-1812.79;-1812.79;;-0.0006879510381881933;-8.9685246
BRK B 18DEC26 545 C;IBKR;Equity and Index Options;;USD;6.85;18.66917949;-1.0;-685.0;-685.0;;-0.00025995645450323116;11.819179490000002
CPRT;IBKR;Stocks;COPART INC;USD;31.61;27.171926655;1100.0;34771.0;34771.0;;0.013195541429973505;4881.880679499999
CPRT 09/18/2026 25.00 P;Schwab;Equity and Index Options;SALESFORCE;USD;0.275;0.34332;-5.0;-137.5;-137.5;99.6875;-5.21810401375099e-05;0.34159999999999996
CPRT 09/18/2026 27.50 P;Schwab;Equity and Index Options;SALESFORCE;USD;0.675;0.99332;-10.0;-675.0;-675.0;219.37499999999997;-0.0002561614697659577;3.1831999999999994
CPRT 18SEP26 25 P;IBKR;Equity and Index Options;;USD;0.125;0.63450071;-5.0;-62.5;-62.5;;-2.3718654607959048e-05;2.54750355
CPRT 18SEP26 32.5 C;IBKR;Equity and Index Options;;USD;1.325;0.28450792;-6.0;-795.0;-795.0;;-0.0003017012866132391;-6.24295248
CRM 09/18/2026 160.00 P;Schwab;Equity and Index Options;SALESFORCE;USD;2.085;6.94315;-2.0;-417.0;-417.0;;-0.00015825086354430277;9.7163
DNOW;IBKR;Stocks;DNOW INC;USD;16.43;26.81106924;25.0;410.75;410.75;;0.00015587899808350685;-259.526731
FAST;IBKR;Stocks;FASTENAL CO;USD;51.02;42.17242457;100.0;5102.0;5102.0;;0.0019362012129569128;884.7575430000006
FCX;IBKR;Stocks;FREEPORT-MCMORAN INC;USD;66.49;11.2079574;500.0;33245.0;33245.0;;0.012616426759065576;27641.021299999997
CASH - Schwab;Schwab;Forex;;USD;1.0;;-1.0;;72675.14;0.0;0.0275801047079211;
Forex;IBKR;Forex;;USD;0.0;;-1.0;;56323.839751292;0.0;0.021374811220078862;
GEHC 17JUN27 57.5 P;IBKR;Equity and Index Options;;USD;3.1965;5.33945409;-1.0;-319.65;-319.65;;-0.00012130668712694574;2.1429540900000004
GEHC 17JUN27 65 C;IBKR;Equity and Index Options;;USD;16.7326;8.8804028;1.0;1673.26;1673.26;;0.0006349996161490168;7.852197200000001
GEHC 17JUN27 80 C;IBKR;Equity and Index Options;;USD;7.4516;3.91948334;-1.0;-745.16;-745.16;;-0.00028278708268266817;-3.53211666
GOOG;IBKR;Stocks;ALPHABET INC-CL C;USD;343.54;93.120965215;200.0;68708.0;68708.0;;0.0260745811328584;50083.806957
GOOG 18DEC26 410 C;IBKR;Equity and Index Options;;USD;7.775;32.055824165;-2.0;-1555.0;-1555.0;;-0.0005901201266460211;48.56164833
GOOG 21JAN28 250 C;IBKR;Equity and Index Options;;USD;122.2549;132.321833;2.0;24450.98;24450.98;;0.009279109591137832;-20.133865999999983
GOOG 21JAN28 250 P;IBKR;Equity and Index Options;;USD;13.4783;13.467856515;-2.0;-2695.66;-2695.66;;-0.0010229988556878541;-0.020886970000002947
H78;IBKR;Stocks;HONGKONG LAND HOLDINGS LTD;USD;8.67;4.208;1000.0;8670.0;8670.0;;0.003290251767216079;4462.0
ICE;IBKR;Stocks;INTERCONTINENTAL EXCHANGE IN;USD;154.73;152.14704663;300.0;46419.0;46419.0;;0.017615939651949616;774.8860109999953
IVV;IBKR;Stocks;ISHARES CORE S&P 500 ETF;USD;780.04;402.65670378;100.0;78004.0;78004.0;;0.029602398944627798;37738.329622
IVV;Schwab;Stocks;ISHARES CORE S&P 500 ETF;USD;776.98;531.9820000000001;10.0;7769.8;7769.8;;0.0029486272411667234;2449.9799999999996
META;IBKR;Stocks;META PLATFORMS INC-CLASS A;USD;589.85;230.10913779;200.0;117970.0;117970.0;;0.04476943494561486;71948.17244200001
META 15JAN27 460 P;IBKR;Equity and Index Options;;USD;10.3;19.71258137;-1.0;-1030.0;-1030.0;;-0.0003908834279391651;9.412581369999998
META 15JAN27 520 P;IBKR;Equity and Index Options;;USD;24.6;62.7404795;1.0;2460.0;2460.0;;0.000933566245369268;-38.1404795
META 15JAN27 640 P;IBKR;Equity and Index Options;;USD;83.6081;77.2419796;-1.0;-8360.81;-8360.81;;-0.003172914634124321;-6.3661204
META 15JAN27 800 C;IBKR;Equity and Index Options;;USD;11.725;35.535755475;-2.0;-2345.0;-2345.0;;-0.0008899239208906235;47.62151095
MSFT;Schwab;Stocks;MICROSOFT CORP;USD;499.45;470.3566;100.0;49945.0;49945.0;;0.018954051270312234;2909.3399999999974
MSFT 09/18/2026 520.00 C;Schwab;Equity and Index Options;MICROSOFT CORP;USD;11.325;13.4931;-1.0;-1132.5;-1132.5;;-0.0004297820214962179;2.168100000000001
MSFT 15JAN27 420 P;IBKR;Equity and Index Options;;USD;9.6015;53.1437823;-2.0;-1920.3;-1920.3;;-0.00072875091909862;87.0845646
NBIS 18DEC26 140 P;IBKR;Equity and Index Options;;USD;8.4035;24.136075115;-2.0;-1680.7;-1680.7;;-0.0006378230847935483;31.465150230000003
NBIS 18DEC26 170 P;IBKR;Equity and Index Options;;USD;15.213;15.93624404;-1.0;-1521.3;-1521.3;;-0.0005773310280814095;0.7232440400000009
NBIS 18DEC26 190 P;IBKR;Equity and Index Options;;USD;21.3037;22.3868947;1.0;2130.37;2130.37;;0.0008084721634745234;-1.0831947
NOV;IBKR;Stocks;NOV INC;USD;21.14;62.158401437;300.0;6342.0;6342.0;;0.00240677932037882;-12305.5204311
NVDA;Moomoo-mom;Stocks;NVIDIA;USD;223.96;0.0;0.4717;105.641932;105.641932;;4.009095195560794e-05;105.64193200000001
NVDA;Moomoo-me;Stocks;NVIDIA;USD;223.96;0.0;1.4431;323.196676;323.196676;;0.00012265264525575115;323.196676
NVO;IBKR;Stocks;NOVO-NORDISK A/S-SPONS ADR;USD;45.89;58.2644644;200.0;9178.0;9178.0;;0.00348303699186957;-2474.8928800000003
PAYC;IBKR;Stocks;PAYCOM SOFTWARE INC;USD;218.13;215.9971074;200.0;43626.0;43626.0;;0.01655600041482914;426.5785199999982
PAYC 21AUG26 145 P;IBKR;Equity and Index Options;;USD;0.0001;3.657044055;-2.0;-0.02;-0.02;;-7.589969474546896e-09;7.31388811
PAYC 21AUG26 200 C;IBKR;Equity and Index Options;;USD;19.559;1.237093905;-2.0;-3911.8;-3911.8;;-0.0014845221295266273;-36.643812190000006
PAYC 21AUG26 200 P;IBKR;Equity and Index Options;;USD;1.2054;4.29295552;-2.0;-241.08;-241.08;;-9.148949204618828e-05;6.175111039999999
PDD;IBKR;Stocks;PDD HOLDINGS INC;USD;84.79;96.7605204;100.0;8479.0;8479.0;;0.003217767558734156;-1197.0520399999998
PDD 18DEC26 100 C;IBKR;Equity and Index Options;;USD;3.1817;12.173745;2.0;636.34;636.34;;0.00024149005877165857;-17.984090000000002
PDD 18DEC26 110 C;IBKR;Equity and Index Options;;USD;1.565;11.5805045;1.0;156.5;156.5;;5.939151113832945e-05;-10.0155045
PDD 18DEC26 120 C;IBKR;Equity and Index Options;;USD;0.8995;5.8261019;-2.0;-179.9;-179.9;;-6.827177542354932e-05;9.853203800000001
PDD 18DEC26 130 C;IBKR;Equity and Index Options;;USD;0.4115;6.3994626;-1.0;-41.15;-41.15;;-1.5616362193880235e-05;5.9879625999999995
PDD 18DEC26 90 P;IBKR;Equity and Index Options;;USD;9.7947;8.553596777;-4.0;-3917.88;-3917.88;;-0.0014868294802468894;-4.964412892000006
PDD 20NOV26 100 C;IBKR;Equity and Index Options;;USD;2.201;10.7569825;1.0;220.1;220.1;;8.352761406738857e-05;-8.555982499999999
PDD 20NOV26 130 C;IBKR;Equity and Index Options;;USD;0.2567;3.12292012;-1.0;-25.67;-25.67;;-9.74172582058094e-06;2.86622012
SKHY 18DEC26 115 P;IBKR;Equity and Index Options;;USD;7.1003;16.440827835;-2.0;-1420.06;-1420.06;;-0.0005389106026012532;18.68105567
SKHY 18DEC26 130 P;IBKR;Equity and Index Options;;USD;11.95;24.4323006;1.0;1195.0;1195.0;;0.00045350067610417695;-12.482300600000002
SMCI 18DEC26 24 P;IBKR;Equity and Index Options;;USD;1.055;2.96300312;-4.0;-422.0;-422.0;;-0.00016014835591293949;7.632012480000001
SMCI 18DEC26 27 P;IBKR;Equity and Index Options;;USD;1.735;4.3169028;2.0;347.0;347.0;;0.00013168597038338862;-5.1638056
SOUHY;IBKR;Stocks;SOUTH32 - ADR;USD;16.75;7.17;300.0;5025.0;5025.0;;0.0019069798304799073;2874.0
TSM;IBKR;Stocks;TAIWAN SEMICONDUCTOR-SP ADR;USD;426.35;181.60807492;300.0;127905.0;127905.0;;0.048539752282096026;73422.57752400001
UNH;IBKR;Stocks;UNITEDHEALTH GROUP INC;USD;401.73;322.13551065;200.0;80346.0;80346.0;;0.030491184370097242;15918.89787
UNH 15JAN27 360 P;IBKR;Equity and Index Options;;USD;14.6484;12.18938278;-1.0;-1464.84;-1464.84;;-0.0005559045442547636;-2.45901722
UNH 17JUN27 360 P;IBKR;Equity and Index Options;;USD;24.6595;21.4891912;-1.0;-2465.95;-2465.95;;-0.0009358242612879457;-3.170308800000001
UNH 18DEC26 440 C;IBKR;Equity and Index Options;;USD;14.4512;26.01244859;-1.0;-1445.12;-1445.12;;-0.0005484208343528604;11.561248590000002
9CI;IBKR;Stocks;CAPITALAND INVESTMENT LTD/SI;SGD;2.74;2.323057932;1200.0;3288.0;2522.1791855760002;;0.0009571631513929695;383.79657135058875
BS6;IBKR;Stocks;YANGZIJIANG SHIPBUILDING;SGD;4.62;0.171019587;7000.0;32340.0;24807.56534718;;0.009414433186146176;23889.258078749215
BS6;Moomoo-me;Stocks;YZJ Shipbuilding;SGD;4.2;4.015;200.0;840.0;652.6800000000001;;0.0002476910638323634;28.382186699000076
C38U;IBKR;Stocks;CAPITALAND INTEGRATED COMMER;SGD;2.43;1.364696005;222.0;539.46;413.81228207142004;;0.00015704112945573337;181.41394126368337
CGP1;IBKR;Stocks;NOBLE GROUP LTD;SGD;0.0;85.85;50.0;0.0;0.0;;0.0;-3292.7172001475
D05;IBKR;Stocks;DBS GROUP HOLDINGS LTD;SGD;75.53;18.538145455;220.0;16616.6;12746.363337908198;;0.004837225432310344;9617.885414163708
F34;IBKR;Stocks;WILMAR INTERNATIONAL LTD;SGD;3.75;3.040549529;9000.0;33750.0;25889.15678625;;0.009824895486469806;4897.886526879441
CASH - DBS;DBS;Forex;;SGD;0.763247271;;-0.777;;1043.95389;18.47775410253007;0.00039617890789672434;
CASH - Moomoo-me;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;75899.04609;1343.3964121419413;0.028803572148516393;
CASH - Moomoo-mom;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;7040.27268;124.6112770323598;0.002671772736684323;
CASH - OCBC;OCBC;Forex;;SGD;0.763247271;;-0.777;;6154.438290000001;108.93220328133066;0.00233559993770413;
CASH - OCBC Joint;OCBC Joint;Forex;;SGD;0.763247271;;-0.777;;30062.78268;532.1046373023601;0.011408780143056855;
CASH - POSB;POSB;Forex;;SGD;0.763247271;;-0.777;;6713.0158200000005;118.81890263214063;0.0025475792577975198;
CASH - UOB;UOB;Forex;;SGD;0.763247271;;-0.777;;6898.25262;122.09755322574074;0.0026178763406756573;
Forex;IBKR;Forex;;SGD;0.767086127;;-0.78173;;14236.943562;266.695645;0.005402898352321347;
Fullerton SGD Cash Fund;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0;
Fullerton SGD Cash Fund;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0;
HMN;IBKR;Stocks;CAPITALAND ASCOTT TRUST;SGD;0.855;0.527017662;68.0;58.14;44.598387423780004;;1.692501995802532e-05;17.108167693896096
O39;IBKR;Stocks;OVERSEA-CHINESE BANKING CORP;SGD;31.79;8.7231;800.0;25432.0;19508.534381864;;0.007403459022574816;14155.439186317039
P40U;Moomoo-mom;Stocks;StarhillGbl Reit;SGD;0.53;0.555;1200.0;636.0;494.172;;0.0001875375197587894;-23.012583810000024
P40U;Moomoo-me;Stocks;StarhillGbl Reit;SGD;0.53;0.5448;4200.0;2226.0;1729.602;;0.000656381319155763;-47.68207365431976
Z74;Moomoo-mom;Stocks;Singtel;SGD;4.3;4.29;100.0;430.0;334.11;;0.00012679423505704314;0.7670861269999837
Z74;Moomoo-me;Stocks;Singtel;SGD;4.3;4.88;100.0;430.0;334.11;;0.00012679423505704314;-44.49099536600001
Z77;Moomoo-me;Stocks;Singtel 10;SGD;4.29;4.28;120.0;514.8;399.9996;;0.0001517992376915484;0.9205033523999804
1299;IBKR;Stocks;AIA GROUP LTD;HKD;70.4;55.302399867;600.0;42240.0;5399.7476544;;0.00204919599335761;1158.0004389948285
1398;IBKR;Stocks;IND & COMM BK OF CHINA - H;HKD;7.165;5.051969413;6000.0;42990.0;5495.62385565;;0.0020855808653987605;1620.7147664529411
14;IBKR;Stocks;HYSAN DEVELOPMENT CO;HKD;17.2;28.5525695;1000.0;17200.0;2198.760882;;0.0008344263988103904;-1451.2549841154828
16;IBKR;Stocks;SUN HUNG KAI PROPERTIES;HKD;116.5;88.851861372;4500.0;524250.0;67017.46467375;;0.02543302555676437;15904.791019716162
1810;IBKR;Stocks;XIAOMI CORP-CLASS B;HKD;25.62;38.85377485;600.0;15372.0;1965.0786208200002;;0.0007457443373554257;-1015.043248652631
1883;IBKR;Stocks;CITIC TELECOM INTERNATIONAL;HKD;2.435;2.45452615;10000.0;24350.0;3112.78066725;;0.0011812955122693608;-24.961241160502386
1972;IBKR;Stocks;SWIRE PROPERTIES LTD;HKD;23.66;19.720971466;5600.0;132496.0;16937.61754776;;0.006427800007952412;2819.8545569993967
200;IBKR;Stocks;MELCO INTERNATIONAL DEVELOP.;HKD;3.325;11.460065577;4000.0;13300.0;1700.2046355000002;;0.0006452250641964066;-4159.78231702613
215;IBKR;Stocks;HUTCHISON TELECOMM HONG KONG;HKD;1.13;1.21222067;50000.0;56500.0;7222.673827500001;;0.0027409936937666897;-525.5337002553232
2318;IBKR;Stocks;PING AN INSURANCE GROUP CO-H;HKD;54.6;86.4593701;500.0;27300.0;3489.8937255;;0.001324409342297887;-2036.370252937222
27;IBKR;Stocks;GALAXY ENTERTAINMENT GROUP L;HKD;34.74;51.5945905;1000.0;34740.0;4440.985641900001;;0.0016853472729461026;-2154.605481019118
2800;IBKR;Stocks;TRACKER FUND OF HONG KONG-B;HKD;25.6;21.769917967;1500.0;38400.0;4908.861504;;0.0018629054485069182;734.4274315998343
2828;IBKR;Stocks;HANGSENG CHINA ENT INDEX;HKD;85.66;92.44076825;400.0;34264.0;4380.136212840001;;0.0016622550074906523;-346.72762739552593
2888;IBKR;Stocks;STANDARD CHARTERED PLC;HKD;234.2;55.6;28.0;6557.6;838.2903697560001;;0.00031812991586273353;639.276942948
3188;IBKR;Stocks;CHINAAMC ETF SERIES - CH;HKD;55.82;26.37987258;1000.0;55820.0;7135.7460717;;0.002708004743116046;3763.476775127418
3690;IBKR;Stocks;MEITUAN-CLASS B;HKD;87.2;231.697888617;60.0;5232.0;668.8323799200001;;0.00025382086735906763;-1108.312691939486
386;IBKR;Stocks;CHINA PETROLEUM & CHEMICAL-H;HKD;4.235;5.815174;2000.0;8470.0;1082.76189945;;0.00041090648825139575;-404.00288115737993
390;IBKR;Stocks;CHINA RAILWAY GROUP LTD-H;HKD;3.4;3.92724184;10000.0;34000.0;4346.387790000001;;0.0016494475325321673;-673.9992634568044
3918;IBKR;Stocks;NAGACORP LTD;HKD;3.465;7.594013956;2036.0;7054.74;901.8422293419001;;0.0003422477495781171;-1074.6664216631627
3968;IBKR;Stocks;CHINA MERCHANTS BANK - H;HKD;47.96;39.7833745;500.0;23980.0;3065.4817413;;0.0011633456420623931;522.6291946559213
5;IBKR;Stocks;HSBC HOLDINGS PLC;HKD;160.8;49.092166333;2400.0;385920.0;49334.0581152;;0.018722199757494527;34272.392773948224
66;IBKR;Stocks;MTR CORP;HKD;33.24;40.07708;500.0;16620.0;2124.6166197000002;;0.0008062887644319005;-437.0088386949
700;IBKR;Stocks;TENCENT HOLDINGS LTD;HKD;440.0;452.856419642;706.0;310640.0;39710.6442084;;0.015070128867817422;-1160.3106959030615
808;IBKR;Stocks;PROSPERITY REIT;HKD;1.48;2.339531045;20000.0;29600.0;3783.9140760000005;;0.0014359896165574162;-2197.561905361142
857;IBKR;Stocks;PETROCHINA CO LTD-H;HKD;9.455;7.7972334;2000.0;18910.0;2417.35862085;;0.000917383907064214;423.8409711123421
939;IBKR;Stocks;CHINA CONSTRUCTION BANK-H;HKD;8.74;6.39177626;4000.0;34960.0;4469.1093276;;0.00169602016874484;1200.7401166734276
9618;IBKR;Stocks;JD.COM INC-CLASS A;HKD;110.2;285.565311947;19.0;2093.8;267.66078690300003;;0.00010157686010634857;-425.93845182598994
CASH - Citic;Citic;Forex;;HKD;0.128244528;;-0.1276;;56493.2093;-285.35623200400005;0.02143908670530944;
CASH - StanChart;StanChart;Forex;;HKD;0.128244528;;-0.1276;;1614.1055479999998;-8.153105177440239;0.0006125505919008393;
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;127600.0;-644.5280000000057;0.04842400524760919;
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;1276.0;-6.445279999999912;0.00048424005247609187;
CASH - StanChart Payroll;StanChart Payroll;Forex;;HKD;0.128244528;;-0.1276;;14971.866888;-75.62529327264019;0.005681800632844971;
Forex;IBKR;Forex;;HKD;0.127834935;;-0.12744;;1640.522712;-5.083959;0.0006225758653190443;
BARCl;IBKR;Stocks;;GBP;5.205;3.5479;1000.0;5205.0;6904.2089140200005;;0.0026201367451653187;2198.0719676124004
Forex;IBKR;Forex;;GBP;1.326457044;;-1.3534;;2799.739873;55.736122;0.00106249700863709;
IMB 18DEC26 30 C;IBKR;Equity and Index Options;;GBP;0.225;0.8986;-1.0;-225.0;-298.4528349;;-0.00011326239532414922;0.8935014648384
IMBl;IBKR;Stocks;IMPERIAL BRANDS PLC;GBP;26.09;29.95515;1000.0;26090.0;34607.26427796;;0.013133403973364679;-5126.9554436166
NWG 18SEP26 5 P;IBKR;Equity and Index Options;;GBP;0.0;0.5086;-1.0;0.0;0.0;;0.0;0.6746360525784001
RRl;IBKR;Stocks;ROLLS-ROYCE HOLDINGS PLC;GBP;15.41;11.090386;100.0;1541.0;2044.0703048040002;;0.0007757215608645065;572.9782417661016
Forex;IBKR;Forex;;EUR;1.159715451;;-1.1571;;2279.807754;-5.153163;0.0008651835630347658;
MC;IBKR;Stocks;LVMH MOET HENNESSY LOUIS VUI;EUR;458.4;619.0851265;10.0;4584.0;5316.135627384;;0.0020174653567197882;-1863.490239479396
MC 18DEC26 440 P;IBKR;Equity and Index Options;;EUR;23.6692;37.9386;-1.0;-2366.92;-2744.9536952809203;;-0.0010417057378113441;16.5484436564994
RI;IBKR;Stocks;PERNOD-RICARD SA;EUR;69.16;83.7114;100.0;6916.0;8020.592059116;;0.003043802444824183;-1687.5483413681402
RMS;IBKR;Stocks;HERMES INTL;EUR;1576.0;2195.362096667;3.0;4728.0;5483.134652328;;0.002080841231799991;-2154.851379805427
RMS 17DEC27 1520 P;IBKR;Equity and Index Options;;EUR;175.0413;193.044;5.0;8752.07;10149.91080723357;;0.003851875659813821;-104.39004674858853
RMS 17DEC27 1680 P;IBKR;Equity and Index Options;;EUR;257.5462;214.886;-5.0;-12877.31;-14934.01537431681;;-0.00566743604117393;-247.36846541375104
RMS 17DEC27 1920 P;IBKR;Equity and Index Options;;EUR;415.5096;388.7186;-1.0;-41550.96;-48187.290315882965;;-0.018287003127934045;-31.069936647741
RMS 18DEC26 1520 P;IBKR;Equity and Index Options;;EUR;82.0596;130.209;5.0;4102.98;4758.28930114398;;0.0018057635273372942;-279.198015681897
RMS 18DEC26 1600 P;IBKR;Equity and Index Options;;EUR;119.869;110.436;-5.0;-5993.45;-6950.69656979595;;-0.0026377787395794536;-54.69797924641496
SAP;IBKR;Stocks;SAP SE;EUR;180.14;210.1117;100.0;18014.0;20891.114134314;;0.007928145928435923;-3475.8643582736736
SAP 18DEC26 190 P;IBKR;Equity and Index Options;;EUR;20.54;31.7783;-2.0;-4108.0;-4764.111072708;;-0.0018079728807602292;26.066460305946606
Forex;IBKR;Forex;;CNH;0.148953656;;-0.14827;;965.2377;-4.4506;0.0003663062339340927;

---

# [pivot_summary] START  
# PORTFOLIO PIVOT SUMMARY

**Report Date :** 2026-08-16  
**Filter      :** Open Positions | Forex Balances | Cash Balances  
**Generated   :** 2026-08-16 10:23:20  

---

## Equity and Index Options

### Schwab

```
            Amount  Quantity  Value in USD
Underlying                                
CPRT         812.5      15.0        -812.5
CRM          417.0       2.0        -417.0
MSFT        1132.5       1.0       -1132.5
```

> **SUBTOTAL [Schwab]**  Amount: `       2,362.00`  Quantity: `          18.00`  Value in USD: `      -2,362.00`

### IBKR

```
              Amount  Quantity  Value in USD
Underlying                                  
AMZN          -78.50       1.0        -78.50
BHP         -1493.24       4.0      -1493.24
BRK         -2497.79       2.0      -2497.79
CPRT         -857.50      11.0       -857.50
GEHC          608.45       1.0        608.45
GOOG        20200.32       2.0      20200.32
IMB          -225.00       1.0       -298.45
MC          -2366.92       1.0      -2744.95
META        -9275.81       3.0      -9275.81
MSFT        -1920.30       2.0      -1920.30
NBIS        -1071.63       2.0      -1071.63
NWG             0.00       1.0          0.00
PAYC        -4152.90       6.0      -4152.90
PDD         -3151.66       4.0      -3151.66
RMS        -47566.67       1.0     -55163.80
SAP         -4108.00       2.0      -4764.11
SKHY         -225.06       1.0       -225.06
SMCI          -75.00       2.0        -75.00
UNH         -5375.91       3.0      -5375.91
```

> **SUBTOTAL [IBKR]**  Amount: `     -63,633.12`  Quantity: `          50.00`  Value in USD: `     -72,337.85`

> **SUBTOTAL [Equity and Index Options]**  Amount: `     -61,271.12`  Quantity: `          68.00`  Value in USD: `     -74,699.85`

---

## Forex

### Citic

```
               Amount  Quantity  Value in USD
Underlying                                   
Forex       442736.75      0.13      56493.21
```

> **SUBTOTAL [Citic]**  Amount: `     442,736.75`  Quantity: `           0.13`  Value in USD: `      56,493.21`

### DBS

```
             Amount  Quantity  Value in USD
Underlying                                 
Forex       1343.57      0.78       1043.95
```

> **SUBTOTAL [DBS]**  Amount: `       1,343.57`  Quantity: `           0.78`  Value in USD: `       1,043.95`

### Moomoo-me

```
              Amount  Quantity  Value in USD
Underlying                                  
Forex       97682.17      1.55      75899.05
```

> **SUBTOTAL [Moomoo-me]**  Amount: `      97,682.17`  Quantity: `           1.55`  Value in USD: `      75,899.05`

### Moomoo-mom

```
             Amount  Quantity  Value in USD
Underlying                                 
Forex       9060.84      1.55       7040.27
```

> **SUBTOTAL [Moomoo-mom]**  Amount: `       9,060.84`  Quantity: `           1.55`  Value in USD: `       7,040.27`

### OCBC

```
             Amount  Quantity  Value in USD
Underlying                                 
Forex       7920.77      0.78       6154.44
```

> **SUBTOTAL [OCBC]**  Amount: `       7,920.77`  Quantity: `           0.78`  Value in USD: `       6,154.44`

### OCBC Joint

```
              Amount  Quantity  Value in USD
Underlying                                  
Forex       38690.84      0.78      30062.78
```

> **SUBTOTAL [OCBC Joint]**  Amount: `      38,690.84`  Quantity: `           0.78`  Value in USD: `      30,062.78`

### POSB

```
             Amount  Quantity  Value in USD
Underlying                                 
Forex       8639.66      0.78       6713.02
```

> **SUBTOTAL [POSB]**  Amount: `       8,639.66`  Quantity: `           0.78`  Value in USD: `       6,713.02`

### Schwab

```
              Amount  Quantity  Value in USD
Underlying                                  
Forex       72675.14       1.0      72675.14
```

> **SUBTOTAL [Schwab]**  Amount: `      72,675.14`  Quantity: `           1.00`  Value in USD: `      72,675.14`

### StanChart

```
              Amount  Quantity  Value in USD
Underlying                                  
Forex       12649.73      0.13       1614.11
```

> **SUBTOTAL [StanChart]**  Amount: `      12,649.73`  Quantity: `           0.13`  Value in USD: `       1,614.11`

### StanChart AsiaMiles TD

```
               Amount  Quantity  Value in USD
Underlying                                   
Forex       1010000.0      0.26      128876.0
```

> **SUBTOTAL [StanChart AsiaMiles TD]**  Amount: `   1,010,000.00`  Quantity: `           0.26`  Value in USD: `     128,876.00`

### StanChart Payroll

```
               Amount  Quantity  Value in USD
Underlying                                   
Forex       117334.38      0.13      14971.87
```

> **SUBTOTAL [StanChart Payroll]**  Amount: `     117,334.38`  Quantity: `           0.13`  Value in USD: `      14,971.87`

### IBKR

```
              Amount  Quantity  Value in USD
Underlying                                  
Forex       97957.79      4.57      78246.09
```

> **SUBTOTAL [IBKR]**  Amount: `      97,957.79`  Quantity: `           4.57`  Value in USD: `      78,246.09`

### UOB

```
             Amount  Quantity  Value in USD
Underlying                                 
Forex       8878.06      0.78       6898.25
```

> **SUBTOTAL [UOB]**  Amount: `       8,878.06`  Quantity: `           0.78`  Value in USD: `       6,898.25`

> **SUBTOTAL [Forex]**  Amount: `   1,925,569.70`  Quantity: `          13.20`  Value in USD: `     486,688.18`

---

## Stocks

### Moomoo-me

```
            Amount  Quantity  Value in USD
Underlying                                
BS6          840.0   -200.00        652.68
NVDA         323.2     -1.44        323.20
P40U        2226.0  -4200.00       1729.60
Z74          430.0   -100.00        334.11
Z77          514.8   -120.00        400.00
```

> **SUBTOTAL [Moomoo-me]**  Amount: `       4,334.00`  Quantity: `      -4,621.44`  Value in USD: `       3,439.59`

### Moomoo-mom

```
            Amount  Quantity  Value in USD
Underlying                                
NVDA        105.64     -0.47        105.64
P40U        636.00  -1200.00        494.17
Z74         430.00   -100.00        334.11
```

> **SUBTOTAL [Moomoo-mom]**  Amount: `       1,171.64`  Quantity: `      -1,300.47`  Value in USD: `         933.92`

### Schwab

```
             Amount  Quantity  Value in USD
Underlying                                 
IVV          7769.8     -10.0        7769.8
MSFT        49945.0    -100.0       49945.0
```

> **SUBTOTAL [Schwab]**  Amount: `      57,714.80`  Quantity: `        -110.00`  Value in USD: `      57,714.80`

### IBKR

```
               Amount  Quantity  Value in USD
Underlying                                   
1299         42240.00    -600.0       5399.75
1398         42990.00   -6000.0       5495.62
14           17200.00   -1000.0       2198.76
16          524250.00   -4500.0      67017.46
1810         15372.00    -600.0       1965.08
1883         24350.00  -10000.0       3112.78
1972        132496.00   -5600.0      16937.62
200          13300.00   -4000.0       1700.20
215          56500.00  -50000.0       7222.67
2318         27300.00    -500.0       3489.89
27           34740.00   -1000.0       4440.99
2800         38400.00   -1500.0       4908.86
2828         34264.00    -400.0       4380.14
2888          6557.60     -28.0        838.29
3188         55820.00   -1000.0       7135.75
3690          5232.00     -60.0        668.83
386           8470.00   -2000.0       1082.76
390          34000.00  -10000.0       4346.39
3918          7054.74   -2036.0        901.84
3968         23980.00    -500.0       3065.48
5           385920.00   -2400.0      49334.06
66           16620.00    -500.0       2124.62
700         310640.00    -706.0      39710.64
808          29600.00  -20000.0       3783.91
857          18910.00   -2000.0       2417.36
939          34960.00   -4000.0       4469.11
9618          2093.80     -19.0        267.66
9CI           3288.00   -1200.0       2522.18
AMZN         78795.00    -300.0      78795.00
BABA         61905.00    -500.0      61905.00
BARCl         5205.00   -1000.0       6904.21
BHP         104136.00   -1200.0     104136.00
BRK          50403.00    -100.0      50403.00
BS6          32340.00   -7000.0      24807.57
C38U           539.46    -222.0        413.81
CGP              0.00     -50.0          0.00
CPRT         34771.00   -1100.0      34771.00
D05          16616.60    -220.0      12746.36
DNOW           410.75     -25.0        410.75
F34          33750.00   -9000.0      25889.16
FAST          5102.00    -100.0       5102.00
FCX          33245.00    -500.0      33245.00
GOOG         68708.00    -200.0      68708.00
H78           8670.00   -1000.0       8670.00
HMN             58.14     -68.0         44.60
ICE          46419.00    -300.0      46419.00
IMB          26090.00   -1000.0      34607.26
IVV          78004.00    -100.0      78004.00
MC            4584.00     -10.0       5316.14
META        117970.00    -200.0     117970.00
NOV           6342.00    -300.0       6342.00
NVO           9178.00    -200.0       9178.00
O39          25432.00    -800.0      19508.53
PAYC         43626.00    -200.0      43626.00
PDD           8479.00    -100.0       8479.00
RI            6916.00    -100.0       8020.59
RMS           4728.00      -3.0       5483.13
RRl           1541.00    -100.0       2044.07
SAP          18014.00    -100.0      20891.11
SOUHY         5025.00    -300.0       5025.00
TSM         127905.00    -300.0     127905.00
UNH          80346.00    -200.0      80346.00
```

> **SUBTOTAL [IBKR]**  Amount: `   3,091,802.09`  Quantity: `    -159,047.00`  Value in USD: `   1,387,055.01`

> **SUBTOTAL [Stocks]**  Amount: `   3,155,022.53`  Quantity: `    -165,078.91`  Value in USD: `   1,449,143.33`

---

## Treasury Bills

### IBKR

```
               Amount  Quantity  Value in USD
Underlying                                   
912797TC1    73006.66  -74000.0      73006.66
912797TV9    66691.16  -68000.0      66691.16
912797UE5    97496.81 -100000.0      97496.81
912797UX3   104998.12 -108000.0     104998.12
912797VF1   255920.71 -264000.0     255920.71
912797VQ7   175811.60 -182000.0     175811.60
```

> **SUBTOTAL [IBKR]**  Amount: `     773,925.06`  Quantity: `    -796,000.00`  Value in USD: `     773,925.06`

> **SUBTOTAL [Treasury Bills]**  Amount: `     773,925.06`  Quantity: `    -796,000.00`  Value in USD: `     773,925.06`

---

> ### GRAND TOTAL  
> Amount: `   5,793,246.17`  Quantity: `    -960,997.72`  Value in USD: `   2,635,056.71`

---

