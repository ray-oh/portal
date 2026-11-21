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

- Current refresh timestamp: **2026-11-21 20:19:09**.


# [transaction_details] START  
Status;Table;Currency;Settle Date;Ticker;Symbol;Asset Category;Transaction Price;Amount;Quantity;Value in USD;Comm/Fee;Code
Open;Trades;EUR;2026-03-04;RI;RI;Stocks;93.0;-9300.0;100.0;-10204.6402671;0.0;"A;O"
Open;Trades;EUR;2026-11-17;RMS;RMS;Stocks;1600.0;-32000.0;20.0;-35112.740703999996;0.0;"A;O"
Open;Trades;EUR;2026-02-16;SAP;SAP;Stocks;220.0;-22000.0;100.0;-24140.009233999997;0.0;"A;O"
Open;Trades;HKD;2026-04-01;1810;1810;Stocks;31.8;-6360.0;200.0;-811.53107736;-25.18126;O
Open;Trades;HKD;2026-04-10;1810;1810;Stocks;31.0;-6200.0;200.0;-791.1152012000001;-25.1767;O
Open;Trades;HKD;2026-04-10;700;700;Stocks;502.0;-50200.0;100.0;-6405.4811452;-92.5907;O
Open;Trades;USD;2026-02-23;CPRT;CPRT;Stocks;45.0;-13500.0;300.0;-13500.0;0.0;"A;O"
Open;Trades;USD;2026-08-10;GOOG;GOOG;Stocks;352.84;70568.0;-200.0;70568.0;-2.4338153;"C;P"
Open;Trades;USD;2026-02-23;NVO;NVO;Stocks;65.0;-13000.0;200.0;-13000.0;0.0;"A;O"
Open;Trades;USD;2026-03-20;PDD;PDD;Stocks;100.0;-10000.0;100.0;-10000.0;0.0;"A;O"
Open;Trades;USD;2026-01-02;TSM;TSM;Stocks;317.21;63442.0;-200.0;63442.0;-0.7795145;"C;P"
Open;Trades;USD;2026-01-02;TSM;TSM;Stocks;318.5;63700.0;-200.0;63700.0;-0.1395145;C
Open;Trades;USD;2026-01-29;UNH;UNH;Stocks;350.0;-70000.0;200.0;-70000.0;0.0;"A;O"
Open;Trades;EUR;2026-03-30;RMS;RMS 18DEC26 1760 C;Equity and Index Options;130.095;6504.75;-5.0;7137.487502948249;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1760 C;Equity and Index Options;181.5;-9075.0;5.0;-9957.753809024998;-5.7;C
Open;Trades;EUR;2026-03-30;RMS;RMS 17DEC27 1760 C;Equity and Index Options;226.53;11326.5;-5.0;12428.264299495499;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 17DEC27 1760 C;Equity and Index Options;284.1;-14205.0;5.0;-15586.765053135;-5.7;C
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 440 C;Equity and Index Options;26.975;-2697.5;1.0;-2959.8943140324996;-1.14;O
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 450 C;Equity and Index Options;22.175;2217.5;-1.0;2433.2032034725;-1.14;O
Open;Trades;EUR;2026-02-18;SAP;SAP 17APR26 194 C;Equity and Index Options;2.2;220.0;-1.0;241.40009234;-1.17;O
Open;Trades;EUR;2026-04-17;SAP;SAP 17APR26 194 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2026-02-18;SAP;SAP 18SEP26 175 C;Equity and Index Options;15.79;-1579.0;1.0;-1732.5942991129998;-1.17;O
Open;Trades;EUR;2026-08-06;SAP;SAP 18SEP26 175 C;Equity and Index Options;7.5;750.0;-1.0;822.9548602499999;-1.17;C
Open;Trades;EUR;2026-02-18;SAP;SAP 18SEP26 195 C;Equity and Index Options;8.52;1704.0;-2.0;1869.7534424879998;-2.34;O
Open;Trades;EUR;2026-08-06;SAP;SAP 18SEP26 195 C;Equity and Index Options;2.2;-440.0;2.0;-482.80018468;-2.34;C
Open;Trades;EUR;2026-03-10;RMS;RMS 19JUN26 2040 P;Equity and Index Options;180.57;-18057.0;1.0;-19813.461215378997;-1.14;C
Open;Trades;EUR;2026-02-18;RMS;RMS 18DEC26 1480 P;Equity and Index Options;30.1;3010.0;-1.0;3302.7921724699995;-1.14;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1480 P;Equity and Index Options;73.05;-7305.0;1.0;-8015.580338835;-1.14;C
Open;Trades;EUR;2026-03-10;RMS;RMS 18DEC26 1960 P;Equity and Index Options;198.32;19832.0;-1.0;21761.121051304;-1.14;O
Open;Trades;EUR;2026-03-24;RMS;RMS 18DEC26 1960 P;Equity and Index Options;359.93;-35993.0;1.0;-39494.152379971;-1.14;C
Open;Trades;EUR;2026-03-24;RMS;RMS 17DEC27 1920 P;Equity and Index Options;388.73;38873.0;-1.0;42654.299043331;-1.14;O
Open;Trades;EUR;2026-03-30;RMS;RMS 18DEC26 1520 P;Equity and Index Options;130.095;-6504.75;5.0;-7137.487502948249;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1600 P;Equity and Index Options;110.55;5527.5;-5.0;6065.1773200425;-5.7;O
Open;Trades;EUR;2026-11-17;RMS;RMS 18DEC26 1600 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-09-04;RMS;RMS 18JUN27 1360 P;Equity and Index Options;108.1375;10813.75;-10.0;11865.637493371249;-11.4;O
Open;Trades;EUR;2026-09-04;RMS;RMS 18JUN27 1440 P;Equity and Index Options;146.275;-7313.75;5.0;-8025.181478871249;-5.7;O
Open;Trades;EUR;2026-03-30;RMS;RMS 17DEC27 1520 P;Equity and Index Options;192.93;-9646.5;5.0;-10584.845412535498;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 17DEC27 1680 P;Equity and Index Options;215.0;10750.0;-5.0;11795.686330249999;-5.7;O
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 410 P;Equity and Index Options;12.5;2500.0;-2.0;2743.1828674999997;-2.28;O
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 430 P;Equity and Index Options;20.2;-2020.0;1.0;-2216.49175694;-1.14;O
Open;Trades;EUR;2026-03-17;MC;MC 18DEC26 440 P;Equity and Index Options;37.95;3795.0;-1.0;4164.151592865;-1.14;O
Open;Trades;EUR;2026-03-04;RI;RI 20MAR26 93 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-02-16;SAP;SAP 20MAR26 220 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-03-10;SAP;SAP 20MAR26 240 P;Equity and Index Options;71.23;-7123.0;1.0;-7815.876626080999;-1.17;C
Open;Trades;EUR;2026-03-10;SAP;SAP 18DEC26 190 P;Equity and Index Options;31.79;6358.0;-2.0;6976.462668625999;-2.34;O
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
Open;Trades;USD;2026-07-17;GEHC;GEHC 17JUN27 65 C;Equity and Index Options;8.87;-887.0;1.0;-887.0;-1.04028;O
Open;Trades;USD;2026-07-17;GEHC;GEHC 17JUN27 80 C;Equity and Index Options;3.93;393.0;-1.0;393.0;-1.0516658;O
Open;Trades;USD;2026-07-17;GEHC;GEHC 17JUN27 57.5 P;Equity and Index Options;5.35;535.0;-1.0;535.0;-1.054591;O
Open;Trades;USD;2026-04-01;GLD;GLD 17APR26 425 P;Equity and Index Options;6.42;-642.0;1.0;-642.0;-0.69795;O
Open;Trades;USD;2026-04-17;GLD;GLD 17APR26 425 P;Equity and Index Options;0.0;0.0;-1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-04-01;GLD;GLD 17APR26 430 P;Equity and Index Options;7.94;794.0;-1.0;794.0;-0.70124;O
Open;Trades;USD;2026-04-17;GLD;GLD 17APR26 430 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-09-04;GLD;GLD 15JAN27 390 P;Equity and Index Options;13.75;2750.0;-2.0;2750.0;-0.45783;O
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
Open;Trades;USD;2026-05-12;PDD;PDD 20NOV26 130 C;Equity and Index Options;3.13;313.0;-1.0;313.0;-0.7079878;O
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
Open;Trades;USD;2026-02-20;912797TC1;912797TC1 3.55127091%;Treasury Bills;97.1272;-71874.13;74000.0;-71874.13;-5.0;O
Open;Trades;USD;2026-11-18;912797TC1;912797TC1 4.07189964%;Treasury Bills;99.6110625;34863.87;-35000.0;34863.87;-5.0;C
Open;Trades;USD;2026-04-17;912797TV9;912797TV9 3.64107797%;Treasury Bills;97.0566889;-65998.55;68000.0;-65998.55;-5.0;O
Open;Trades;USD;2026-06-12;912797UE5;912797UE5 3.8394375%;Treasury Bills;96.9013111;-96901.31;100000.0;-96901.31;-5.0;O
Open;Trades;USD;2026-06-12;912797UX3;912797UX3 3.815%;Treasury Bills;96.61841;-104347.88;108000.0;-104347.88;-7.5;O
Open;Trades;USD;2026-06-22;912797VF1;912797VF1 4.043%;Treasury Bills;96.212;-31749.96;33000.0;-31749.96;-12.5;"O;P"
Open;Trades;USD;2026-06-23;912797VF1;912797VF1 4.03054553%;Treasury Bills;96.268675;-84716.43;88000.0;-84716.43;-5.0;O
Open;Trades;USD;2026-07-10;912797VF1;912797VF1 3.986%;Treasury Bills;96.470857091;-137953.32;143000.0;-137953.32;-7.5;"O;P"
Open;Trades;USD;2026-08-06;912797VQ7;912797VQ7 4.05724169%;Treasury Bills;96.4099167;-96409.92;100000.0;-96409.92;-5.0;O
Open;Trades;USD;2026-08-06;912797VQ7;912797VQ7 4.011%;Treasury Bills;96.41821;-79062.93;82000.0;-79062.93;-7.5;O
Open;Trades;USD;2026-08-31;912797VR5;912797VR5 4.1558258%;Treasury Bills;96.2942056;-96294.21;100000.0;-96294.21;-5.0;O
Open;Transaction Fees;EUR;2026-03-04;RI;RI;Stocks;93.0;-37.2;0.0;-40.8185610684;;
Open;Transaction Fees;EUR;2026-11-17;RMS;RMS;Stocks;1600.0;-128.0;0.0;-140.450962816;;
Open;Transaction Fees;HKD;2026-04-01;1810;1810;Stocks;31.8;-7.18126;0.0;-0.9163232177047601;;
Open;Transaction Fees;HKD;2026-04-10;1810;1810;Stocks;31.0;-7.1767;0.0;-0.9157413652342001;;
Open;Transaction Fees;HKD;2026-04-10;700;700;Stocks;502.0;-52.4307;0.0;-6.690116738638201;;
Open;Withholding Tax;EUR;2026-02-18;RMS;RMS;;;-3.72;;-4.08185610684;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-10.62160406296;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;9.68;;10.62160406296;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-10.62160406296;;
Open;Withholding Tax;EUR;2026-04-30;MC;MC;;;-18.75;;-20.573871506249997;;
Open;Withholding Tax;EUR;2026-05-08;SAP;SAP;;;-61.32;;-67.28478937403999;;
Open;Withholding Tax;EUR;2026-07-24;RI;RI;;;-58.75;;-64.46479738625;;
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
Open;Dividends;EUR;2026-02-18;RMS;RMS;;;15.0;;16.459097205;;
Open;Dividends;EUR;2026-04-23;RMS;RMS;;;39.0;;42.793652732999995;;
Open;Dividends;EUR;2026-04-30;MC;MC;;;75.0;;82.29548602499999;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;232.5;;255.11600667749997;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;17.5;;19.2022800725;;
Open;Dividends;EUR;2026-07-24;RI;RI;;;235.0;;257.859189545;;
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
Open;Trades;EUR;2025-10-22;MC;MC;Stocks;618.7;-6187.0;10.0;-6788.828960489;-3.851265;O
Open;Trades;EUR;2025-10-22;RMS;RMS;Stocks;2194.0;-6582.0;3.0;-7222.251853553999;-4.08629;O
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
Open;Trades;EUR;2025-09-03;RMS;RMS 19JUN26 2040 P;Equity and Index Options;170.0;17000.0;-1.0;18653.643498999998;-1.14;O
Open;Trades;EUR;2025-07-15;RI;RI 20MAR26 93 P;Equity and Index Options;9.3;930.0;-1.0;1020.4640267099999;-1.14;O
Open;Trades;EUR;2025-03-26;SAP;SAP 16MAY25 235 P;Equity and Index Options;5.35;535.0;-1.0;587.041133645;-1.17;O
Open;Trades;EUR;2025-05-16;SAP;SAP 16MAY25 235 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2025-08-15;SAP;SAP 20MAR26 220 P;Equity and Index Options;9.9;990.0;-1.0;1086.3004155299998;-1.17;O
Open;Trades;EUR;2025-08-08;SAP;SAP 20MAR26 240 P;Equity and Index Options;13.8;1380.0;-1.0;1514.2369428599998;-1.17;O
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
Open;Transaction Fees;EUR;2025-10-22;MC;MC;Stocks;618.7;-24.75;0.0;-27.157510388249996;;
Open;Transaction Fees;EUR;2025-10-22;RMS;RMS;Stocks;2194.0;-26.33;0.0;-28.891201960509996;;
Open;Transaction Fees;GBP;2025-05-22;IMB;IMB;Stocks;30.75;-153.75;0.0;-204.26588936250002;;
Open;Transaction Fees;GBP;2025-05-22;IMB;IMB;Stocks;0.0;-1.5;0.0;-1.9928379450000002;;
Open;Transaction Fees;GBP;2025-10-17;RR.;RR.;Stocks;11.025;-5.5125;0.0;-7.3236794478750005;;
Open;Transaction Fees;HKD;2025-03-26;1810;1810;Stocks;53.35;-11.304095;0.0;-1.44239377263047;;
Open;Transaction Fees;HKD;2025-06-30;2888;2888;Stocks;134.1;-14.382185;0.0;-1.8351556741888102;;
Open;Withholding Tax;EUR;2025-12-04;MC;MC;;;-13.75;;-15.08750577125;;
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
Open;Dividends;EUR;2025-12-04;MC;MC;;;55.0;;60.350023085;;
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
Open;Trades;EUR;2024-11-15;SAP;SAP 21MAR25 220 P;Equity and Index Options;12.7;1270.0;-1.0;1393.5368966899998;-1.17;O
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
Open;Trades;EUR;2022-10-24;MC;MC 16DEC22 720 P;Equity and Index Options;87.32;-8732.0;1.0;-9581.389119603999;-1.1;C
Open;Trades;EUR;2022-10-24;MC;MC 15DEC23 720 P;Equity and Index Options;124.72;12472.0;-1.0;13685.190689383999;-1.1;O
Open;Trades;EUR;2022-01-21;MC;MC 16DEC22 720.0 P;Equity and Index Options;94.1;9410.0;-1.0;10325.340313269999;-1.1;O
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
Open;Trades;EUR;2020-05-04;MC;MC 19MAR21 360.0 P;Equity and Index Options;50.8;5080.0;-1.0;5574.147586759999;-1.1;O
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

---

# [open_positions] START  
Symbol;Account;Asset Category;Description;Currency;Current Price;Cost Price;Quantity;Value;Value in USD;Unrealized Gain in USD;Weight;Unrealized Gain in USD
912797TC1 - United States Treasury B 12/24/26;IBKR;Treasury Bills;;USD;99.619021;97.133959459;390.0;38851.42;38851.42;;0.015807077181189254;969.1740009900029
912797TV9 - United States Treasury B 02/18/27;IBKR;Treasury Bills;;USD;98.9611;97.064044118;680.0;67293.55;67293.55;;0.027379033730201317;1289.9979997600026
912797UE5 - United States Treasury B 04/15/27;IBKR;Treasury Bills;;USD;98.273793;96.90631;1000.0;98273.79;98273.79;;0.03998364495861373;1367.482999999993
912797UX3 - United States Treasury B 05/13/27;IBKR;Treasury Bills;;USD;97.921618;96.625351852;1080.0;105755.35;105755.35;;0.0430275902341197;1399.967439840001
912797VF1 - United States Treasury B 06/10/27;IBKR;Treasury Bills;;USD;97.613931;96.38057197;2640.0;257700.78;257700.78;;0.10484806267345367;3256.067839199969
912797VQ7 - United States Treasury B 07/08/27;IBKR;Treasury Bills;;USD;97.277311;96.420521978;1820.0;177044.71;177044.71;;0.07203235803199133;1559.3560200400018
912797VR5 - United States Treasury B 08/05/27;IBKR;Treasury Bills;;USD;96.916542;96.29921;1000.0;96916.54;96916.54;;0.03943143462745546;617.3320000000047
AMZN;IBKR;Stocks;AMAZON.COM INC;USD;257.37;207.8670155;300.0;77211.0;77211.0;;0.031414044486322595;14850.895349999999
BABA;IBKR;Stocks;ALIBABA GROUP HOLDING-SP ADR;USD;83.31;111.16845686;500.0;41655.0;41655.0;;0.01694774090580057;-13929.228430000003
BABA 15JAN27 100 C;IBKR;Equity and Index Options;;USD;1.43;2.72447076;-1.0;-143.0;-143.0;;-5.8180937451193883e-05;1.29447076
BABA 15JAN27 110 C;IBKR;Equity and Index Options;;USD;0.595;1.06450496;-1.0;-59.5;-59.5;;-2.420815229612613e-05;0.4695049600000001
BABA 15JAN27 75 P;IBKR;Equity and Index Options;;USD;2.565;2.38447777;-1.0;-256.5;-256.5;;-0.00010435951367993868;-0.18052222999999978
BABA 15JAN27 90 C;IBKR;Equity and Index Options;;USD;3.675;6.005473;1.0;367.5;367.5;;0.00014952094065254372;-2.3304730000000005
BABA 18DEC26 90 P;IBKR;Equity and Index Options;;USD;8.3634;3.44559593;-2.0;-1672.68;-1672.68;;-0.000680546087103937;-9.835608140000001
BHP;IBKR;Stocks;BHP GROUP LTD-SPON ADR;USD;68.55;35.102226747;1200.0;82260.0;82260.0;;0.03346827912402244;40137.327903599995
BHP 18DEC26 95 C;IBKR;Equity and Index Options;;USD;0.0056;4.0005117;-4.0;-2.24;-2.24;;-9.11365733501219e-07;15.9796468
BRK B;IBKR;Stocks;BERKSHIRE HATHAWAY INC-CL B;USD;572.04;469.0869395;100.0;57204.0;57204.0;;0.023274002419287378;10295.306049999994
BRK B 18DEC26 510 P;IBKR;Equity and Index Options;;USD;0.7338;9.1593754;-1.0;-73.38;-73.38;;-2.9855364966214034e-05;8.4255754
BRK B 18DEC26 545 C;IBKR;Equity and Index Options;;USD;31.6883;18.66917949;-1.0;-3168.83;-3168.83;;-0.0012892692309333337;-13.01912051
CPRT;IBKR;Stocks;COPART INC;USD;30.86;27.171926655;1100.0;33946.0;33946.0;;0.013811259459568025;4056.880679499999
CPRT 09/18/2026 25.00 P;Schwab;Equity and Index Options;SALESFORCE;USD;0.275;0.34332;-5.0;-137.5;-137.5;99.6875;-5.594320908768643e-05;0.34159999999999996
CPRT 09/18/2026 27.50 P;Schwab;Equity and Index Options;SALESFORCE;USD;0.675;0.99332;-10.0;-675.0;-675.0;219.37499999999997;-0.00027463029915773335;3.1831999999999994
CPRT 20NOV26 35 C;IBKR;Equity and Index Options;;USD;0.0007;1.223198932;-6.0;-0.42;-0.42;;-1.7088107503147854e-07;7.334993592000001
CRM 09/18/2026 160.00 P;Schwab;Equity and Index Options;SALESFORCE;USD;2.085;6.94315;-2.0;-417.0;-417.0;;-0.00016966049592411083;9.7163
DNOW;IBKR;Stocks;DNOW INC;USD;13.45;26.81106924;25.0;336.25;336.25;;0.0001368065749507968;-334.026731
FAST;IBKR;Stocks;FASTENAL CO;USD;52.68;42.17242457;100.0;5268.0;5268.0;;0.0021433369125376882;1050.7575430000002
FCX;IBKR;Stocks;FREEPORT-MCMORAN INC;USD;49.12;11.2079574;500.0;24560.0;24560.0;;0.009992474292316936;18956.0213
CASH - Schwab;Schwab;Forex;;USD;1.0;;-1.0;;72675.14;0.0;0.02956858583634097;
Forex;IBKR;Forex;;USD;0.0;;-1.0;;17806.283811092;0.0;0.007244659333226222;
GEHC 17JUN27 57.5 P;IBKR;Equity and Index Options;;USD;1.2573;5.33945409;-1.0;-125.73;-125.73;;-5.115447038978047e-05;4.08215409
GEHC 17JUN27 65 C;IBKR;Equity and Index Options;;USD;20.748;8.8804028;1.0;2074.8;2074.8;;0.0008441525106555041;11.8675972
GEHC 17JUN27 80 C;IBKR;Equity and Index Options;;USD;10.1092;3.91948334;-1.0;-1010.92;-1010.92;;-0.00041130261040671974;-6.189716659999999
GLD 15JAN27 390 P;IBKR;Equity and Index Options;;USD;55.0053;13.74771085;-2.0;-11001.06;-11001.06;;-0.004475887998299517;-82.5151783
GLD 15JAN27 405 P;IBKR;Equity and Index Options;;USD;69.82;20.505473;1.0;6982.0;6982.0;;0.002840694442547103;49.314527
GOOG;IBKR;Stocks;ALPHABET INC-CL C;USD;271.02;93.120965215;200.0;54204.0;54204.0;;0.022053423311919673;35579.80695699999
GOOG 18DEC26 410 C;IBKR;Equity and Index Options;;USD;0.0402;32.055824165;-2.0;-8.04;-8.04;;-3.271152007745446e-06;64.03124833
GOOG 21JAN28 250 C;IBKR;Equity and Index Options;;USD;61.046;132.321833;2.0;12209.2;12209.2;;0.00496743147922459;-142.551666
GOOG 21JAN28 250 P;IBKR;Equity and Index Options;;USD;26.576;13.467856515;-2.0;-5315.2;-5315.2;;-0.0021625406904936064;-26.216286970000002
H78;IBKR;Stocks;HONGKONG LAND HOLDINGS LTD;USD;8.42;4.208;1000.0;8420.0;8420.0;;0.003425758694678689;4212.0
ICE;IBKR;Stocks;INTERCONTINENTAL EXCHANGE IN;USD;170.88;152.14704663;300.0;51264.0;51264.0;;0.020857255786699325;5619.886010999997
IVV;IBKR;Stocks;ISHARES CORE S&P 500 ETF;USD;719.05;402.65670378;100.0;71905.0;71905.0;;0.029255246905091584;31639.329621999997
IVV;Schwab;Stocks;ISHARES CORE S&P 500 ETF;USD;776.98;531.9820000000001;10.0;7769.8;7769.8;;0.003161218516141862;2449.9799999999996
LULU 15JAN27 85 P;IBKR;Equity and Index Options;;USD;1.67;4.59553934;-2.0;-334.0;-334.0;;-0.00013589114062027103;5.8510786800000005
MCD 18DEC26 245 P;IBKR;Equity and Index Options;;USD;0.6251;6.4943602;-1.0;-62.51;-62.51;;-2.543280000051839e-05;5.8692602
MCD 18DEC26 275 C;IBKR;Equity and Index Options;;USD;5.1;6.505473;1.0;510.0;510.0;;0.00020749844825250965;-1.4054730000000006
META;IBKR;Stocks;META PLATFORMS INC-CLASS A;USD;596.22;230.10913779;200.0;119244.0;119244.0;;0.04851557835965149;73222.17244200001
META 15JAN27 460 P;IBKR;Equity and Index Options;;USD;4.075;19.71258137;-1.0;-407.5;-407.5;;-0.00016579532875077977;15.63758137
META 15JAN27 520 P;IBKR;Equity and Index Options;;USD;11.7195;62.7404795;1.0;1171.95;1171.95;;0.00047681922829319356;-51.020979499999996
META 15JAN27 640 P;IBKR;Equity and Index Options;;USD;61.4117;77.2419796;-1.0;-6141.17;-6141.17;;-0.002498594598931107;15.83027959999999
META 15JAN27 800 C;IBKR;Equity and Index Options;;USD;1.1757;35.535755475;-2.0;-235.14;-235.14;;-9.566899043548063e-05;68.72011095
MSFT;Schwab;Stocks;MICROSOFT CORP;USD;499.45;470.3566;100.0;49945.0;49945.0;;0.02032060783915999;2909.3399999999974
MSFT 09/18/2026 520.00 C;Schwab;Equity and Index Options;MICROSOFT CORP;USD;11.325;13.4931;-1.0;-1132.5;-1132.5;;-0.00046076861303130824;2.168100000000001
MSFT 15JAN27 420 P;IBKR;Equity and Index Options;;USD;4.613;53.1437823;-2.0;-922.6;-922.6;;-0.00037536876148581456;97.0615646
MU 15JAN27 700 P;IBKR;Equity and Index Options;;USD;82.13;55.29338782;-1.0;-8213.0;-8213.0;;-0.0033415387362703175;-26.836612179999996
MU 15JAN27 760 C;IBKR;Equity and Index Options;;USD;53.657;86.755473;1.0;5365.7;5365.7;;0.0021830871054676296;-33.098473
MU 15JAN27 800 C;IBKR;Equity and Index Options;;USD;40.4159;68.94310663;-1.0;-4041.59;-4041.59;;-0.001644360104848746;28.527206630000002
NBIS 18DEC26 140 P;IBKR;Equity and Index Options;;USD;44.6094;24.136075115;-2.0;-8921.88;-8921.88;;-0.0036299534421472563;-40.94664977
NOV;IBKR;Stocks;NOV INC;USD;16.47;62.158401437;300.0;4941.0;4941.0;;0.002010293789834608;-13706.520431100002
NVDA;Moomoo-mom;Stocks;NVIDIA;USD;223.96;0.0;0.4717;105.641932;105.641932;;4.2981445020386556e-05;105.64193200000001
NVDA;Moomoo-me;Stocks;NVIDIA;USD;223.96;0.0;1.4431;323.196676;323.196676;;0.00013149570343209635;323.196676
NVDA 15JAN27 145 P;IBKR;Equity and Index Options;;USD;10.525;7.59437044;-1.0;-1052.5;-1052.5;;-0.0004282198368348361;-2.9306295600000007
NVDA 15JAN27 165 C;IBKR;Equity and Index Options;;USD;4.675;12.435473;1.0;467.5;467.5;;0.00019020691089813386;-7.760473
NVDA 15JAN27 185 C;IBKR;Equity and Index Options;;USD;1.525;5.84440649;-1.0;-152.5;-152.5;;-6.204610462452495e-05;4.31940649
NVO;IBKR;Stocks;NOVO-NORDISK A/S-SPONS ADR;USD;38.87;58.2644644;200.0;7774.0;7774.0;;0.003162927326892177;-3878.892880000001
PDD;IBKR;Stocks;PDD HOLDINGS INC;USD;86.0;96.7605204;100.0;8600.0;8600.0;;0.003498993441120751;-1076.0520400000005
PDD 18DEC26 120 C;IBKR;Equity and Index Options;;USD;0.039;5.8261019;-2.0;-7.8;-7.8;;-3.17350567915603e-06;11.574203800000001
PDD 18DEC26 90 P;IBKR;Equity and Index Options;;USD;5.5528;8.553596777;-4.0;-2221.12;-2221.12;;-0.0009036842223188515;12.003187107999995
PDD 20NOV26 100 C;IBKR;Equity and Index Options;;USD;0.0001;10.7569825;1.0;0.01;0.01;;4.068597024559013e-09;-10.7568825
PDD 20NOV26 130 C;IBKR;Equity and Index Options;;USD;0.0001;3.12292012;-1.0;-0.01;-0.01;;-4.068597024559013e-09;3.1228201199999996
PLTR 18DEC26 180 P;IBKR;Equity and Index Options;;USD;27.797;10.19781688;-2.0;-5559.4;-5559.4;;-0.0022618958298333373;-35.19836624
PLTR 18DEC26 195 P;IBKR;Equity and Index Options;;USD;41.5849;16.655473;1.0;4158.49;4158.49;;0.001691922004065841;24.929426999999997
SKHY 15JAN27 125 P;IBKR;Equity and Index Options;;USD;16.1453;11.39193216;-1.0;-1614.53;-1614.53;;-0.0006568871954061263;-4.753367839999999
SKHY 18DEC26 115 P;IBKR;Equity and Index Options;;USD;8.5;16.440827835;-2.0;-1700.0;-1700.0;;-0.0006916614941750322;15.88165567
SKHY 18DEC26 130 P;IBKR;Equity and Index Options;;USD;16.8972;24.4323006;1.0;1689.72;1689.72;;0.0006874789764337855;-7.5351006
SMCI 18DEC26 24 P;IBKR;Equity and Index Options;;USD;2.3818;2.96300312;-4.0;-952.72;-952.72;;-0.0003876233757237863;2.3248124800000003
SMCI 18DEC26 27 P;IBKR;Equity and Index Options;;USD;4.2261;4.3169028;2.0;845.22;845.22;;0.0003438859577097769;-0.18160560000000103
SOUHY;IBKR;Stocks;SOUTH32 - ADR;USD;13.16;7.17;300.0;3948.0;3948.0;;0.0016062821052958984;1797.0
TSM;IBKR;Stocks;TAIWAN SEMICONDUCTOR-SP ADR;USD;352.09;181.60807492;300.0;105627.0;105627.0;;0.04297536979130949;51144.57752399999
TSM 19MAR27 310 P;IBKR;Equity and Index Options;;USD;16.7473;17.84415929;-1.0;-1674.73;-1674.73;;-0.0006813801494939715;1.0968592900000012
TSM 19MAR27 360 C;IBKR;Equity and Index Options;;USD;34.3;38.055473;1.0;3430.0;3430.0;;0.0013955287794237415;-3.755473000000002
TSM 19MAR27 400 C;IBKR;Equity and Index Options;;USD;18.8751;21.19409028;-1.0;-1887.51;-1887.51;;-0.0007679517569825383;2.3189902800000013
UNH;IBKR;Stocks;UNITEDHEALTH GROUP INC;USD;330.48;322.13551065;200.0;66096.0;66096.0;;0.026891798893525252;1668.8978700000007
UNH 15JAN27 360 P;IBKR;Equity and Index Options;;USD;39.2386;12.18938278;-1.0;-3923.86;-3923.86;;-0.0015964605120786129;-27.049217219999996
UNH 17JUN27 360 P;IBKR;Equity and Index Options;;USD;51.6713;21.4891912;-1.0;-5167.13;-5167.13;;-0.0021022969743509615;-30.1821088
UNH 18DEC26 440 C;IBKR;Equity and Index Options;;USD;0.205;26.01244859;-1.0;-20.5;-20.5;;-8.340623900345976e-06;25.807448590000003
9CI;IBKR;Stocks;CAPITALAND INVESTMENT LTD/SI;SGD;2.32;2.323057932;1200.0;2784.0;2138.35178592;;0.0008700091713654564;-2.818506186820137
BS6;IBKR;Stocks;YANGZIJIANG SHIPBUILDING;SGD;4.36;0.171019587;7000.0;30520.0;23441.988687600002;;0.009537600542411541;22522.484278468804
BS6;Moomoo-me;Stocks;YZJ Shipbuilding;SGD;4.2;4.015;200.0;840.0;652.6800000000001;;0.0002655491905989177;28.419186810000078
C38U;IBKR;Stocks;CAPITALAND INTEGRATED COMMER;SGD;2.33;1.362308617;222.0;517.26;397.3002316038;;0.00016164545401598274;165.00601312742555
CGP1;IBKR;Stocks;NOBLE GROUP LTD;SGD;0.0;85.85;50.0;0.0;0.0;;0.0;-3297.009713025
D05;IBKR;Stocks;DBS GROUP HOLDINGS LTD;SGD;67.34;18.388145455;220.0;14814.8;11379.042398724;;0.004629673804577932;8271.832913034232
F34;IBKR;Stocks;WILMAR INTERNATIONAL LTD;SGD;2.91;3.040549529;9000.0;26190.0;20116.1757447;;0.008184461278039262;-902.4595425263952
CASH - DBS;DBS;Forex;;SGD;0.763247271;;-0.777;;1043.95389;18.47775410253007;0.0004247427690630807;
CASH - Moomoo-me;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;75899.04609;1343.3964121419413;0.03088026330886414;
CASH - Moomoo-mom;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;7040.27268;124.6112770323598;0.002864403247793211;
CASH - OCBC;OCBC;Forex;;SGD;0.763247271;;-0.777;;6154.438290000001;108.93220328133066;0.0025039929314526065;
CASH - OCBC Joint;OCBC Joint;Forex;;SGD;0.763247271;;-0.777;;30062.78268;532.1046373023601;0.012231334816181223;
CASH - POSB;POSB;Forex;;SGD;0.763247271;;-0.777;;6713.0158200000005;118.81890263214063;0.0027312556191069585;
CASH - UOB;UOB;Forex;;SGD;0.763247271;;-0.777;;6898.25262;122.09755322574074;0.0028066210084388417;
Forex;IBKR;Forex;;SGD;0.76808613;;-0.77404;;14883.495195;114.482957;0.006055494426541536;
Fullerton SGD Cash Fund;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0;
Fullerton SGD Cash Fund;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0;
HMN;IBKR;Stocks;CAPITALAND ASCOTT TRUST;SGD;0.83;0.505252956;68.0;56.44;43.3507811772;;1.7637685930986477e-05;16.96149161733318
O39;IBKR;Stocks;OVERSEA-CHINESE BANKING CORP;SGD;27.42;8.7231;800.0;21936.0;16848.737347680002;;0.006855072264034717;11488.663651197601
P40U;Moomoo-mom;Stocks;StarhillGbl Reit;SGD;0.53;0.555;1200.0;636.0;494.172;;0.00020105867288203766;-23.04258390000002
P40U;Moomoo-me;Stocks;StarhillGbl Reit;SGD;0.53;0.5448;4200.0;2226.0;1729.602;;0.0007037053550871318;-47.74423384079976
Z74;Moomoo-mom;Stocks;Singtel;SGD;4.3;4.29;100.0;430.0;334.11;;0.00013593589518754118;0.7680861299999837
Z74;Moomoo-me;Stocks;Singtel;SGD;4.3;4.88;100.0;430.0;334.11;;0.00013593589518754118;-44.54899554000001
Z77;Moomoo-me;Stocks;Singtel 10;SGD;4.29;4.28;120.0;514.8;399.9996;;0.00016274371823847954;0.9217033559999804
1299;IBKR;Stocks;AIA GROUP LTD;HKD;75.55;55.302399867;600.0;45330.0;5784.0729145800005;;0.002353306185009257;1550.1468631969783
1398;IBKR;Stocks;IND & COMM BK OF CHINA - H;HKD;7.865;5.051969413;6000.0;47190.0;6021.407474940001;;0.0024498680536198286;2153.643153693154
14;IBKR;Stocks;HYSAN DEVELOPMENT CO;HKD;15.42;28.5525695;1000.0;15420.0;1967.5800649200003;;0.0008005290397715142;-1675.7057035912073
16;IBKR;Stocks;SUN HUNG KAI PROPERTIES;HKD;109.1;88.851861372;4500.0;490950.0;62644.8400047;;0.025487660964709787;11626.410675930756
1810;IBKR;Stocks;XIAOMI CORP-CLASS B;HKD;28.52;38.85377485;600.0;17112.0;2183.477955312;;0.0008883691912172601;-791.1490035109598
1883;IBKR;Stocks;CITIC TELECOM INTERNATIONAL;HKD;2.37;2.45452615;10000.0;23700.0;3024.1016562000004;;0.0012303851000379305;-107.85471316759883
1972;IBKR;Stocks;SWIRE PROPERTIES LTD;HKD;25.6;19.720971466;5600.0;143360.0;18292.625039360002;;0.00744253198065138;4200.893147193762
200;IBKR;Stocks;MELCO INTERNATIONAL DEVELOP.;HKD;2.04;11.460065577;4000.0;8160.0;1041.20968416;;0.00042362626229154056;-4807.972305977773
215;IBKR;Stocks;HUTCHISON TELECOMM HONG KONG;HKD;1.085;1.21222067;50000.0;54250.0;6922.258010500001;;0.0028163878344750095;-811.6629511600712
2318;IBKR;Stocks;PING AN INSURANCE GROUP CO-H;HKD;50.4;86.4593701;500.0;25200.0;3215.5004952000004;;0.0013082575747238754;-2300.5738574037714
27;IBKR;Stocks;GALAXY ENTERTAINMENT GROUP L;HKD;32.94;51.5945905;1000.0;32940.0;4203.11850444;;0.0017100795441033513;-2380.311309146954
2800;IBKR;Stocks;TRACKER FUND OF HONG KONG-B;HKD;22.94;21.769917967;1500.0;34410.0;4390.68936666;;0.0017863945692955772;223.9523426509597
2828;IBKR;Stocks;HANGSENG CHINA ENT INDEX;HKD;78.94;92.44076825;400.0;31576.0;4029.0731601760003;;0.0016392675071222654;-689.0750316421503
2888;IBKR;Stocks;STANDARD CHARTERED PLC;HKD;213.0;55.6;28.0;5964.0;761.001783864;;0.00030962095935131717;562.3553088272
3188;IBKR;Stocks;CHINAAMC ETF SERIES - CH;HKD;48.2;26.37987258;1000.0;48200.0;6150.282693200001;;0.0025023021865750316;2784.2313700133773
3690;IBKR;Stocks;MEITUAN-CLASS B;HKD;67.55;231.697888617;60.0;4053.0;517.159662978;;0.0002104114266014233;-1256.7086122238047
386;IBKR;Stocks;CHINA PETROLEUM & CHEMICAL-H;HKD;4.685;5.815174;2000.0;9370.0;1195.6047476200001;;0.00048644339187153623;-288.4186552906481
390;IBKR;Stocks;CHINA RAILWAY GROUP LTD-H;HKD;2.84;3.92724184;10000.0;28400.0;3623.8180184000003;;0.001474385520720558;-1387.312172588159
3918;IBKR;Stocks;NAGACORP LTD;HKD;2.06;7.594013956;2036.0;4194.16;535.17156972016;;0.00021773974561920196;-1437.692687226113
3968;IBKR;Stocks;CHINA MERCHANTS BANK - H;HKD;53.75;39.7833745;500.0;26875.0;3429.2291987500003;;0.001395215171456514;891.0653018159315
5;IBKR;Stocks;HSBC HOLDINGS PLC;HKD;133.3;49.092166333;2400.0;319920.0;40821.54438192;;0.01660864140101834;25787.650557710265
66;IBKR;Stocks;MTR CORP;HKD;32.58;40.07708;500.0;16290.0;2078.59139154;;0.0008456950750893623;-478.31080263004026
700;IBKR;Stocks;TENCENT HOLDINGS LTD;HKD;348.4;452.856419642;706.0;245970.4;31385.632658910403;;0.012769549164994504;-9409.962157717582
808;IBKR;Stocks;PROSPERITY REIT;HKD;1.34;2.339531045;20000.0;26800.0;3419.6592568;;0.0013913215477222166;-2550.7877540994236
857;IBKR;Stocks;PETROCHINA CO LTD-H;HKD;10.12;7.7972334;2000.0;20240.0;2582.60833424;;0.0010507592584290174;592.7664406773031
939;IBKR;Stocks;CHINA CONSTRUCTION BANK-H;HKD;10.48;6.39177626;4000.0;41920.0;5348.959553920001;;0.0021762760925565423;2086.6167397553013
9618;IBKR;Stocks;JD.COM INC-CLASS A;HKD;98.6;285.565311947;19.0;1873.4;239.04438998840004;;9.725752938443288e-05;-453.2759527724294
CASH - Citic;Citic;Forex;;HKD;0.128244528;;-0.1276;;56493.2093;-285.35623200400005;0.022984810326576958;
CASH - StanChart;StanChart;Forex;;HKD;0.128244528;;-0.1276;;1614.1055479999998;-8.153105177440239;0.0006567145029916994;
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;127600.0;-644.5280000000057;0.051915298033373004;
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;1276.0;-6.445279999999912;0.00051915298033373;
CASH - StanChart Payroll;StanChart Payroll;Forex;;HKD;0.128244528;;-0.1276;;14971.866888;-75.62529327264019;0.006091449307261041;
Forex;IBKR;Forex;;HKD;0.127599226;;-0.1275;;4923.595161;-3.831754;0.0020032124622177756;
BARCl;IBKR;Stocks;;GBP;4.1095;3.5479;1000.0;4109.5;5459.711689985001;;0.0022213366736823035;746.1185266079999
Forex;IBKR;Forex;;GBP;1.32855863;;-1.3088;;3944.816648;-59.553923;0.001604986927648366;
IMB 18DEC26 30 C;IBKR;Equity and Index Options;;GBP;0.05;0.8986;-1.0;-50.0;-66.4279315;;-2.7026848444850992e-05;1.127414853418
IMBl;IBKR;Stocks;IMPERIAL BRANDS PLC;GBP;27.71;29.95515;1000.0;27710.0;36814.359637300004;;0.014978279408136421;-2982.8134081444987
RR. 15JAN27 11 P;IBKR;Equity and Index Options;;GBP;0.0775;0.4636;-1.0;-77.5;-102.96329382500001;;-4.189161508951904e-05;0.512956487043
RRl;IBKR;Stocks;ROLLS-ROYCE HOLDINGS PLC;GBP;13.26;11.090386;100.0;1326.0;1761.66874338;;0.0007167520207574484;288.2459403468819
Forex;IBKR;Forex;;EUR;1.097273147;;-1.0967;;725.792995;-0.379307;0.00029529592199027747;
MC;IBKR;Stocks;LVMH MOET HENNESSY LOUIS VUI;EUR;391.9;619.0851265;10.0;3919.0;4300.2134630929995;;0.0017495835700908787;-2492.841387062481
MC 18DEC26 440 P;IBKR;Equity and Index Options;;EUR;53.0166;37.9386;-1.0;-5301.66;-5817.369152524019;;-0.0023668530824720612;-16.544684510465995
MC 20NOV26 410 P;IBKR;Equity and Index Options;;EUR;18.1429;12.4886;-2.0;-3628.58;-3981.5433957412597;;-0.0016199295613065479;-12.408623110164202
MC 20NOV26 430 P;IBKR;Equity and Index Options;;EUR;38.1;20.2114;1.0;3810.0;4180.61069007;;0.0017009220214458402;19.6286804174242
MC 20NOV26 440 C;IBKR;Equity and Index Options;;EUR;0.0001;26.9864;1.0;0.01;0.010972731469999998;;4.464362261012704e-09;-29.611342326886096
MC 20NOV26 450 C;IBKR;Equity and Index Options;;EUR;0.0001;22.1636;-1.0;-0.01;-0.010972731469999998;;-4.464362261012704e-09;24.319413393534496
RI;IBKR;Stocks;PERNOD-RICARD SA;EUR;70.94;83.7114;100.0;7094.0;7784.055704817999;;0.003167018587962412;-1401.3714269595798
RMS;IBKR;Stocks;HERMES INTL;EUR;1272.0;1581.624621304;23.0;29256.0;32101.823188631995;;0.013060938230818766;-7814.083999959234
RMS 17DEC27 1520 P;IBKR;Equity and Index Options;;EUR;309.5538;193.044;5.0;15477.69;16983.25361459043;;0.0069098015123653725;639.2153745117031
RMS 17DEC27 1680 P;IBKR;Equity and Index Options;;EUR;436.426;214.886;-5.0;-21821.3;-23943.926522631096;;-0.009741818820623652;-1215.4494649319
RMS 17DEC27 1920 P;IBKR;Equity and Index Options;;EUR;651.8588;388.7186;-1.0;-65185.88;-71526.71568756436;;-0.02910133826229028;-288.7366753562094
RMS 18DEC26 1520 P;IBKR;Equity and Index Options;;EUR;248.236;130.209;5.0;12411.8;13619.134845934597;;0.005541077151123747;647.539288604845
RMS 18DEC26 1600 P;IBKR;Equity and Index Options;;EUR;328.0;110.436;-3.0;-9840.0;-10797.167766479999;;-0.0043929324648365005;-716.181404861724
RMS 18JUN27 1360 P;IBKR;Equity and Index Options;;EUR;174.3204;108.0235;-10.0;-17432.04;-19127.709389429878;;-0.0077822941508463896;-727.458080993443
RMS 18JUN27 1440 P;IBKR;Equity and Index Options;;EUR;227.3649;146.389;5.0;11368.25;12474.07545338275;;0.005075198627375767;444.26340312078645
SAP;IBKR;Stocks;SAP SE;EUR;187.02;210.1117;100.0;18702.0;20521.202395194;;0.008349250300545959;-2533.79023285799
SAP 18DEC26 190 P;IBKR;Equity and Index Options;;EUR;8.6814;31.7783;-2.0;-1736.28;-1905.1734196731597;;-0.0007751382906551138;50.6872162978886
Forex;IBKR;Forex;;CNH;0.148953656;;-0.14942;;972.7242;3.0359;0.00039576227858365465;

---

# [pivot_summary] START  
# PORTFOLIO PIVOT SUMMARY

**Report Date :** 2026-11-21  
**Filter      :** Open Positions | Forex Balances | Cash Balances  
**Generated   :** 2026-11-21 20:19:09  

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
BABA        -1764.18       4.0      -1764.18
BHP            -2.24       4.0         -2.24
BRK         -3242.21       2.0      -3242.21
CPRT           -0.42       6.0         -0.42
GEHC          938.15       1.0        938.15
GLD         -4019.06       1.0      -4019.06
GOOG         6885.96       2.0       6885.96
IMB           -50.00       1.0        -66.43
LULU         -334.00       2.0       -334.00
MC          -5120.24       2.0      -5618.30
MCD           447.49       0.0        447.49
META        -5611.86       3.0      -5611.86
MSFT         -922.60       2.0       -922.60
MU          -6888.89       1.0      -6888.89
NBIS        -8921.88       2.0      -8921.88
NVDA         -737.50       1.0       -737.50
PDD         -2228.92       6.0      -2228.92
PLTR        -1400.91       1.0      -1400.91
RMS        -75021.48       4.0     -82319.06
RR.           -77.50       1.0       -102.96
SAP         -1736.28       2.0      -1905.17
SKHY        -1624.81       2.0      -1624.81
SMCI         -107.50       2.0       -107.50
TSM          -132.24       1.0       -132.24
UNH         -9111.49       3.0      -9111.49
```

> **SUBTOTAL [IBKR]**  Amount: `    -120,784.61`  Quantity: `          56.00`  Value in USD: `    -128,791.03`

> **SUBTOTAL [Equity and Index Options]**  Amount: `    -118,422.61`  Quantity: `          74.00`  Value in USD: `    -131,153.03`

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
Forex       85836.91      4.46      43256.71
```

> **SUBTOTAL [IBKR]**  Amount: `      85,836.91`  Quantity: `           4.46`  Value in USD: `      43,256.71`

### UOB

```
             Amount  Quantity  Value in USD
Underlying                                 
Forex       8878.06      0.78       6898.25
```

> **SUBTOTAL [UOB]**  Amount: `       8,878.06`  Quantity: `           0.78`  Value in USD: `       6,898.25`

> **SUBTOTAL [Forex]**  Amount: `   1,913,448.82`  Quantity: `          13.09`  Value in USD: `     451,698.79`

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
1299         45330.00    -600.0       5784.07
1398         47190.00   -6000.0       6021.41
14           15420.00   -1000.0       1967.58
16          490950.00   -4500.0      62644.84
1810         17112.00    -600.0       2183.48
1883         23700.00  -10000.0       3024.10
1972        143360.00   -5600.0      18292.63
200           8160.00   -4000.0       1041.21
215          54250.00  -50000.0       6922.26
2318         25200.00    -500.0       3215.50
27           32940.00   -1000.0       4203.12
2800         34410.00   -1500.0       4390.69
2828         31576.00    -400.0       4029.07
2888          5964.00     -28.0        761.00
3188         48200.00   -1000.0       6150.28
3690          4053.00     -60.0        517.16
386           9370.00   -2000.0       1195.60
390          28400.00  -10000.0       3623.82
3918          4194.16   -2036.0        535.17
3968         26875.00    -500.0       3429.23
5           319920.00   -2400.0      40821.54
66           16290.00    -500.0       2078.59
700         245970.40    -706.0      31385.63
808          26800.00  -20000.0       3419.66
857          20240.00   -2000.0       2582.61
939          41920.00   -4000.0       5348.96
9618          1873.40     -19.0        239.04
9CI           2784.00   -1200.0       2138.35
AMZN         77211.00    -300.0      77211.00
BABA         41655.00    -500.0      41655.00
BARCl         4109.50   -1000.0       5459.71
BHP          82260.00   -1200.0      82260.00
BRK          57204.00    -100.0      57204.00
BS6          30520.00   -7000.0      23441.99
C38U           517.26    -222.0        397.30
CGP              0.00     -50.0          0.00
CPRT         33946.00   -1100.0      33946.00
D05          14814.80    -220.0      11379.04
DNOW           336.25     -25.0        336.25
F34          26190.00   -9000.0      20116.18
FAST          5268.00    -100.0       5268.00
FCX          24560.00    -500.0      24560.00
GOOG         54204.00    -200.0      54204.00
H78           8420.00   -1000.0       8420.00
HMN             56.44     -68.0         43.35
ICE          51264.00    -300.0      51264.00
IMB          27710.00   -1000.0      36814.36
IVV          71905.00    -100.0      71905.00
MC            3919.00     -10.0       4300.21
META        119244.00    -200.0     119244.00
NOV           4941.00    -300.0       4941.00
NVO           7774.00    -200.0       7774.00
O39          21936.00    -800.0      16848.74
PDD           8600.00    -100.0       8600.00
RI            7094.00    -100.0       7784.06
RMS          29256.00     -23.0      32101.82
RRl           1326.00    -100.0       1761.67
SAP          18702.00    -100.0      20521.20
SOUHY         3948.00    -300.0       3948.00
TSM         105627.00    -300.0     105627.00
UNH          66096.00    -200.0      66096.00
```

> **SUBTOTAL [IBKR]**  Amount: `   2,783,066.21`  Quantity: `    -158,867.00`  Value in USD: `   1,233,379.49`

> **SUBTOTAL [Stocks]**  Amount: `   2,846,286.65`  Quantity: `    -164,898.91`  Value in USD: `   1,295,467.81`

---

## Treasury Bills

### IBKR

```
               Amount  Quantity  Value in USD
Underlying                                   
912797TC1    38851.42  -39000.0      38851.42
912797TV9    67293.55  -68000.0      67293.55
912797UE5    98273.79 -100000.0      98273.79
912797UX3   105755.35 -108000.0     105755.35
912797VF1   257700.78 -264000.0     257700.78
912797VQ7   177044.71 -182000.0     177044.71
912797VR5    96916.54 -100000.0      96916.54
```

> **SUBTOTAL [IBKR]**  Amount: `     841,836.14`  Quantity: `    -861,000.00`  Value in USD: `     841,836.14`

> **SUBTOTAL [Treasury Bills]**  Amount: `     841,836.14`  Quantity: `    -861,000.00`  Value in USD: `     841,836.14`

---

> ### GRAND TOTAL  
> Amount: `   5,483,149.00`  Quantity: `  -1,025,811.83`  Value in USD: `   2,457,849.71`

---

