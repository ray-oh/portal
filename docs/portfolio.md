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

- Current refresh timestamp: **2026-11-25 21:45:01**.


# [transaction_details] START  
Status;Table;Currency;Settle Date;Ticker;Symbol;Asset Category;Transaction Price;Amount;Quantity;Value in USD;Comm/Fee;Code
Open;Trades;EUR;2026-11-20;MC;MC;Stocks;410.0;-82000.0;200.0;-90224.705124;0.0;"A;O"
Open;Trades;EUR;2026-11-20;MC;MC;Stocks;430.0;43000.0;-100.0;47312.955126;0.0;"C;Ex"
Open;Trades;EUR;2026-03-04;RI;RI;Stocks;93.0;-9300.0;100.0;-10232.8019226;0.0;"A;O"
Open;Trades;EUR;2026-11-17;RMS;RMS;Stocks;1600.0;-32000.0;20.0;-35209.641024;0.0;"A;O"
Open;Trades;EUR;2026-02-16;SAP;SAP;Stocks;220.0;-22000.0;100.0;-24206.628204;0.0;"A;O"
Open;Trades;HKD;2026-04-01;1810;1810;Stocks;31.8;-6360.0;200.0;-811.53107736;-25.18126;O
Open;Trades;HKD;2026-04-10;1810;1810;Stocks;31.0;-6200.0;200.0;-791.1152012000001;-25.1767;O
Open;Trades;HKD;2026-04-10;700;700;Stocks;502.0;-50200.0;100.0;-6405.4811452;-92.5907;O
Open;Trades;USD;2026-02-23;CPRT;CPRT;Stocks;45.0;-13500.0;300.0;-13500.0;0.0;"A;O"
Open;Trades;USD;2026-11-24;GLD;GLD;Stocks;390.0;-78000.0;200.0;-78000.0;0.0;"A;O"
Open;Trades;USD;2026-08-10;GOOG;GOOG;Stocks;352.84;70568.0;-200.0;70568.0;-2.4338153;"C;P"
Open;Trades;USD;2026-02-23;NVO;NVO;Stocks;65.0;-13000.0;200.0;-13000.0;0.0;"A;O"
Open;Trades;USD;2026-03-20;PDD;PDD;Stocks;100.0;-10000.0;100.0;-10000.0;0.0;"A;O"
Open;Trades;USD;2026-01-02;TSM;TSM;Stocks;317.21;63442.0;-200.0;63442.0;-0.7795145;"C;P"
Open;Trades;USD;2026-01-02;TSM;TSM;Stocks;318.5;63700.0;-200.0;63700.0;-0.1395145;C
Open;Trades;USD;2026-01-29;UNH;UNH;Stocks;350.0;-70000.0;200.0;-70000.0;0.0;"A;O"
Open;Trades;EUR;2026-03-30;RMS;RMS 18DEC26 1760 C;Equity and Index Options;130.095;6504.75;-5.0;7157.1847640895;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1760 C;Equity and Index Options;181.5;-9075.0;5.0;-9985.23413415;-5.7;C
Open;Trades;EUR;2026-03-30;RMS;RMS 17DEC27 1760 C;Equity and Index Options;226.53;11326.5;-5.0;12462.562470573;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 17DEC27 1760 C;Equity and Index Options;284.1;-14205.0;5.0;-15629.77971081;-5.7;C
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 440 C;Equity and Index Options;26.975;-2697.5;1.0;-2968.062708195;-1.14;O
Open;Trades;EUR;2026-11-20;MC;MC 20NOV26 440 C;Equity and Index Options;0.0;0.0;-1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 450 C;Equity and Index Options;22.175;2217.5;-1.0;2439.918092835;-1.14;O
Open;Trades;EUR;2026-11-20;MC;MC 20NOV26 450 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2026-02-18;SAP;SAP 17APR26 194 C;Equity and Index Options;2.2;220.0;-1.0;242.06628204;-1.17;O
Open;Trades;EUR;2026-04-17;SAP;SAP 17APR26 194 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2026-02-18;SAP;SAP 18SEP26 175 C;Equity and Index Options;15.79;-1579.0;1.0;-1737.375724278;-1.17;O
Open;Trades;EUR;2026-08-06;SAP;SAP 18SEP26 175 C;Equity and Index Options;7.5;750.0;-1.0;825.2259615;-1.17;C
Open;Trades;EUR;2026-02-18;SAP;SAP 18SEP26 195 C;Equity and Index Options;8.52;1704.0;-2.0;1874.913384528;-2.34;O
Open;Trades;EUR;2026-08-06;SAP;SAP 18SEP26 195 C;Equity and Index Options;2.2;-440.0;2.0;-484.13256408;-2.34;C
Open;Trades;EUR;2026-03-10;RMS;RMS 19JUN26 2040 P;Equity and Index Options;180.57;-18057.0;1.0;-19868.140249074;-1.14;C
Open;Trades;EUR;2026-02-18;RMS;RMS 18DEC26 1480 P;Equity and Index Options;30.1;3010.0;-1.0;3311.90685882;-1.14;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1480 P;Equity and Index Options;73.05;-7305.0;1.0;-8037.70086501;-1.14;C
Open;Trades;EUR;2026-03-10;RMS;RMS 18DEC26 1960 P;Equity and Index Options;198.32;19832.0;-1.0;21821.175024624;-1.14;O
Open;Trades;EUR;2026-03-24;RMS;RMS 18DEC26 1960 P;Equity and Index Options;359.93;-35993.0;1.0;-39603.144043026;-1.14;C
Open;Trades;EUR;2026-03-24;RMS;RMS 17DEC27 1920 P;Equity and Index Options;388.73;38873.0;-1.0;42772.011735186;-1.14;O
Open;Trades;EUR;2026-03-30;RMS;RMS 18DEC26 1520 P;Equity and Index Options;130.095;-6504.75;5.0;-7157.1847640895;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1600 P;Equity and Index Options;110.55;5527.5;-5.0;6081.915336255;-5.7;O
Open;Trades;EUR;2026-11-17;RMS;RMS 18DEC26 1600 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-09-04;RMS;RMS 18JUN27 1360 P;Equity and Index Options;108.1375;10813.75;-10.0;11898.3829882275;-11.4;O
Open;Trades;EUR;2026-09-04;RMS;RMS 18JUN27 1440 P;Equity and Index Options;146.275;-7313.75;5.0;-8047.3285012275;-5.7;O
Open;Trades;EUR;2026-03-30;RMS;RMS 17DEC27 1520 P;Equity and Index Options;192.93;-9646.5;5.0;-10614.056316813;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 17DEC27 1680 P;Equity and Index Options;215.0;10750.0;-5.0;11828.2387815;-5.7;O
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 410 P;Equity and Index Options;12.5;2500.0;-2.0;2750.753205;-2.28;O
Open;Trades;EUR;2026-11-20;MC;MC 20NOV26 410 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 430 P;Equity and Index Options;20.2;-2020.0;1.0;-2222.60858964;-1.14;O
Open;Trades;EUR;2026-11-20;MC;MC 20NOV26 430 P;Equity and Index Options;0.0;0.0;-1.0;0.0;0.0;"C;Ex"
Open;Trades;EUR;2026-03-17;MC;MC 18DEC26 440 P;Equity and Index Options;37.95;3795.0;-1.0;4175.64336519;-1.14;O
Open;Trades;EUR;2026-03-04;RI;RI 20MAR26 93 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-02-16;SAP;SAP 20MAR26 220 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-03-10;SAP;SAP 20MAR26 240 P;Equity and Index Options;71.23;-7123.0;1.0;-7837.4460316859995;-1.17;C
Open;Trades;EUR;2026-03-10;SAP;SAP 18DEC26 190 P;Equity and Index Options;31.79;6358.0;-2.0;6995.715550956;-2.34;O
Open;Trades;GBP;2026-07-28;IMB;IMB 18DEC26 30 C;Equity and Index Options;0.9;900.0;-1.0;1195.702767;-1.4;O
Open;Trades;GBP;2026-02-27;IMB;IMB 20MAR26 34.5 C;Equity and Index Options;0.23;230.0;-1.0;305.56848490000004;-1.4;O
Open;Trades;GBP;2026-03-20;IMB;IMB 20MAR26 34.5 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;GBP;2026-10-23;RR.;RR. 15JAN27 11 P;Equity and Index Options;0.465;465.0;-1.0;617.7797629500001;-1.4;O
Open;Trades;HKD;2026-05-14;16;16 29JUN26 150 C;Equity and Index Options;3.86;15440.0;-4.0;1970.1320494400002;-42.88;O
Open;Trades;HKD;2026-06-29;16;16 29JUN26 150 C;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-08-03;AMZN;AMZN 18SEP26 300 C;Equity and Index Options;8.03;803.0;-1.0;803.0;-1.0601118;O
Open;Trades;USD;2026-09-18;AMZN;AMZN 18SEP26 300 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-11-12;BABA;BABA 18DEC26 90 P;Equity and Index Options;3.45;690.0;-2.0;690.0;-0.880814;O
Open;Trades;USD;2026-11-17;BABA;BABA 15JAN27 90 C;Equity and Index Options;6.0;-600.0;1.0;-600.0;-0.5473;O
Open;Trades;USD;2026-11-17;BABA;BABA 15JAN27 100 C;Equity and Index Options;2.73;273.0;-1.0;273.0;-0.5529238;O
Open;Trades;USD;2026-11-17;BABA;BABA 15JAN27 110 C;Equity and Index Options;1.07;107.0;-1.0;107.0;-0.5495042;O
Open;Trades;USD;2026-11-17;BABA;BABA 15JAN27 75 P;Equity and Index Options;2.39;239.0;-1.0;239.0;-0.5522234;O
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
Open;Trades;USD;2026-09-01;CPRT;CPRT 18SEP26 32.5 C;Equity and Index Options;1.121666667;-673.0;6.0;-673.0;-1.04576;"C;P"
Open;Trades;USD;2026-07-22;CPRT;CPRT 18SEP26 25 P;Equity and Index Options;0.65;325.0;-5.0;325.0;-7.749645;O
Open;Trades;USD;2026-09-18;CPRT;CPRT 18SEP26 25 P;Equity and Index Options;0.0;0.0;5.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-09-01;CPRT;CPRT 20NOV26 35 C;Equity and Index Options;1.225;735.0;-6.0;735.0;-1.080641;"O;P"
Open;Trades;USD;2026-11-20;CPRT;CPRT 20NOV26 35 C;Equity and Index Options;0.0;0.0;6.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-07-17;GEHC;GEHC 17JUN27 65 C;Equity and Index Options;8.87;-887.0;1.0;-887.0;-1.04028;O
Open;Trades;USD;2026-07-17;GEHC;GEHC 17JUN27 80 C;Equity and Index Options;3.93;393.0;-1.0;393.0;-1.0516658;O
Open;Trades;USD;2026-07-17;GEHC;GEHC 17JUN27 57.5 P;Equity and Index Options;5.35;535.0;-1.0;535.0;-1.054591;O
Open;Trades;USD;2026-04-01;GLD;GLD 17APR26 425 P;Equity and Index Options;6.42;-642.0;1.0;-642.0;-0.69795;O
Open;Trades;USD;2026-04-17;GLD;GLD 17APR26 425 P;Equity and Index Options;0.0;0.0;-1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-04-01;GLD;GLD 17APR26 430 P;Equity and Index Options;7.94;794.0;-1.0;794.0;-0.70124;O
Open;Trades;USD;2026-04-17;GLD;GLD 17APR26 430 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-09-04;GLD;GLD 15JAN27 390 P;Equity and Index Options;13.75;2750.0;-2.0;2750.0;-0.45783;O
Open;Trades;USD;2026-11-24;GLD;GLD 15JAN27 390 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2026-09-04;GLD;GLD 15JAN27 405 P;Equity and Index Options;20.5;-2050.0;1.0;-2050.0;-0.5473;O
Open;Trades;USD;2026-03-10;GOOG;GOOG 17APR26 330 C;Equity and Index Options;4.3;860.0;-2.0;860.0;-1.40248;O
Open;Trades;USD;2026-04-14;GOOG;GOOG 17APR26 330 C;Equity and Index Options;3.62;-724.0;2.0;-724.0;-1.4009;"C;P"
Open;Trades;USD;2026-04-14;GOOG;GOOG 22MAY26 350 C;Equity and Index Options;7.88;1576.0;-2.0;1576.0;-1.4399456;"O;P"
Open;Trades;USD;2026-05-04;GOOG;GOOG 22MAY26 350 C;Equity and Index Options;32.43;-6486.0;2.0;-6486.0;-0.6965;C
Open;Trades;USD;2026-05-04;GOOG;GOOG 18DEC26 410 C;Equity and Index Options;32.06;6412.0;-2.0;6412.0;-0.8351672;O
Open;Trades;USD;2026-08-10;GOOG;GOOG 21JAN28 250 C;Equity and Index Options;132.315;-26463.0;2.0;-26463.0;-1.3666;"O;P"
Open;Trades;USD;2026-08-10;GOOG;GOOG 21JAN28 250 P;Equity and Index Options;13.475;2695.0;-2.0;2695.0;-1.428697;"O;P"
Open;Trades;USD;2026-03-06;ICE;ICE 17APR26 180 C;Equity and Index Options;1.26;378.0;-3.0;378.0;-2.10372;"O;P"
Open;Trades;USD;2026-04-17;ICE;ICE 17APR26 180 C;Equity and Index Options;0.0;0.0;3.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-09-04;LULU;LULU 15JAN27 85 P;Equity and Index Options;4.6;920.0;-2.0;920.0;-0.892132;O
Open;Trades;USD;2026-09-14;MCD;MCD 18DEC26 275 C;Equity and Index Options;6.5;-650.0;1.0;-650.0;-0.5473;O
Open;Trades;USD;2026-09-14;MCD;MCD 18DEC26 245 P;Equity and Index Options;6.5;650.0;-1.0;650.0;-0.56398;O
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
Open;Trades;USD;2026-11-13;MU;MU 15JAN27 760 C;Equity and Index Options;86.75;-8675.0;1.0;-8675.0;-0.5473;O
Open;Trades;USD;2026-11-13;MU;MU 15JAN27 800 C;Equity and Index Options;68.95;6895.0;-1.0;6895.0;-0.689337;O
Open;Trades;USD;2026-11-13;MU;MU 15JAN27 700 P;Equity and Index Options;55.3;5530.0;-1.0;5530.0;-0.661218;O
Open;Trades;USD;2026-08-07;NBIS;NBIS 18DEC26 140 P;Equity and Index Options;24.14;4828.0;-2.0;4828.0;-0.7849768;O
Open;Trades;USD;2026-08-07;NBIS;NBIS 18DEC26 170 P;Equity and Index Options;39.78;-3978.0;1.0;-3978.0;-0.68947;O
Open;Trades;USD;2026-08-14;NBIS;NBIS 18DEC26 170 P;Equity and Index Options;15.94;3188.0;-2.0;3188.0;-0.7511928;"C;O"
Open;Trades;USD;2026-10-16;NBIS;NBIS 18DEC26 170 P;Equity and Index Options;24.4;-2440.0;1.0;-2440.0;-0.5473;C
Open;Trades;USD;2026-08-14;NBIS;NBIS 18DEC26 190 P;Equity and Index Options;22.38;-2238.0;1.0;-2238.0;-0.68947;O
Open;Trades;USD;2026-10-16;NBIS;NBIS 18DEC26 190 P;Equity and Index Options;36.9;3690.0;-1.0;3690.0;-0.623314;C
Open;Trades;USD;2026-11-13;NVDA;NVDA 15JAN27 165 C;Equity and Index Options;12.43;-1243.0;1.0;-1243.0;-0.5473;O
Open;Trades;USD;2026-11-13;NVDA;NVDA 15JAN27 185 C;Equity and Index Options;5.85;585.0;-1.0;585.0;-0.559351;O
Open;Trades;USD;2026-11-13;NVDA;NVDA 15JAN27 145 P;Equity and Index Options;7.6;760.0;-1.0;760.0;-0.562956;O
Open;Trades;USD;2026-02-18;NVO;NVO 20MAR26 60 C;Equity and Index Options;0.29;58.0;-2.0;58.0;-1.40408;O
Open;Trades;USD;2026-03-20;NVO;NVO 20MAR26 60 C;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-02-23;NVO;NVO 20MAR26 65 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2026-02-18;PDD;PDD 20MAR26 100 P;Equity and Index Options;3.25;325.0;-1.0;325.0;-1.05204;O
Open;Trades;USD;2026-03-20;PDD;PDD 20MAR26 100 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2026-05-12;PDD;PDD 20NOV26 100 C;Equity and Index Options;10.75;-1075.0;1.0;-1075.0;-0.69825;O
Open;Trades;USD;2026-11-20;PDD;PDD 20NOV26 100 C;Equity and Index Options;0.0;0.0;-1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-05-12;PDD;PDD 20NOV26 130 C;Equity and Index Options;3.13;313.0;-1.0;313.0;-0.7079878;O
Open;Trades;USD;2026-11-20;PDD;PDD 20NOV26 130 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 100 C;Equity and Index Options;12.32;-1232.0;1.0;-1232.0;-1.05075;O
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 100 C;Equity and Index Options;12.01;-1201.0;1.0;-1201.0;-0.69825;O
Open;Trades;USD;2026-11-19;PDD;PDD 18DEC26 100 C;Equity and Index Options;0.31;62.0;-2.0;62.0;-0.8318772;C
Open;Trades;USD;2026-03-19;PDD;PDD 18DEC26 110 C;Equity and Index Options;11.57;-1157.0;1.0;-1157.0;-1.05045;O
Open;Trades;USD;2026-11-19;PDD;PDD 18DEC26 110 C;Equity and Index Options;0.06;6.0;-1.0;6.0;-0.7654236;C
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 120 C;Equity and Index Options;5.94;594.0;-1.0;594.0;-1.0662764;O
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 120 C;Equity and Index Options;5.73;573.0;-1.0;573.0;-0.7133438;O
Open;Trades;USD;2026-03-19;PDD;PDD 18DEC26 130 C;Equity and Index Options;6.41;641.0;-1.0;641.0;-1.05374;O
Open;Trades;USD;2026-11-19;PDD;PDD 18DEC26 130 C;Equity and Index Options;0.02;-2.0;1.0;-2.0;-0.7833;C
Open;Trades;USD;2026-03-19;PDD;PDD 18DEC26 90 P;Equity and Index Options;9.77;977.0;-1.0;977.0;-1.05374;O
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 90 P;Equity and Index Options;8.08;808.0;-1.0;808.0;-1.0706848;O
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 90 P;Equity and Index Options;8.12;812.0;-1.0;812.0;-0.7182672;O
Open;Trades;USD;2026-05-12;PDD;PDD 18DEC26 90 P;Equity and Index Options;8.28;828.0;-1.0;828.0;-0.7185968;O
Open;Trades;USD;2026-10-23;PLTR;PLTR 18DEC26 180 P;Equity and Index Options;10.2;2040.0;-2.0;2040.0;-0.436624;O
Open;Trades;USD;2026-10-23;PLTR;PLTR 18DEC26 195 P;Equity and Index Options;16.65;-1665.0;1.0;-1665.0;-0.5473;O
Open;Trades;USD;2026-08-07;SKHY;SKHY 18DEC26 115 P;Equity and Index Options;16.44;3288.0;-2.0;3288.0;0.1655672;O
Open;Trades;USD;2026-08-07;SKHY;SKHY 18DEC26 130 P;Equity and Index Options;24.43;-2443.0;1.0;-2443.0;-0.23006;O
Open;Trades;USD;2026-11-13;SKHY;SKHY 15JAN27 125 P;Equity and Index Options;11.4;1140.0;-1.0;1140.0;-0.806784;O
Open;Trades;USD;2026-08-07;SMCI;SMCI 18DEC26 24 P;Equity and Index Options;2.97;1188.0;-4.0;1188.0;-2.7987528;"O;P"
Open;Trades;USD;2026-08-07;SMCI;SMCI 18DEC26 27 P;Equity and Index Options;4.31;-862.0;2.0;-862.0;-1.38056;"O;P"
Open;Trades;USD;2026-03-20;TSM;TSM 20MAR26 230 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-11-17;TSM;TSM 19MAR27 360 C;Equity and Index Options;38.05;-3805.0;1.0;-3805.0;-0.5473;O
Open;Trades;USD;2026-11-17;TSM;TSM 19MAR27 400 C;Equity and Index Options;21.2;2120.0;-1.0;2120.0;-0.590972;O
Open;Trades;USD;2026-11-17;TSM;TSM 19MAR27 310 P;Equity and Index Options;17.85;1785.0;-1.0;1785.0;-0.584071;O
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
Open;Trades;USD;2026-04-17;912797TV9;912797TV9 3.64107797%;Treasury Bills;97.0566889;-65998.55;68000.0;-65998.55;-5.0;O
Open;Trades;USD;2026-06-12;912797UE5;912797UE5 3.8394375%;Treasury Bills;96.9013111;-96901.31;100000.0;-96901.31;-5.0;O
Open;Trades;USD;2026-06-12;912797UX3;912797UX3 3.815%;Treasury Bills;96.61841;-104347.88;108000.0;-104347.88;-7.5;O
Open;Trades;USD;2026-06-22;912797VF1;912797VF1 4.043%;Treasury Bills;96.212;-31749.96;33000.0;-31749.96;-12.5;"O;P"
Open;Trades;USD;2026-06-23;912797VF1;912797VF1 4.03054553%;Treasury Bills;96.268675;-84716.43;88000.0;-84716.43;-5.0;O
Open;Trades;USD;2026-07-10;912797VF1;912797VF1 3.986%;Treasury Bills;96.470857091;-137953.32;143000.0;-137953.32;-7.5;"O;P"
Open;Trades;USD;2026-08-06;912797VQ7;912797VQ7 4.05724169%;Treasury Bills;96.4099167;-96409.92;100000.0;-96409.92;-5.0;O
Open;Trades;USD;2026-08-06;912797VQ7;912797VQ7 4.011%;Treasury Bills;96.41821;-79062.93;82000.0;-79062.93;-7.5;O
Open;Trades;USD;2026-08-31;912797VR5;912797VR5 4.1558258%;Treasury Bills;96.2942056;-96294.21;100000.0;-96294.21;-5.0;O
Open;Transaction Fees;EUR;2026-03-04;RI;RI;Stocks;93.0;-37.2;0.0;-40.9312076904;;
Open;Transaction Fees;EUR;2026-11-17;RMS;RMS;Stocks;1600.0;-128.0;0.0;-140.838564096;;
Open;Transaction Fees;EUR;2026-11-20;MC;MC;Stocks;410.0;-164.0;0.0;-180.449410248;;
Open;Transaction Fees;HKD;2026-04-01;1810;1810;Stocks;31.8;-7.18126;0.0;-0.9163232177047601;;
Open;Transaction Fees;HKD;2026-04-10;1810;1810;Stocks;31.0;-7.1767;0.0;-0.9157413652342001;;
Open;Transaction Fees;HKD;2026-04-10;700;700;Stocks;502.0;-52.4307;0.0;-6.690116738638201;;
Open;Withholding Tax;EUR;2026-02-18;RMS;RMS;;;-3.72;;-4.09312076904;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-10.650916409759999;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;9.68;;10.650916409759999;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-10.650916409759999;;
Open;Withholding Tax;EUR;2026-04-30;MC;MC;;;-18.75;;-20.6306490375;;
Open;Withholding Tax;EUR;2026-05-08;SAP;SAP;;;-61.32;;-67.47047461224;;
Open;Withholding Tax;EUR;2026-07-24;RI;RI;;;-58.75;;-64.6427003175;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.55666189882;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;90.57;;11.55666189882;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.55666189882;;
Open;Withholding Tax;HKD;2026-01-26;939;939;;;-81.86;;-10.44527264036;;
Open;Withholding Tax;HKD;2026-02-09;3968;3968;;;-33.22;;-4.23884628772;;
Open;Withholding Tax;HKD;2026-06-16;1398;1398;;;-116.65;;-14.884449712900002;;
Open;Withholding Tax;HKD;2026-06-26;386;386;;;-25.78;;-3.2895080462800004;;
Open;Withholding Tax;HKD;2026-07-15;2318;2318;;;-100.56;;-12.831378166560002;;
Open;Withholding Tax;HKD;2026-07-31;857;857;;;-22.27;;-2.84163476302;;
Open;Withholding Tax;HKD;2026-08-13;390;390;;;-98.9;;-12.619563451400001;;
Open;Withholding Tax;HKD;2026-08-21;3968;3968;;;-57.66;;-7.35737137116;;
Open;Withholding Tax;HKD;2026-08-21;939;939;;;-93.42;;-11.920319692920001;;
Open;Withholding Tax;HKD;2026-10-14;386;386;;;-24.46;;-3.1210770679600004;;
Open;Withholding Tax;HKD;2026-10-14;390;390;;;-0.35;;-0.044659729100000004;;
Open;Withholding Tax;HKD;2026-10-16;2318;2318;;;-56.67;;-7.231048137420001;;
Open;Withholding Tax;HKD;2026-10-26;857;857;;;-36.57;;-4.666303694820001;;
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
Open;Withholding Tax;USD;2026-03-26;META;META;;;-31.5;;-31.5;;
Open;Withholding Tax;USD;2026-03-27;NOV;NOV;;;-8.1;;-8.1;;
Open;Withholding Tax;USD;2026-03-31;ICE;ICE;;;-18.56;;-18.56;;
Open;Withholding Tax;USD;2026-03-31;ICE;ICE;;;-28.24;;-28.24;;
Open;Withholding Tax;USD;2026-04-08;NVO;NVO;;;-65.74;;-65.74;;
Open;Withholding Tax;USD;2026-04-09;TSM;TSM;;;-59.16;;-59.16;;
Open;Withholding Tax;USD;2026-05-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2026-05-26;FAST;FAST;;;-7.13;;-7.13;;
Open;Withholding Tax;USD;2026-05-26;FAST;FAST;;;-0.07;;-0.07;;
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
Open;Withholding Tax;USD;2026-08-25;FAST;FAST;;;-7.8;;-7.8;;
Open;Withholding Tax;USD;2026-08-25;NVO;NVO;;;-31.28;;-31.28;;
Open;Withholding Tax;USD;2026-09-14;GOOG;GOOG;;;-13.2;;-13.2;;
Open;Withholding Tax;USD;2026-09-18;IVV;IVV;;;-66.08;;-66.08;;
Open;Withholding Tax;USD;2026-09-22;UNH;UNH;;;-139.2;;-139.2;;
Open;Withholding Tax;USD;2026-09-25;NOV;NOV;;;-8.1;;-8.1;;
Open;Withholding Tax;USD;2026-09-28;META;META;;;-31.5;;-31.5;;
Open;Withholding Tax;USD;2026-09-30;ICE;ICE;;;-46.8;;-46.8;;
Open;Withholding Tax;USD;2026-10-08;TSM;TSM;;;-69.07;;-69.07;;
Open;Withholding Tax;USD;2026-11-02;FCX;FCX;;;-21.06;;-21.06;;
Open;Withholding Tax;USD;2026-11-02;FCX;FCX;;;-1.44;;-1.44;;
Open;Dividends;CNH;2026-07-31;3188;3188;;;548.9;;81.76066177839999;;
Open;Dividends;CNH;2026-07-31;3188;3188;;;1.1;;0.1638490216;;
Open;Dividends;EUR;2026-02-18;RMS;RMS;;;15.0;;16.50451923;;
Open;Dividends;EUR;2026-04-23;RMS;RMS;;;39.0;;42.911749998;;
Open;Dividends;EUR;2026-04-30;MC;MC;;;75.0;;82.52259615;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;232.5;;255.820048065;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;17.5;;19.255272435;;
Open;Dividends;EUR;2026-07-24;RI;RI;;;235.0;;258.57080127;;
Open;Dividends;GBP;2025-12-31;IMB;IMB;;;400.8;;532.486298904;;
Open;Dividends;GBP;2026-03-31;IMB;IMB;;;400.8;;532.486298904;;
Open;Dividends;GBP;2026-06-03;RR.;RR.;;;5.0;;6.64279315;;
Open;Dividends;GBP;2026-06-30;IMB;IMB;;;416.8;;553.7432369840001;;
Open;Dividends;GBP;2026-09-23;RR.;RR.;;;6.0;;7.971351780000001;;
Open;Dividends;GBP;2026-09-30;IMB;IMB;;;416.8;;553.7432369840001;;
Open;Dividends;HKD;2026-01-26;1398;1398;;;931.62;;118.87399092612002;;
Open;Dividends;HKD;2026-01-26;939;939;;;818.62;;104.45527838812;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;220.7;;28.161149178200002;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;333.34;;42.53392599484;;
Open;Dividends;HKD;2026-03-19;16;16;;;4410.0;;562.71258666;;
Open;Dividends;HKD;2026-03-27;14;14;;;810.0;;103.35537306;;
Open;Dividends;HKD;2026-04-23;808;808;;;1142.0;;145.718316092;;
Open;Dividends;HKD;2026-04-30;5;5;;;8148.56;;1039.7499490145601;;
Open;Dividends;HKD;2026-04-30;5;5;;;306.5;;39.109162769;;
Open;Dividends;HKD;2026-05-07;1972;1972;;;4480.0;;571.6445324800001;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;3.84;;0.48998102784;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;103.61;;13.22055580586;;
Open;Dividends;HKD;2026-05-29;215;215;;;2605.0;;332.39598373;;
Open;Dividends;HKD;2026-05-29;2800;2800;;;285.0;;36.36577941;;
Open;Dividends;HKD;2026-06-01;700;700;;;3741.8;;477.45078384680005;;
Open;Dividends;HKD;2026-06-12;1299;1299;;;864.48;;110.30697889248002;;
Open;Dividends;HKD;2026-06-12;27;27;;;800.0;;102.07938080000001;;
Open;Dividends;HKD;2026-06-16;1398;1398;;;1166.46;;148.83939315996003;;
Open;Dividends;HKD;2026-06-22;66;66;;;439.66;;56.10027570316001;;
Open;Dividends;HKD;2026-06-22;66;66;;;5.34;;0.6813798668400001;;
Open;Dividends;HKD;2026-06-25;1883;1883;;;1300.0;;165.87899380000002;;
Open;Dividends;HKD;2026-06-26;386;386;;;257.8;;32.8950804628;;
Open;Dividends;HKD;2026-06-26;5;5;;;1661.92;;212.05970567392004;;
Open;Dividends;HKD;2026-06-26;5;5;;;217.73;;27.78217947698;;
Open;Dividends;HKD;2026-06-30;2828;2828;;;280.0;;35.727783280000004;;
Open;Dividends;HKD;2026-07-15;2318;2318;;;1005.64;;128.31888563464;;
Open;Dividends;HKD;2026-07-31;857;857;;;222.66;;28.41124366116;;
Open;Dividends;HKD;2026-07-31;857;857;;;316.75;;40.417054835500004;;
Open;Dividends;HKD;2026-08-07;3918;3918;;;172.04;;21.95217084104;;
Open;Dividends;HKD;2026-08-13;390;390;;;989.0;;126.195634514;;
Open;Dividends;HKD;2026-08-21;3968;3968;;;576.63;;73.57754168838001;;
Open;Dividends;HKD;2026-08-21;939;939;;;936.74;;119.52729896324001;;
Open;Dividends;HKD;2026-09-11;14;14;;;270.0;;34.45179102;;
Open;Dividends;HKD;2026-09-15;27;27;;;900.0;;114.8393034;;
Open;Dividends;HKD;2026-09-15;66;66;;;0.84;;0.10718334984000001;;
Open;Dividends;HKD;2026-09-15;66;66;;;209.16;;26.68865411016;;
Open;Dividends;HKD;2026-09-16;215;215;;;1140.0;;145.46311764;;
Open;Dividends;HKD;2026-09-18;808;808;;;1126.0;;143.67672847600002;;
Open;Dividends;HKD;2026-09-22;1299;1299;;;323.4;;41.2655896884;;
Open;Dividends;HKD;2026-09-25;5;5;;;1881.81;;240.11749947906;;
Open;Dividends;HKD;2026-09-28;3918;3918;;;154.74;;19.744704231240004;;
Open;Dividends;HKD;2026-09-29;2888;2888;;;31.98;;4.08062324748;;
Open;Dividends;HKD;2026-09-29;2888;2888;;;12.79;;1.63199410054;;
Open;Dividends;HKD;2026-09-30;1883;1883;;;500.0;;63.79961300000001;;
Open;Dividends;HKD;2026-09-30;2828;2828;;;260.0;;33.17579876;;
Open;Dividends;HKD;2026-10-08;1972;1972;;;2072.0;;264.38559627200004;;
Open;Dividends;HKD;2026-10-14;386;386;;;244.6;;31.210770679600003;;
Open;Dividends;HKD;2026-10-14;390;390;;;3.6;;0.45935721360000004;;
Open;Dividends;HKD;2026-10-14;390;390;;;659.69;;84.17593339994;;
Open;Dividends;HKD;2026-10-16;2318;2318;;;566.73;;72.31430935098001;;
Open;Dividends;HKD;2026-10-26;857;857;;;207.41;;26.46535546466;;
Open;Dividends;HKD;2026-10-26;857;857;;;370.0;;47.211713620000005;;
Open;Dividends;HKD;2026-11-19;16;16;;;13185.0;;1682.39579481;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.96;;0.7373626848;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.26;;0.19970239380000002;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.36;;0.2765110068;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.86;;0.6605540718;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;0.8;;0.6144689040000001;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;0.4;;0.30723445200000005;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;9.04;;6.943498615199999;;
Open;Dividends;SGD;2026-04-17;D05;D05;;;33.0;;25.34684229;;
Open;Dividends;SGD;2026-04-17;D05;D05;;;145.2;;111.52610607599999;;
Open;Dividends;SGD;2026-05-08;O39;O39;;;128.0;;98.31502464;;
Open;Dividends;SGD;2026-05-08;O39;O39;;;336.0;;258.07693968;;
Open;Dividends;SGD;2026-05-14;9CI;9CI;;;144.0;;110.60440272000001;;
Open;Dividends;SGD;2026-05-14;BS6;BS6;;;1400.0;;1075.320582;;
Open;Dividends;SGD;2026-05-14;F34;F34;;;900.0;;691.277517;;
Open;Dividends;SGD;2026-05-20;D05;D05;;;33.0;;25.34684229;;
Open;Dividends;SGD;2026-05-20;D05;D05;;;145.2;;111.52610607599999;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;0.6;;0.460851678;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;0.13;;0.09985119690000001;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;8.1;;6.221497653;;
Open;Dividends;SGD;2026-08-25;D05;D05;;;145.2;;111.52610607599999;;
Open;Dividends;SGD;2026-08-25;D05;D05;;;33.0;;25.34684229;;
Open;Dividends;SGD;2026-08-28;HMN;HMN;;;0.26;;0.19970239380000002;;
Open;Dividends;SGD;2026-08-28;HMN;HMN;;;1.22;;0.9370650786;;
Open;Dividends;SGD;2026-08-28;HMN;HMN;;;0.24;;0.1843406712;;
Open;Dividends;SGD;2026-08-28;O39;O39;;;376.0;;288.80038488;;
Open;Dividends;SGD;2026-09-02;F34;F34;;;450.0;;345.6387585;;
Open;Dividends;SGD;2026-09-25;C38U;C38U;;;0.42;;0.3225961746;;
Open;Dividends;SGD;2026-09-25;C38U;C38U;;;4.0;;3.07234452;;
Open;Dividends;SGD;2026-09-25;C38U;C38U;;;0.11;;0.0844894743;;
Open;Dividends;USD;2026-01-08;TSM;TSM;;;556.79;;556.79;;
Open;Dividends;USD;2026-02-02;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2026-02-26;FAST;FAST;;;24.0;;24.0;;
Open;Dividends;USD;2026-03-16;GOOG;GOOG;;;84.0;;84.0;;
Open;Dividends;USD;2026-03-17;UNH;UNH;;;442.0;;442.0;;
Open;Dividends;USD;2026-03-20;IVV;IVV;;;178.35;;178.35;;
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
Open;Dividends;USD;2026-08-25;FAST;FAST;;;26.0;;26.0;;
Open;Dividends;USD;2026-08-25;NVO;NVO;;;115.84;;115.84;;
Open;Dividends;USD;2026-09-14;GOOG;GOOG;;;44.0;;44.0;;
Open;Dividends;USD;2026-09-18;IVV;IVV;;;220.26;;220.26;;
Open;Dividends;USD;2026-09-22;UNH;UNH;;;464.0;;464.0;;
Open;Dividends;USD;2026-09-23;BHP;BHP;;;2360.83;;2360.83;;
Open;Dividends;USD;2026-09-25;NOV;NOV;;;27.0;;27.0;;
Open;Dividends;USD;2026-09-28;META;META;;;105.0;;105.0;;
Open;Dividends;USD;2026-09-30;ICE;ICE;;;156.0;;156.0;;
Open;Dividends;USD;2026-10-08;TSM;TSM;;;328.88;;328.88;;
Open;Dividends;USD;2026-10-14;H78;H78;;;80.0;;80.0;;
Open;Dividends;USD;2026-10-15;SOUHY;SOUHY;;;74.52;;74.52;;
Open;Dividends;USD;2026-11-02;FCX;FCX;;;70.2;;70.2;;
Open;Dividends;USD;2026-11-02;FCX;FCX;;;4.8;;4.8;;
Open;Trades;EUR;2025-10-22;MC;MC;Stocks;618.7;-6187.0;10.0;-6807.564031734;-3.851265;O
Open;Trades;EUR;2025-10-22;RMS;RMS;Stocks;2194.0;-6582.0;3.0;-7242.183038124;-4.08629;O
Open;Trades;GBP;2025-05-22;IMB;IMB;Stocks;30.75;-30750.0;1000.0;-40853.177872500004;-153.75;"A;O"
Open;Trades;GBP;2025-10-17;RR.;RR.;Stocks;11.025;-1102.5;100.0;-1464.735889575;-6.538600281;O
Open;Trades;HKD;2025-03-26;1810;1810;Stocks;53.35;-10670.0;200.0;-1361.4837414200001;-31.90695;O
Open;Trades;HKD;2025-06-30;2888;2888;Stocks;134.1;13410.0;-100.0;1711.10562066;-33.70307;C
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
Open;Trades;USD;2025-03-27;TSM;TSM;Stocks;185.0;-185000.0;1000.0;-185000.0;0.0;"A;O"
Open;Trades;USD;2025-07-03;TSM;TSM;Stocks;236.2;70860.0;-300.0;70860.0;-0.80717175;"C;P"
Open;Trades;EUR;2025-09-03;RMS;RMS 19JUN26 2040 P;Equity and Index Options;170.0;17000.0;-1.0;18705.121794;-1.14;O
Open;Trades;EUR;2025-07-15;RI;RI 20MAR26 93 P;Equity and Index Options;9.3;930.0;-1.0;1023.28019226;-1.14;O
Open;Trades;EUR;2025-03-26;SAP;SAP 16MAY25 235 P;Equity and Index Options;5.35;535.0;-1.0;588.6611858699999;-1.17;O
Open;Trades;EUR;2025-05-16;SAP;SAP 16MAY25 235 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2025-08-15;SAP;SAP 20MAR26 220 P;Equity and Index Options;9.9;990.0;-1.0;1089.29826918;-1.17;O
Open;Trades;EUR;2025-08-08;SAP;SAP 20MAR26 240 P;Equity and Index Options;13.8;1380.0;-1.0;1518.41576916;-1.17;O
Open;Trades;EUR;2025-03-21;SAP;SAP 21MAR25 220 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;GBP;2025-11-26;IMB;IMB 19DEC25 33 C;Equity and Index Options;0.31;310.0;-1.0;411.85317530000003;-1.4;O
Open;Trades;GBP;2025-12-19;IMB;IMB 19DEC25 33 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;GBP;2025-05-08;IMB;IMB 20JUN25 30.75 P;Equity and Index Options;0.95;950.0;-1.0;1262.1306985;-1.4;O
Open;Trades;GBP;2025-05-22;IMB;IMB 20JUN25 30.75 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
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
Open;Transaction Fees;EUR;2025-10-22;MC;MC;Stocks;618.7;-24.75;0.0;-27.2324567295;;
Open;Transaction Fees;EUR;2025-10-22;RMS;RMS;Stocks;2194.0;-26.33;0.0;-28.970932755059998;;
Open;Transaction Fees;GBP;2025-05-22;IMB;IMB;Stocks;30.75;-153.75;0.0;-204.26588936250002;;
Open;Transaction Fees;GBP;2025-05-22;IMB;IMB;Stocks;0.0;-1.5;0.0;-1.9928379450000002;;
Open;Transaction Fees;GBP;2025-10-17;RR.;RR.;Stocks;11.025;-5.5125;0.0;-7.3236794478750005;;
Open;Transaction Fees;HKD;2025-03-26;1810;1810;Stocks;53.35;-11.304095;0.0;-1.44239377263047;;
Open;Transaction Fees;HKD;2025-06-30;2888;2888;Stocks;134.1;-14.382185;0.0;-1.8351556741888102;;
Open;Withholding Tax;EUR;2025-12-04;MC;MC;;;-13.75;;-15.1291426275;;
Open;Withholding Tax;HKD;2025-01-24;1398;1398;;;-93.17;;-11.888419886420001;;
Open;Withholding Tax;HKD;2025-01-27;939;939;;;-85.25;;-10.877834016500001;;
Open;Withholding Tax;HKD;2025-05-28;939;939;;;-88.71;;-11.31932733846;;
Open;Withholding Tax;HKD;2025-06-27;386;386;;;-30.46;;-3.8866724239600003;;
Open;Withholding Tax;HKD;2025-06-30;2318;2318;;;-88.13;;-11.24531978738;;
Open;Withholding Tax;HKD;2025-07-24;857;857;;;-54.54;;-6.959261786040001;;
Open;Withholding Tax;HKD;2025-07-30;3968;3968;;;-94.82;;-12.09895860932;;
Open;Withholding Tax;HKD;2025-07-31;390;390;;;-194.8;;-24.856329224800003;;
Open;Withholding Tax;HKD;2025-08-22;1398;1398;;;-108.23;;-13.810064229980002;;
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
Open;Withholding Tax;USD;2025-12-15;GOOG;GOOG;;;-25.2;;-25.2;;
Open;Withholding Tax;USD;2025-12-19;IVV;IVV;;;-72.41;;-72.41;;
Open;Withholding Tax;USD;2025-12-19;NOV;NOV;;;-0.88;;-0.88;;
Open;Withholding Tax;USD;2025-12-19;NOV;NOV;;;-5.87;;-5.87;;
Open;Withholding Tax;USD;2025-12-23;META;META;;;-31.5;;-31.5;;
Open;Withholding Tax;USD;2025-12-31;ICE;ICE;;;-43.2;;-43.2;;
Open;Dividends;CNH;2025-07-31;3188;3188;;;530.0;;78.94543768;;
Open;Dividends;EUR;2025-12-04;MC;MC;;;55.0;;60.51657051;;
Open;Dividends;GBP;2025-09-30;IMB;IMB;;;400.8;;532.486298904;;
Open;Dividends;HKD;2025-01-24;1398;1398;;;931.74;;118.88930283324001;;
Open;Dividends;HKD;2025-01-27;939;939;;;852.52;;108.78089214952;;
Open;Dividends;HKD;2025-03-19;14;14;;;810.0;;103.35537306;;
Open;Dividends;HKD;2025-03-20;16;16;;;4275.0;;545.4866911500001;;
Open;Dividends;HKD;2025-04-23;808;808;;;1120.0;;142.91113312000002;;
Open;Dividends;HKD;2025-04-25;5;5;;;6698.42;;854.71320742292;;
Open;Dividends;HKD;2025-05-08;1972;1972;;;4256.0;;543.0623058560001;;
Open;Dividends;HKD;2025-05-19;2888;2888;;;277.72;;35.43685704472001;;
Open;Dividends;HKD;2025-05-28;939;939;;;887.1;;113.1932733846;;
Open;Dividends;HKD;2025-05-29;215;215;;;2605.0;;332.39598373;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;319.22;;40.732224923720004;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;10.78;;1.37551965628;;
Open;Dividends;HKD;2025-05-30;700;700;;;2727.0;;347.963089302;;
Open;Dividends;HKD;2025-06-12;1299;1299;;;785.88;;100.27767972888;;
Open;Dividends;HKD;2025-06-12;27;27;;;500.0;;63.79961300000001;;
Open;Dividends;HKD;2025-06-13;66;66;;;445.0;;56.781655570000005;;
Open;Dividends;HKD;2025-06-20;1883;1883;;;1280.0;;163.32700928000003;;
Open;Dividends;HKD;2025-06-20;5;5;;;1881.53;;240.08177169578002;;
Open;Dividends;HKD;2025-06-27;386;386;;;304.6;;38.8667242396;;
Open;Dividends;HKD;2025-06-30;2318;2318;;;881.33;;112.45702585058001;;
Open;Dividends;HKD;2025-07-24;857;857;;;545.4;;69.5926178604;;
Open;Dividends;HKD;2025-07-30;2828;2828;;;520.0;;66.35159752;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;133.58;;17.044704609080004;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;961.35;;122.66751591510001;;
Open;Dividends;HKD;2025-07-31;390;390;;;1948.0;;248.563292248;;
Open;Dividends;HKD;2025-08-22;1398;1398;;;1082.3;;138.1006422998;;
Open;Dividends;HKD;2025-09-09;14;14;;;270.0;;34.45179102;;
Open;Dividends;HKD;2025-09-16;66;66;;;36.12;;4.60888404312;;
Open;Dividends;HKD;2025-09-16;66;66;;;173.88;;22.18695341688;;
Open;Dividends;HKD;2025-09-18;808;808;;;1170.0;;149.29109442;;
Open;Dividends;HKD;2025-09-26;5;5;;;1866.43;;238.15502338318004;;
Open;Dividends;HKD;2025-09-30;2888;2888;;;26.82;;3.4222112413200003;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;1.24;;0.9524268012;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.24;;0.1843406712;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.45;;0.34563875850000003;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.49;;0.3763622037;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.46;;0.35331961980000004;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.06;;0.0460851678;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;5.33;;4.0938990729;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.24;;0.1843406712;;
Open;Dividends;SGD;2025-04-16;D05;D05;;;132.0;;101.38736916;;
Open;Dividends;SGD;2025-05-09;O39;O39;;;328.0;;251.93225064;;
Open;Dividends;SGD;2025-05-09;O39;O39;;;128.0;;98.31502464;;
Open;Dividends;SGD;2025-05-13;9CI;9CI;;;144.0;;110.60440272000001;;
Open;Dividends;SGD;2025-05-13;BS6;BS6;;;840.0;;645.1923492000001;;
Open;Dividends;SGD;2025-05-15;F34;F34;;;900.0;;691.277517;;
Open;Dividends;SGD;2025-05-27;D05;D05;;;132.0;;101.38736916;;
Open;Dividends;SGD;2025-05-27;D05;D05;;;33.0;;25.34684229;;
Open;Dividends;SGD;2025-08-21;O39;O39;;;328.0;;251.93225064;;
Open;Dividends;SGD;2025-08-25;D05;D05;;;132.0;;101.38736916;;
Open;Dividends;SGD;2025-08-25;D05;D05;;;33.0;;25.34684229;;
Open;Dividends;SGD;2025-08-28;F34;F34;;;360.0;;276.5110068;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;0.33;;0.25346842290000005;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;1.18;;0.9063416334;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;0.21;;0.1612980873;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;0.73;;0.5607028749;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;0.33;;0.25346842290000005;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;14.41;;11.0681211333;;
Open;Dividends;SGD;2025-11-24;D05;D05;;;132.0;;101.38736916;;
Open;Dividends;SGD;2025-11-24;D05;D05;;;33.0;;25.34684229;;
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
Open;Dividends;USD;2025-12-15;GOOG;GOOG;;;84.0;;84.0;;
Open;Dividends;USD;2025-12-19;IVV;IVV;;;241.36;;241.36;;
Open;Dividends;USD;2025-12-19;NOV;NOV;;;2.93;;2.93;;
Open;Dividends;USD;2025-12-19;NOV;NOV;;;19.58;;19.58;;
Open;Dividends;USD;2025-12-23;META;META;;;105.0;;105.0;;
Open;Dividends;USD;2025-12-31;ICE;ICE;;;144.0;;144.0;;
Open;Trades;HKD;2024-03-24;1299;1299;Stocks;55.2;-33120.0;600.0;-4226.086365120001;-61.43992;O
Open;Trades;HKD;2024-12-27;3968;3968;Stocks;39.7;-19850.0;500.0;-2532.8446361;-41.68725;O
Open;Trades;SGD;2024-09-09;C38U;C38UNR;Stocks;0.0;0.0;-0.36;0.0;0.0;C
Open;Trades;SGD;2024-05-16;F34;F34;Stocks;3.16;-3160.0;1000.0;-2427.1521708;-3.90576;O
Open;Trades;USD;2024-12-13;AMZN;AMZN;Stocks;200.0;120000.0;-600.0;120000.0;-3.4356;"A;C"
Open;Trades;USD;2024-12-13;AMZN;AMZN;Stocks;205.0;-61500.0;300.0;-61500.0;0.0;"Ex;O"
Open;Trades;USD;2024-05-03;BABA;BABA;Stocks;78.0;15600.0;-200.0;15600.0;-0.1248;"A;C"
Open;Trades;USD;2024-05-10;BABA;BABA;Stocks;80.0;8000.0;-100.0;8000.0;0.0;"A;C"
Open;Trades;USD;2024-09-19;BABA;BABA;Stocks;87.83;43915.0;-500.0;43915.0;-2.58112325;"C;P"
Open;Trades;EUR;2024-11-15;SAP;SAP 21MAR25 220 P;Equity and Index Options;12.7;1270.0;-1.0;1397.38262814;-1.17;O
Open;Trades;GBP;2024-06-03;RR.;RR. 20DEC24 4.8 P;Equity and Index Options;0.52;520.0;-1.0;690.8504876000001;-1.4;O
Open;Trades;GBP;2024-12-20;RR.;RR. 20DEC24 4.8 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
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
Open;Trades;USD;2024-01-19;MCD;MCD 19JAN24 280 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
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
Open;Transaction Fees;HKD;2024-03-24;1299;1299;Stocks;55.2;-34.94392;0.0;-4.45881714540592;;
Open;Transaction Fees;HKD;2024-12-27;3968;3968;Stocks;39.7;-20.565725;0.0;-2.6241705921288503;;
Open;Withholding Tax;HKD;2024-07-26;2318;2318;;;-82.34;;-10.506520268840001;;
Open;Withholding Tax;HKD;2024-07-26;386;386;;;-43.88;;-5.599054036880001;;
Open;Withholding Tax;HKD;2024-07-29;857;857;;;-50.57;;-6.452692858820001;;
Open;Withholding Tax;HKD;2024-08-02;939;939;;;-175.36;;-22.375800271360003;;
Open;Withholding Tax;HKD;2024-08-08;390;390;;;-230.1;;-29.3605819026;;
Open;Withholding Tax;HKD;2024-08-19;1398;1398;;;-201.43;;-25.70231209318;;
Open;Withholding Tax;HKD;2024-09-26;386;386;;;-31.86;;-4.06531134036;;
Open;Withholding Tax;HKD;2024-10-18;2318;2318;;;-51.02;;-6.510112510520001;;
Open;Withholding Tax;HKD;2024-10-28;857;857;;;-48.07;;-6.13369479382;;
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
Open;Dividends;HKD;2024-03-20;16;16;;;4275.0;;545.4866911500001;;
Open;Dividends;HKD;2024-03-22;14;14;;;810.0;;103.35537306;;
Open;Dividends;HKD;2024-04-17;808;808;;;1328.0;;169.45177212800002;;
Open;Dividends;HKD;2024-04-25;5;5;;;5119.61;;653.25827342186;;
Open;Dividends;HKD;2024-04-25;5;5;;;703.64;;89.78391938264001;;
Open;Dividends;HKD;2024-04-26;27;27;;;300.0;;38.2797678;;
Open;Dividends;HKD;2024-05-02;1972;1972;;;4032.0;;514.4800792320001;;
Open;Dividends;HKD;2024-05-17;2888;2888;;;210.1;;26.808597382600002;;
Open;Dividends;HKD;2024-05-27;215;215;;;2605.0;;332.39598373;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;41.55;;5.3017478403;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;183.45;;23.4080780097;;
Open;Dividends;HKD;2024-05-31;700;700;;;2060.4;;262.9054452504;;
Open;Dividends;HKD;2024-06-13;1883;1883;;;1930.0;;246.26650618000002;;
Open;Dividends;HKD;2024-06-14;1299;1299;;;714.42;;91.15943903892;;
Open;Dividends;HKD;2024-06-21;5;5;;;5808.32;;741.13713636032;;
Open;Dividends;HKD;2024-07-16;66;66;;;16.91;;2.1577029116600004;;
Open;Dividends;HKD;2024-07-16;66;66;;;428.09;;54.62395265834;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;741.05;;94.5574064273;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;823.39;;105.06392669614;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;-741.05;;-94.5574064273;;
Open;Dividends;HKD;2024-07-26;386;386;;;438.8;;55.990540368800005;;
Open;Dividends;HKD;2024-07-29;857;857;;;505.74;;64.53203255724;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;36.4;;4.6446118264;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;223.6;;28.5311869336;;
Open;Dividends;HKD;2024-08-02;939;939;;;1753.57;;223.75417473682;;
Open;Dividends;HKD;2024-08-08;390;390;;;2301.0;;293.605819026;;
Open;Dividends;HKD;2024-08-19;1398;1398;;;2014.29;;257.02184493954;;
Open;Dividends;HKD;2024-09-09;215;215;;;1140.0;;145.46311764;;
Open;Dividends;HKD;2024-09-16;808;808;;;1276.0;;162.81661237600002;;
Open;Dividends;HKD;2024-09-17;66;66;;;72.66;;9.271359761160001;;
Open;Dividends;HKD;2024-09-17;66;66;;;137.34;;17.524477698840002;;
Open;Dividends;HKD;2024-09-20;14;14;;;270.0;;34.45179102;;
Open;Dividends;HKD;2024-09-25;1299;1299;;;267.0;;34.068993342000006;;
Open;Dividends;HKD;2024-09-26;386;386;;;318.6;;40.6531134036;;
Open;Dividends;HKD;2024-09-27;1883;1883;;;600.0;;76.5595356;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;21.85;;2.7880430881000002;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;358.15;;45.6996627919;;
Open;Dividends;HKD;2024-09-27;5;5;;;1869.78;;238.58248079028002;;
Open;Dividends;HKD;2024-10-09;1972;1972;;;1904.0;;242.94892630400003;;
Open;Dividends;HKD;2024-10-10;2888;2888;;;89.69;;11.44437457994;;
Open;Dividends;HKD;2024-10-18;2318;2318;;;510.24;;65.10622907424;;
Open;Dividends;HKD;2024-10-25;27;27;;;500.0;;63.79961300000001;;
Open;Dividends;HKD;2024-10-28;857;857;;;480.66;;61.331843969160005;;
Open;Dividends;HKD;2024-11-21;16;16;;;12325.6;;1572.7370199856002;;
Open;Dividends;HKD;2024-11-21;16;16;;;274.4;;35.0132276144;;
Open;Dividends;HKD;2024-11-29;2800;2800;;;930.0;;118.66728018;;
Open;Dividends;HKD;2024-12-19;5;5;;;1865.55;;238.04273606430002;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;0.31;;0.2381067003;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;1.62;;1.2442995306000002;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;0.17;;0.13057464210000003;;
Open;Dividends;SGD;2024-03-28;C38U;C38U;;;10.08;;7.7423081904;;
Open;Dividends;SGD;2024-04-19;D05;D05;;;108.0;;82.95330204;;
Open;Dividends;SGD;2024-05-08;BS6;BS6;;;455.0;;349.47918915;;
Open;Dividends;SGD;2024-05-10;9CI;9CI;;;144.0;;110.60440272000001;;
Open;Dividends;SGD;2024-05-14;F34;F34;;;880.0;;675.9157944;;
Open;Dividends;SGD;2024-05-20;D05;D05;;;118.8;;91.248632244;;
Open;Dividends;SGD;2024-05-21;O39;O39;;;336.0;;258.07693968;;
Open;Dividends;SGD;2024-08-23;O39;O39;;;352.0;;270.36631776;;
Open;Dividends;SGD;2024-08-26;D05;D05;;;118.8;;91.248632244;;
Open;Dividends;SGD;2024-08-29;F34;F34;;;540.0;;414.7665102;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;1.03;;0.7911287139000001;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;0.39;;0.29955359070000004;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;0.32;;0.2457875616;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;0.11;;0.0844894743;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;0.09;;0.0691277517;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;9.84;;7.5579675192;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;3.83;;2.9417698779;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;0.07;;0.05376602910000001;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;0.09;;0.0691277517;;
Open;Dividends;SGD;2024-11-25;D05;D05;;;118.8;;91.248632244;;
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
Open;Trades;HKD;2023-04-25;3918;3918;Stocks;6.1031;4.96487185;-0.8135;0.6335138052491881;0.0;C
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
Open;Trades;USD;2023-09-15;GLD;GLD 15SEP23 156 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
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
Open;Withholding Tax;HKD;2023-06-30;386;386;;;-43.34;;-5.530150454840001;;
Open;Withholding Tax;HKD;2023-07-10;2318;2318;;;-85.01;;-10.847210202260001;;
Open;Withholding Tax;HKD;2023-08-04;939;939;;;-168.79;;-21.53747335654;;
Open;Withholding Tax;HKD;2023-08-10;390;390;;;-217.64;;-27.77069554664;;
Open;Withholding Tax;HKD;2023-08-18;1398;1398;;;-197.54;;-25.20595110404;;
Open;Withholding Tax;HKD;2023-09-28;386;386;;;-31.56;;-4.02703157256;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.36209740836;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;49.86;;6.36209740836;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.36209740836;;
Open;Withholding Tax;HKD;2023-10-30;857;857;;;-0.87;;-0.11101132662;;
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
Open;Dividends;HKD;2023-03-16;16;16;;;5571.25;;710.8871878525;;
Open;Dividends;HKD;2023-03-16;16;16;;;53.75;;6.858458397500001;;
Open;Dividends;HKD;2023-03-20;14;14;;;38.61;;4.92660611586;;
Open;Dividends;HKD;2023-03-20;14;14;;;1131.39;;144.36448830414002;;
Open;Dividends;HKD;2023-04-11;808;808;;;1500.0;;191.398839;;
Open;Dividends;HKD;2023-04-27;5;5;;;4330.56;;552.57610414656;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;3394.56;;433.14322861056;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;413.44;;52.75462399744001;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;1.1;;0.1403591486;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;139.46;;17.79498805796;;
Open;Dividends;HKD;2023-05-29;215;215;;;2605.0;;332.39598373;;
Open;Dividends;HKD;2023-05-31;2800;2800;;;150.0;;19.1398839;;
Open;Dividends;HKD;2023-06-05;700;700;;;1454.4;;185.58031429440004;;
Open;Dividends;HKD;2023-06-14;1883;1883;;;1850.0;;236.05856810000003;;
Open;Dividends;HKD;2023-06-23;5;5;;;1879.72;;239.85081709672002;;
Open;Dividends;HKD;2023-06-30;386;386;;;433.4;;55.301504548400004;;
Open;Dividends;HKD;2023-07-10;2318;2318;;;850.13;;108.47592999938001;;
Open;Dividends;HKD;2023-07-18;66;66;;;445.0;;56.781655570000005;;
Open;Dividends;HKD;2023-07-28;2828;2828;;;80.0;;10.207938080000002;;
Open;Dividends;HKD;2023-07-28;857;857;;;485.54;;61.95452819204001;;
Open;Dividends;HKD;2023-08-04;939;939;;;1687.94;;215.37983753444001;;
Open;Dividends;HKD;2023-08-10;390;390;;;2176.4;;277.7069554664;;
Open;Dividends;HKD;2023-08-18;1398;1398;;;1975.41;;252.06078703266002;;
Open;Dividends;HKD;2023-09-04;215;215;;;1140.0;;145.46311764;;
Open;Dividends;HKD;2023-09-05;14;14;;;270.0;;34.45179102;;
Open;Dividends;HKD;2023-09-14;808;808;;;1490.0;;190.12284674000003;;
Open;Dividends;HKD;2023-09-21;5;5;;;1879.68;;239.84571312768003;;
Open;Dividends;HKD;2023-09-28;2828;2828;;;568.0;;72.476360368;;
Open;Dividends;HKD;2023-09-28;386;386;;;315.6;;40.27031572560001;;
Open;Dividends;HKD;2023-09-29;1883;1883;;;600.0;;76.5595356;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;645.48;;82.36274839848001;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;1202.52;;153.44062124952;;
Open;Dividends;HKD;2023-10-13;2888;2888;;;60.04;;7.661057529040001;;
Open;Dividends;HKD;2023-10-13;66;66;;;210.0;;26.79583746;;
Open;Dividends;HKD;2023-10-25;2318;2318;;;505.93;;64.55627641018;;
Open;Dividends;HKD;2023-10-27;27;27;;;200.0;;25.519845200000002;;
Open;Dividends;HKD;2023-10-30;857;857;;;8.7;;1.1101132662;;
Open;Dividends;HKD;2023-10-30;857;857;;;449.44;;57.348196133440005;;
Open;Dividends;HKD;2023-11-16;16;16;;;16650.0;;2124.5271129000002;;
Open;Dividends;HKD;2023-11-30;2800;2800;;;825.0;;105.26936145;;
Open;Dividends;HKD;2023-12-21;5;5;;;1872.67;;238.95124255342003;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;0.13;;0.09985119690000001;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;0.2;;0.15361722600000002;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;9.58;;7.3582651254;;
Open;Dividends;SGD;2023-04-21;D05;D05;;;84.0;;64.51923492;;
Open;Dividends;SGD;2023-04-21;D05;D05;;;100.0;;76.80861300000001;;
Open;Dividends;SGD;2023-05-10;9CI;9CI;;;144.0;;110.60440272000001;;
Open;Dividends;SGD;2023-05-12;F34;F34;;;880.0;;675.9157944;;
Open;Dividends;SGD;2023-05-19;O39;O39;;;320.0;;245.7875616;;
Open;Dividends;SGD;2023-05-22;D05;D05;;;84.0;;64.51923492;;
Open;Dividends;SGD;2023-05-25;BS6;BS6;;;350.0;;268.8301455;;
Open;Dividends;SGD;2023-08-24;D05;D05;;;96.0;;73.73626848;;
Open;Dividends;SGD;2023-08-25;O39;O39;;;320.0;;245.7875616;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;1.11;;0.8525756043000001;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;0.5;;0.384043065;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;0.28;;0.21506411640000003;;
Open;Dividends;SGD;2023-08-30;F34;F34;;;480.0;;368.6813424;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;0.04;;0.0307234452;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;0.44;;0.3379578972;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;9.32;;7.158562731600001;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.3;;0.230425839;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.08;;0.0614468904;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.1;;0.07680861300000001;;
Open;Dividends;SGD;2023-11-27;D05;D05;;;96.0;;73.73626848;;
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
Open;Trades;HKD;2022-10-31;16;16;Stocks;85.0;-255000.0;3000.0;-32537.802630000002;-543.2675;"O;P"
Open;Trades;HKD;2022-01-18;200;200;Stocks;9.2;-18400.0;2000.0;-2347.8257584000003;-42.5244;O
Open;Trades;HKD;2022-10-31;2800;2800;Stocks;24.0;-24000.0;1000.0;-3062.381424;-23.584;"A;O"
Open;Trades;HKD;2022-09-13;3918;3918;Stocks;6.0527;5.74280176;-0.9488;0.7327770596474378;0.0;C
Open;Trades;HKD;2022-06-29;700;700;Stocks;485.3;-99971.8;206.0;-12756.324301826802;-220.3252263;"A;O"
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;-0.0476;0.0;0.0;O
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;0.0476;0.0;0.0;Ca
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;221.0;10.5196;-0.0476;1.3422928178296;0.0;C
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
Open;Trades;USD;2022-09-22;GLD;GLD;Stocks;155.73;31146.0;-200.0;31146.0;-1.7392434;"C;P"
Open;Trades;USD;2022-10-26;GOOG;GOOG;Stocks;132.5;-265000.0;2000.0;-265000.0;0.0;"A;O"
Open;Trades;USD;2022-10-27;GOOG;GOOG;Stocks;93.91;46955.0;-500.0;46955.0;-3.6402695;"C;P"
Open;Trades;USD;2022-10-27;GOOG;GOOG;Stocks;93.988;46994.0;-500.0;46994.0;-3.6411626;"C;P"
Open;Trades;USD;2022-10-28;GOOG;GOOG;Stocks;96.655;38662.0;-400.0;38662.0;-2.9373598;"C;P"
Open;Trades;USD;2022-12-21;GOOG;GOOG;Stocks;132.5;-119250.0;900.0;-119250.0;0.0;"A;O"
Open;Trades;USD;2022-12-29;GOOG;GOOG;Stocks;89.06;44530.0;-500.0;44530.0;-3.584737;"C;P"
Open;Trades;USD;2022-07-13;IVV;IVV;Stocks;440.0;-44000.0;100.0;-44000.0;0.0;"A;O"
Open;Trades;USD;2022-10-27;MCD;MCD;Stocks;264.79;26479.0;-100.0;26479.0;-1.6193691;C
Open;Trades;USD;2022-06-14;META;META;Stocks;325.0;-65000.0;200.0;-65000.0;0.0;"A;O"
Open;Trades;USD;2022-07-27;META;META;Stocks;310.0;-31000.0;100.0;-31000.0;0.0;"A;O"
Open;Trades;USD;2022-10-06;META;META;Stocks;230.0;-23000.0;100.0;-23000.0;0.0;"A;O"
Open;Trades;USD;2022-11-07;META;META;Stocks;96.5;19300.0;-200.0;19300.0;-1.46797;"C;P"
Open;Trades;EUR;2022-10-24;MC;MC 16DEC22 720 P;Equity and Index Options;87.32;-8732.0;1.0;-9607.830794424;-1.1;C
Open;Trades;EUR;2022-10-24;MC;MC 15DEC23 720 P;Equity and Index Options;124.72;12472.0;-1.0;13722.957589104;-1.1;O
Open;Trades;EUR;2022-01-21;MC;MC 16DEC22 720.0 P;Equity and Index Options;94.1;9410.0;-1.0;10353.83506362;-1.1;O
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
Open;Trades;USD;2022-09-22;GLD;GLD 15SEP23 156 P;Equity and Index Options;8.57;1714.0;-2.0;1714.0;-1.4419106;"O;P"
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
Open;Trades;USD;2022-10-27;MCD;MCD 19JAN24 280 P;Equity and Index Options;30.7;3070.0;-1.0;3070.0;-1.121633;O
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
Open;Transaction Fees;HKD;2022-01-18;200;200;Stocks;9.2;-24.5244;0.0;-3.1292944581144004;;
Open;Transaction Fees;HKD;2022-06-29;700;700;Stocks;485.3;-132.8491963;0.0;-16.951454622602064;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-225.845;0.0;-28.817647195970004;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-113.4225;0.0;-14.472623210985;;
Open;Transaction Fees;HKD;2022-10-31;2800;2800;Stocks;24.0;-0.684;0.0;-0.08727787058400001;;
Open;Withholding Tax;HKD;2022-06-20;2318;2318;;;-90.17;;-11.505622208420002;;
Open;Withholding Tax;HKD;2022-06-23;386;386;;;-72.32;;-9.22797602432;;
Open;Withholding Tax;HKD;2022-07-27;1398;1398;;;-205.93;;-26.276508610180002;;
Open;Withholding Tax;HKD;2022-07-29;857;857;;;-22.62;;-2.88629449212;;
Open;Withholding Tax;HKD;2022-07-29;939;939;;;-170.38;;-21.74035612588;;
Open;Withholding Tax;HKD;2022-08-09;390;390;;;-229.56;;-29.291678320560003;;
Open;Withholding Tax;HKD;2022-09-29;386;386;;;-37.02;;-4.723723346520001;;
Open;Withholding Tax;HKD;2022-10-20;2318;2318;;;-42.38;;-5.4076551978800005;;
Open;Withholding Tax;HKD;2022-10-28;857;857;;;-46.68;;-5.9563318696800005;;
Open;Withholding Tax;USD;2022-02-01;FCX;FCX;;;-11.25;;-11.25;;
Open;Withholding Tax;USD;2022-02-01;FCX;FCX;;;-11.25;;-11.25;;
Open;Withholding Tax;USD;2022-03-10;MSFT;MSFT;;;-18.6;;-18.6;;
Open;Withholding Tax;USD;2022-03-15;MCD;MCD;;;-4.97;;-4.97;;
Open;Withholding Tax;USD;2022-03-15;MCD;MCD;;;-36.43;;-36.43;;
Open;Withholding Tax;USD;2022-03-25;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2022-05-02;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2022-06-09;MSFT;MSFT;;;-18.23;;-18.23;;
Open;Withholding Tax;USD;2022-06-09;MSFT;MSFT;;;-0.37;;-0.37;;
Open;Withholding Tax;USD;2022-06-21;MCD;MCD;;;-41.4;;-41.4;;
Open;Withholding Tax;USD;2022-06-24;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2022-08-01;FCX;FCX;;;-1.49;;-1.49;;
Open;Withholding Tax;USD;2022-08-01;FCX;FCX;;;-21.01;;-21.01;;
Open;Withholding Tax;USD;2022-09-08;MSFT;MSFT;;;-18.6;;-18.6;;
Open;Withholding Tax;USD;2022-09-16;MCD;MCD;;;-41.4;;-41.4;;
Open;Withholding Tax;USD;2022-09-30;IVV;IVV;;;-57.18;;-57.18;;
Open;Withholding Tax;USD;2022-09-30;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2022-11-01;FCX;FCX;;;-0.9;;-0.9;;
Open;Withholding Tax;USD;2022-11-01;FCX;FCX;;;-21.6;;-21.6;;
Open;Withholding Tax;USD;2022-12-08;MSFT;MSFT;;;-20.4;;-20.4;;
Open;Withholding Tax;USD;2022-12-19;IVV;IVV;;;-51.71;;-51.71;;
Open;Withholding Tax;USD;2022-12-23;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;HKD;2022-03-17;16;16;;;1875.0;;239.24854875000003;;
Open;Dividends;HKD;2022-03-25;14;14;;;1170.0;;149.29109442;;
Open;Dividends;HKD;2022-04-11;808;808;;;1724.0;;219.98106562400002;;
Open;Dividends;HKD;2022-04-28;5;5;;;3388.17;;432.32786955642007;;
Open;Dividends;HKD;2022-04-29;27;27;;;300.0;;38.2797678;;
Open;Dividends;HKD;2022-05-05;1972;1972;;;3584.0;;457.315625984;;
Open;Dividends;HKD;2022-05-12;2888;2888;;;90.34;;11.527314076840002;;
Open;Dividends;HKD;2022-06-06;700;700;;;640.0;;81.66350464000001;;
Open;Dividends;HKD;2022-06-20;2318;2318;;;901.7;;115.05622208420002;;
Open;Dividends;HKD;2022-06-23;386;386;;;723.2;;92.27976024320002;;
Open;Dividends;HKD;2022-07-19;66;66;;;510.0;;65.07560526;;
Open;Dividends;HKD;2022-07-27;1398;1398;;;2059.31;;262.76636209406;;
Open;Dividends;HKD;2022-07-29;857;857;;;226.22;;28.86549690572;;
Open;Dividends;HKD;2022-07-29;939;939;;;1703.8;;217.4035612588;;
Open;Dividends;HKD;2022-08-09;390;390;;;2295.6;;292.9167832056;;
Open;Dividends;HKD;2022-09-08;808;808;;;1750.0;;223.29864550000002;;
Open;Dividends;HKD;2022-09-16;14;14;;;270.0;;34.45179102;;
Open;Dividends;HKD;2022-09-29;386;386;;;370.2;;47.2372334652;;
Open;Dividends;HKD;2022-09-29;5;5;;;1695.13;;216.29727596938002;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;1755.52;;224.00299322752002;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;36.48;;4.65481976448;;
Open;Dividends;HKD;2022-10-14;2888;2888;;;40.18;;5.1269369006800005;;
Open;Dividends;HKD;2022-10-14;66;66;;;210.0;;26.79583746;;
Open;Dividends;HKD;2022-10-20;2318;2318;;;531.06;;67.76284495956;;
Open;Dividends;HKD;2022-10-28;857;857;;;466.76;;59.55821472776;;
Open;Dividends;HKD;2022-11-17;16;16;;;16650.0;;2124.5271129000002;;
Open;Dividends;SGD;2022-01-28;C38U;C38U;;;0.48;;0.3686813424;;
Open;Dividends;SGD;2022-01-28;C38U;C38U;;;8.49;;6.5210512437000006;;
Open;Dividends;SGD;2022-03-15;C38U;C38U;;;0.19;;0.14593636470000002;;
Open;Dividends;SGD;2022-03-15;C38U;C38U;;;0.5;;0.384043065;;
Open;Dividends;SGD;2022-04-22;D05;D05;;;72.0;;55.302201360000005;;
Open;Dividends;SGD;2022-05-06;F34;F34;;;840.0;;645.1923492000001;;
Open;Dividends;SGD;2022-05-20;9CI;9CI;;;36.0;;27.651100680000003;;
Open;Dividends;SGD;2022-05-20;9CI;9CI;;;144.0;;110.60440272000001;;
Open;Dividends;SGD;2022-05-20;O39;O39;;;224.0;;172.05129312;;
Open;Dividends;SGD;2022-05-25;D05;D05;;;72.0;;55.302201360000005;;
Open;Dividends;SGD;2022-05-27;BS6;BS6;;;350.0;;268.8301455;;
Open;Dividends;SGD;2022-08-24;F34;F34;;;480.0;;368.6813424;;
Open;Dividends;SGD;2022-08-25;O39;O39;;;224.0;;172.05129312;;
Open;Dividends;SGD;2022-08-26;D05;D05;;;72.0;;55.302201360000005;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;1.3;;0.998511969;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;0.06;;0.0460851678;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;8.31;;6.382795740300001;;
Open;Dividends;SGD;2022-11-24;D05;D05;;;72.0;;55.302201360000005;;
Open;Dividends;USD;2022-02-01;FCX;FCX;;;37.5;;37.5;;
Open;Dividends;USD;2022-02-01;FCX;FCX;;;37.5;;37.5;;
Open;Dividends;USD;2022-03-10;MSFT;MSFT;;;62.0;;62.0;;
Open;Dividends;USD;2022-03-15;MCD;MCD;;;16.56;;16.56;;
Open;Dividends;USD;2022-03-15;MCD;MCD;;;121.44;;121.44;;
Open;Dividends;USD;2022-03-25;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2022-03-28;BHP;BHP;;;2643.0;;2643.0;;
Open;Dividends;USD;2022-03-28;BHP;BHP;;;957.0;;957.0;;
Open;Dividends;USD;2022-04-07;SOUHY;SOUHY;;;121.78;;121.78;;
Open;Dividends;USD;2022-05-02;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2022-05-11;H78;H78;;;160.0;;160.0;;
Open;Dividends;USD;2022-06-08;9618;9618;;;11.97;;11.97;;
Open;Dividends;USD;2022-06-09;MSFT;MSFT;;;60.76;;60.76;;
Open;Dividends;USD;2022-06-09;MSFT;MSFT;;;1.24;;1.24;;
Open;Dividends;USD;2022-06-21;MCD;MCD;;;138.0;;138.0;;
Open;Dividends;USD;2022-06-24;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2022-08-01;FCX;FCX;;;4.95;;4.95;;
Open;Dividends;USD;2022-08-01;FCX;FCX;;;70.05;;70.05;;
Open;Dividends;USD;2022-09-08;MSFT;MSFT;;;62.0;;62.0;;
Open;Dividends;USD;2022-09-16;MCD;MCD;;;138.0;;138.0;;
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
Open;Trades;HKD;2021-09-19;1398;1398;Stocks;4.18;-12540.0;3000.0;-1600.09429404;-35.33858;O
Open;Trades;HKD;2021-05-10;14;14;Stocks;28.5;-28500.0;1000.0;-3636.5779410000005;-52.5695;O
Open;Trades;HKD;2021-09-15;200;200;Stocks;9.33;-9330.0;1000.0;-1190.50077858;-31.25191;O
Open;Trades;HKD;2021-04-25;2318;2318;Stocks;86.3;-43150.0;500.0;-5505.9066019;-79.68505;O
Open;Trades;HKD;2021-04-25;390;390;Stocks;3.92;-39200.0;10000.0;-5001.8896592;-72.4184;"O;P"
Open;Trades;HKD;2021-05-11;3918;3918;Stocks;7.8;-15600.0;2000.0;-1990.5479256;-34.4212;O
Open;Trades;HKD;2021-04-20;700;700;Stocks;614.5;-61450.0;100.0;-7840.9724377;-112.81915;O
Open;Trades;HKD;2021-08-15;700;700;Stocks;455.8;-91160.0;200.0;-11631.94544216;-194.38932;O
Open;Trades;HKD;2021-04-26;939;939;Stocks;6.38;-25520.0;4000.0;-3256.3322475200002;-47.10504;O
Open;Trades;SGD;2021-09-10;C38U;C38U;Stocks;2.0;1.2144;-0.6072;0.932763796272;0.0;C
Open;Trades;USD;2021-12-20;BABA;BABA;Stocks;230.0;-46000.0;200.0;-46000.0;0.0;"A;O"
Open;Trades;USD;2021-02-09;FCX;FCX;Stocks;31.38;15690.0;-500.0;15690.0;-2.906249;"C;P"
Open;Trades;USD;2021-01-19;MSFT;MSFT;Stocks;213.0;-21300.0;100.0;-21300.0;-1.0;O
Open;Trades;EUR;2021-03-19;MC;MC 19MAR21 360.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-08-19;700;700 30AUG21 450.0 P;Equity and Index Options;32.3;6460.0;-2.0;824.29099996;-24.0;O
Open;Trades;HKD;2021-08-30;700;700 30AUG21 450.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-09-03;700;700 29JUN22 500.0 P;Equity and Index Options;67.08;13416.0;-2.0;1711.871216016;-32.832;O
Open;Trades;HKD;2021-12-06;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.05;1025.0;-1.0;130.78920665;-19.0;O
Open;Trades;HKD;2021-12-20;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.5;1250.0;-1.0;159.4990325;-19.0;O
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
Open;Trades;USD;2021-03-19;GLD;GLD 19MAR21 150.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-03-19;GLD;GLD 19MAR21 155.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-01-15;GOOG;GOOG 15JAN21 1280.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-09-28;IVV;IVV 18MAR22 445.0 P;Equity and Index Options;24.5;2450.0;-1.0;2450.0;-0.316645;O
Open;Trades;USD;2021-01-15;MSFT;MSFT 15JAN21 170.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-01-15;MSFT;MSFT 15JAN21 175.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-01-15;MSFT;MSFT 15JAN21 185.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-01-15;MSFT;MSFT 15JAN21 190.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-03-19;MSFT;MSFT 19MAR21 140.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2021-01-19;MSFT;MSFT 21JAN22 215.0 P;Equity and Index Options;27.0;5400.0;-2.0;5400.0;-1.57094;"O;P"
Open;Corporate Actions;SGD;2021-09-20;9CI;C31;Stocks;;0.0;1200.0;0.0;;
Open;Corporate Actions;SGD;2021-09-20;9CI;C31;Stocks;;1141.2;-1200.0;876.539891556;;
Open;Corporate Actions;SGD;2021-09-20;C38U;C38U;Stocks;;0.0;185.6072;0.0;;
Open;Corporate Actions;USD;2021-01-04;NOV;US62955J1034) (NOV;Stocks;;0.0;300.0;0.0;;
Open;Corporate Actions;USD;2021-01-04;NOV;US62955J1034) (NOV.OLD;Stocks;;0.0;-300.0;0.0;;
Open;Transaction Fees;HKD;2021-04-20;700;700;Stocks;614.5;-63.65915;0.0;-8.1228582678179;;
Open;Transaction Fees;HKD;2021-04-25;2318;2318;Stocks;86.3;-45.16505;0.0;-5.7630254222513;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-24.63504;0.0;-3.1434120364790403;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-4.10584;0.0;-0.52390200607984;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-12.31752;0.0;-1.5717060182395202;;
Open;Transaction Fees;HKD;2021-04-26;939;939;Stocks;6.38;-26.68904;0.0;-3.40550084668304;;
Open;Transaction Fees;HKD;2021-05-10;14;14;Stocks;28.5;-29.7695;0.0;-3.7985651584070004;;
Open;Transaction Fees;HKD;2021-05-11;3918;3918;Stocks;7.8;-16.4212;0.0;-2.0953324099912;;
Open;Transaction Fees;HKD;2021-08-15;700;700;Stocks;455.8;-121.46132;0.0;-15.498370420938322;;
Open;Transaction Fees;HKD;2021-09-15;200;200;Stocks;9.33;-13.25191;0.0;-1.6909334590216603;;
Open;Transaction Fees;HKD;2021-09-19;1398;1398;Stocks;4.18;-17.33858;0.0;-2.2123893879390804;;
Open;Withholding Tax;HKD;2021-06-28;386;386;;;-31.38;;-4.00406371188;;
Open;Withholding Tax;HKD;2021-07-27;1398;1398;;;-95.97;;-12.24569771922;;
Open;Withholding Tax;HKD;2021-07-30;857;857;;;-21.21;;-2.7063795834600004;;
Open;Withholding Tax;HKD;2021-08-05;390;390;;;-216.71;;-27.652028266460004;;
Open;Withholding Tax;HKD;2021-08-05;939;939;;;-156.33;;-19.947587000580004;;
Open;Withholding Tax;HKD;2021-09-30;386;386;;;-38.44;;-4.90491424744;;
Open;Withholding Tax;HKD;2021-10-25;2318;2318;;;-52.84;;-6.742343101840001;;
Open;Withholding Tax;HKD;2021-10-29;857;857;;;-31.31;;-3.99513176606;;
Open;Withholding Tax;USD;2021-03-11;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-03-15;MCD;MCD;;;-38.7;;-38.7;;
Open;Withholding Tax;USD;2021-05-03;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-06-10;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-06-15;MCD;MCD;;;-38.7;;-38.7;;
Open;Withholding Tax;USD;2021-08-02;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-09-09;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-09-15;MCD;MCD;;;-38.7;;-38.7;;
Open;Withholding Tax;USD;2021-11-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-12-09;MSFT;MSFT;;;-18.6;;-18.6;;
Open;Withholding Tax;USD;2021-12-15;MCD;MCD;;;-41.4;;-41.4;;
Open;Withholding Tax;USD;2021-12-17;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;HKD;2021-03-18;16;16;;;1875.0;;239.24854875000003;;
Open;Dividends;HKD;2021-04-07;808;808;;;1784.0;;227.63701918400002;;
Open;Dividends;HKD;2021-04-29;5;5;;;2796.85;;356.87589523810004;;
Open;Dividends;HKD;2021-05-06;1972;1972;;;3416.0;;435.878956016;;
Open;Dividends;HKD;2021-05-20;2888;2888;;;89.41;;11.408646796660001;;
Open;Dividends;HKD;2021-06-07;700;700;;;320.0;;40.83175232000001;;
Open;Dividends;HKD;2021-06-28;386;386;;;313.8;;40.04063711880001;;
Open;Dividends;HKD;2021-07-05;3918;3918;;;291.4;;37.1824144564;;
Open;Dividends;HKD;2021-07-20;66;66;;;490.0;;62.523620740000005;;
Open;Dividends;HKD;2021-07-27;1398;1398;;;959.73;;122.46080516898002;;
Open;Dividends;HKD;2021-07-30;857;857;;;212.14;;27.06889980364;;
Open;Dividends;HKD;2021-08-05;390;390;;;2167.1;;276.5202826646;;
Open;Dividends;HKD;2021-08-05;939;939;;;1563.34;;199.48097397484;;
Open;Dividends;HKD;2021-09-06;14;14;;;270.0;;34.45179102;;
Open;Dividends;HKD;2021-09-08;808;808;;;1802.0;;229.933805252;;
Open;Dividends;HKD;2021-09-30;386;386;;;384.4;;49.0491424744;;
Open;Dividends;HKD;2021-09-30;5;5;;;1308.18;;166.92275546868;;
Open;Dividends;HKD;2021-10-05;1972;1972;;;1736.0;;221.512256336;;
Open;Dividends;HKD;2021-10-19;66;66;;;125.0;;15.949903250000002;;
Open;Dividends;HKD;2021-10-22;2888;2888;;;29.9;;3.8152168574000003;;
Open;Dividends;HKD;2021-10-25;2318;2318;;;528.4;;67.42343101840001;;
Open;Dividends;HKD;2021-10-29;857;857;;;313.06;;39.946213691560004;;
Open;Dividends;HKD;2021-11-18;16;16;;;5550.0;;708.1757043;;
Open;Dividends;SGD;2021-05-06;F34;F34;;;520.0;;399.4047876;;
Open;Dividends;SGD;2021-05-06;F34;F34;;;720.0;;553.0220136;;
Open;Dividends;SGD;2021-05-18;9CI;C31;;;108.0;;82.95330204;;
Open;Dividends;SGD;2021-05-24;D05;D05;;;36.0;;27.651100680000003;;
Open;Dividends;SGD;2021-05-28;BS6;BS6;;;315.0;;241.94713095;;
Open;Dividends;SGD;2021-06-25;D05;D05;;;36.0;;27.651100680000003;;
Open;Dividends;SGD;2021-06-29;O39;O39;;;127.2;;97.70055573600001;;
Open;Dividends;SGD;2021-08-26;D05;D05;;;66.0;;50.69368458;;
Open;Dividends;SGD;2021-08-26;O39;O39;;;200.0;;153.61722600000002;;
Open;Dividends;SGD;2021-08-27;F34;F34;;;400.0;;307.23445200000003;;
Open;Dividends;SGD;2021-11-26;D05;D05;;;66.0;;50.69368458;;
Open;Dividends;USD;2021-03-11;MSFT;MSFT;;;56.0;;56.0;;
Open;Dividends;USD;2021-03-15;MCD;MCD;;;129.0;;129.0;;
Open;Dividends;USD;2021-03-23;BHP;BHP;;;2424.0;;2424.0;;
Open;Dividends;USD;2021-04-08;SOUHY;SOUHY;;;21.0;;21.0;;
Open;Dividends;USD;2021-05-03;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2021-05-12;H78;H78;;;160.0;;160.0;;
Open;Dividends;USD;2021-06-10;MSFT;MSFT;;;56.0;;56.0;;
Open;Dividends;USD;2021-06-15;MCD;MCD;;;129.0;;129.0;;
Open;Dividends;USD;2021-08-02;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2021-09-09;MSFT;MSFT;;;56.0;;56.0;;
Open;Dividends;USD;2021-09-15;MCD;MCD;;;129.0;;129.0;;
Open;Dividends;USD;2021-09-21;BHP;BHP;;;4800.0;;4800.0;;
Open;Dividends;USD;2021-10-07;SOUHY;SOUHY;;;52.5;;52.5;;
Open;Dividends;USD;2021-10-07;SOUHY;SOUHY;;;30.0;;30.0;;
Open;Dividends;USD;2021-10-13;H78;H78;;;60.0;;60.0;;
Open;Dividends;USD;2021-11-01;FCX;FCX;;;75.0;;75.0;;
Open;Dividends;USD;2021-12-09;MSFT;MSFT;;;62.0;;62.0;;
Open;Dividends;USD;2021-12-15;MCD;MCD;;;138.0;;138.0;;
Open;Dividends;USD;2021-12-17;NOV;NOV;;;15.0;;15.0;;
Open;Trades;HKD;2020-03-18;16;16;Stocks;97.5;-48750.0;500.0;-6220.4622675;-89.31625;O
Open;Trades;HKD;2020-03-18;16;16;Stocks;96.5;-48250.0;500.0;-6156.662654500001;-88.90275;O
Open;Trades;HKD;2020-07-20;16;16;Stocks;94.05;-47025.0;500.0;-6000.3536026500005;-86.889675;O
Open;Trades;HKD;2020-01-29;1972;1972;Stocks;24.65;-24650.0;1000.0;-3145.3209209;-45.38555;O
Open;Trades;HKD;2020-05-04;1972;1972;Stocks;20.9;-20900.0;1000.0;-2666.8238234;-39.5643;O
Open;Trades;HKD;2020-05-24;1972;1972;Stocks;17.78;-35560.0;2000.0;-4537.4284765600005;-67.40812;"O;P"
Open;Trades;HKD;2020-07-20;1972;1972;Stocks;18.2;-29120.0;1600.0;-3715.6894611200005;-55.08224;"O;P"
Open;Trades;HKD;2020-07-22;27;27;Stocks;51.5;-51500.0;1000.0;-6571.360139;-94.5905;O
Open;Trades;HKD;2020-03-18;2800;2800;Stocks;21.7;-10850.0;500.0;-1384.4516021000002;-18.29295;O
Open;Trades;HKD;2020-03-31;5;5;Stocks;40.8;-16320.0;400.0;-2082.41936832;-35.44064;O
Open;Trades;HKD;2020-05-14;5;5;Stocks;38.0;-45600.0;1200.0;-5818.524705600001;-83.7112;O
Open;Trades;HKD;2020-05-21;66;66;Stocks;40.0;-20000.0;500.0;-2551.98452;-38.54;O
Open;Trades;HKD;2020-09-29;700;700;Stocks;530.0;-53000.0;100.0;-6762.758978000001;-101.981;"A;O"
Open;Trades;HKD;2020-04-23;808;808;Stocks;2.38;-23800.0;10000.0;-3036.8615788;-48.6826;"O;P"
Open;Trades;HKD;2020-07-22;808;808;Stocks;2.29;-22900.0;10000.0;-2922.0222754;-41.9383;O
Open;Trades;SGD;2020-07-22;BS6;BS6;Stocks;0.935;-3085.5;3300.0;-2369.929754115;-2.5;O
Open;Trades;SGD;2020-08-26;BS6;BS6;Stocks;0.92;-3404.0;3700.0;-2614.56518652;-2.7232;O
Open;Trades;SGD;2020-07-23;9CI;C31;Stocks;2.83;-3396.0;1200.0;-2608.42049748;-2.7168;O
Open;Trades;SGD;2020-07-22;D05;D05;Stocks;21.2;-4240.0;200.0;-3256.6851912;-3.392;O
Open;Trades;SGD;2020-09-08;F34;F34;Stocks;4.32;-4320.0;1000.0;-3318.1320816;-3.456;O
Open;Trades;SGD;2020-07-22;O39;O39;Stocks;9.13;-3652.0;400.0;-2805.05054676;-2.9216;O
Open;Trades;SGD;2020-08-26;O39;O39;Stocks;8.62;-3448.0;400.0;-2648.36097624;-2.7584;O
Open;Trades;USD;2020-01-06;AMZN;AMZN;Stocks;1900.0;9500.0;-5.0;9500.0;-1.197245;C
Open;Trades;USD;2020-04-13;AMZN;AMZN;Stocks;2146.093;32191.395;-15.0;32191.395;-1.71321483;C
Open;Trades;USD;2020-06-08;AMZN;AMZN;Stocks;2499.5;74985.0;-30.0;74985.0;-2.6607385;C
Open;Trades;USD;2020-06-09;CPRT;CPRT;Stocks;88.35;-8835.0;100.0;-8835.0;-1.0;O
Open;Trades;USD;2020-06-12;CPRT;CPRT;Stocks;85.13;-8513.0;100.0;-8513.0;-1.0;O
Open;Trades;USD;2020-01-17;FCX;FCX;Stocks;15.0;-6000.0;400.0;-6000.0;0.0;"A;O"
Open;Trades;USD;2020-03-18;GLD;GLD;Stocks;141.0;-14100.0;100.0;-14100.0;-1.0;O
Open;Trades;USD;2020-06-10;GLD;GLD;Stocks;161.2;-16120.0;100.0;-16120.0;-1.0;O
Open;Trades;USD;2020-01-07;H78;H78;Stocks;5.6;-1120.0;200.0;-1120.0;-2.0;"O;P"
Open;Trades;USD;2020-05-25;H78;H78;Stocks;3.5;-700.0;200.0;-700.0;-2.0;O
Open;Trades;USD;2020-07-20;H78;H78;Stocks;3.97;-2382.0;600.0;-2382.0;-2.0;O
Open;Trades;USD;2020-06-10;MCD;MCD;Stocks;196.17;-19617.0;100.0;-19617.0;-1.0;O
Open;Trades;USD;2020-03-18;SOUHY;SOUHY;Stocks;4.85;-485.0;100.0;-485.0;-1.0;O
Open;Trades;EUR;2020-05-04;MC;MC 19MAR21 360.0 P;Equity and Index Options;50.8;5080.0;-1.0;5589.53051256;-1.1;O
Open;Trades;HKD;2020-07-24;27;27 28AUG20 50.0 P;Equity and Index Options;2.18;4360.0;-2.0;556.3326253600001;-24.0;O
Open;Trades;HKD;2020-08-28;27;27 28AUG20 50.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2020-07-24;700;700 29SEP20 530.0 P;Equity and Index Options;38.02;3802.0;-1.0;485.13225725200004;-21.0;O
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
Open;Trades;USD;2020-03-27;GLD;GLD 19MAR21 150.0 P;Equity and Index Options;15.18;1518.0;-1.0;1518.0;-0.7593478;O
Open;Trades;USD;2020-03-27;GLD;GLD 19MAR21 155.0 P;Equity and Index Options;18.08;1808.0;-1.0;1808.0;-0.7657568;O
Open;Trades;USD;2020-04-17;GOOG;GOOG 15JAN21 1280.0 P;Equity and Index Options;141.0;14100.0;-1.0;14100.0;-0.54741;O
Open;Trades;USD;2020-02-27;MSFT;MSFT 15JAN21 170.0 P;Equity and Index Options;19.8;1980.0;-1.0;1980.0;-1.139558;O
Open;Trades;USD;2020-02-27;MSFT;MSFT 15JAN21 175.0 P;Equity and Index Options;22.95;2295.0;-1.0;2295.0;-1.1465195;O
Open;Trades;USD;2020-02-07;MSFT;MSFT 15JAN21 185.0 P;Equity and Index Options;17.0;1700.0;-1.0;1700.0;-1.13099;O
Open;Trades;USD;2020-02-27;MSFT;MSFT 15JAN21 185.0 P;Equity and Index Options;30.0;3000.0;-1.0;3000.0;-1.1621;O
Open;Trades;USD;2020-02-07;MSFT;MSFT 15JAN21 190.0 P;Equity and Index Options;19.5;1950.0;-1.0;1950.0;-0.836165;O
Open;Trades;USD;2020-03-12;MSFT;MSFT 19MAR21 140.0 P;Equity and Index Options;21.99;4398.0;-2.0;4398.0;-0.9887958;O
Open;Transaction Fees;HKD;2020-01-29;1972;1972;Stocks;24.65;-25.66555;0.0;-3.2749043148643002;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;97.5;-50.31625;0.0;-6.4203145552225;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;96.5;-50.30275;0.0;-6.418591965671501;;
Open;Transaction Fees;HKD;2020-03-18;2800;2800;Stocks;21.7;-0.29295;0.0;-0.0373801932567;;
Open;Transaction Fees;HKD;2020-03-31;5;5;Stocks;40.8;-17.44064;0.0;-2.22541216494464;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099720426276;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099720426276;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099720426276;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099720426276;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099720426276;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099720426276;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099720426276;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099720426276;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-5.12852;0.0;-0.6543951825255201;;
Open;Transaction Fees;HKD;2020-05-04;1972;1972;Stocks;20.9;-21.5643;0.0;-2.7515879892318003;;
Open;Transaction Fees;HKD;2020-05-14;5;5;Stocks;38.0;-47.2312;0.0;-6.026664563051201;;
Open;Transaction Fees;HKD;2020-05-21;66;66;Stocks;40.0;-20.54;0.0;-2.62088810204;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.5226479608867121;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.5226479608867121;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.9629926175468482;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.9629926175468482;;
Open;Transaction Fees;HKD;2020-07-20;16;16;Stocks;94.05;-49.269675;0.0;-6.286772395271551;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.04587471186256;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.04587471186256;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-4.09828;0.0;-0.52293735593128;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-11.29484;0.0;-1.4412128417938401;;
Open;Transaction Fees;HKD;2020-07-22;808;808;Stocks;2.29;-23.6183;0.0;-3.0136767994358005;;
Open;Transaction Fees;HKD;2020-07-22;27;27;Stocks;51.5;-53.3905;0.0;-6.812586475753001;;
Open;Transaction Fees;HKD;2020-09-29;700;700;Stocks;530.0;-54.431;0.0;-6.945353470406;;
Open;Withholding Tax;HKD;2020-06-19;386;386;;;-41.54;;-5.30047184804;;
Open;Withholding Tax;HKD;2020-07-21;1398;1398;;;-74.27;;-9.47679451502;;
Open;Withholding Tax;HKD;2020-07-31;857;857;;;-14.44;;-1.84253282344;;
Open;Withholding Tax;HKD;2020-11-05;386;386;;;-15.98;;-2.03903563148;;
Open;Withholding Tax;HKD;2020-11-13;857;857;;;-19.59;;-2.4996688373400002;;
Open;Withholding Tax;USD;2020-02-03;FCX;FCX;;;-16.5;;-16.5;;
Open;Withholding Tax;USD;2020-03-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2020-09-15;MCD;MCD;;;-37.5;;-37.5;;
Open;Withholding Tax;USD;2020-12-15;MCD;MCD;;;-38.7;;-38.7;;
Open;Dividends;HKD;2020-05-07;1972;1972;;;590.0;;75.28354334000001;;
Open;Dividends;HKD;2020-05-29;2800;2800;;;45.0;;5.74196517;;
Open;Dividends;HKD;2020-06-19;386;386;;;415.4;;53.0047184804;;
Open;Dividends;HKD;2020-07-02;200;200;;;30.1;;3.8407367026000006;;
Open;Dividends;HKD;2020-07-21;1398;1398;;;862.3;;110.0288125798;;
Open;Dividends;HKD;2020-07-30;2828;2828;;;488.0;;62.268422288000004;;
Open;Dividends;HKD;2020-07-31;857;857;;;144.4;;18.425328234400002;;
Open;Dividends;HKD;2020-09-09;808;808;;;1798.0;;229.423408348;;
Open;Dividends;HKD;2020-10-06;1972;1972;;;1680.0;;214.36669968;;
Open;Dividends;HKD;2020-10-09;66;66;;;5.75;;0.7336955495;;
Open;Dividends;HKD;2020-10-09;66;66;;;119.25;;15.216207700500002;;
Open;Dividends;HKD;2020-11-05;386;386;;;159.8;;20.3903563148;;
Open;Dividends;HKD;2020-11-13;857;857;;;195.86;;24.991584404360005;;
Open;Dividends;HKD;2020-11-19;16;16;;;5550.0;;708.1757043;;
Open;Dividends;SGD;2020-06-24;F34;F34;;;665.0;;510.77727645000004;;
Open;Dividends;SGD;2020-08-27;F34;F34;;;280.0;;215.06411640000002;;
Open;Dividends;SGD;2020-10-05;D05;D05;;;36.0;;27.651100680000003;;
Open;Dividends;SGD;2020-10-07;O39;O39;;;63.6;;48.850277868000006;;
Open;Dividends;SGD;2020-12-29;D05;D05;;;36.0;;27.651100680000003;;
Open;Dividends;USD;2020-02-03;FCX;FCX;;;55.0;;55.0;;
Open;Dividends;USD;2020-03-24;BHP;BHP;;;1560.0;;1560.0;;
Open;Dividends;USD;2020-03-27;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2020-04-02;SOUHY;SOUHY;;;11.0;;11.0;;
Open;Dividends;USD;2020-04-02;SOUHY;SOUHY;;;11.0;;11.0;;
Open;Dividends;USD;2020-05-13;H78;H78;;;32.0;;32.0;;
Open;Dividends;USD;2020-09-15;MCD;MCD;;;125.0;;125.0;;
Open;Dividends;USD;2020-09-22;BHP;BHP;;;1320.0;;1320.0;;
Open;Dividends;USD;2020-10-08;SOUHY;SOUHY;;;15.0;;15.0;;
Open;Dividends;USD;2020-10-14;H78;H78;;;60.0;;60.0;;
Open;Dividends;USD;2020-12-15;MCD;MCD;;;129.0;;129.0;;
Open;Trades;HKD;2019-03-08;1398;1398;Stocks;5.9;-17700.0;3000.0;-2258.5063002;-36.4779;O
Open;Trades;USD;2019-02-11;AMZN;AMZN;Stocks;1800.0;-180000.0;100.0;-180000.0;0.0;"A;O"
Open;Trades;USD;2019-02-19;AMZN;AMZN;Stocks;1628.88;81444.0;-50.0;81444.0;-2.064722;C
Open;Trades;USD;2019-10-14;FCX;FCX;Stocks;15.0;-1500.0;100.0;-1500.0;0.0;"A;O"
Open;Trades;USD;2019-02-11;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2019-03-08;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;3.8;1900.0;-5.0;1900.0;-3.9787;O
Open;Trades;USD;2019-10-14;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2019-03-08;1398;1398;Stocks;5.9;-18.4779;0.0;-2.3577657381054005;;
Open;Withholding Tax;HKD;2019-06-21;386;386;;;-60.6;;-7.732513095600001;;
Open;Withholding Tax;HKD;2019-07-23;1398;1398;;;-85.12;;-10.861246117120002;;
Open;Withholding Tax;HKD;2019-08-02;857;857;;;-20.47;;-2.61195615622;;
Open;Withholding Tax;HKD;2019-09-26;386;386;;;-26.78;;-3.4171072722800004;;
Open;Withholding Tax;HKD;2019-11-01;857;857;;;-17.26;;-2.2023626407600005;;
Open;Withholding Tax;USD;2019-02-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-03-29;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-05-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-06-28;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-08-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-09-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-11-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-12-20;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;CNH;2019-07-31;3188;3188;;;390.0;;58.091925839999995;;
Open;Dividends;HKD;2019-01-04;200;200;;;24.38;;3.11086912988;;
Open;Dividends;HKD;2019-04-08;5;5;;;1318.35;;168.2204395971;;
Open;Dividends;HKD;2019-05-16;2888;2888;;;150.56;;19.211339466560002;;
Open;Dividends;HKD;2019-06-21;386;386;;;606.0;;77.32513095600001;;
Open;Dividends;HKD;2019-07-05;200;200;;;23.5;;2.998581811;;
Open;Dividends;HKD;2019-07-05;5;5;;;625.02;;79.75206823452001;;
Open;Dividends;HKD;2019-07-23;1398;1398;;;854.32;;109.01057075632002;;
Open;Dividends;HKD;2019-07-30;2828;2828;;;640.0;;81.66350464000001;;
Open;Dividends;HKD;2019-08-02;857;857;;;204.72;;26.12211354672;;
Open;Dividends;HKD;2019-09-26;386;386;;;267.8;;34.171072722800005;;
Open;Dividends;HKD;2019-09-26;5;5;;;625.44;;79.80565990944001;;
Open;Dividends;HKD;2019-09-27;2828;2828;;;520.0;;66.35159752;;
Open;Dividends;HKD;2019-10-04;200;200;;;61.1;;7.7963127086;;
Open;Dividends;HKD;2019-10-21;2888;2888;;;70.24;;8.96256963424;;
Open;Dividends;HKD;2019-11-01;857;857;;;172.56;;22.01852243856;;
Open;Dividends;HKD;2019-11-20;5;5;;;626.63;;79.95750298838;;
Open;Dividends;SGD;2019-05-16;F34;F34;;;490.0;;376.3622037;;
Open;Dividends;SGD;2019-08-30;F34;F34;;;210.0;;161.29808730000002;;
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
Open;Trades;HKD;2018-01-01;1398;1398;Stocks;6.48;6480.0;-1000.0;826.84298448;-25.17496;C
Open;Trades;HKD;2018-02-05;215;215;Stocks;3.35;6700.0;-2000.0;854.9148142;-25.1809;C
Open;Trades;HKD;2018-02-19;215;215;Stocks;3.4;6800.0;-2000.0;867.6747368000001;-25.1836;C
Open;Trades;USD;2018-08-17;FCX;FCX;Stocks;21.0;-21000.0;1000.0;-21000.0;0.0;"A;O"
Open;Trades;USD;2018-10-26;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;216.21;21621.0;-1.0;21621.0;-1.367373;O
Open;Trades;USD;2018-01-18;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;3.08;3080.0;-10.0;3080.0;-5.006148;O
Open;Trades;USD;2018-08-17;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;0.0;0.0;10.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2018-01-01;1398;1398;Stocks;6.48;-7.17496;0.0;-0.91551934258096;;
Open;Transaction Fees;HKD;2018-02-05;215;215;Stocks;3.35;-7.1809;0.0;-0.9162772819834001;;
Open;Transaction Fees;HKD;2018-02-19;215;215;Stocks;3.4;-7.1836;0.0;-0.9166217998936002;;
Open;Withholding Tax;HKD;2018-06-14;386;386;;;-98.64;;-12.586387652640001;;
Open;Withholding Tax;HKD;2018-07-26;857;857;;;-14.86;;-1.89612449836;;
Open;Withholding Tax;HKD;2018-09-21;386;386;;;-36.5;;-4.657371749;;
Open;Withholding Tax;HKD;2018-11-01;857;857;;;-20.39;;-2.6017482181400005;;
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
Open;Dividends;HKD;2018-04-06;5;5;;;1317.81;;168.15153601506;;
Open;Dividends;HKD;2018-05-17;2888;2888;;;110.45;;14.093334511700002;;
Open;Dividends;HKD;2018-06-14;386;386;;;986.4;;125.8638765264;;
Open;Dividends;HKD;2018-07-04;200;200;;;40.0;;5.103969040000001;;
Open;Dividends;HKD;2018-07-05;5;5;;;627.87;;80.11572602862;;
Open;Dividends;HKD;2018-07-26;857;857;;;148.62;;18.96379696812;;
Open;Dividends;HKD;2018-07-30;2828;2828;;;440.0;;56.14365944000001;;
Open;Dividends;HKD;2018-09-21;386;386;;;365.0;;46.57371749000001;;
Open;Dividends;HKD;2018-09-27;5;5;;;627.73;;80.09786213698001;;
Open;Dividends;HKD;2018-09-28;2828;2828;;;800.0;;102.07938080000001;;
Open;Dividends;HKD;2018-10-05;200;200;;;45.0;;5.74196517;;
Open;Dividends;HKD;2018-10-22;2888;2888;;;60.13;;7.6725414593800005;;
Open;Dividends;HKD;2018-11-01;857;857;;;203.88;;26.01493019688;;
Open;Dividends;HKD;2018-11-21;5;5;;;626.32;;79.91794722832002;;
Open;Dividends;SGD;2018-05-16;F34;F34;;;490.0;;376.3622037;;
Open;Dividends;SGD;2018-08-31;F34;F34;;;245.0;;188.18110185;;
Open;Dividends;USD;2018-03-27;BHP;BHP;;;1320.0;;1320.0;;
Open;Dividends;USD;2018-03-30;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-04-05;SOUHY;SOUHY;;;73.0;;73.0;;
Open;Dividends;USD;2018-06-29;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-09-25;BHP;BHP;;;1512.0;;1512.0;;
Open;Dividends;USD;2018-09-28;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-10-11;SOUHY;SOUHY;;;62.0;;62.0;;
Open;Dividends;USD;2018-11-01;FCX;FCX;;;50.0;;50.0;;
Open;Dividends;USD;2018-12-21;NOV;NOV;;;15.0;;15.0;;
Open;Withholding Tax;HKD;2025-09-25;386;386;;;-19.34;;-2.46776903084;;
Open;Withholding Tax;HKD;2025-10-23;857;857;;;-48.11;;-6.1387987628600005;;
Open;Withholding Tax;HKD;2025-10-24;2318;2318;;;-52.02;;-6.637711736520001;;
Open;Withholding Tax;HKD;2025-12-23;390;390;;;-89.85;;-11.4647904561;;
Open;Dividends;HKD;2025-09-16;215;215;;;1140.0;;145.46311764;;
Open;Dividends;HKD;2025-09-23;1299;1299;;;294.0;;37.514172444;;
Open;Dividends;HKD;2025-09-25;386;386;;;193.4;;24.677690308400003;;
Open;Dividends;HKD;2025-09-26;1883;1883;;;600.0;;76.5595356;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;26.5;;3.3813794890000004;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;173.5;;22.138465711000002;;
Open;Dividends;HKD;2025-09-30;3918;3918;;;159.42;;20.34186860892;;
Open;Dividends;HKD;2025-10-09;1972;1972;;;1960.0;;250.09448296000002;;
Open;Dividends;HKD;2025-10-23;857;857;;;482.1;;61.51558685460001;;
Open;Dividends;HKD;2025-10-24;2318;2318;;;520.24;;66.38222133424;;
Open;Dividends;HKD;2025-10-31;27;27;;;700.0;;89.31945820000001;;
Open;Dividends;HKD;2025-11-20;16;16;;;12600.0;;1607.7502476000002;;
Open;Dividends;HKD;2025-11-28;2800;2800;;;825.0;;105.26936145;;
Open;Dividends;HKD;2025-12-18;5;5;;;1866.53;;238.16778330578;;
Open;Dividends;HKD;2025-12-23;390;390;;;898.95;;114.70532421270002;;
Open;Dividends;CNH;2022-07-29;3188;3188;;;370.0;;55.11285272;;
Open;Dividends;HKD;2022-05-26;215;215;;;2605.0;;332.39598373;;
Open;Dividends;HKD;2022-05-31;2800;2800;;;40.0;;5.103969040000001;;
Open;Dividends;HKD;2022-06-13;1883;1883;;;1700.0;;216.91868420000003;;
Open;Dividends;HKD;2022-07-28;2828;2828;;;460.0;;58.695643960000005;;
Open;Dividends;HKD;2022-09-02;215;215;;;1140.0;;145.46311764;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;283.5;;36.174380571;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;76.5;;9.761340789;;
Open;Dividends;HKD;2022-09-30;1883;1883;;;600.0;;76.5595356;;
Open;Dividends;HKD;2022-11-30;2800;2800;;;280.0;;35.727783280000004;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;243.6;;36.285110601599996;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;56.4;;8.4009861984;;
Open;Dividends;HKD;2021-05-26;215;215;;;2605.0;;332.39598373;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;12.22;;1.5592625417200001;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;52.78;;6.734687148280001;;
Open;Dividends;HKD;2021-06-04;1883;1883;;;1600.0;;204.15876160000002;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;8.8;;1.1228731888;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;311.2;;39.7088791312;;
Open;Dividends;HKD;2021-09-03;215;215;;;1109.72;;141.59941307672003;;
Open;Dividends;HKD;2021-09-03;215;215;;;30.28;;3.8637045632800007;;
Open;Dividends;HKD;2021-09-03;215;215;;;9637.06;;1229.68139691556;;
Open;Dividends;HKD;2021-09-03;215;215;;;262.94;;33.550940484440005;;
Open;Dividends;HKD;2021-09-29;1883;1883;;;550.0;;70.1795743;;
Open;Dividends;HKD;2021-09-29;2828;2828;;;396.0;;50.529293496;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;234.9;;29.973058187400003;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;35.1;;4.4787328326;;
Open;Trades;HKD;2020-09-24;1883;1883;Stocks;2.45;-24500.0;10000.0;-3126.1810370000003;-45.2615;O
Open;Trades;HKD;2020-09-02;215;215;Stocks;1.21;-60500.0;50000.0;-7719.753173000001;-111.0335;O
Open;Transaction Fees;HKD;2020-09-02;215;215;Stocks;1.21;-62.6335;0.0;-7.991986121671;;
Open;Transaction Fees;HKD;2020-09-24;1883;1883;Stocks;2.45;-25.6615;0.0;-3.2743875379990004;;
Open;Dividends;CNH;2020-07-31;3188;3188;;;380.0;;56.60238928;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;9.0;;1.1483930340000001;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;591.0;;75.41114256600001;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;2.64;;0.33686195664;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;327.36;;41.77088262336;;
Open;Trades;USD;2026-11-13;TSM;TSM 01/15/2027 390.00 P;Equity and Index Options;23.63;2362.29;-1.0;2362.29;-0.71;O
Open;Trades;USD;2026-11-13;NVDA;NVDA 01/15/2027 150.00 P;Equity and Index Options;9.55;954.32;-1.0;954.32;-0.68;O
Open;Trades;USD;2026-11-12;CPRT;CPRT 12/18/2026 27.50 P;Equity and Index Options;0.25;-256.61;10.0;-256.61;-6.61;C
Open;Trades;USD;2026-09-24;CPRT;CPRT 12/18/2026 27.50 P;Equity and Index Options;1.45;1443.32;-10.0;1443.32;-6.68;O
Open;Trades;USD;2026-09-18;CPRT;CPRT 09/18/2026 25.00 P;Equity and Index Options;0.0;0.0;5.0;0.0;;"Ep;C"
Open;Trades;USD;2026-09-18;CPRT;CPRT 09/18/2026 27.50 P;Equity and Index Options;0.0;0.0;10.0;0.0;;"Ep;C"
Open;Trades;USD;2026-09-18;MSFT;MSFT 09/18/2026 520.00 C;Equity and Index Options;0.0;0.0;1.0;0.0;;"Ep;C"
Open;Withholding Tax;USD;2026-09-18;IVV;IVV;Stocks;;-6.61;;-6.61;;
Open;Dividends;USD;2026-09-18;IVV;IVV;Stocks;;22.03;;22.03;;
Open;Withholding Tax;USD;2026-09-10;MSFT;MSFT;Stocks;;-27.3;;-27.3;;
Open;Dividends;USD;2026-09-10;MSFT;MSFT;Stocks;;91.0;;91.0;;
Open;Trades;USD;2026-08-06;CPRT;CPRT 09/18/2026 27.50 P;Equity and Index Options;1.0;993.32;-10.0;993.32;-6.68;O
Open;Trades;USD;2026-08-04;MSFT;MSFT 09/18/2026 520.00 C;Equity and Index Options;13.5;1349.31;-1.0;1349.31;-0.69;O
Open;Trades;USD;2026-08-04;CPRT;CPRT 09/18/2026 25.00 P;Equity and Index Options;0.35;171.66;-5.0;171.66;-3.34;O
Open;Trades;USD;2026-08-04;MSFT;MSFT 08/21/2026 355.00 P;Equity and Index Options;0.1;-10.66;1.0;-10.66;-0.66;C
Open;Trades;USD;2026-07-23;MSFT;MSFT 08/21/2026 355.00 P;Equity and Index Options;8.45;844.32;-1.0;844.32;-0.68;O
Open;Withholding Tax;USD;2026-06-18;IVV;IVV;Stocks;;-5.99;;-5.99;;
Open;Dividends;USD;2026-06-18;IVV;IVV;Stocks;;19.96;;19.96;;
Open;Withholding Tax;USD;2026-06-11;MSFT;MSFT;Stocks;;-27.3;;-27.3;;
Open;Dividends;USD;2026-06-11;MSFT;MSFT;Stocks;;91.0;;91.0;;

---

# [open_positions] START  
Symbol;Account;Asset Category;Description;Currency;Current Price;Cost Price;Quantity;Value;Value in USD;Unrealized Gain in USD;Weight
912797TV9 - United States Treasury B 02/18/27;IBKR;Treasury Bills;;USD;99.021744;97.064044118;680.0;67334.79;67334.79;1331.23591976;0.027502027918640183
912797UE5 - United States Treasury B 04/15/27;IBKR;Treasury Bills;;USD;98.33103;96.90631;1000.0;98331.03;98331.03;1424.7199999999934;0.040162043014148344
912797UX3 - United States Treasury B 05/13/27;IBKR;Treasury Bills;;USD;97.975521;96.625351852;1080.0;105813.56;105813.56;1458.1826798400066;0.04321818604157982
912797VF1 - United States Treasury B 06/10/27;IBKR;Treasury Bills;;USD;97.670694;96.38057197;2640.0;257850.63;257850.63;3405.922159199979;0.10531576953160411
912797VQ7 - United States Treasury B 07/08/27;IBKR;Treasury Bills;;USD;97.332937;96.420521978;1820.0;177145.95;177145.95;1660.5953400400085;0.07235298220391033
912797VR5 - United States Treasury B 08/05/27;IBKR;Treasury Bills;;USD;96.976931;96.29921;1000.0;96976.93;96976.93;677.7209999999911;0.03960897830562796
AMZN;IBKR;Stocks;AMAZON.COM INC;USD;252.29;207.8670155;300.0;75687.0;75687.0;13326.895349999995;0.03091338054337319
BABA;IBKR;Stocks;ALIBABA GROUP HOLDING-SP ADR;USD;84.86;111.16845686;500.0;42430.0;42430.0;-13154.228430000003;0.017329987137227325
BABA 15JAN27 100 C;IBKR;Equity and Index Options;;USD;1.395;2.72447076;-1.0;-139.5;-139.5;132.947076;-5.697697868591119e-05
BABA 15JAN27 110 C;IBKR;Equity and Index Options;;USD;0.505;1.06450496;-1.0;-50.5;-50.5;55.95049600000001;-2.0626074721423048e-05
BABA 15JAN27 75 P;IBKR;Equity and Index Options;;USD;2.095;2.38447777;-1.0;-209.5;-209.5;28.947776999999995;-8.556757730966591e-05
BABA 15JAN27 90 C;IBKR;Equity and Index Options;;USD;3.775;6.005473;1.0;377.5;377.5;-223.04730000000004;0.00015418501400667724
BABA 18DEC26 90 P;IBKR;Equity and Index Options;;USD;7.106;3.44559593;-2.0;-1421.2;-1421.2;-732.0808139999999;-0.0005804708394868602
BHP;IBKR;Stocks;BHP GROUP LTD-SPON ADR;USD;69.61;35.102226747;1200.0;83532.0;83532.0;41409.327903599995;0.034117569774849706
BHP 18DEC26 95 C;IBKR;Equity and Index Options;;USD;0.0093;4.0005117;-4.0;-3.72;-3.72;1596.4846799999998;-1.5193860982909651e-06
BRK B;IBKR;Stocks;BERKSHIRE HATHAWAY INC-CL B;USD;577.21;469.0869395;100.0;57721.0;57721.0;10812.306050000001;0.023575399188024943
BRK B 18DEC26 510 P;IBKR;Equity and Index Options;;USD;0.5471;9.1593754;-1.0;-54.71;-54.71;861.22754;-2.2345595010080296e-05
BRK B 18DEC26 545 C;IBKR;Equity and Index Options;;USD;36.0532;18.66917949;-1.0;-3605.32;-3605.32;-1738.4020509999996;-0.001472546529002791
CASH - Schwab;Schwab;Forex;;USD;1.0;;1.0;;78478.7;;0.03205361446020085
CPRT;IBKR;Stocks;COPART INC;USD;32.35;27.171926655;1100.0;35585.0;35585.0;5695.880679500002;0.01453423502894731
DNOW;IBKR;Stocks;DNOW INC;USD;13.12;26.81106924;25.0;328.0;328.0;-342.276731;0.0001339673764084507
FAST;IBKR;Stocks;FASTENAL CO;USD;52.27;42.17242457;100.0;5227.0;5227.0;1009.7575430000006;0.0021349008429480845
FCX;IBKR;Stocks;FREEPORT-MCMORAN INC;USD;47.73;11.2079574;500.0;23865.0;23865.0;18261.0213;0.009747351945084377
Forex;IBKR;Forex;;USD;0.0;;-1.0;;-64245.416188908;0.0;-0.026240212966759175
GEHC 17JUN27 57.5 P;IBKR;Equity and Index Options;;USD;1.4407;5.33945409;-1.0;-144.07;-144.07;389.875409;-5.884353633891917e-05
GEHC 17JUN27 65 C;IBKR;Equity and Index Options;;USD;20.9449;8.8804028;1.0;2094.49;2094.49;1206.44972;0.0008554674701638288
GEHC 17JUN27 80 C;IBKR;Equity and Index Options;;USD;10.1431;3.91948334;-1.0;-1014.31;-1014.31;-622.361666;-0.00041428185842943784
GLD;IBKR;Stocks;SPDR GOLD SHARES;USD;325.45;376.25228915;200.0;65090.0;65090.0;-10160.457830000007;0.026585172348859924
GLD 15JAN27 405 P;IBKR;Equity and Index Options;;USD;79.55;20.505473;1.0;7955.0;7955.0;5904.4527;0.0032491173150281257
GOOG;IBKR;Stocks;ALPHABET INC-CL C;USD;276.58;93.120965215;200.0;55316.0;55316.0;36691.80695699999;0.02259310790673737
GOOG 18DEC26 410 C;IBKR;Equity and Index Options;;USD;0.0215;32.055824165;-2.0;-4.3;-4.3;6406.864832999999;-1.7562796297449326e-06
GOOG 21JAN28 250 C;IBKR;Equity and Index Options;;USD;66.1333;132.321833;2.0;13226.66;13226.66;-13237.706599999998;0.005402258959898165
GOOG 21JAN28 250 P;IBKR;Equity and Index Options;;USD;27.0014;13.467856515;-2.0;-5400.28;-5400.28;-2706.708697;-0.0022056748276555733
H78;IBKR;Stocks;HONGKONG LAND HOLDINGS LTD;USD;8.35;4.208;1000.0;8350.0;8350.0;4141.999999999999;0.0034104499786907416
ICE;IBKR;Stocks;INTERCONTINENTAL EXCHANGE IN;USD;174.89;152.14704663;300.0;52467.0;52467.0;6822.886010999994;0.021429470542750555
IVV;IBKR;Stocks;ISHARES CORE S&P 500 ETF;USD;721.98;402.65670378;100.0;72198.0;72198.0;31932.329622000005;0.029488343420540616
IVV;Schwab;Stocks;ISHARES CORE S&P 500 ETF;USD;693.71;455.0;10.0;6937.1;6937.1;2387.1000000000004;0.0028333691673264124
LULU 15JAN27 85 P;IBKR;Equity and Index Options;;USD;1.3501;4.59553934;-2.0;-270.02;-270.02;649.087868;-0.00011028619200551784
MCD 18DEC26 245 P;IBKR;Equity and Index Options;;USD;0.2672;6.4943602;-1.0;-26.72;-26.72;622.7160200000001;-1.0913439931810372e-05
MCD 18DEC26 275 C;IBKR;Equity and Index Options;;USD;8.9135;6.505473;1.0;891.35;891.35;240.80270000000007;0.0003640604297611967
META;IBKR;Stocks;META PLATFORMS INC-CLASS A;USD;589.82;230.10913779;200.0;117964.0;117964.0;71942.17244200001;0.048180876800751456
META 15JAN27 460 P;IBKR;Equity and Index Options;;USD;4.8;19.71258137;-1.0;-480.0;-480.0;1491.2581369999998;-0.00019604981913431808
META 15JAN27 520 P;IBKR;Equity and Index Options;;USD;13.0522;62.7404795;1.0;1305.22;1305.22;-4968.82795;0.0005331003019385305
META 15JAN27 640 P;IBKR;Equity and Index Options;;USD;65.6103;77.2419796;-1.0;-6561.03;-6561.03;1163.1679599999998;-0.002679768218405906
META 15JAN27 800 C;IBKR;Equity and Index Options;;USD;0.7546;35.535755475;-2.0;-150.92;-150.92;6956.231095;-6.164133063281517e-05
MSFT;Schwab;Stocks;MICROSOFT CORP;USD;484.52;470.3566;100.0;48452.0;48452.0;1416.3399999999967;0.019789595493116624
MSFT 15JAN27 420 P;IBKR;Equity and Index Options;;USD;4.679;53.1437823;-2.0;-935.8;-935.8;9692.95646;-0.0003822154598872809
MU 15JAN27 700 P;IBKR;Equity and Index Options;;USD;116.6951;55.29338782;-1.0;-11669.51;-11669.51;-6140.1712179999995;-0.004766261093512742
MU 15JAN27 760 C;IBKR;Equity and Index Options;;USD;33.3362;86.755473;1.0;3333.62;3333.62;-5341.927299999999;0.0013615741626303028
MU 15JAN27 800 C;IBKR;Equity and Index Options;;USD;24.6196;68.94310663;-1.0;-2461.96;-2461.96;4432.350663;-0.0010055558598248452
NBIS 18DEC26 140 P;IBKR;Equity and Index Options;;USD;53.603;24.136075115;-2.0;-10720.6;-10720.6;-5893.384977000001;-0.004378691022940355
NOV;IBKR;Stocks;NOV INC;USD;16.58;62.158401437;300.0;4974.0;4974.0;-13673.520431100002;0.002031566250779371
NVDA;Moomoo-mom;Stocks;NVIDIA;USD;223.96;0.0;0.4717;105.641932;105.641932;105.64193200000001;4.314808679499985e-05
NVDA;Moomoo-me;Stocks;NVIDIA;USD;223.96;0.0;1.4431;323.196676;323.196676;323.196676;0.00013200552057211
NVDA 01/15/2027 150.00 P;Schwab;Equity and Index Options;PUT NVIDIA CORP $150 EXP 01/15/27;USD;28.146;9.5432;-1.0;-2814.6;-2814.6;-1860.2800000000002;-0.0011495871269488576
NVDA 15JAN27 145 P;IBKR;Equity and Index Options;;USD;9.4;7.59437044;-1.0;-940.0;-940.0;-180.56295600000007;-0.00038393089580470623
NVDA 15JAN27 165 C;IBKR;Equity and Index Options;;USD;10.25;12.435473;1.0;1025.0;1025.0;-218.5473;0.0004186480512764084
NVDA 15JAN27 185 C;IBKR;Equity and Index Options;;USD;4.525;5.84440649;-1.0;-452.5;-452.5;131.94064899999995;-0.00018481779824641443
NVO;IBKR;Stocks;NOVO-NORDISK A/S-SPONS ADR;USD;40.31;58.2644644;200.0;8062.0;8062.0;-3590.89288;0.0032928200872101507
PDD;IBKR;Stocks;PDD HOLDINGS INC;USD;87.22;96.7605204;100.0;8722.0;8722.0;-954.0520400000005;0.003562388588519838
PDD 18DEC26 120 C;IBKR;Equity and Index Options;;USD;0.0013;5.8261019;-2.0;-0.26;-0.26;1164.9603800000002;-1.0619365203108895e-07
PDD 18DEC26 90 P;IBKR;Equity and Index Options;;USD;5.1838;8.553596777;-4.0;-2073.52;-2073.52;1347.9187107999999;-0.0008469025436903983
PLTR 18DEC26 180 P;IBKR;Equity and Index Options;;USD;21.2654;10.19781688;-2.0;-4253.08;-4253.08;-2213.516624;-0.0017371157599245532
PLTR 18DEC26 195 P;IBKR;Equity and Index Options;;USD;34.2952;16.655473;1.0;3429.52;3429.52;1763.9727;0.001400743282744847
SKHY 15JAN27 125 P;IBKR;Equity and Index Options;;USD;21.6288;11.39193216;-1.0;-2162.88;-2162.88;-1023.6867839999999;-0.0008834004850192372
SKHY 18DEC26 115 P;IBKR;Equity and Index Options;;USD;12.1141;16.440827835;-2.0;-2422.82;-2422.82;865.345567;-0.0009895696308229345
SKHY 18DEC26 130 P;IBKR;Equity and Index Options;;USD;22.2543;24.4323006;1.0;2225.43;2225.43;-217.8000600000001;0.000908948227075178
SMCI 18DEC26 24 P;IBKR;Equity and Index Options;;USD;3.0106;2.96300312;-4.0;-1204.24;-1204.24;-19.03875199999998;-0.0004918563212381483
SMCI 18DEC26 27 P;IBKR;Equity and Index Options;;USD;5.1323;4.3169028;2.0;1026.46;1026.46;163.07943999999992;0.0004192443694762753
SOUHY;IBKR;Stocks;SOUTH32 - ADR;USD;13.71;7.17;300.0;4113.0;4113.0;1962.0000000000002;0.001679901887707188
TSM;IBKR;Stocks;TAIWAN SEMICONDUCTOR-SP ADR;USD;349.2;181.60807492;300.0;104760.0;104760.0;50277.57752399999;0.04278787302606492
TSM 01/15/2027 390.00 P;Schwab;Equity and Index Options;PUT TAIWAN SEMICONDUCTOR$390 EXP 01/15/27;USD;75.5268;23.6229;-1.0;-7552.68;-7552.68;-5190.389999999999;-0.0030847948916237116
TSM 19MAR27 310 P;IBKR;Equity and Index Options;;USD;20.625;17.84415929;-1.0;-2062.5;-2062.5;-278.084071;-0.0008424015665927729
TSM 19MAR27 360 C;IBKR;Equity and Index Options;;USD;34.975;38.055473;1.0;3497.5;3497.5;-308.0472999999998;0.0014285088383797447
TSM 19MAR27 400 C;IBKR;Equity and Index Options;;USD;19.25;21.19409028;-1.0;-1925.0;-1925.0;194.4090280000001;-0.0007862414621532548
UNH;IBKR;Stocks;UNITEDHEALTH GROUP INC;USD;341.88;322.13551065;200.0;68376.0;68376.0;3948.897869999996;0.02792729673568361
UNH 15JAN27 360 P;IBKR;Equity and Index Options;;USD;29.163;12.18938278;-1.0;-2916.3;-2916.3;-1697.361722;-0.0011911251823779412
UNH 17JUN27 360 P;IBKR;Equity and Index Options;;USD;45.3671;21.4891912;-1.0;-4536.71;-4536.71;-2387.79088;-0.0018529607811767752
UNH 18DEC26 440 C;IBKR;Equity and Index Options;;USD;0.1494;26.01244859;-1.0;-14.94;-14.94;2586.3048590000003;-6.10205062055565e-06
9CI;IBKR;Stocks;CAPITALAND INVESTMENT LTD/SI;SGD;2.26;2.323057932;1200.0;2712.0;2083.0495845600003;-58.120707546820185;0.0008507947797933426
BS6;IBKR;Stocks;YANGZIJIANG SHIPBUILDING;SGD;4.27;0.171019587;7000.0;29890.0;22958.0944257;22038.5900165688;0.00937693804130642
BS6;Moomoo-me;Stocks;YZJ Shipbuilding;SGD;4.2;4.015;200.0;840.0;652.6800000000001;28.419186810000078;0.000266578741567889
C38U;IBKR;Stocks;CAPITALAND INTEGRATED COMMER;SGD;2.29;1.362308617;222.0;508.38;390.4796267694;158.18540829302555;0.0001594863754245352
CGP1;IBKR;Stocks;NOBLE GROUP LTD;SGD;0.0;85.85;50.0;0.0;0.0;-3297.009713025;0.0
D05;IBKR;Stocks;DBS GROUP HOLDINGS LTD;SGD;66.24;18.388145455;220.0;14572.8;11193.165555264;8085.956069574231;0.004571704338854138
F34;IBKR;Stocks;WILMAR INTERNATIONAL LTD;SGD;2.82;3.040549529;9000.0;25380.0;19494.0259794;-1524.6093078263973;0.007962083890543892
CASH - DBS;DBS;Forex;;SGD;0.763247271;;-0.777;;1043.95389;18.47775410253007;0.00042638952358139124
CASH - Moomoo-me;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;75899.04609;1343.3964121419413;0.030999988038357858
CASH - Moomoo-mom;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;7040.27268;124.6112770323598;0.002875508719938085
CASH - OCBC;OCBC;Forex;;SGD;0.763247271;;-0.777;;6154.438290000001;108.93220328133066;0.0025137010700579626
CASH - OCBC Joint;OCBC Joint;Forex;;SGD;0.763247271;;-0.777;;30062.78268;532.1046373023601;0.012278756473100646
CASH - POSB;POSB;Forex;;SGD;0.763247271;;-0.777;;6713.0158200000005;118.81890263214063;0.002741844869493367
CASH - UOB;UOB;Forex;;SGD;0.763247271;;-0.777;;6898.25262;122.09755322574074;0.0028175024551954915
Forex;IBKR;Forex;;SGD;0.76808613;;-0.77558;;14913.106822;144.094584;0.0060910664482997185
Fullerton SGD Cash Fund;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0
Fullerton SGD Cash Fund;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0
HMN;IBKR;Stocks;CAPITALAND ASCOTT TRUST;SGD;0.81;0.505252956;68.0;55.08;42.3061840404;15.916894480533186;1.7279416102882485e-05
O39;IBKR;Stocks;OVERSEA-CHINESE BANKING CORP;SGD;26.62;8.7231;800.0;21296.0;16357.16222448;10997.088527997603;0.0066808722826250085
P40U;Moomoo-mom;Stocks;StarhillGbl Reit;SGD;0.53;0.555;1200.0;636.0;494.172;-23.04258390000002;0.00020183819004425881
P40U;Moomoo-me;Stocks;StarhillGbl Reit;SGD;0.53;0.5448;4200.0;2226.0;1729.602;-47.74423384079976;0.0007064336651549059
Z74;Moomoo-mom;Stocks;Singtel;SGD;4.3;4.29;100.0;430.0;334.11;0.7680861299999837;0.00013646292723118128
Z74;Moomoo-me;Stocks;Singtel;SGD;4.3;4.88;100.0;430.0;334.11;-44.54899554000001;0.00013646292723118128
Z77;Moomoo-me;Stocks;Singtel 10;SGD;4.29;4.28;120.0;514.8;399.9996;0.9217033559999804;0.00016337468590374912
1299;IBKR;Stocks;AIA GROUP LTD;HKD;74.0;55.302399867;600.0;44400.0;5665.4056344;1431.4795830169785;0.002313961979055555
1398;IBKR;Stocks;IND & COMM BK OF CHINA - H;HKD;7.685;5.051969413;6000.0;46110.0;5883.600310860001;2015.8359896131537;0.0024030807850056683
14;IBKR;Stocks;HYSAN DEVELOPMENT CO;HKD;15.28;28.5525695;1000.0;15280.0;1949.7161732800002;-1693.5695952312074;0.0007963364648641642
16;IBKR;Stocks;SUN HUNG KAI PROPERTIES;HKD;108.5;88.851861372;4500.0;488250.0;62300.32209450001;11281.89276573076;0.02544576433049268
1810;IBKR;Stocks;XIAOMI CORP-CLASS B;HKD;26.5;38.85377485;600.0;15900.0;2028.8276934000003;-945.7992654229598;0.0008286485465536787
1883;IBKR;Stocks;CITIC TELECOM INTERNATIONAL;HKD;2.335;2.45452615;10000.0;23350.0;2979.4419271;-152.514442267599;0.0012169146894357482
1972;IBKR;Stocks;SWIRE PROPERTIES LTD;HKD;24.96;19.720971466;5600.0;139776.0;17835.309413376002;3743.577521209762;0.007284602468118679
200;IBKR;Stocks;MELCO INTERNATIONAL DEVELOP.;HKD;1.875;11.460065577;4000.0;7500.0;956.9941950000001;-4892.187795137774;0.00039087195592154656
215;IBKR;Stocks;HUTCHISON TELECOMM HONG KONG;HKD;1.08;1.21222067;50000.0;54000.0;6890.358204;-843.5627576600707;0.0028142780826351348
2318;IBKR;Stocks;PING AN INSURANCE GROUP CO-H;HKD;50.2;86.4593701;500.0;25100.0;3202.7405726;-2313.3337800037716;0.0013081181458174424
27;IBKR;Stocks;GALAXY ENTERTAINMENT GROUP L;HKD;32.3;51.5945905;1000.0;32300.0;4121.4549998;-2461.9748137869537;0.001683355223502127
2800;IBKR;Stocks;TRACKER FUND OF HONG KONG-B;HKD;22.34;21.769917967;1500.0;33510.0;4275.850063260001;109.1130392509594;0.00174641589905747
2828;IBKR;Stocks;HANGSENG CHINA ENT INDEX;HKD;76.44;92.44076825;400.0;30576.0;3901.4739341760005;-816.6742576421502;0.001593506789900961
2888;IBKR;Stocks;STANDARD CHARTERED PLC;HKD;205.8;55.6;28.0;5762.4;735.2777799024;536.6313048656001;0.00030031474117364257
3188;IBKR;Stocks;CHINAAMC ETF SERIES - CH;HKD;48.4;26.37987258;1000.0;48400.0;6175.8025384;2809.751215213377;0.0025224270222137133
3690;IBKR;Stocks;MEITUAN-CLASS B;HKD;64.2;231.697888617;60.0;3852.0;491.51221855200004;-1282.3560566498047;0.0002007518365613063
386;IBKR;Stocks;CHINA PETROLEUM & CHEMICAL-H;HKD;4.49;5.815174;2000.0;8980.0;1145.84104948;-338.1823534306479;0.000468004021890065
390;IBKR;Stocks;CHINA RAILWAY GROUP LTD-H;HKD;2.705;3.92724184;10000.0;27050.0;3451.5590633;-1559.5711276881586;0.0014097448543570444
3918;IBKR;Stocks;NAGACORP LTD;HKD;1.93;7.594013956;2036.0;3929.48;501.39860658248006;-1471.4656503637932;0.00020478980444727982
3968;IBKR;Stocks;CHINA MERCHANTS BANK - H;HKD;53.25;39.7833745;500.0;26625.0;3397.32939225;859.1654953159316;0.00138759544352149
5;IBKR;Stocks;HSBC HOLDINGS PLC;HKD;130.6;49.092166333;2400.0;313440.0;39994.70139744;24960.807573230264;0.01633532078187327
66;IBKR;Stocks;MTR CORP;HKD;31.86;40.07708;500.0;15930.0;2032.6556701800002;-524.2465239900403;0.0008302120343773649
700;IBKR;Stocks;TENCENT HOLDINGS LTD;HKD;341.6;452.856419642;706.0;241169.6;30773.0542947296;-10022.54052189838;0.012568857768108933
808;IBKR;Stocks;PROSPERITY REIT;HKD;1.335;2.339531045;20000.0;26700.0;3406.8993342000003;-2563.5476766994243;0.0013915041630807056
857;IBKR;Stocks;PETROCHINA CO LTD-H;HKD;9.57;7.7972334;2000.0;19140.0;2442.24918564;452.4072920773034;0.0009975052315117868
939;IBKR;Stocks;CHINA CONSTRUCTION BANK-H;HKD;10.25;6.39177626;4000.0;41000.0;5231.568266;1969.2254518353009;0.002136766692371121
9618;IBKR;Stocks;JD.COM INC-CLASS A;HKD;97.05;285.565311947;19.0;1843.95;235.2865927827;-457.0337499781293;9.609977908287143e-05
CASH - Citic;Citic;Forex;;HKD;0.128244528;;-0.1276;;56493.2093;-285.35623200400005;0.023073923886629533
CASH - StanChart;StanChart;Forex;;HKD;0.128244528;;-0.1276;;1614.1055479999998;-8.153105177440239;0.0006592606265606235
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;127600.0;-644.5280000000057;0.05211657691987289
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;1276.0;-6.445279999999912;0.0005211657691987289
CASH - StanChart Payroll;StanChart Payroll;Forex;;HKD;0.128244528;;-0.1276;;14971.866888;-75.62529327264019;0.006115066240615595
Forex;IBKR;Forex;;HKD;0.127599226;;-0.12753;;4924.753654;-2.673261;0.0020114522148911916
BARCl;IBKR;Stocks;;GBP;4.013;3.5479;1000.0;4013.0;5331.50578219;617.9126188130001;0.0021775848839831677
Forex;IBKR;Forex;;GBP;1.32855863;;-1.3115;;3952.954641;-51.41593;0.0016145334216962775
IMB 18DEC26 30 C;IBKR;Equity and Index Options;;GBP;0.12;0.8986;-1.0;-120.0;-159.4270356;1034.4157493179998;-6.511591978020936e-05
IMBl;IBKR;Stocks;IMPERIAL BRANDS PLC;GBP;28.56;29.95515;1000.0;28560.0;37943.634472800004;-1853.5385726445015;0.015497588907689827
RR. 15JAN27 11 P;IBKR;Equity and Index Options;;GBP;0.07;0.4636;-1.0;-70.0;-92.99910410000001;522.920676768;-3.798428653845546e-05
RRl;IBKR;Stocks;ROLLS-ROYCE HOLDINGS PLC;GBP;13.024;11.090386;100.0;1302.4;1730.3147597120003;256.89195667888185;0.0007067247826812056
Forex;IBKR;Forex;;EUR;1.100301282;;-1.0965;;545.834635;-1.892268;0.00022293912806040943
MC;IBKR;Stocks;LVMH MOET HENNESSY LOUIS VUI;EUR;394.7;397.5114;110.0;43417.0;47771.780760593996;-340.27257266362705;0.019511768703830725
MC 18DEC26 440 P;IBKR;Equity and Index Options;;EUR;50.3693;37.9386;-1.0;-5036.93;-5542.14053634426;-1367.7515146157402;-0.002263615937015135
RI;IBKR;Stocks;PERNOD-RICARD SA;EUR;72.08;83.7114;100.0;7208.0;7930.971640656;-1279.8044331454798;0.0032393032410625304
RMS;IBKR;Stocks;HERMES INTL;EUR;1251.0;1581.624621304;23.0;28773.0;31658.968786986;-8367.093977675779;0.012930698134724222
RMS 17DEC27 1520 P;IBKR;Equity and Index Options;;EUR;328.814;193.044;5.0;16440.7;18089.7232869774;7469.395252857;0.0073885145387537115
RMS 17DEC27 1680 P;IBKR;Equity and Index Options;;EUR;457.4289;214.886;-5.0;-22871.45;-25165.4857561989;-13343.51319049989;-0.010278518606104275
RMS 17DEC27 1920 P;IBKR;Equity and Index Options;;EUR;673.5299;388.7186;-1.0;-67352.99;-74108.58124353319;-31337.823851808662;-0.030268695727282496
RMS 18DEC26 1520 P;IBKR;Equity and Index Options;;EUR;269.0;130.209;5.0;13450.0;14799.0522429;7635.595761503099;0.006044482324124727
RMS 18DEC26 1600 P;IBKR;Equity and Index Options;;EUR;349.0;110.436;-3.0;-10470.0;-11520.15442254;-7874.76825117144;-0.004705258731121628
RMS 18JUN27 1360 P;IBKR;Equity and Index Options;;EUR;188.6359;108.0235;-10.0;-18863.59;-20755.63226012238;-8869.79270650968;-0.00847737073044877
RMS 18JUN27 1440 P;IBKR;Equity and Index Options;;EUR;243.6624;146.389;5.0;12183.12;13405.10255475984;5351.502336224939;0.0054751415236201076
SAP;IBKR;Stocks;SAP SE;EUR;190.08;210.1117;100.0;19008.0;20914.526768256;-2204.09051906394;0.008542269146242589
SAP 18DEC26 190 P;IBKR;Equity and Index Options;;EUR;6.4832;31.7783;-2.0;-1296.64;-1426.6946542924802;5566.44619166364;-0.00058271506027904
Forex;IBKR;Forex;;CNH;0.148953656;;-0.15004;;976.7604;7.0721;0.0003989452078282587

---

# [pivot_summary] START  
# PORTFOLIO PIVOT SUMMARY

**Report Date :** 2026-11-25  
**Filter      :** Open Positions | Forex Balances | Cash Balances  
**Generated   :** 2026-11-25 21:45:01  

---

## Equity and Index Options

### Schwab

```
             Amount  Quantity  Value in USD
Underlying                                 
NVDA       -2814.60       1.0      -2814.60
TSM        -7552.68       1.0      -7552.68
```

> **SUBTOTAL [Schwab]**  Amount: `     -10,367.28`  Quantity: `           2.00`  Value in USD: `     -10,367.28`

### IBKR

```
              Amount  Quantity  Value in USD
Underlying                                  
BABA        -1443.20       4.0      -1443.20
BHP            -3.72       4.0         -3.72
BRK         -3660.03       2.0      -3660.03
GEHC          936.11       1.0        936.11
GLD          7955.00      -1.0       7955.00
GOOG         7822.08       2.0       7822.08
IMB          -120.00       1.0       -159.43
LULU         -270.02       2.0       -270.02
MC          -5036.93       1.0      -5542.14
MCD           864.63       0.0        864.63
META        -5886.73       3.0      -5886.73
MSFT         -935.80       2.0       -935.80
MU         -10797.85       1.0     -10797.85
NBIS       -10720.60       2.0     -10720.60
NVDA         -367.50       1.0       -367.50
PDD         -2073.78       6.0      -2073.78
PLTR         -823.56       1.0       -823.56
RMS        -77484.21       4.0     -85255.98
RR.           -70.00       1.0        -93.00
SAP         -1296.64       2.0      -1426.69
SKHY        -2360.27       2.0      -2360.27
SMCI         -177.78       2.0       -177.78
TSM          -490.00       1.0       -490.00
UNH         -7467.95       3.0      -7467.95
```

> **SUBTOTAL [IBKR]**  Amount: `    -113,908.75`  Quantity: `          47.00`  Value in USD: `    -122,378.21`

> **SUBTOTAL [Equity and Index Options]**  Amount: `    -124,276.03`  Quantity: `          49.00`  Value in USD: `    -132,745.49`

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
CASH - Schwab  78478.7      -1.0       78478.7
```

> **SUBTOTAL [Schwab]**  Amount: `      78,478.70`  Quantity: `          -1.00`  Value in USD: `      78,478.70`

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
Forex       3621.21      4.46     -38932.01
```

> **SUBTOTAL [IBKR]**  Amount: `       3,621.21`  Quantity: `           4.46`  Value in USD: `     -38,932.01`

### UOB

```
             Amount  Quantity  Value in USD
Underlying                                 
Forex       8878.06      0.78       6898.25
```

> **SUBTOTAL [UOB]**  Amount: `       8,878.06`  Quantity: `           0.78`  Value in USD: `       6,898.25`

> **SUBTOTAL [Forex]**  Amount: `   1,837,036.68`  Quantity: `          11.09`  Value in USD: `     375,313.64`

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
IVV          6937.1     -10.0        6937.1
MSFT        48452.0    -100.0       48452.0
```

> **SUBTOTAL [Schwab]**  Amount: `      55,389.10`  Quantity: `        -110.00`  Value in USD: `      55,389.10`

### IBKR

```
               Amount  Quantity  Value in USD
Underlying                                   
1299         44400.00    -600.0       5665.41
1398         46110.00   -6000.0       5883.60
14           15280.00   -1000.0       1949.72
16          488250.00   -4500.0      62300.32
1810         15900.00    -600.0       2028.83
1883         23350.00  -10000.0       2979.44
1972        139776.00   -5600.0      17835.31
200           7500.00   -4000.0        956.99
215          54000.00  -50000.0       6890.36
2318         25100.00    -500.0       3202.74
27           32300.00   -1000.0       4121.45
2800         33510.00   -1500.0       4275.85
2828         30576.00    -400.0       3901.47
2888          5762.40     -28.0        735.28
3188         48400.00   -1000.0       6175.80
3690          3852.00     -60.0        491.51
386           8980.00   -2000.0       1145.84
390          27050.00  -10000.0       3451.56
3918          3929.48   -2036.0        501.40
3968         26625.00    -500.0       3397.33
5           313440.00   -2400.0      39994.70
66           15930.00    -500.0       2032.66
700         241169.60    -706.0      30773.05
808          26700.00  -20000.0       3406.90
857          19140.00   -2000.0       2442.25
939          41000.00   -4000.0       5231.57
9618          1843.95     -19.0        235.29
9CI           2712.00   -1200.0       2083.05
AMZN         75687.00    -300.0      75687.00
BABA         42430.00    -500.0      42430.00
BARCl         4013.00   -1000.0       5331.51
BHP          83532.00   -1200.0      83532.00
BRK          57721.00    -100.0      57721.00
BS6          29890.00   -7000.0      22958.09
C38U           508.38    -222.0        390.48
CGP              0.00     -50.0          0.00
CPRT         35585.00   -1100.0      35585.00
D05          14572.80    -220.0      11193.17
DNOW           328.00     -25.0        328.00
F34          25380.00   -9000.0      19494.03
FAST          5227.00    -100.0       5227.00
FCX          23865.00    -500.0      23865.00
GLD          65090.00    -200.0      65090.00
GOOG         55316.00    -200.0      55316.00
H78           8350.00   -1000.0       8350.00
HMN             55.08     -68.0         42.31
ICE          52467.00    -300.0      52467.00
IMB          28560.00   -1000.0      37943.63
IVV          72198.00    -100.0      72198.00
MC           43417.00    -110.0      47771.78
META        117964.00    -200.0     117964.00
NOV           4974.00    -300.0       4974.00
NVO           8062.00    -200.0       8062.00
O39          21296.00    -800.0      16357.16
PDD           8722.00    -100.0       8722.00
RI            7208.00    -100.0       7930.97
RMS          28773.00     -23.0      31658.97
RRl           1302.40    -100.0       1730.31
SAP          19008.00    -100.0      20914.53
SOUHY         4113.00    -300.0       4113.00
TSM         104760.00    -300.0     104760.00
UNH          68376.00    -200.0      68376.00
```

> **SUBTOTAL [IBKR]**  Amount: `   2,861,337.09`  Quantity: `    -159,167.00`  Value in USD: `   1,342,573.62`

> **SUBTOTAL [Stocks]**  Amount: `   2,922,231.83`  Quantity: `    -165,198.91`  Value in USD: `   1,402,336.23`

---

## Treasury Bills

### IBKR

```
               Amount  Quantity  Value in USD
Underlying                                   
912797TV9    67334.79  -68000.0      67334.79
912797UE5    98331.03 -100000.0      98331.03
912797UX3   105813.56 -108000.0     105813.56
912797VF1   257850.63 -264000.0     257850.63
912797VQ7   177145.95 -182000.0     177145.95
912797VR5    96976.93 -100000.0      96976.93
```

> **SUBTOTAL [IBKR]**  Amount: `     803,452.89`  Quantity: `    -822,000.00`  Value in USD: `     803,452.89`

> **SUBTOTAL [Treasury Bills]**  Amount: `     803,452.89`  Quantity: `    -822,000.00`  Value in USD: `     803,452.89`

---

> ### GRAND TOTAL  
> Amount: `   5,438,445.37`  Quantity: `    -987,138.82`  Value in USD: `   2,448,357.27`

---

