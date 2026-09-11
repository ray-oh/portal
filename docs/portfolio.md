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

- Current refresh timestamp: **2026-09-11 23:15:52**.


# [transaction_details] START  
Status;Table;Currency;Settle Date;Ticker;Symbol;Asset Category;Transaction Price;Amount;Quantity;Value in USD;Comm/Fee;Code
Open;Trades;EUR;2026-03-04;RI;RI;Stocks;93.0;-9300.0;100.0;-10801.02;0.0;"A;O"
Open;Trades;EUR;2026-02-16;SAP;SAP;Stocks;220.0;-22000.0;100.0;-25550.8;0.0;"A;O"
Open;Trades;HKD;2026-04-01;1810;1810;Stocks;31.8;-6360.0;200.0;-812.8365818399999;-25.18126;O
Open;Trades;HKD;2026-04-10;1810;1810;Stocks;31.0;-6200.0;200.0;-792.3878628;-25.1767;O
Open;Trades;HKD;2026-04-10;700;700;Stocks;502.0;-50200.0;100.0;-6415.785598799999;-92.5907;O
Open;Trades;USD;2026-02-23;CPRT;CPRT;Stocks;45.0;-13500.0;300.0;-13500.0;0.0;"A;O"
Open;Trades;USD;2026-08-10;GOOG;GOOG;Stocks;352.84;70568.0;-200.0;70568.0;-2.4338153;"C;P"
Open;Trades;USD;2026-02-23;NVO;NVO;Stocks;65.0;-13000.0;200.0;-13000.0;0.0;"A;O"
Open;Trades;USD;2026-03-20;PDD;PDD;Stocks;100.0;-10000.0;100.0;-10000.0;0.0;"A;O"
Open;Trades;USD;2026-01-02;TSM;TSM;Stocks;317.21;63442.0;-200.0;63442.0;-0.7795145;"C;P"
Open;Trades;USD;2026-01-02;TSM;TSM;Stocks;318.5;63700.0;-200.0;63700.0;-0.1395145;C
Open;Trades;USD;2026-01-29;UNH;UNH;Stocks;350.0;-70000.0;200.0;-70000.0;0.0;"A;O"
Open;Trades;EUR;2026-03-30;RMS;RMS 18DEC26 1760 C;Equity and Index Options;130.095;6504.75;-5.0;7554.61665;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1760 C;Equity and Index Options;181.5;-9075.0;5.0;-10539.705;-5.7;C
Open;Trades;EUR;2026-03-30;RMS;RMS 17DEC27 1760 C;Equity and Index Options;226.53;11326.5;-5.0;13154.597099999999;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 17DEC27 1760 C;Equity and Index Options;284.1;-14205.0;5.0;-16497.686999999998;-5.7;C
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 440 C;Equity and Index Options;26.975;-2697.5;1.0;-3132.8765;-1.14;O
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 450 C;Equity and Index Options;22.175;2217.5;-1.0;2575.4045;-1.14;O
Open;Trades;EUR;2026-02-18;SAP;SAP 17APR26 194 C;Equity and Index Options;2.2;220.0;-1.0;255.508;-1.17;O
Open;Trades;EUR;2026-04-17;SAP;SAP 17APR26 194 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2026-02-18;SAP;SAP 18SEP26 175 C;Equity and Index Options;15.79;-1579.0;1.0;-1833.8506;-1.17;O
Open;Trades;EUR;2026-08-06;SAP;SAP 18SEP26 175 C;Equity and Index Options;7.5;750.0;-1.0;871.05;-1.17;C
Open;Trades;EUR;2026-02-18;SAP;SAP 18SEP26 195 C;Equity and Index Options;8.52;1704.0;-2.0;1979.0256;-2.34;O
Open;Trades;EUR;2026-08-06;SAP;SAP 18SEP26 195 C;Equity and Index Options;2.2;-440.0;2.0;-511.016;-2.34;C
Open;Trades;EUR;2026-03-10;RMS;RMS 19JUN26 2040 P;Equity and Index Options;180.57;-18057.0;1.0;-20971.3998;-1.14;C
Open;Trades;EUR;2026-02-18;RMS;RMS 18DEC26 1480 P;Equity and Index Options;30.1;3010.0;-1.0;3495.814;-1.14;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1480 P;Equity and Index Options;73.05;-7305.0;1.0;-8484.027;-1.14;C
Open;Trades;EUR;2026-03-10;RMS;RMS 18DEC26 1960 P;Equity and Index Options;198.32;19832.0;-1.0;23032.8848;-1.14;O
Open;Trades;EUR;2026-03-24;RMS;RMS 18DEC26 1960 P;Equity and Index Options;359.93;-35993.0;1.0;-41802.2702;-1.14;C
Open;Trades;EUR;2026-03-24;RMS;RMS 17DEC27 1920 P;Equity and Index Options;388.73;38873.0;-1.0;45147.1022;-1.14;O
Open;Trades;EUR;2026-03-30;RMS;RMS 18DEC26 1520 P;Equity and Index Options;130.095;-6504.75;5.0;-7554.61665;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1600 P;Equity and Index Options;110.55;5527.5;-5.0;6419.6385;-5.7;O
Open;Trades;EUR;2026-09-04;RMS;RMS 18JUN27 1360 P;Equity and Index Options;108.1375;10813.75;-10.0;12559.089249999999;-11.4;O
Open;Trades;EUR;2026-09-04;RMS;RMS 18JUN27 1440 P;Equity and Index Options;146.275;-7313.75;5.0;-8494.18925;-5.7;O
Open;Trades;EUR;2026-03-30;RMS;RMS 17DEC27 1520 P;Equity and Index Options;192.93;-9646.5;5.0;-11203.445099999999;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 17DEC27 1680 P;Equity and Index Options;215.0;10750.0;-5.0;12485.05;-5.7;O
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 410 P;Equity and Index Options;12.5;2500.0;-2.0;2903.5;-2.28;O
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 430 P;Equity and Index Options;20.2;-2020.0;1.0;-2346.028;-1.14;O
Open;Trades;EUR;2026-03-17;MC;MC 18DEC26 440 P;Equity and Index Options;37.95;3795.0;-1.0;4407.513;-1.14;O
Open;Trades;EUR;2026-03-04;RI;RI 20MAR26 93 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-02-16;SAP;SAP 20MAR26 220 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-03-10;SAP;SAP 20MAR26 240 P;Equity and Index Options;71.23;-7123.0;1.0;-8272.6522;-1.17;C
Open;Trades;EUR;2026-03-10;SAP;SAP 18DEC26 190 P;Equity and Index Options;31.79;6358.0;-2.0;7384.1812;-2.34;O
Open;Trades;GBP;2026-07-28;IMB;IMB 18DEC26 30 C;Equity and Index Options;0.9;900.0;-1.0;1193.8113396;-1.4;O
Open;Trades;GBP;2026-02-27;IMB;IMB 20MAR26 34.5 C;Equity and Index Options;0.23;230.0;-1.0;305.08512012;-1.4;O
Open;Trades;GBP;2026-03-20;IMB;IMB 20MAR26 34.5 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;GBP;2026-03-20;NWG;NWG 20MAR26 5 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2026-05-14;16;16 29JUN26 150 C;Equity and Index Options;3.86;15440.0;-4.0;1973.3013873599998;-42.88;O
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
Open;Trades;USD;2026-09-01;CPRT;CPRT 18SEP26 32.5 C;Equity and Index Options;1.121666667;-673.0;6.0;-673.0;-1.04576;"C;P"
Open;Trades;USD;2026-07-22;CPRT;CPRT 18SEP26 25 P;Equity and Index Options;0.65;325.0;-5.0;325.0;-7.749645;O
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
Open;Trades;USD;2026-08-31;912797VR5;912797VR5 4.1558258%;Treasury Bills;96.2942056;-96294.21;100000.0;-96294.21;-5.0;O
Open;Transaction Fees;EUR;2026-03-04;RI;RI;Stocks;93.0;-37.2;0.0;-43.204080000000005;;
Open;Transaction Fees;HKD;2026-04-01;1810;1810;Stocks;31.8;-7.18126;0.0;-0.9177973005824399;;
Open;Transaction Fees;HKD;2026-04-10;1810;1810;Stocks;31.0;-7.1767;0.0;-0.9172145120898;;
Open;Transaction Fees;HKD;2026-04-10;700;700;Stocks;502.0;-52.4307;0.0;-6.7008790835658;;
Open;Withholding Tax;EUR;2026-02-18;RMS;RMS;;;-3.72;;-4.3204080000000005;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-11.242352;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;9.68;;11.242352;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-11.242352;;
Open;Withholding Tax;EUR;2026-04-30;MC;MC;;;-18.75;;-21.77625;;
Open;Withholding Tax;EUR;2026-05-08;SAP;SAP;;;-61.32;;-71.217048;;
Open;Withholding Tax;EUR;2026-07-24;RI;RI;;;-58.75;;-68.23225;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.575253021579998;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;90.57;;11.575253021579998;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.575253021579998;;
Open;Withholding Tax;HKD;2026-01-26;939;939;;;-81.86;;-10.462075878839999;;
Open;Withholding Tax;HKD;2026-02-09;3968;3968;;;-33.22;;-4.24566529068;;
Open;Withholding Tax;HKD;2026-06-16;1398;1398;;;-116.65;;-14.9083942251;;
Open;Withholding Tax;HKD;2026-06-26;386;386;;;-25.78;;-3.29479985532;;
Open;Withholding Tax;HKD;2026-07-15;2318;2318;;;-100.56;;-12.85201991664;;
Open;Withholding Tax;HKD;2026-07-31;857;857;;;-22.27;;-2.8462060813799996;;
Open;Withholding Tax;HKD;2026-08-13;390;390;;;-98.9;;-12.6398644566;;
Open;Withholding Tax;HKD;2026-08-21;3968;3968;;;-57.66;;-7.369207124039999;;
Open;Withholding Tax;HKD;2026-08-21;939;939;;;-93.42;;-11.93949582948;;
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
Open;Dividends;CNH;2026-07-31;3188;3188;;;548.9;;81.76066177839999;;
Open;Dividends;CNH;2026-07-31;3188;3188;;;1.1;;0.1638490216;;
Open;Dividends;EUR;2026-02-18;RMS;RMS;;;15.0;;17.421;;
Open;Dividends;EUR;2026-04-23;RMS;RMS;;;39.0;;45.2946;;
Open;Dividends;EUR;2026-04-30;MC;MC;;;75.0;;87.105;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;232.5;;270.0255;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;17.5;;20.3245;;
Open;Dividends;EUR;2026-07-24;RI;RI;;;235.0;;272.929;;
Open;Dividends;GBP;2025-12-31;IMB;IMB;;;400.8;;531.6439832352;;
Open;Dividends;GBP;2026-03-31;IMB;IMB;;;400.8;;531.6439832352;;
Open;Dividends;GBP;2026-06-30;IMB;IMB;;;416.8;;552.8672959392001;;
Open;Dividends;HKD;2026-01-26;1398;1398;;;931.62;;119.06522270027999;;
Open;Dividends;HKD;2026-01-26;939;939;;;818.62;;104.62331487828;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;220.7;;28.206451825799995;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;333.34;;42.60235002995999;;
Open;Dividends;HKD;2026-03-19;16;16;;;4410.0;;563.6178185399999;;
Open;Dividends;HKD;2026-03-27;14;14;;;810.0;;103.52164013999999;;
Open;Dividends;HKD;2026-04-23;808;808;;;1142.0;;145.952732148;;
Open;Dividends;HKD;2026-04-30;5;5;;;8148.56;;1041.42258762864;;
Open;Dividends;HKD;2026-04-30;5;5;;;306.5;;39.172077411;;
Open;Dividends;HKD;2026-05-07;1972;1972;;;4480.0;;572.56413312;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;3.84;;0.49076925695999996;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;103.61;;13.241823623339998;;
Open;Dividends;HKD;2026-05-29;215;215;;;2605.0;;332.93070687;;
Open;Dividends;HKD;2026-05-29;2800;2800;;;285.0;;36.42428079;;
Open;Dividends;HKD;2026-06-01;700;700;;;3741.8;;478.2188556492;;
Open;Dividends;HKD;2026-06-12;1299;1299;;;864.48;;110.48442897311999;;
Open;Dividends;HKD;2026-06-12;27;27;;;800.0;;102.24359519999999;;
Open;Dividends;HKD;2026-06-16;1398;1398;;;1166.46;;149.07883007124;;
Open;Dividends;HKD;2026-06-22;66;66;;;439.66;;56.19052383204;;
Open;Dividends;HKD;2026-06-22;66;66;;;5.34;;0.6824759979599999;;
Open;Dividends;HKD;2026-06-25;1883;1883;;;1300.0;;166.14584219999998;;
Open;Dividends;HKD;2026-06-26;386;386;;;257.8;;32.9479985532;;
Open;Dividends;HKD;2026-06-26;5;5;;;1661.92;;212.40084466848;;
Open;Dividends;HKD;2026-06-26;5;5;;;217.73;;27.826872478619997;;
Open;Dividends;HKD;2026-06-30;2828;2828;;;280.0;;35.78525832;;
Open;Dividends;HKD;2026-07-15;2318;2318;;;1005.64;;128.52531134615998;;
Open;Dividends;HKD;2026-07-31;857;857;;;222.66;;28.456948634039996;;
Open;Dividends;HKD;2026-07-31;857;857;;;316.75;;40.4820734745;;
Open;Dividends;HKD;2026-08-07;3918;3918;;;172.04;;21.987485147759998;;
Open;Dividends;HKD;2026-08-13;390;390;;;989.0;;126.39864456599999;;
Open;Dividends;HKD;2026-08-21;3968;3968;;;576.63;;73.69590537522;;
Open;Dividends;HKD;2026-08-21;939;939;;;936.74;;119.71958170955999;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.96;;0.73735619424;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.26;;0.19970063593999998;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.36;;0.27650857283999997;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.86;;0.66054825734;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;0.8;;0.6144634952;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;0.4;;0.3072317476;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;9.04;;6.943437495759999;;
Open;Dividends;SGD;2026-04-17;D05;D05;;;33.0;;25.346619176999997;;
Open;Dividends;SGD;2026-04-17;D05;D05;;;145.2;;111.52512437879999;;
Open;Dividends;SGD;2026-05-08;O39;O39;;;128.0;;98.314159232;;
Open;Dividends;SGD;2026-05-08;O39;O39;;;336.0;;258.074667984;;
Open;Dividends;SGD;2026-05-14;9CI;9CI;;;144.0;;110.60342913599999;;
Open;Dividends;SGD;2026-05-14;BS6;BS6;;;1400.0;;1075.3111165999999;;
Open;Dividends;SGD;2026-05-14;F34;F34;;;900.0;;691.2714321;;
Open;Dividends;SGD;2026-05-20;D05;D05;;;33.0;;25.346619176999997;;
Open;Dividends;SGD;2026-05-20;D05;D05;;;145.2;;111.52512437879999;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;0.6;;0.46084762139999996;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;0.13;;0.09985031796999999;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;8.1;;6.2214428888999995;;
Open;Dividends;SGD;2026-08-25;D05;D05;;;145.2;;111.52512437879999;;
Open;Dividends;SGD;2026-08-25;D05;D05;;;33.0;;25.346619176999997;;
Open;Dividends;SGD;2026-08-28;HMN;HMN;;;0.26;;0.19970063593999998;;
Open;Dividends;SGD;2026-08-28;HMN;HMN;;;1.22;;0.93705683018;;
Open;Dividends;SGD;2026-08-28;HMN;HMN;;;0.24;;0.18433904856;;
Open;Dividends;SGD;2026-08-28;O39;O39;;;376.0;;288.797842744;;
Open;Dividends;SGD;2026-09-02;F34;F34;;;450.0;;345.63571605;;
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
Open;Trades;EUR;2025-10-22;MC;MC;Stocks;618.7;-6187.0;10.0;-7185.5818;-3.851265;O
Open;Trades;EUR;2025-10-22;RMS;RMS;Stocks;2194.0;-6582.0;3.0;-7644.3348;-4.08629;O
Open;Trades;GBP;2025-05-22;IMB;IMB;Stocks;30.75;-30750.0;1000.0;-40788.554103;-153.75;"A;O"
Open;Trades;HKD;2025-03-26;1810;1810;Stocks;53.35;-10670.0;200.0;-1363.67395098;-31.90695;O
Open;Trades;HKD;2025-06-30;2888;2888;Stocks;134.1;13410.0;-100.0;1713.85826454;-33.70307;C
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
Open;Trades;EUR;2025-09-03;RMS;RMS 19JUN26 2040 P;Equity and Index Options;170.0;17000.0;-1.0;19743.8;-1.14;O
Open;Trades;EUR;2025-07-15;RI;RI 20MAR26 93 P;Equity and Index Options;9.3;930.0;-1.0;1080.102;-1.14;O
Open;Trades;EUR;2025-03-26;SAP;SAP 16MAY25 235 P;Equity and Index Options;5.35;535.0;-1.0;621.349;-1.17;O
Open;Trades;EUR;2025-05-16;SAP;SAP 16MAY25 235 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2025-08-15;SAP;SAP 20MAR26 220 P;Equity and Index Options;9.9;990.0;-1.0;1149.786;-1.17;O
Open;Trades;EUR;2025-08-08;SAP;SAP 20MAR26 240 P;Equity and Index Options;13.8;1380.0;-1.0;1602.732;-1.17;O
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
Open;Transaction Fees;EUR;2025-10-22;MC;MC;Stocks;618.7;-24.75;0.0;-28.74465;;
Open;Transaction Fees;EUR;2025-10-22;RMS;RMS;Stocks;2194.0;-26.33;0.0;-30.579662;;
Open;Transaction Fees;GBP;2025-05-22;IMB;IMB;Stocks;30.75;-153.75;0.0;-203.942770515;;
Open;Transaction Fees;GBP;2025-05-22;IMB;IMB;Stocks;0.0;-1.5;0.0;-1.9896855660000001;;
Open;Transaction Fees;HKD;2025-03-26;1810;1810;Stocks;53.35;-11.304095;0.0;-1.4447141416029299;;
Open;Transaction Fees;HKD;2025-06-30;2888;2888;Stocks;134.1;-14.382185;0.0;-1.83810787653939;;
Open;Withholding Tax;EUR;2025-12-04;MC;MC;;;-13.75;;-15.96925;;
Open;Withholding Tax;HKD;2025-01-24;1398;1398;;;-93.17;;-11.90754470598;;
Open;Withholding Tax;HKD;2025-01-27;939;939;;;-85.25;;-10.8953331135;;
Open;Withholding Tax;HKD;2025-05-28;939;939;;;-88.71;;-11.337536662739998;;
Open;Withholding Tax;HKD;2025-06-27;386;386;;;-30.46;;-3.89292488724;;
Open;Withholding Tax;HKD;2025-06-30;2318;2318;;;-88.13;;-11.263410056219998;;
Open;Withholding Tax;HKD;2025-07-24;857;857;;;-54.54;;-6.970457102759999;;
Open;Withholding Tax;HKD;2025-07-30;3968;3968;;;-94.82;;-12.118422121079998;;
Open;Withholding Tax;HKD;2025-07-31;390;390;;;-194.8;;-24.896315431199998;;
Open;Withholding Tax;HKD;2025-08-22;1398;1398;;;-108.23;;-13.832280385619999;;
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
Open;Dividends;EUR;2025-12-04;MC;MC;;;55.0;;63.877;;
Open;Dividends;GBP;2025-09-30;IMB;IMB;;;400.8;;531.6439832352;;
Open;Dividends;HKD;2025-01-24;1398;1398;;;931.74;;119.08055923955999;;
Open;Dividends;HKD;2025-01-27;939;939;;;852.52;;108.95588722487999;;
Open;Dividends;HKD;2025-03-19;14;14;;;810.0;;103.52164013999999;;
Open;Dividends;HKD;2025-03-20;16;16;;;4275.0;;546.36421185;;
Open;Dividends;HKD;2025-04-23;808;808;;;1120.0;;143.14103328;;
Open;Dividends;HKD;2025-04-25;5;5;;;6698.42;;856.08817869948;;
Open;Dividends;HKD;2025-05-08;1972;1972;;;4256.0;;543.935926464;;
Open;Dividends;HKD;2025-05-19;2888;2888;;;277.72;;35.49386407368;;
Open;Dividends;HKD;2025-05-28;939;939;;;887.1;;113.3753666274;;
Open;Dividends;HKD;2025-05-29;215;215;;;2605.0;;332.93070687;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;319.22;;40.79775057468;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;10.78;;1.3777324453199997;;
Open;Dividends;HKD;2025-05-30;700;700;;;2727.0;;348.52285513799995;;
Open;Dividends;HKD;2025-06-12;1299;1299;;;785.88;;100.43899574471999;;
Open;Dividends;HKD;2025-06-12;27;27;;;500.0;;63.902246999999996;;
Open;Dividends;HKD;2025-06-13;66;66;;;445.0;;56.87299983;;
Open;Dividends;HKD;2025-06-20;1883;1883;;;1280.0;;163.58975232;;
Open;Dividends;HKD;2025-06-20;5;5;;;1881.53;;240.46798959581997;;
Open;Dividends;HKD;2025-06-27;386;386;;;304.6;;38.9292488724;;
Open;Dividends;HKD;2025-06-30;2318;2318;;;881.33;;112.63793469702;;
Open;Dividends;HKD;2025-07-24;857;857;;;545.4;;69.7045710276;;
Open;Dividends;HKD;2025-07-30;2828;2828;;;520.0;;66.45833687999999;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;133.58;;17.07212430852;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;961.35;;122.8648503069;;
Open;Dividends;HKD;2025-07-31;390;390;;;1948.0;;248.96315431199997;;
Open;Dividends;HKD;2025-08-22;1398;1398;;;1082.3;;138.32280385619998;;
Open;Dividends;HKD;2025-09-09;14;14;;;270.0;;34.507213379999996;;
Open;Dividends;HKD;2025-09-16;66;66;;;36.12;;4.61629832328;;
Open;Dividends;HKD;2025-09-16;66;66;;;173.88;;22.22264541672;;
Open;Dividends;HKD;2025-09-18;808;808;;;1170.0;;149.53125798;;
Open;Dividends;HKD;2025-09-26;5;5;;;1866.43;;238.53814173642;;
Open;Dividends;HKD;2025-09-30;2888;2888;;;26.82;;3.4277165290799996;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;1.24;;0.9524184175599999;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.24;;0.18433904856;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.45;;0.34563571605;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.49;;0.37635889080999996;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.46;;0.35331650974;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.06;;0.04608476214;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;5.33;;4.09386303677;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.24;;0.18433904856;;
Open;Dividends;SGD;2025-04-16;D05;D05;;;132.0;;101.38647670799999;;
Open;Dividends;SGD;2025-05-09;O39;O39;;;328.0;;251.93003303199998;;
Open;Dividends;SGD;2025-05-09;O39;O39;;;128.0;;98.314159232;;
Open;Dividends;SGD;2025-05-13;9CI;9CI;;;144.0;;110.60342913599999;;
Open;Dividends;SGD;2025-05-13;BS6;BS6;;;840.0;;645.18666996;;
Open;Dividends;SGD;2025-05-15;F34;F34;;;900.0;;691.2714321;;
Open;Dividends;SGD;2025-05-27;D05;D05;;;132.0;;101.38647670799999;;
Open;Dividends;SGD;2025-05-27;D05;D05;;;33.0;;25.346619176999997;;
Open;Dividends;SGD;2025-08-21;O39;O39;;;328.0;;251.93003303199998;;
Open;Dividends;SGD;2025-08-25;D05;D05;;;132.0;;101.38647670799999;;
Open;Dividends;SGD;2025-08-25;D05;D05;;;33.0;;25.346619176999997;;
Open;Dividends;SGD;2025-08-28;F34;F34;;;360.0;;276.50857284;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;0.33;;0.25346619177;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;1.18;;0.9063336554199999;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;0.21;;0.16129666749;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;0.73;;0.56069793937;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;0.33;;0.25346619177;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;14.41;;11.06802370729;;
Open;Dividends;SGD;2025-11-24;D05;D05;;;132.0;;101.38647670799999;;
Open;Dividends;SGD;2025-11-24;D05;D05;;;33.0;;25.346619176999997;;
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
Open;Trades;HKD;2024-03-24;1299;1299;Stocks;55.2;-33120.0;600.0;-4232.88484128;-61.43992;O
Open;Trades;HKD;2024-12-27;3968;3968;Stocks;39.7;-19850.0;500.0;-2536.9192058999997;-41.68725;O
Open;Trades;SGD;2024-09-09;C38U;C38UNR;Stocks;0.0;0.0;-0.36;0.0;0.0;C
Open;Trades;SGD;2024-05-16;F34;F34;Stocks;3.16;-3160.0;1000.0;-2427.1308060399997;-3.90576;O
Open;Trades;USD;2024-12-13;AMZN;AMZN;Stocks;200.0;120000.0;-600.0;120000.0;-3.4356;"A;C"
Open;Trades;USD;2024-12-13;AMZN;AMZN;Stocks;205.0;-61500.0;300.0;-61500.0;0.0;"Ex;O"
Open;Trades;USD;2024-05-03;BABA;BABA;Stocks;78.0;15600.0;-200.0;15600.0;-0.1248;"A;C"
Open;Trades;USD;2024-05-10;BABA;BABA;Stocks;80.0;8000.0;-100.0;8000.0;0.0;"A;C"
Open;Trades;USD;2024-09-19;BABA;BABA;Stocks;87.83;43915.0;-500.0;43915.0;-2.58112325;"C;P"
Open;Trades;EUR;2024-11-15;SAP;SAP 21MAR25 220 P;Equity and Index Options;12.7;1270.0;-1.0;1474.978;-1.17;O
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
Open;Transaction Fees;HKD;2024-03-24;1299;1299;Stocks;55.2;-34.94392;0.0;-4.465990013976479;;
Open;Transaction Fees;HKD;2024-12-27;3968;3968;Stocks;39.7;-20.565725;0.0;-2.62839207736815;;
Open;Withholding Tax;HKD;2024-07-26;2318;2318;;;-82.34;;-10.52342203596;;
Open;Withholding Tax;HKD;2024-07-26;386;386;;;-43.88;;-5.60806119672;;
Open;Withholding Tax;HKD;2024-07-29;857;857;;;-50.57;;-6.46307326158;;
Open;Withholding Tax;HKD;2024-08-02;939;939;;;-175.36;;-22.41179606784;;
Open;Withholding Tax;HKD;2024-08-08;390;390;;;-230.1;;-29.407814069399997;;
Open;Withholding Tax;HKD;2024-08-19;1398;1398;;;-201.43;;-25.74365922642;;
Open;Withholding Tax;HKD;2024-09-26;386;386;;;-31.86;;-4.071851178839999;;
Open;Withholding Tax;HKD;2024-10-18;2318;2318;;;-51.02;;-6.52058528388;;
Open;Withholding Tax;HKD;2024-10-28;857;857;;;-48.07;;-6.14356202658;;
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
Open;Dividends;HKD;2024-03-20;16;16;;;4275.0;;546.36421185;;
Open;Dividends;HKD;2024-03-22;14;14;;;810.0;;103.52164013999999;;
Open;Dividends;HKD;2024-04-17;808;808;;;1328.0;;169.724368032;;
Open;Dividends;HKD;2024-04-25;5;5;;;5119.61;;654.3091655273399;;
Open;Dividends;HKD;2024-04-25;5;5;;;703.64;;89.92835415815999;;
Open;Dividends;HKD;2024-04-26;27;27;;;300.0;;38.3413482;;
Open;Dividends;HKD;2024-05-02;1972;1972;;;4032.0;;515.307719808;;
Open;Dividends;HKD;2024-05-17;2888;2888;;;210.1;;26.8517241894;;
Open;Dividends;HKD;2024-05-27;215;215;;;2605.0;;332.93070687;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;41.55;;5.3102767257;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;183.45;;23.445734424299996;;
Open;Dividends;HKD;2024-05-31;700;700;;;2060.4;;263.3283794376;;
Open;Dividends;HKD;2024-06-13;1883;1883;;;1930.0;;246.66267341999998;;
Open;Dividends;HKD;2024-06-14;1299;1299;;;714.42;;91.30608660347998;;
Open;Dividends;HKD;2024-06-21;5;5;;;5808.32;;742.3293985900799;;
Open;Dividends;HKD;2024-07-16;66;66;;;16.91;;2.16117399354;;
Open;Dividends;HKD;2024-07-16;66;66;;;428.09;;54.711825836459994;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;741.05;;94.70952027869998;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;823.39;;105.23294231466;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;-741.05;;-94.70952027869998;;
Open;Dividends;HKD;2024-07-26;386;386;;;438.8;;56.0806119672;;
Open;Dividends;HKD;2024-07-29;857;857;;;505.74;;64.63584479555999;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;36.4;;4.6520835815999995;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;223.6;;28.577084858399996;;
Open;Dividends;HKD;2024-08-02;939;939;;;1753.57;;224.11412654357997;;
Open;Dividends;HKD;2024-08-08;390;390;;;2301.0;;294.07814069399996;;
Open;Dividends;HKD;2024-08-19;1398;1398;;;2014.29;;257.43531421925996;;
Open;Dividends;HKD;2024-09-09;215;215;;;1140.0;;145.69712316;;
Open;Dividends;HKD;2024-09-16;808;808;;;1276.0;;163.078534344;;
Open;Dividends;HKD;2024-09-17;66;66;;;72.66;;9.286274534039999;;
Open;Dividends;HKD;2024-09-17;66;66;;;137.34;;17.55266920596;;
Open;Dividends;HKD;2024-09-20;14;14;;;270.0;;34.507213379999996;;
Open;Dividends;HKD;2024-09-25;1299;1299;;;267.0;;34.123799897999994;;
Open;Dividends;HKD;2024-09-26;386;386;;;318.6;;40.7185117884;;
Open;Dividends;HKD;2024-09-27;1883;1883;;;600.0;;76.6826964;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;21.85;;2.7925281939;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;358.15;;45.773179526099995;;
Open;Dividends;HKD;2024-09-27;5;5;;;1869.78;;238.96628679132;;
Open;Dividends;HKD;2024-10-09;1972;1972;;;1904.0;;243.33975657599998;;
Open;Dividends;HKD;2024-10-10;2888;2888;;;89.69;;11.462785066859999;;
Open;Dividends;HKD;2024-10-18;2318;2318;;;510.24;;65.21096501856;;
Open;Dividends;HKD;2024-10-25;27;27;;;500.0;;63.902246999999996;;
Open;Dividends;HKD;2024-10-28;857;857;;;480.66;;61.43050808604;;
Open;Dividends;HKD;2024-11-21;16;16;;;12325.6;;1575.2670712463998;;
Open;Dividends;HKD;2024-11-21;16;16;;;274.4;;35.0695531536;;
Open;Dividends;HKD;2024-11-29;2800;2800;;;930.0;;118.85817941999998;;
Open;Dividends;HKD;2024-12-19;5;5;;;1865.55;;238.42567378169997;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;0.31;;0.23810460438999997;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;1.62;;1.24428857778;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;0.17;;0.13057349273;;
Open;Dividends;SGD;2024-03-28;C38U;C38U;;;10.08;;7.7422400395199995;;
Open;Dividends;SGD;2024-04-19;D05;D05;;;108.0;;82.95257185199999;;
Open;Dividends;SGD;2024-05-08;BS6;BS6;;;455.0;;349.47611289499997;;
Open;Dividends;SGD;2024-05-10;9CI;9CI;;;144.0;;110.60342913599999;;
Open;Dividends;SGD;2024-05-14;F34;F34;;;880.0;;675.9098447199999;;
Open;Dividends;SGD;2024-05-20;D05;D05;;;118.8;;91.24782903719999;;
Open;Dividends;SGD;2024-05-21;O39;O39;;;336.0;;258.074667984;;
Open;Dividends;SGD;2024-08-23;O39;O39;;;352.0;;270.363937888;;
Open;Dividends;SGD;2024-08-26;D05;D05;;;118.8;;91.24782903719999;;
Open;Dividends;SGD;2024-08-29;F34;F34;;;540.0;;414.76285925999997;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;1.03;;0.79112175007;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;0.39;;0.29955095391;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;0.32;;0.24578539808;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;0.11;;0.08448873058999999;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;0.09;;0.06912714320999999;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;9.84;;7.5579009909599995;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;3.83;;2.94174398327;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;0.07;;0.05376555583;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;0.09;;0.06912714320999999;;
Open;Dividends;SGD;2024-11-25;D05;D05;;;118.8;;91.24782903719999;;
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
Open;Trades;HKD;2023-04-25;3918;3918;Stocks;6.1031;4.96487185;-0.8135;0.6345329345640939;0.0;C
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
Open;Withholding Tax;HKD;2023-06-30;386;386;;;-43.34;;-5.53904676996;;
Open;Withholding Tax;HKD;2023-07-10;2318;2318;;;-85.01;;-10.86466003494;;
Open;Withholding Tax;HKD;2023-08-04;939;939;;;-168.79;;-21.57212054226;;
Open;Withholding Tax;HKD;2023-08-10;390;390;;;-217.64;;-27.815370074159997;;
Open;Withholding Tax;HKD;2023-08-18;1398;1398;;;-197.54;;-25.246499744759998;;
Open;Withholding Tax;HKD;2023-09-28;386;386;;;-31.56;;-4.03350983064;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.37233207084;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;49.86;;6.37233207084;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.37233207084;;
Open;Withholding Tax;HKD;2023-10-30;857;857;;;-0.87;;-0.11118990977999998;;
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
Open;Dividends;HKD;2023-03-16;16;16;;;5571.25;;712.0307871975;;
Open;Dividends;HKD;2023-03-16;16;16;;;53.75;;6.8694915524999995;;
Open;Dividends;HKD;2023-03-20;14;14;;;38.61;;4.93453151334;;
Open;Dividends;HKD;2023-03-20;14;14;;;1131.39;;144.59672646666002;;
Open;Dividends;HKD;2023-04-11;808;808;;;1500.0;;191.706741;;
Open;Dividends;HKD;2023-04-27;5;5;;;4330.56;;553.46502953664;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;3394.56;;433.84002315263996;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;413.44;;52.83948999936;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;1.1;;0.1405849434;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;139.46;;17.82361473324;;
Open;Dividends;HKD;2023-05-29;215;215;;;2605.0;;332.93070687;;
Open;Dividends;HKD;2023-05-31;2800;2800;;;150.0;;19.1706741;;
Open;Dividends;HKD;2023-06-05;700;700;;;1454.4;;185.8788560736;;
Open;Dividends;HKD;2023-06-14;1883;1883;;;1850.0;;236.43831389999997;;
Open;Dividends;HKD;2023-06-23;5;5;;;1879.72;;240.23666346168;;
Open;Dividends;HKD;2023-06-30;386;386;;;433.4;;55.390467699599995;;
Open;Dividends;HKD;2023-07-10;2318;2318;;;850.13;;108.65043448421999;;
Open;Dividends;HKD;2023-07-18;66;66;;;445.0;;56.87299983;;
Open;Dividends;HKD;2023-07-28;2828;2828;;;80.0;;10.22435952;;
Open;Dividends;HKD;2023-07-28;857;857;;;485.54;;62.05419401676;;
Open;Dividends;HKD;2023-08-04;939;939;;;1687.94;;215.72631760235998;;
Open;Dividends;HKD;2023-08-10;390;390;;;2176.4;;278.1537007416;;
Open;Dividends;HKD;2023-08-18;1398;1398;;;1975.41;;252.46627549253998;;
Open;Dividends;HKD;2023-09-04;215;215;;;1140.0;;145.69712316;;
Open;Dividends;HKD;2023-09-05;14;14;;;270.0;;34.507213379999996;;
Open;Dividends;HKD;2023-09-14;808;808;;;1490.0;;190.42869606;;
Open;Dividends;HKD;2023-09-21;5;5;;;1879.68;;240.23155128192;;
Open;Dividends;HKD;2023-09-28;2828;2828;;;568.0;;72.59295259199999;;
Open;Dividends;HKD;2023-09-28;386;386;;;315.6;;40.3350983064;;
Open;Dividends;HKD;2023-09-29;1883;1883;;;600.0;;76.6826964;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;645.48;;82.49524478712;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;1202.52;;153.68746012487998;;
Open;Dividends;HKD;2023-10-13;2888;2888;;;60.04;;7.6733818197599994;;
Open;Dividends;HKD;2023-10-13;66;66;;;210.0;;26.838943739999998;;
Open;Dividends;HKD;2023-10-25;2318;2318;;;505.93;;64.66012764941999;;
Open;Dividends;HKD;2023-10-27;27;27;;;200.0;;25.560898799999997;;
Open;Dividends;HKD;2023-10-30;857;857;;;8.7;;1.1118990977999998;;
Open;Dividends;HKD;2023-10-30;857;857;;;449.44;;57.44045178336;;
Open;Dividends;HKD;2023-11-16;16;16;;;16650.0;;2127.9448251;;
Open;Dividends;HKD;2023-11-30;2800;2800;;;825.0;;105.43870754999999;;
Open;Dividends;HKD;2023-12-21;5;5;;;1872.67;;239.33564177898;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;0.13;;0.09985031796999999;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;0.2;;0.1536158738;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;9.58;;7.358200355019999;;
Open;Dividends;SGD;2023-04-21;D05;D05;;;84.0;;64.518666996;;
Open;Dividends;SGD;2023-04-21;D05;D05;;;100.0;;76.8079369;;
Open;Dividends;SGD;2023-05-10;9CI;9CI;;;144.0;;110.60342913599999;;
Open;Dividends;SGD;2023-05-12;F34;F34;;;880.0;;675.9098447199999;;
Open;Dividends;SGD;2023-05-19;O39;O39;;;320.0;;245.78539808;;
Open;Dividends;SGD;2023-05-22;D05;D05;;;84.0;;64.518666996;;
Open;Dividends;SGD;2023-05-25;BS6;BS6;;;350.0;;268.82777914999997;;
Open;Dividends;SGD;2023-08-24;D05;D05;;;96.0;;73.73561942399999;;
Open;Dividends;SGD;2023-08-25;O39;O39;;;320.0;;245.78539808;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;1.11;;0.85256809959;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;0.5;;0.3840396845;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;0.28;;0.21506222332;;
Open;Dividends;SGD;2023-08-30;F34;F34;;;480.0;;368.67809711999996;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;0.04;;0.03072317476;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;0.44;;0.33795492235999997;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;9.32;;7.15849971908;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.3;;0.23042381069999998;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.08;;0.06144634952;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.1;;0.0768079369;;
Open;Dividends;SGD;2023-11-27;D05;D05;;;96.0;;73.73561942399999;;
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
Open;Trades;HKD;2022-10-31;16;16;Stocks;85.0;-255000.0;3000.0;-32590.145969999998;-543.2675;"O;P"
Open;Trades;HKD;2022-01-18;200;200;Stocks;9.2;-18400.0;2000.0;-2351.6026896;-42.5244;O
Open;Trades;HKD;2022-10-31;2800;2800;Stocks;24.0;-24000.0;1000.0;-3067.307856;-23.584;"A;O"
Open;Trades;HKD;2022-09-13;3918;3918;Stocks;6.0527;5.74280176;-0.9488;0.7339558730791094;0.0;C
Open;Trades;HKD;2022-06-29;700;700;Stocks;485.3;-99971.8;206.0;-12776.8453132692;-220.3252263;"A;O"
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;-0.0476;0.0;0.0;O
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;0.0476;0.0;0.0;Ca
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;221.0;10.5196;-0.0476;1.3444521550824;0.0;C
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
Open;Trades;USD;2022-06-14;META;META;Stocks;325.0;-65000.0;200.0;-65000.0;0.0;"A;O"
Open;Trades;USD;2022-07-27;META;META;Stocks;310.0;-31000.0;100.0;-31000.0;0.0;"A;O"
Open;Trades;USD;2022-10-06;META;META;Stocks;230.0;-23000.0;100.0;-23000.0;0.0;"A;O"
Open;Trades;USD;2022-11-07;META;META;Stocks;96.5;19300.0;-200.0;19300.0;-1.46797;"C;P"
Open;Trades;EUR;2022-10-24;MC;MC 16DEC22 720 P;Equity and Index Options;87.32;-8732.0;1.0;-10141.3448;-1.1;C
Open;Trades;EUR;2022-10-24;MC;MC 15DEC23 720 P;Equity and Index Options;124.72;12472.0;-1.0;14484.9808;-1.1;O
Open;Trades;EUR;2022-01-21;MC;MC 16DEC22 720.0 P;Equity and Index Options;94.1;9410.0;-1.0;10928.774;-1.1;O
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
Open;Transaction Fees;HKD;2022-01-18;200;200;Stocks;9.2;-24.5244;0.0;-3.1343285326536;;
Open;Transaction Fees;HKD;2022-06-29;700;700;Stocks;485.3;-132.8491963;0.0;-16.97872431142817;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-225.845;0.0;-28.864005947429998;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-113.4225;0.0;-14.495905220714999;;
Open;Transaction Fees;HKD;2022-10-31;2800;2800;Stocks;24.0;-0.684;0.0;-0.087418273896;;
Open;Withholding Tax;HKD;2022-06-20;2318;2318;;;-90.17;;-11.52413122398;;
Open;Withholding Tax;HKD;2022-06-23;386;386;;;-72.32;;-9.242821006079998;;
Open;Withholding Tax;HKD;2022-07-27;1398;1398;;;-205.93;;-26.31877944942;;
Open;Withholding Tax;HKD;2022-07-29;857;857;;;-22.62;;-2.89093765428;;
Open;Withholding Tax;HKD;2022-07-29;939;939;;;-170.38;;-21.77532968772;;
Open;Withholding Tax;HKD;2022-08-09;390;390;;;-229.56;;-29.338799642639998;;
Open;Withholding Tax;HKD;2022-09-29;386;386;;;-37.02;;-4.73132236788;;
Open;Withholding Tax;HKD;2022-10-20;2318;2318;;;-42.38;;-5.41635445572;;
Open;Withholding Tax;HKD;2022-10-28;857;857;;;-46.68;;-5.965913779919999;;
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
Open;Dividends;HKD;2022-03-17;16;16;;;1875.0;;239.63342624999999;;
Open;Dividends;HKD;2022-03-25;14;14;;;1170.0;;149.53125798;;
Open;Dividends;HKD;2022-04-11;808;808;;;1724.0;;220.334947656;;
Open;Dividends;HKD;2022-04-28;5;5;;;3388.17;;433.02335243598;;
Open;Dividends;HKD;2022-04-29;27;27;;;300.0;;38.3413482;;
Open;Dividends;HKD;2022-05-05;1972;1972;;;3584.0;;458.05130649599994;;
Open;Dividends;HKD;2022-05-12;2888;2888;;;90.34;;11.54585798796;;
Open;Dividends;HKD;2022-06-06;700;700;;;640.0;;81.79487616;;
Open;Dividends;HKD;2022-06-20;2318;2318;;;901.7;;115.2413122398;;
Open;Dividends;HKD;2022-06-23;386;386;;;723.2;;92.4282100608;;
Open;Dividends;HKD;2022-07-19;66;66;;;510.0;;65.18029193999999;;
Open;Dividends;HKD;2022-07-27;1398;1398;;;2059.31;;263.18907253914;;
Open;Dividends;HKD;2022-07-29;857;857;;;226.22;;28.91193263268;;
Open;Dividends;HKD;2022-07-29;939;939;;;1703.8;;217.75329687719997;;
Open;Dividends;HKD;2022-08-09;390;390;;;2295.6;;293.3879964264;;
Open;Dividends;HKD;2022-09-08;808;808;;;1750.0;;223.6578645;;
Open;Dividends;HKD;2022-09-16;14;14;;;270.0;;34.507213379999996;;
Open;Dividends;HKD;2022-09-29;386;386;;;370.2;;47.31322367879999;;
Open;Dividends;HKD;2022-09-29;5;5;;;1695.13;;216.64523191422;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;1755.52;;224.36334530687998;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;36.48;;4.662307941119999;;
Open;Dividends;HKD;2022-10-14;2888;2888;;;40.18;;5.13518456892;;
Open;Dividends;HKD;2022-10-14;66;66;;;210.0;;26.838943739999998;;
Open;Dividends;HKD;2022-10-20;2318;2318;;;531.06;;67.87185458363999;;
Open;Dividends;HKD;2022-10-28;857;857;;;466.76;;59.65402561943999;;
Open;Dividends;HKD;2022-11-17;16;16;;;16650.0;;2127.9448251;;
Open;Dividends;SGD;2022-01-28;C38U;C38U;;;0.48;;0.36867809712;;
Open;Dividends;SGD;2022-01-28;C38U;C38U;;;8.49;;6.520993842809999;;
Open;Dividends;SGD;2022-03-15;C38U;C38U;;;0.19;;0.14593508011;;
Open;Dividends;SGD;2022-03-15;C38U;C38U;;;0.5;;0.3840396845;;
Open;Dividends;SGD;2022-04-22;D05;D05;;;72.0;;55.301714567999994;;
Open;Dividends;SGD;2022-05-06;F34;F34;;;840.0;;645.18666996;;
Open;Dividends;SGD;2022-05-20;9CI;9CI;;;36.0;;27.650857283999997;;
Open;Dividends;SGD;2022-05-20;9CI;9CI;;;144.0;;110.60342913599999;;
Open;Dividends;SGD;2022-05-20;O39;O39;;;224.0;;172.049778656;;
Open;Dividends;SGD;2022-05-25;D05;D05;;;72.0;;55.301714567999994;;
Open;Dividends;SGD;2022-05-27;BS6;BS6;;;350.0;;268.82777914999997;;
Open;Dividends;SGD;2022-08-24;F34;F34;;;480.0;;368.67809711999996;;
Open;Dividends;SGD;2022-08-25;O39;O39;;;224.0;;172.049778656;;
Open;Dividends;SGD;2022-08-26;D05;D05;;;72.0;;55.301714567999994;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;1.3;;0.9985031797;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;0.06;;0.04608476214;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;8.31;;6.38273955639;;
Open;Dividends;SGD;2022-11-24;D05;D05;;;72.0;;55.301714567999994;;
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
Open;Trades;HKD;2021-09-19;1398;1398;Stocks;4.18;-12540.0;3000.0;-1602.6683547599998;-35.33858;O
Open;Trades;HKD;2021-05-10;14;14;Stocks;28.5;-28500.0;1000.0;-3642.428079;-52.5695;O
Open;Trades;HKD;2021-09-15;200;200;Stocks;9.33;-9330.0;1000.0;-1192.41592902;-31.25191;O
Open;Trades;HKD;2021-04-25;2318;2318;Stocks;86.3;-43150.0;500.0;-5514.7639161;-79.68505;O
Open;Trades;HKD;2021-04-25;390;390;Stocks;3.92;-39200.0;10000.0;-5009.9361647999995;-72.4184;"O;P"
Open;Trades;HKD;2021-05-11;3918;3918;Stocks;7.8;-15600.0;2000.0;-1993.7501063999998;-34.4212;O
Open;Trades;HKD;2021-04-20;700;700;Stocks;614.5;-61450.0;100.0;-7853.5861563;-112.81915;O
Open;Trades;HKD;2021-08-15;700;700;Stocks;455.8;-91160.0;200.0;-11650.65767304;-194.38932;O
Open;Trades;HKD;2021-04-26;939;939;Stocks;6.38;-25520.0;4000.0;-3261.57068688;-47.10504;O
Open;Trades;SGD;2021-09-10;C38U;C38U;Stocks;2.0;1.2144;-0.6072;0.9327555857135998;0.0;C
Open;Trades;USD;2021-12-20;BABA;BABA;Stocks;230.0;-46000.0;200.0;-46000.0;0.0;"A;O"
Open;Trades;USD;2021-02-09;FCX;FCX;Stocks;31.38;15690.0;-500.0;15690.0;-2.906249;"C;P"
Open;Trades;USD;2021-01-19;MSFT;MSFT;Stocks;213.0;-21300.0;100.0;-21300.0;-1.0;O
Open;Trades;EUR;2021-03-19;MC;MC 19MAR21 360.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-08-19;700;700 30AUG21 450.0 P;Equity and Index Options;32.3;6460.0;-2.0;825.61703124;-24.0;O
Open;Trades;HKD;2021-08-30;700;700 30AUG21 450.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-09-03;700;700 29JUN22 500.0 P;Equity and Index Options;67.08;13416.0;-2.0;1714.6250915039998;-32.832;O
Open;Trades;HKD;2021-12-06;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.05;1025.0;-1.0;130.99960635;-19.0;O
Open;Trades;HKD;2021-12-20;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.5;1250.0;-1.0;159.7556175;-19.0;O
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
Open;Corporate Actions;SGD;2021-09-20;9CI;C31;Stocks;;1141.2;-1200.0;876.5321759028;;
Open;Corporate Actions;SGD;2021-09-20;C38U;C38U;Stocks;;0.0;185.6072;0.0;;
Open;Corporate Actions;USD;2021-01-04;NOV;US62955J1034) (NOV;Stocks;;0.0;300.0;0.0;;
Open;Corporate Actions;USD;2021-01-04;NOV;US62955J1034) (NOV.OLD;Stocks;;0.0;-300.0;0.0;;
Open;Transaction Fees;HKD;2021-04-20;700;700;Stocks;614.5;-63.65915;0.0;-8.135925454220098;;
Open;Transaction Fees;HKD;2021-04-25;2318;2318;Stocks;86.3;-45.16505;0.0;-5.7722963617347;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-24.63504;0.0;-3.1484688218697596;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-4.10584;0.0;-0.5247448036449599;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-12.31752;0.0;-1.5742344109348798;;
Open;Transaction Fees;HKD;2021-04-26;939;939;Stocks;6.38;-26.68904;0.0;-3.4109792525457596;;
Open;Transaction Fees;HKD;2021-05-10;14;14;Stocks;28.5;-29.7695;0.0;-3.8046758841329997;;
Open;Transaction Fees;HKD;2021-05-11;3918;3918;Stocks;7.8;-16.4212;0.0;-2.0987031568727996;;
Open;Transaction Fees;HKD;2021-08-15;700;700;Stocks;455.8;-121.46132;0.0;-15.523302543172079;;
Open;Transaction Fees;HKD;2021-09-15;200;200;Stocks;9.33;-13.25191;0.0;-1.69365365208354;;
Open;Transaction Fees;HKD;2021-09-19;1398;1398;Stocks;4.18;-17.33858;0.0;-2.2159484435785197;;
Open;Withholding Tax;HKD;2021-06-28;386;386;;;-31.38;;-4.010505021719999;;
Open;Withholding Tax;HKD;2021-07-27;1398;1398;;;-95.97;;-12.26539728918;;
Open;Withholding Tax;HKD;2021-07-30;857;857;;;-21.21;;-2.71073331774;;
Open;Withholding Tax;HKD;2021-08-05;390;390;;;-216.71;;-27.69651189474;;
Open;Withholding Tax;HKD;2021-08-05;939;939;;;-156.33;;-19.97967654702;;
Open;Withholding Tax;HKD;2021-09-30;386;386;;;-38.44;;-4.912804749359999;;
Open;Withholding Tax;HKD;2021-10-25;2318;2318;;;-52.84;;-6.75318946296;;
Open;Withholding Tax;HKD;2021-10-29;857;857;;;-31.31;;-4.001558707139999;;
Open;Withholding Tax;USD;2021-03-11;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-05-03;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-06-10;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-08-02;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-09-09;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-11-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-12-09;MSFT;MSFT;;;-18.6;;-18.6;;
Open;Withholding Tax;USD;2021-12-17;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;HKD;2021-03-18;16;16;;;1875.0;;239.63342624999999;;
Open;Dividends;HKD;2021-04-07;808;808;;;1784.0;;228.00321729599997;;
Open;Dividends;HKD;2021-04-29;5;5;;;2796.85;;357.44999904389994;;
Open;Dividends;HKD;2021-05-06;1972;1972;;;3416.0;;436.58015150399996;;
Open;Dividends;HKD;2021-05-20;2888;2888;;;89.41;;11.426999808539998;;
Open;Dividends;HKD;2021-06-07;700;700;;;320.0;;40.89743808;;
Open;Dividends;HKD;2021-06-28;386;386;;;313.8;;40.105050217199995;;
Open;Dividends;HKD;2021-07-05;3918;3918;;;291.4;;37.2422295516;;
Open;Dividends;HKD;2021-07-20;66;66;;;490.0;;62.624202059999995;;
Open;Dividends;HKD;2021-07-27;1398;1398;;;959.73;;122.65780702661999;;
Open;Dividends;HKD;2021-07-30;857;857;;;212.14;;27.112445357159995;;
Open;Dividends;HKD;2021-08-05;390;390;;;2167.1;;276.96511894739996;;
Open;Dividends;HKD;2021-08-05;939;939;;;1563.34;;199.80187764995998;;
Open;Dividends;HKD;2021-09-06;14;14;;;270.0;;34.507213379999996;;
Open;Dividends;HKD;2021-09-08;808;808;;;1802.0;;230.30369818799997;;
Open;Dividends;HKD;2021-09-30;386;386;;;384.4;;49.12804749359999;;
Open;Dividends;HKD;2021-09-30;5;5;;;1308.18;;167.19128296092;;
Open;Dividends;HKD;2021-10-05;1972;1972;;;1736.0;;221.86860158399998;;
Open;Dividends;HKD;2021-10-19;66;66;;;125.0;;15.975561749999999;;
Open;Dividends;HKD;2021-10-22;2888;2888;;;29.9;;3.8213543705999995;;
Open;Dividends;HKD;2021-10-25;2318;2318;;;528.4;;67.53189462959999;;
Open;Dividends;HKD;2021-10-29;857;857;;;313.06;;40.010474891639994;;
Open;Dividends;HKD;2021-11-18;16;16;;;5550.0;;709.3149417;;
Open;Dividends;SGD;2021-05-06;F34;F34;;;520.0;;399.40127187999997;;
Open;Dividends;SGD;2021-05-06;F34;F34;;;720.0;;553.01714568;;
Open;Dividends;SGD;2021-05-18;9CI;C31;;;108.0;;82.95257185199999;;
Open;Dividends;SGD;2021-05-24;D05;D05;;;36.0;;27.650857283999997;;
Open;Dividends;SGD;2021-05-28;BS6;BS6;;;315.0;;241.94500123499998;;
Open;Dividends;SGD;2021-06-25;D05;D05;;;36.0;;27.650857283999997;;
Open;Dividends;SGD;2021-06-29;O39;O39;;;127.2;;97.6996957368;;
Open;Dividends;SGD;2021-08-26;D05;D05;;;66.0;;50.693238353999995;;
Open;Dividends;SGD;2021-08-26;O39;O39;;;200.0;;153.6158738;;
Open;Dividends;SGD;2021-08-27;F34;F34;;;400.0;;307.2317476;;
Open;Dividends;SGD;2021-11-26;D05;D05;;;66.0;;50.693238353999995;;
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
Open;Trades;HKD;2020-03-18;16;16;Stocks;97.5;-48750.0;500.0;-6230.4690825;-89.31625;O
Open;Trades;HKD;2020-03-18;16;16;Stocks;96.5;-48250.0;500.0;-6166.5668355;-88.90275;O
Open;Trades;HKD;2020-07-20;16;16;Stocks;94.05;-47025.0;500.0;-6010.006330349999;-86.889675;O
Open;Trades;HKD;2020-01-29;1972;1972;Stocks;24.65;-24650.0;1000.0;-3150.3807770999997;-45.38555;O
Open;Trades;HKD;2020-05-04;1972;1972;Stocks;20.9;-20900.0;1000.0;-2671.1139246;-39.5643;O
Open;Trades;HKD;2020-05-24;1972;1972;Stocks;17.78;-35560.0;2000.0;-4544.7278066399995;-67.40812;"O;P"
Open;Trades;HKD;2020-07-20;1972;1972;Stocks;18.2;-29120.0;1600.0;-3721.66686528;-55.08224;"O;P"
Open;Trades;HKD;2020-07-22;27;27;Stocks;51.5;-51500.0;1000.0;-6581.931441;-94.5905;O
Open;Trades;HKD;2020-03-18;2800;2800;Stocks;21.7;-10850.0;500.0;-1386.6787599;-18.29295;O
Open;Trades;HKD;2020-03-31;5;5;Stocks;40.8;-16320.0;400.0;-2085.7693420799997;-35.44064;O
Open;Trades;HKD;2020-05-14;5;5;Stocks;38.0;-45600.0;1200.0;-5827.884926399999;-83.7112;O
Open;Trades;HKD;2020-05-21;66;66;Stocks;40.0;-20000.0;500.0;-2556.08988;-38.54;O
Open;Trades;HKD;2020-09-29;700;700;Stocks;530.0;-53000.0;100.0;-6773.638182;-101.981;"A;O"
Open;Trades;HKD;2020-04-23;808;808;Stocks;2.38;-23800.0;10000.0;-3041.7469572;-48.6826;"O;P"
Open;Trades;HKD;2020-07-22;808;808;Stocks;2.29;-22900.0;10000.0;-2926.7229125999997;-41.9383;O
Open;Trades;SGD;2020-07-22;BS6;BS6;Stocks;0.935;-3085.5;3300.0;-2369.9088930495;-2.5;O
Open;Trades;SGD;2020-08-26;BS6;BS6;Stocks;0.92;-3404.0;3700.0;-2614.542172076;-2.7232;O
Open;Trades;SGD;2020-07-23;9CI;C31;Stocks;2.83;-3396.0;1200.0;-2608.397537124;-2.7168;O
Open;Trades;SGD;2020-07-22;D05;D05;Stocks;21.2;-4240.0;200.0;-3256.65652456;-3.392;O
Open;Trades;SGD;2020-09-08;F34;F34;Stocks;4.32;-4320.0;1000.0;-3318.1028740799998;-3.456;O
Open;Trades;SGD;2020-07-22;O39;O39;Stocks;9.13;-3652.0;400.0;-2805.025855588;-2.9216;O
Open;Trades;SGD;2020-08-26;O39;O39;Stocks;8.62;-3448.0;400.0;-2648.337664312;-2.7584;O
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
Open;Trades;USD;2020-03-18;SOUHY;SOUHY;Stocks;4.85;-485.0;100.0;-485.0;-1.0;O
Open;Trades;EUR;2020-05-04;MC;MC 19MAR21 360.0 P;Equity and Index Options;50.8;5080.0;-1.0;5899.912;-1.1;O
Open;Trades;HKD;2020-07-24;27;27 28AUG20 50.0 P;Equity and Index Options;2.18;4360.0;-2.0;557.2275938399999;-24.0;O
Open;Trades;HKD;2020-08-28;27;27 28AUG20 50.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2020-07-24;700;700 29SEP20 530.0 P;Equity and Index Options;38.02;3802.0;-1.0;485.91268618799995;-21.0;O
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
Open;Transaction Fees;HKD;2020-01-29;1972;1972;Stocks;24.65;-25.66555;0.0;-3.2801726309816996;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;97.5;-50.31625;0.0;-6.430642871227499;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;96.5;-50.30275;0.0;-6.4289175105585;;
Open;Transaction Fees;HKD;2020-03-18;2800;2800;Stocks;21.7;-0.29295;0.0;-0.0374403265173;;
Open;Transaction Fees;HKD;2020-03-31;5;5;Stocks;40.8;-17.44064;0.0;-2.2289921702361597;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39162619878444;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39162619878444;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39162619878444;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39162619878444;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39162619878444;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39162619878444;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39162619878444;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39162619878444;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-5.12852;0.0;-0.6554479035688799;;
Open;Transaction Fees;HKD;2020-05-04;1972;1972;Stocks;20.9;-21.5643;0.0;-2.7560144499641996;;
Open;Transaction Fees;HKD;2020-05-14;5;5;Stocks;38.0;-47.2312;0.0;-6.0363596170128;;
Open;Transaction Fees;HKD;2020-05-21;66;66;Stocks;40.0;-20.54;0.0;-2.6251043067599995;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.523488741077928;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.523488741077928;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.9661504703117119;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.9661504703117119;;
Open;Transaction Fees;HKD;2020-07-20;16;16;Stocks;94.05;-49.269675;0.0;-6.296885882919449;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.04755720334064;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.04755720334064;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-4.09828;0.0;-0.52377860167032;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-11.29484;0.0;-1.44353131101096;;
Open;Transaction Fees;HKD;2020-07-22;808;808;Stocks;2.29;-23.6183;0.0;-3.0185248806402;;
Open;Transaction Fees;HKD;2020-07-22;27;27;Stocks;51.5;-53.3905;0.0;-6.823545836907;;
Open;Transaction Fees;HKD;2020-09-29;700;700;Stocks;530.0;-54.431;0.0;-6.956526412913999;;
Open;Withholding Tax;HKD;2020-06-19;386;386;;;-41.54;;-5.308998680759999;;
Open;Withholding Tax;HKD;2020-07-21;1398;1398;;;-74.27;;-9.492039769379998;;
Open;Withholding Tax;HKD;2020-07-31;857;857;;;-14.44;;-1.8454968933599998;;
Open;Withholding Tax;HKD;2020-11-05;386;386;;;-15.98;;-2.0423158141199997;;
Open;Withholding Tax;HKD;2020-11-13;857;857;;;-19.59;;-2.5036900374599997;;
Open;Withholding Tax;USD;2020-02-03;FCX;FCX;;;-16.5;;-16.5;;
Open;Withholding Tax;USD;2020-03-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;HKD;2020-05-07;1972;1972;;;590.0;;75.40465146;;
Open;Dividends;HKD;2020-05-29;2800;2800;;;45.0;;5.75120223;;
Open;Dividends;HKD;2020-06-19;386;386;;;415.4;;53.08998680759999;;
Open;Dividends;HKD;2020-07-02;200;200;;;30.1;;3.8469152693999997;;
Open;Dividends;HKD;2020-07-21;1398;1398;;;862.3;;110.20581517619999;;
Open;Dividends;HKD;2020-07-30;2828;2828;;;488.0;;62.368593071999996;;
Open;Dividends;HKD;2020-07-31;857;857;;;144.4;;18.4549689336;;
Open;Dividends;HKD;2020-09-09;808;808;;;1798.0;;229.792480212;;
Open;Dividends;HKD;2020-10-06;1972;1972;;;1680.0;;214.71154991999998;;
Open;Dividends;HKD;2020-10-09;66;66;;;5.75;;0.7348758405;;
Open;Dividends;HKD;2020-10-09;66;66;;;119.25;;15.240685909499998;;
Open;Dividends;HKD;2020-11-05;386;386;;;159.8;;20.4231581412;;
Open;Dividends;HKD;2020-11-13;857;857;;;195.86;;25.03178819484;;
Open;Dividends;HKD;2020-11-19;16;16;;;5550.0;;709.3149417;;
Open;Dividends;SGD;2020-06-24;F34;F34;;;665.0;;510.772780385;;
Open;Dividends;SGD;2020-08-27;F34;F34;;;280.0;;215.06222332;;
Open;Dividends;SGD;2020-10-05;D05;D05;;;36.0;;27.650857283999997;;
Open;Dividends;SGD;2020-10-07;O39;O39;;;63.6;;48.8498478684;;
Open;Dividends;SGD;2020-12-29;D05;D05;;;36.0;;27.650857283999997;;
Open;Dividends;USD;2020-02-03;FCX;FCX;;;55.0;;55.0;;
Open;Dividends;USD;2020-03-24;BHP;BHP;;;1560.0;;1560.0;;
Open;Dividends;USD;2020-03-27;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2020-04-02;SOUHY;SOUHY;;;11.0;;11.0;;
Open;Dividends;USD;2020-04-02;SOUHY;SOUHY;;;11.0;;11.0;;
Open;Dividends;USD;2020-05-13;H78;H78;;;32.0;;32.0;;
Open;Dividends;USD;2020-09-22;BHP;BHP;;;1320.0;;1320.0;;
Open;Dividends;USD;2020-10-08;SOUHY;SOUHY;;;15.0;;15.0;;
Open;Dividends;USD;2020-10-14;H78;H78;;;60.0;;60.0;;
Open;Trades;HKD;2019-03-08;1398;1398;Stocks;5.9;-17700.0;3000.0;-2262.1395438;-36.4779;O
Open;Trades;USD;2019-02-11;AMZN;AMZN;Stocks;1800.0;-180000.0;100.0;-180000.0;0.0;"A;O"
Open;Trades;USD;2019-02-19;AMZN;AMZN;Stocks;1628.88;81444.0;-50.0;81444.0;-2.064722;C
Open;Trades;USD;2019-10-14;FCX;FCX;Stocks;15.0;-1500.0;100.0;-1500.0;0.0;"A;O"
Open;Trades;USD;2019-02-11;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2019-03-08;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;3.8;1900.0;-5.0;1900.0;-3.9787;O
Open;Trades;USD;2019-10-14;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2019-03-08;1398;1398;Stocks;5.9;-18.4779;0.0;-2.3615586596826;;
Open;Withholding Tax;HKD;2019-06-21;386;386;;;-60.6;;-7.7449523364;;
Open;Withholding Tax;HKD;2019-07-23;1398;1398;;;-85.12;;-10.87871852928;;
Open;Withholding Tax;HKD;2019-08-02;857;857;;;-20.47;;-2.61615799218;;
Open;Withholding Tax;HKD;2019-09-26;386;386;;;-26.78;;-3.42260434932;;
Open;Withholding Tax;HKD;2019-11-01;857;857;;;-17.26;;-2.2059055664400002;;
Open;Withholding Tax;USD;2019-02-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-03-29;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-05-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-06-28;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-08-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-09-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-11-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-12-20;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;CNH;2019-07-31;3188;3188;;;390.0;;58.091925839999995;;
Open;Dividends;HKD;2019-01-04;200;200;;;24.38;;3.1158735637199997;;
Open;Dividends;HKD;2019-04-08;5;5;;;1318.35;;168.49105466489996;;
Open;Dividends;HKD;2019-05-16;2888;2888;;;150.56;;19.242244616639997;;
Open;Dividends;HKD;2019-06-21;386;386;;;606.0;;77.449523364;;
Open;Dividends;HKD;2019-07-05;200;200;;;23.5;;3.0034056089999996;;
Open;Dividends;HKD;2019-07-05;5;5;;;625.02;;79.88036483987999;;
Open;Dividends;HKD;2019-07-23;1398;1398;;;854.32;;109.18593531408;;
Open;Dividends;HKD;2019-07-30;2828;2828;;;640.0;;81.79487616;;
Open;Dividends;HKD;2019-08-02;857;857;;;204.72;;26.164136011679997;;
Open;Dividends;HKD;2019-09-26;386;386;;;267.8;;34.2260434932;;
Open;Dividends;HKD;2019-09-26;5;5;;;625.44;;79.93404272736;;
Open;Dividends;HKD;2019-09-27;2828;2828;;;520.0;;66.45833687999999;;
Open;Dividends;HKD;2019-10-04;200;200;;;61.1;;7.8088545834;;
Open;Dividends;HKD;2019-10-21;2888;2888;;;70.24;;8.976987658559999;;
Open;Dividends;HKD;2019-11-01;857;857;;;172.56;;22.053943484639998;;
Open;Dividends;HKD;2019-11-20;5;5;;;626.63;;80.08613007522;;
Open;Dividends;SGD;2019-05-16;F34;F34;;;490.0;;376.35889081;;
Open;Dividends;SGD;2019-08-30;F34;F34;;;210.0;;161.29666749;;
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
Open;Trades;HKD;2018-01-01;1398;1398;Stocks;6.48;6480.0;-1000.0;828.1731211199999;-25.17496;C
Open;Trades;HKD;2018-02-05;215;215;Stocks;3.35;6700.0;-2000.0;856.2901098;-25.1809;C
Open;Trades;HKD;2018-02-19;215;215;Stocks;3.4;6800.0;-2000.0;869.0705591999999;-25.1836;C
Open;Trades;USD;2018-08-17;FCX;FCX;Stocks;21.0;-21000.0;1000.0;-21000.0;0.0;"A;O"
Open;Trades;USD;2018-10-26;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;216.21;21621.0;-1.0;21621.0;-1.367373;O
Open;Trades;USD;2018-01-18;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;3.08;3080.0;-10.0;3080.0;-5.006148;O
Open;Trades;USD;2018-08-17;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;0.0;0.0;10.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2018-01-01;1398;1398;Stocks;6.48;-7.17496;0.0;-0.9169921322702399;;
Open;Transaction Fees;HKD;2018-02-05;215;215;Stocks;3.35;-7.1809;0.0;-0.9177512909646;;
Open;Transaction Fees;HKD;2018-02-19;215;215;Stocks;3.4;-7.1836;0.0;-0.9180963630983999;;
Open;Withholding Tax;HKD;2018-06-14;386;386;;;-98.64;;-12.60663528816;;
Open;Withholding Tax;HKD;2018-07-26;857;857;;;-14.86;;-1.8991747808399997;;
Open;Withholding Tax;HKD;2018-09-21;386;386;;;-36.5;;-4.664864031;;
Open;Withholding Tax;HKD;2018-11-01;857;857;;;-20.39;;-2.6059336326599998;;
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
Open;Dividends;HKD;2018-04-06;5;5;;;1317.81;;168.42204023813997;;
Open;Dividends;HKD;2018-05-17;2888;2888;;;110.45;;14.116006362299999;;
Open;Dividends;HKD;2018-06-14;386;386;;;986.4;;126.06635288159998;;
Open;Dividends;HKD;2018-07-04;200;200;;;40.0;;5.11217976;;
Open;Dividends;HKD;2018-07-05;5;5;;;627.87;;80.24460764778;;
Open;Dividends;HKD;2018-07-26;857;857;;;148.62;;18.99430389828;;
Open;Dividends;HKD;2018-07-30;2828;2828;;;440.0;;56.23397736;;
Open;Dividends;HKD;2018-09-21;386;386;;;365.0;;46.64864031;;
Open;Dividends;HKD;2018-09-27;5;5;;;627.73;;80.22671501862;;
Open;Dividends;HKD;2018-09-28;2828;2828;;;800.0;;102.24359519999999;;
Open;Dividends;HKD;2018-10-05;200;200;;;45.0;;5.75120223;;
Open;Dividends;HKD;2018-10-22;2888;2888;;;60.13;;7.68488422422;;
Open;Dividends;HKD;2018-11-01;857;857;;;203.88;;26.056780236719998;;
Open;Dividends;HKD;2018-11-21;5;5;;;626.32;;80.04651068208;;
Open;Dividends;SGD;2018-05-16;F34;F34;;;490.0;;376.35889081;;
Open;Dividends;SGD;2018-08-31;F34;F34;;;245.0;;188.179445405;;
Open;Dividends;USD;2018-03-27;BHP;BHP;;;1320.0;;1320.0;;
Open;Dividends;USD;2018-03-30;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-04-05;SOUHY;SOUHY;;;73.0;;73.0;;
Open;Dividends;USD;2018-06-29;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-09-25;BHP;BHP;;;1512.0;;1512.0;;
Open;Dividends;USD;2018-09-28;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-10-11;SOUHY;SOUHY;;;62.0;;62.0;;
Open;Dividends;USD;2018-11-01;FCX;FCX;;;50.0;;50.0;;
Open;Dividends;USD;2018-12-21;NOV;NOV;;;15.0;;15.0;;
Open;Withholding Tax;HKD;2025-09-25;386;386;;;-19.34;;-2.47173891396;;
Open;Withholding Tax;HKD;2025-10-23;857;857;;;-48.11;;-6.148674206339999;;
Open;Withholding Tax;HKD;2025-10-24;2318;2318;;;-52.02;;-6.64838977788;;
Open;Withholding Tax;HKD;2025-12-23;390;390;;;-89.85;;-11.483233785899998;;
Open;Dividends;HKD;2025-09-16;215;215;;;1140.0;;145.69712316;;
Open;Dividends;HKD;2025-09-23;1299;1299;;;294.0;;37.574521235999995;;
Open;Dividends;HKD;2025-09-25;386;386;;;193.4;;24.717389139599998;;
Open;Dividends;HKD;2025-09-26;1883;1883;;;600.0;;76.6826964;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;26.5;;3.3868190909999996;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;173.5;;22.174079708999997;;
Open;Dividends;HKD;2025-09-30;3918;3918;;;159.42;;20.374592433479997;;
Open;Dividends;HKD;2025-10-09;1972;1972;;;1960.0;;250.49680823999998;;
Open;Dividends;HKD;2025-10-23;857;857;;;482.1;;61.6145465574;;
Open;Dividends;HKD;2025-10-24;2318;2318;;;520.24;;66.48900995855999;;
Open;Dividends;HKD;2025-10-31;27;27;;;700.0;;89.46314579999999;;
Open;Dividends;HKD;2025-11-20;16;16;;;12600.0;;1610.3366244;;
Open;Dividends;HKD;2025-11-28;2800;2800;;;825.0;;105.43870754999999;;
Open;Dividends;HKD;2025-12-18;5;5;;;1866.53;;238.55092218581999;;
Open;Dividends;HKD;2025-12-23;390;390;;;898.95;;114.8898498813;;
Open;Dividends;CNH;2022-07-29;3188;3188;;;370.0;;55.11285272;;
Open;Dividends;HKD;2022-05-26;215;215;;;2605.0;;332.93070687;;
Open;Dividends;HKD;2022-05-31;2800;2800;;;40.0;;5.11217976;;
Open;Dividends;HKD;2022-06-13;1883;1883;;;1700.0;;217.26763979999998;;
Open;Dividends;HKD;2022-07-28;2828;2828;;;460.0;;58.79006724;;
Open;Dividends;HKD;2022-09-02;215;215;;;1140.0;;145.69712316;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;283.5;;36.232574049;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;76.5;;9.777043790999999;;
Open;Dividends;HKD;2022-09-30;1883;1883;;;600.0;;76.6826964;;
Open;Dividends;HKD;2022-11-30;2800;2800;;;280.0;;35.78525832;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;243.6;;36.285110601599996;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;56.4;;8.4009861984;;
Open;Dividends;HKD;2021-05-26;215;215;;;2605.0;;332.93070687;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;12.22;;1.56177091668;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;52.78;;6.745521193319999;;
Open;Dividends;HKD;2021-06-04;1883;1883;;;1600.0;;204.48719039999997;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;8.8;;1.1246795472;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;311.2;;39.7727585328;;
Open;Dividends;HKD;2021-09-03;215;215;;;1109.72;;141.82720308167998;;
Open;Dividends;HKD;2021-09-03;215;215;;;30.28;;3.86992007832;;
Open;Dividends;HKD;2021-09-03;215;215;;;9637.06;;1231.6595769476398;;
Open;Dividends;HKD;2021-09-03;215;215;;;262.94;;33.60491365236;;
Open;Dividends;HKD;2021-09-29;1883;1883;;;550.0;;70.2924717;;
Open;Dividends;HKD;2021-09-29;2828;2828;;;396.0;;50.610579623999996;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;234.9;;30.0212756406;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;35.1;;4.4859377394;;
Open;Trades;HKD;2020-09-24;1883;1883;Stocks;2.45;-24500.0;10000.0;-3131.210103;-45.2615;O
Open;Trades;HKD;2020-09-02;215;215;Stocks;1.21;-60500.0;50000.0;-7732.1718869999995;-111.0335;O
Open;Transaction Fees;HKD;2020-09-02;215;215;Stocks;1.21;-62.6335;0.0;-8.004842774949;;
Open;Transaction Fees;HKD;2020-09-24;1883;1883;Stocks;2.45;-25.6615;0.0;-3.2796550227809997;;
Open;Dividends;CNH;2020-07-31;3188;3188;;;380.0;;56.60238928;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;9.0;;1.150240446;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;591.0;;75.532455954;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;2.64;;0.33740386416;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;327.36;;41.83807915584;;

---

# [open_positions] START  
Symbol;Account;Asset Category;Description;Currency;Current Price;Cost Price;Quantity;Value;Value in USD;Unrealized Gain in USD;Weight;Unrealized Gain in USD
912797TC1 - United States Treasury B 12/24/26;IBKR;Treasury Bills;;USD;98.883954;97.133959459;740.0;73174.13;73174.13;;0.028060260720090893;1294.9959603400048
912797TV9 - United States Treasury B 02/18/27;IBKR;Treasury Bills;;USD;98.261798;97.064044118;680.0;66818.02;66818.02;;0.02562286783594486;814.4726397600004
912797UE5 - United States Treasury B 04/15/27;IBKR;Treasury Bills;;USD;97.638289;96.90631;1000.0;97638.29;97638.29;;0.037441591361097745;731.9789999999955
912797UX3 - United States Treasury B 05/13/27;IBKR;Treasury Bills;;USD;97.329156;96.625351852;1080.0;105115.49;105115.49;;0.04030889134069796;760.1084798400035
912797VF1 - United States Treasury B 06/10/27;IBKR;Treasury Bills;;USD;96.997996;96.38057197;2640.0;256074.71;256074.71;;0.09819758877108162;1629.9994391999871
912797VQ7 - United States Treasury B 07/08/27;IBKR;Treasury Bills;;USD;96.643405;96.420521978;1820.0;175891.0;175891.0;;0.06744934744447945;405.64710004000887
912797VR5 - United States Treasury B 08/05/27;IBKR;Treasury Bills;;USD;96.290761;96.29921;1000.0;96290.76;96290.76;;0.03692485077083525;-8.448999999998819
AMZN;IBKR;Stocks;AMAZON.COM INC;USD;251.89;207.8670155;300.0;75567.0;75567.0;;0.0289778603699847;13206.895349999993
AMZN 18SEP26 300 C;IBKR;Equity and Index Options;;USD;0.0158;8.01939888;-1.0;-1.58;-1.58;;-6.058864237640217e-07;8.00359888
BABA;IBKR;Stocks;ALIBABA GROUP HOLDING-SP ADR;USD;108.56;111.16845686;500.0;54280.0;54280.0;;0.02081488296323487;-1304.228430000002
BHP;IBKR;Stocks;BHP GROUP LTD-SPON ADR;USD;87.35;35.102226747;1200.0;104820.0;104820.0;;0.040195579075281485;62697.32790359999
BHP 18DEC26 95 C;IBKR;Equity and Index Options;;USD;3.75;4.0005117;-4.0;-1500.0;-1500.0;;-0.0005752086301557168;1.0020467999999987
BRK B;IBKR;Stocks;BERKSHIRE HATHAWAY INC-CL B;USD;507.0;469.0869395;100.0;50700.0;50700.0;;0.019442051699263226;3791.3060499999974
BRK B 18DEC26 510 P;IBKR;Equity and Index Options;;USD;15.6999;9.1593754;-1.0;-1569.99;-1569.99;;-0.0006020478648387825;-6.5405245999999995
BRK B 18DEC26 545 C;IBKR;Equity and Index Options;;USD;5.4;18.66917949;-1.0;-540.0;-540.0;;-0.00020707510685605802;13.26917949
CPRT;IBKR;Stocks;COPART INC;USD;30.75;27.171926655;1100.0;33825.0;33825.0;;0.012970954610011413;3935.8806795
CPRT 09/18/2026 25.00 P;Schwab;Equity and Index Options;SALESFORCE;USD;0.275;0.34332;-5.0;-137.5;-137.5;99.6875;-5.2727457764274036e-05;0.34159999999999996
CPRT 09/18/2026 27.50 P;Schwab;Equity and Index Options;SALESFORCE;USD;0.675;0.99332;-10.0;-675.0;-675.0;219.37499999999997;-0.00025884388357007255;3.1831999999999994
CPRT 18SEP26 25 P;IBKR;Equity and Index Options;;USD;0.0267;0.63450071;-5.0;-13.35;-13.35;;-5.119356808385879e-06;3.0390035500000003
CPRT 20NOV26 35 C;IBKR;Equity and Index Options;;USD;0.9;1.223198932;-6.0;-540.0;-540.0;;-0.00020707510685605802;1.9391935920000003
CRM 09/18/2026 160.00 P;Schwab;Equity and Index Options;SALESFORCE;USD;2.085;6.94315;-2.0;-417.0;-417.0;;-0.00015990799918328926;9.7163
DNOW;IBKR;Stocks;DNOW INC;USD;15.47;26.81106924;25.0;386.75;386.75;;0.00014830795847514897;-283.5267309999999
FAST;IBKR;Stocks;FASTENAL CO;USD;49.01;42.17242457;100.0;4901.0;4901.0;;0.0018793983309287785;683.7575430000002
FCX;IBKR;Stocks;FREEPORT-MCMORAN INC;USD;71.21;11.2079574;500.0;35605.0;35605.0;;0.013653535517796196;30001.021299999997
CASH - Schwab;Schwab;Forex;;USD;1.0;;-1.0;;72675.14;0.0;0.027868911817183293;
Forex;IBKR;Forex;;USD;0.0;;-1.0;;1934.563088292;0.0;0.0007418515893108362;
GEHC 17JUN27 57.5 P;IBKR;Equity and Index Options;;USD;3.8746;5.33945409;-1.0;-387.46;-387.46;;-0.00014858022389342267;1.4648540900000002
GEHC 17JUN27 65 C;IBKR;Equity and Index Options;;USD;8.6706;8.8804028;1.0;867.06;867.06;;0.0003324935965752105;-0.2098028000000003
GEHC 17JUN27 80 C;IBKR;Equity and Index Options;;USD;3.1583;3.91948334;-1.0;-315.83;-315.83;;-0.00012111209444138668;0.7611833400000001
GLD 15JAN27 390 P;IBKR;Equity and Index Options;;USD;17.15;13.74771085;-2.0;-3430.0;-3430.0;;-0.0013153104009560723;-6.804578299999996
GLD 15JAN27 405 P;IBKR;Equity and Index Options;;USD;24.9273;20.505473;1.0;2492.73;2492.73;;0.0009558932057653733;4.421827
GOOG;IBKR;Stocks;ALPHABET INC-CL C;USD;330.39;93.120965215;200.0;66078.0;66078.0;;0.025339090575619633;47453.80695699999
GOOG 18DEC26 410 C;IBKR;Equity and Index Options;;USD;4.25;32.055824165;-2.0;-850.0;-850.0;;-0.0003259515570882395;55.611648329999994
GOOG 21JAN28 250 C;IBKR;Equity and Index Options;;USD;111.4884;132.321833;2.0;22297.68;22297.68;;0.008550545312300348;-41.666866
GOOG 21JAN28 250 P;IBKR;Equity and Index Options;;USD;14.0262;13.467856515;-2.0;-2805.24;-2805.24;;-0.0010757321717720153;-1.11668697
H78;IBKR;Stocks;HONGKONG LAND HOLDINGS LTD;USD;8.53;4.208;1000.0;8530.0;8530.0;;0.0032710197434855093;4321.999999999999
ICE;IBKR;Stocks;INTERCONTINENTAL EXCHANGE IN;USD;155.81;152.14704663;300.0;46743.0;46743.0;;0.017924651332912447;1098.8860109999991
IVV;IBKR;Stocks;ISHARES CORE S&P 500 ETF;USD;761.53;402.65670378;100.0;76153.0;76153.0;;0.02920257520816553;35887.329622
IVV;Schwab;Stocks;ISHARES CORE S&P 500 ETF;USD;776.98;531.9820000000001;10.0;7769.8;7769.8;;0.002979504009722592;2449.9799999999996
LULU 15JAN27 85 P;IBKR;Equity and Index Options;;USD;4.5752;4.59553934;-2.0;-915.04;-915.04;;-0.00035089260329179136;0.040678680000000966
META;IBKR;Stocks;META PLATFORMS INC-CLASS A;USD;644.38;230.10913779;200.0;128876.0;128876.0;;0.04942039161329877;82854.17244200001
META 15JAN27 460 P;IBKR;Equity and Index Options;;USD;5.775;19.71258137;-1.0;-577.5;-577.5;;-0.00022145532260995095;13.937581369999998
META 15JAN27 520 P;IBKR;Equity and Index Options;;USD;14.325;62.7404795;1.0;1432.5;1432.5;;0.0005493242417987095;-48.415479500000004
META 15JAN27 640 P;IBKR;Equity and Index Options;;USD;56.225;77.2419796;-1.0;-5622.5;-5622.5;;-0.0021560736820336783;21.016979599999992
META 15JAN27 800 C;IBKR;Equity and Index Options;;USD;20.875;35.535755475;-2.0;-4175.0;-4175.0;;-0.0016009973539334117;29.321510950000004
MSFT;Schwab;Stocks;MICROSOFT CORP;USD;499.45;470.3566;100.0;49945.0;49945.0;;0.01915253002208485;2909.3399999999974
MSFT 09/18/2026 520.00 C;Schwab;Equity and Index Options;MICROSOFT CORP;USD;11.325;13.4931;-1.0;-1132.5;-1132.5;;-0.00043428251576756616;2.168100000000001
MSFT 15JAN27 420 P;IBKR;Equity and Index Options;;USD;8.2783;53.1437823;-2.0;-1655.66;-1655.66;;-0.000634899947069076;89.7309646
NBIS 18DEC26 140 P;IBKR;Equity and Index Options;;USD;6.3157;24.136075115;-2.0;-1263.14;-1263.14;;-0.00048437935272992806;35.64075023
NBIS 18DEC26 170 P;IBKR;Equity and Index Options;;USD;13.7827;15.93624404;-1.0;-1378.27;-1378.27;;-0.0005285285324564798;2.15354404
NBIS 18DEC26 190 P;IBKR;Equity and Index Options;;USD;21.33;22.3868947;1.0;2133.0;2133.0;;0.0008179466720814292;-1.0568947000000009
NOV;IBKR;Stocks;NOV INC;USD;21.32;62.158401437;300.0;6396.0;6396.0;;0.0024526895989839762;-12251.5204311
NVDA;Moomoo-mom;Stocks;NVIDIA;USD;223.96;0.0;0.4717;105.641932;105.641932;;4.051076732848225e-05;105.64193200000001
NVDA;Moomoo-me;Stocks;NVIDIA;USD;223.96;0.0;1.4431;323.196676;323.196676;;0.00012393701151522735;323.196676
NVO;IBKR;Stocks;NOVO-NORDISK A/S-SPONS ADR;USD;44.01;58.2644644;200.0;8802.0;8802.0;;0.003375324241753746;-2850.892880000001
PDD;IBKR;Stocks;PDD HOLDINGS INC;USD;77.84;96.7605204;100.0;7784.0;7784.0;;0.002984949318088066;-1892.05204
PDD 18DEC26 100 C;IBKR;Equity and Index Options;;USD;0.925;12.173745;2.0;185.0;185.0;;7.094239771920506e-05;-22.49749
PDD 18DEC26 110 C;IBKR;Equity and Index Options;;USD;0.4433;11.5805045;1.0;44.33;44.33;;1.699933238320195e-05;-11.1372045
PDD 18DEC26 120 C;IBKR;Equity and Index Options;;USD;0.2583;5.8261019;-2.0;-51.66;-51.66;;-1.9810185222562885e-05;11.1356038
PDD 18DEC26 130 C;IBKR;Equity and Index Options;;USD;0.1385;6.3994626;-1.0;-13.85;-13.85;;-5.3110930184377845e-06;6.2609626
PDD 18DEC26 90 P;IBKR;Equity and Index Options;;USD;13.3741;8.553596777;-4.0;-5349.64;-5349.64;;-0.0020514393974841523;-19.282012892000004
PDD 20NOV26 100 C;IBKR;Equity and Index Options;;USD;0.445;10.7569825;1.0;44.5;44.5;;1.7064522694619596e-05;-10.3119825
PDD 20NOV26 130 C;IBKR;Equity and Index Options;;USD;0.0169;3.12292012;-1.0;-1.69;-1.69;;-6.480683899754409e-07;3.1060201199999997
SKHY 18DEC26 115 P;IBKR;Equity and Index Options;;USD;1.7709;16.440827835;-2.0;-354.18;-354.18;;-0.00013581826175236783;29.339855670000002
SKHY 18DEC26 130 P;IBKR;Equity and Index Options;;USD;3.6;24.4323006;1.0;360.0;360.0;;0.00013805007123737203;-20.8323006
SMCI 18DEC26 24 P;IBKR;Equity and Index Options;;USD;0.8;2.96300312;-4.0;-320.0;-320.0;;-0.00012271117443321956;8.65201248
SMCI 18DEC26 27 P;IBKR;Equity and Index Options;;USD;1.3875;4.3169028;2.0;277.5;277.5;;0.0001064135965788076;-5.8588056
SOUHY;IBKR;Stocks;SOUTH32 - ADR;USD;18.2;7.17;300.0;5460.0;5460.0;;0.002093759413766809;3309.0
TSM;IBKR;Stocks;TAIWAN SEMICONDUCTOR-SP ADR;USD;428.03;181.60807492;300.0;128409.0;128409.0;;0.049241309993110285;73926.577524
UNH;IBKR;Stocks;UNITEDHEALTH GROUP INC;USD;388.28;322.13551065;200.0;77656.0;77656.0;;0.02977893425558156;13228.897869999992
UNH 15JAN27 360 P;IBKR;Equity and Index Options;;USD;17.1752;12.18938278;-1.0;-1717.52;-1717.52;;-0.0006586215509766977;-4.9858172199999995
UNH 17JUN27 360 P;IBKR;Equity and Index Options;;USD;27.6569;21.4891912;-1.0;-2765.69;-2765.69;;-0.0010605658375569095;-6.1677088
UNH 18DEC26 440 C;IBKR;Equity and Index Options;;USD;8.0253;26.01244859;-1.0;-802.53;-802.53;;-0.00030774812130591155;17.987148590000004
9CI;IBKR;Stocks;CAPITALAND INVESTMENT LTD/SI;SGD;2.61;2.323057932;1200.0;3132.0;2405.624583708;;0.0009224906809757301;264.4731390347938
BS6;IBKR;Stocks;YANGZIJIANG SHIPBUILDING;SGD;5.0;0.171019587;7000.0;35000.0;26882.777915;;0.010308803906178337;25963.28159971279
BS6;Moomoo-me;Stocks;YZJ Shipbuilding;SGD;4.2;4.015;200.0;840.0;652.6800000000001;;0.00025028477915335553;28.418936653000074
C38U;IBKR;Stocks;CAPITALAND INTEGRATED COMMER;SGD;2.28;1.364696005;222.0;506.16;388.77105341304;;0.0001490829767186065;156.0717975128569
CGP1;IBKR;Stocks;NOBLE GROUP LTD;SGD;0.0;85.85;50.0;0.0;0.0;;0.0;-3296.9806914325
D05;IBKR;Stocks;DBS GROUP HOLDINGS LTD;SGD;76.79;18.388145455;220.0;16893.8;12975.7792440122;;0.00497585346943416;9868.597109217744
F34;IBKR;Stocks;WILMAR INTERNATIONAL LTD;SGD;3.71;3.040549529;9000.0;33390.0;25646.17013091;;0.009834598926494133;4627.719858081894
CASH - DBS;DBS;Forex;;SGD;0.763247271;;-0.777;;1043.95389;18.47775410253007;0.0004003275246750879;
CASH - Moomoo-me;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;75899.04609;1343.3964121419413;0.029105190887703008;
CASH - Moomoo-mom;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;7040.27268;124.6112770323598;0.0026997504027903444;
CASH - OCBC;OCBC;Forex;;SGD;0.763247271;;-0.777;;6154.438290000001;108.93220328133066;0.0023600573454458616;
CASH - OCBC Joint;OCBC Joint;Forex;;SGD;0.763247271;;-0.777;;30062.78268;532.1046373023601;0.01152824802935454;
CASH - POSB;POSB;Forex;;SGD;0.763247271;;-0.777;;6713.0158200000005;118.81890263214063;0.0025742564226905705;
CASH - UOB;UOB;Forex;;SGD;0.763247271;;-0.777;;6898.25262;122.09755322574074;0.0026452896266788563;
Forex;IBKR;Forex;;SGD;0.768079369;;-0.78851;;15154.498468;392.659527;0.005811332202983459;
Fullerton SGD Cash Fund;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0;
Fullerton SGD Cash Fund;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0;
HMN;IBKR;Stocks;CAPITALAND ASCOTT TRUST;SGD;0.845;0.505252956;68.0;57.46;44.133840542739996;;1.6924110641400206e-05;17.744783271909196
O39;IBKR;Stocks;OVERSEA-CHINESE BANKING CORP;SGD;31.39;8.7231;800.0;25112.0;19288.009114328;;0.007396419534055726;13927.982599348878
P40U;Moomoo-mom;Stocks;StarhillGbl Reit;SGD;0.53;0.555;1200.0;636.0;494.172;;0.0001895013327875406;-23.04238107000002
P40U;Moomoo-me;Stocks;StarhillGbl Reit;SGD;0.53;0.5448;4200.0;2226.0;1729.602;;0.000663254664756392;-47.74381357703975
Z74;Moomoo-mom;Stocks;Singtel;SGD;4.3;4.29;100.0;430.0;334.11;;0.00012812197028088437;0.7680793689999836
Z74;Moomoo-me;Stocks;Singtel;SGD;4.3;4.88;100.0;430.0;334.11;;0.00012812197028088437;-44.548603402000005
Z77;Moomoo-me;Stocks;Singtel 10;SGD;4.29;4.28;120.0;514.8;399.9996;;0.00015338881465255642;0.9216952427999803
1299;IBKR;Stocks;AIA GROUP LTD;HKD;75.3;55.302399867;600.0;45180.0;5774.20703892;;0.0022142491473951136;1533.4698997274384
1398;IBKR;Stocks;IND & COMM BK OF CHINA - H;HKD;7.61;5.051969413;6000.0;45660.0;5835.553196039999;;0.0022377737067299885;1961.5668288483478
14;IBKR;Stocks;HYSAN DEVELOPMENT CO;HKD;15.93;28.5525695;1000.0;15930.0;2035.9255894199998;;0.0007807213129261655;-1613.2211079273332
16;IBKR;Stocks;SUN HUNG KAI PROPERTIES;HKD;116.7;88.851861372;4500.0;525150.0;67116.5300241;;0.025737338197311728;16016.027697870275
1810;IBKR;Stocks;XIAOMI CORP-CLASS B;HKD;25.92;38.85377485;600.0;15552.0;1987.615490688;;0.0007621957224499514;-991.7967301285054
1883;IBKR;Stocks;CITIC TELECOM INTERNATIONAL;HKD;2.4;2.45452615;10000.0;24000.0;3067.307856;;0.0011762279667437523;-69.68687010518106
1972;IBKR;Stocks;SWIRE PROPERTIES LTD;HKD;24.64;19.720971466;5600.0;137984.0;17634.975300095997;;0.006762526656798746;3520.5741354528172
200;IBKR;Stocks;MELCO INTERNATIONAL DEVELOP.;HKD;3.145;11.460065577;4000.0;12580.0;1607.78053452;;0.0006165394925681835;-4250.810994581212
215;IBKR;Stocks;HUTCHISON TELECOMM HONG KONG;HKD;1.14;1.21222067;50000.0;57000.0;7284.856158;;0.0027935414210164115;-461.5063092845496
2318;IBKR;Stocks;PING AN INSURANCE GROUP CO-H;HKD;54.85;86.4593701;500.0;27425.0;3505.03824795;;0.0013440854994978086;-2019.9097756446145
27;IBKR;Stocks;GALAXY ENTERTAINMENT GROUP L;HKD;32.08;51.5945905;1000.0;32080.0;4099.96816752;;0.0015722247155474823;-2494.0523644697078
2800;IBKR;Stocks;TRACKER FUND OF HONG KONG-B;HKD;25.58;21.769917967;1500.0;38370.0;4903.85843478;;0.001880494461831574;730.4184094890838
2828;IBKR;Stocks;HANGSENG CHINA ENT INDEX;HKD;85.36;92.44076825;400.0;34144.0;4363.756643136;;0.001673380320687445;-361.9816013290065
2888;IBKR;Stocks;STANDARD CHARTERED PLC;HKD;241.8;55.6;28.0;6770.4;865.2875461775999;;0.0003318139094184125;666.3215099184
3188;IBKR;Stocks;CHINAAMC ETF SERIES - CH;HKD;55.18;26.37987258;1000.0;55180.0;7052.25197892;;0.0027043441335383436;3680.785712048625
3690;IBKR;Stocks;MEITUAN-CLASS B;HKD;74.75;231.697888617;60.0;4485.0;573.2031555899999;;0.0002198076012852387;-1203.5187293438426
386;IBKR;Stocks;CHINA PETROLEUM & CHEMICAL-H;HKD;4.845;5.815174;2000.0;9690.0;1238.42554686;;0.00047490204157279;-247.985194323912
390;IBKR;Stocks;CHINA RAILWAY GROUP LTD-H;HKD;3.41;3.92724184;10000.0;34100.0;4358.133245399999;;0.0016712239027484145;-661.0583163682896
3918;IBKR;Stocks;NAGACORP LTD;HKD;3.15;7.594013956;2036.0;6413.4;819.6613418195999;;0.00031431751841309917;-1156.3766483301554
3968;IBKR;Stocks;CHINA MERCHANTS BANK - H;HKD;52.15;39.7833745;500.0;26075.0;3332.5021810499998;;0.0012779226763684724;790.2551572574984
5;IBKR;Stocks;HSBC HOLDINGS PLC;HKD;164.7;49.092166333;2400.0;395280.0;50518.560388319995;;0.0193724746122696;35460.48164219303
66;IBKR;Stocks;MTR CORP;HKD;33.56;40.07708;500.0;16780.0;2144.5594093199998;;0.0008223793867483401;-416.45605587875997
700;IBKR;Stocks;TENCENT HOLDINGS LTD;HKD;425.6;452.856419642;706.0;300473.6;38401.876408358396;;0.014726060482840647;-2459.3460019418117
808;IBKR;Stocks;PROSPERITY REIT;HKD;1.4;2.339531045;20000.0;28000.0;3578.525832;;0.0013722659612010444;-2401.525796070325
857;IBKR;Stocks;PETROCHINA CO LTD-H;HKD;10.09;7.7972334;2000.0;20180.0;2579.09468892;;0.0009890116820370383;586.0517503462008
939;IBKR;Stocks;CHINA CONSTRUCTION BANK-H;HKD;9.765;6.39177626;4000.0;39060.0;4992.043535639999;;0.0019143110158754567;1724.4526129579504
9618;IBKR;Stocks;JD.COM INC-CLASS A;HKD;105.5;285.565311947;19.0;2004.5;256.184108223;;9.823953997241049e-05;-437.24996552643125
CASH - Citic;Citic;Forex;;HKD;0.128244528;;-0.1276;;56493.2093;-285.35623200400005;0.021663587689702134;
CASH - StanChart;StanChart;Forex;;HKD;0.128244528;;-0.1276;;1614.1055479999998;-8.153105177440239;0.0006189649607945483;
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;127600.0;-644.5280000000057;0.04893108080524631;
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;1276.0;-6.445279999999912;0.000489310808052463;
CASH - StanChart Payroll;StanChart Payroll;Forex;;HKD;0.128244528;;-0.1276;;14971.866888;-75.62529327264019;0.005741298029013476;
Forex;IBKR;Forex;;HKD;0.127804494;;-0.12752;;1830.949075;-4.084804;0.0007021184728770844;
BARCl;IBKR;Stocks;;GBP;4.8435;3.5479;1000.0;4843.5;6424.694692614001;;0.002463693222204802;1718.5577462064
Forex;IBKR;Forex;;GBP;1.326457044;;-1.3512;;2795.188796;51.185044;0.001071877812249178;
IMB 18DEC26 30 C;IBKR;Equity and Index Options;;GBP;0.075;0.8986;-1.0;-75.0;-99.4842783;;-3.8149476961982066e-05;1.0924700214384
IMBl;IBKR;Stocks;IMPERIAL BRANDS PLC;GBP;24.88;29.95515;1000.0;24880.0;33002.25125472;;0.012655453157521519;-6731.9684668566015
NWG 18SEP26 5 P;IBKR;Equity and Index Options;;GBP;0.0;0.5086;-1.0;0.0;0.0;;0.0;0.6746360525784001
RRl;IBKR;Stocks;ROLLS-ROYCE HOLDINGS PLC;GBP;14.3;11.090386;100.0;1430.0;1896.83357292;;0.0007273833607417914;425.74150988210164
Forex;IBKR;Forex;;EUR;1.1614;;-1.1611;;6325.065783;-1.634242887;0.0024254882831228172;
MC;IBKR;Stocks;LVMH MOET HENNESSY LOUIS VUI;EUR;406.5;619.0851265;10.0;4065.0;4721.091;;0.001810408191300322;-2468.9636591710005
MC 18DEC26 440 P;IBKR;Equity and Index Options;;EUR;48.1872;37.9386;-1.0;-4818.72;-5596.461408;;-0.0021460886001433426;-11.902724039999995
MC 20NOV26 410 P;IBKR;Equity and Index Options;;EUR;23.9444;12.4886;-2.0;-4788.88;-5561.805232;;-0.002132798912461079;-26.609532240000004
MC 20NOV26 430 P;IBKR;Equity and Index Options;;EUR;36.1078;20.2114;1.0;3610.78;4193.559892;;0.001608114560635517;18.462078959999996
MC 20NOV26 440 C;IBKR;Equity and Index Options;;EUR;11.4362;26.9864;1.0;1143.62;1328.2002679999998;;0.0005093281711524905;-18.06000228
MC 20NOV26 450 C;IBKR;Equity and Index Options;;EUR;8.9215;22.1636;-1.0;-892.15;-1036.14301;;-0.00039733226761834743;15.379374939999998
RI;IBKR;Stocks;PERNOD-RICARD SA;EUR;59.5;83.7114;100.0;5950.0;6910.33;;0.0026499209688159693;-2811.911996
RMS;IBKR;Stocks;HERMES INTL;EUR;1409.0;2195.362096667;3.0;4227.0;4909.2378;;0.0018825573000311097;-2739.8428172071617
RMS 17DEC27 1520 P;IBKR;Equity and Index Options;;EUR;245.5737;193.044;5.0;12278.69;14260.470566;;0.005468497159763186;305.03996789999997
RMS 17DEC27 1680 P;IBKR;Equity and Index Options;;EUR;350.5057;214.886;-5.0;-17525.29;-20353.871806;;-0.00780514847992955;-787.5435978999999
RMS 17DEC27 1920 P;IBKR;Equity and Index Options;;EUR;538.8821;388.7186;-1.0;-53888.21;-62585.767093999995;;-0.023999915571589647;-174.39988890000006
RMS 18DEC26 1520 P;IBKR;Equity and Index Options;;EUR;158.0686;130.209;5.0;7903.43;9179.043602;;0.003519910064297344;161.7806972
RMS 18DEC26 1600 P;IBKR;Equity and Index Options;;EUR;217.4442;110.436;-5.0;-10872.21;-12626.984693999999;;-0.004842100379221961;-621.3966174
RMS 18JUN27 1360 P;IBKR;Equity and Index Options;;EUR;125.9183;108.0235;-10.0;-12591.83;-14624.151362;;-0.0056079587147505865;-207.83020720000005
RMS 18JUN27 1440 P;IBKR;Equity and Index Options;;EUR;165.1033;146.389;5.0;8255.17;9587.554438;;0.003676562703216895;108.67394009999988
SAP;IBKR;Stocks;SAP SE;EUR;177.82;210.1117;100.0;17782.0;20652.0148;;0.007919478095375727;-3750.358038000002
SAP 18DEC26 190 P;IBKR;Equity and Index Options;;EUR;20.49;31.7783;-2.0;-4098.0;-4759.4172;;-0.0018251052319677046;26.220463240000008
Forex;IBKR;Forex;;CNH;0.148953656;;-0.14893;;969.5343;-0.154;0.0003717896643946545;

---

# [pivot_summary] START  
# PORTFOLIO PIVOT SUMMARY

**Report Date :** 2026-09-11  
**Filter      :** Open Positions | Forex Balances | Cash Balances  
**Generated   :** 2026-09-11 23:15:52  

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
AMZN           -1.58       1.0         -1.58
BHP         -1500.00       4.0      -1500.00
BRK         -2109.99       2.0      -2109.99
CPRT         -553.35      11.0       -553.35
GEHC          163.77       1.0        163.77
GLD          -937.27       1.0       -937.27
GOOG        18642.44       2.0      18642.44
IMB           -75.00       1.0        -99.48
LULU         -915.04       2.0       -915.04
MC          -5745.35       2.0      -6672.65
META        -8942.50       3.0      -8942.50
MSFT        -1655.66       2.0      -1655.66
NBIS         -508.41       2.0       -508.41
NWG             0.00       1.0          0.00
PDD         -5143.01       4.0      -5143.01
RMS        -66440.25       6.0     -77163.71
SAP         -4098.00       2.0      -4759.42
SKHY            5.82       1.0          5.82
SMCI          -42.50       2.0        -42.50
UNH         -5285.74       3.0      -5285.74
```

> **SUBTOTAL [IBKR]**  Amount: `     -85,141.62`  Quantity: `          53.00`  Value in USD: `     -97,478.28`

> **SUBTOTAL [Equity and Index Options]**  Amount: `     -82,779.62`  Quantity: `          71.00`  Value in USD: `     -99,840.28`

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
Forex       49538.0      4.58       29009.8
```

> **SUBTOTAL [IBKR]**  Amount: `      49,538.00`  Quantity: `           4.58`  Value in USD: `      29,009.80`

### UOB

```
             Amount  Quantity  Value in USD
Underlying                                 
Forex       8878.06      0.78       6898.25
```

> **SUBTOTAL [UOB]**  Amount: `       8,878.06`  Quantity: `           0.78`  Value in USD: `       6,898.25`

> **SUBTOTAL [Forex]**  Amount: `   1,877,149.91`  Quantity: `          13.21`  Value in USD: `     437,451.88`

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
1299         45180.00    -600.0       5774.21
1398         45660.00   -6000.0       5835.55
14           15930.00   -1000.0       2035.93
16          525150.00   -4500.0      67116.53
1810         15552.00    -600.0       1987.62
1883         24000.00  -10000.0       3067.31
1972        137984.00   -5600.0      17634.98
200          12580.00   -4000.0       1607.78
215          57000.00  -50000.0       7284.86
2318         27425.00    -500.0       3505.04
27           32080.00   -1000.0       4099.97
2800         38370.00   -1500.0       4903.86
2828         34144.00    -400.0       4363.76
2888          6770.40     -28.0        865.29
3188         55180.00   -1000.0       7052.25
3690          4485.00     -60.0        573.20
386           9690.00   -2000.0       1238.43
390          34100.00  -10000.0       4358.13
3918          6413.40   -2036.0        819.66
3968         26075.00    -500.0       3332.50
5           395280.00   -2400.0      50518.56
66           16780.00    -500.0       2144.56
700         300473.60    -706.0      38401.88
808          28000.00  -20000.0       3578.53
857          20180.00   -2000.0       2579.09
939          39060.00   -4000.0       4992.04
9618          2004.50     -19.0        256.18
9CI           3132.00   -1200.0       2405.62
AMZN         75567.00    -300.0      75567.00
BABA         54280.00    -500.0      54280.00
BARCl         4843.50   -1000.0       6424.69
BHP         104820.00   -1200.0     104820.00
BRK          50700.00    -100.0      50700.00
BS6          35000.00   -7000.0      26882.78
C38U           506.16    -222.0        388.77
CGP              0.00     -50.0          0.00
CPRT         33825.00   -1100.0      33825.00
D05          16893.80    -220.0      12975.78
DNOW           386.75     -25.0        386.75
F34          33390.00   -9000.0      25646.17
FAST          4901.00    -100.0       4901.00
FCX          35605.00    -500.0      35605.00
GOOG         66078.00    -200.0      66078.00
H78           8530.00   -1000.0       8530.00
HMN             57.46     -68.0         44.13
ICE          46743.00    -300.0      46743.00
IMB          24880.00   -1000.0      33002.25
IVV          76153.00    -100.0      76153.00
MC            4065.00     -10.0       4721.09
META        128876.00    -200.0     128876.00
NOV           6396.00    -300.0       6396.00
NVO           8802.00    -200.0       8802.00
O39          25112.00    -800.0      19288.01
PDD           7784.00    -100.0       7784.00
RI            5950.00    -100.0       6910.33
RMS           4227.00      -3.0       4909.24
RRl           1430.00    -100.0       1896.83
SAP          17782.00    -100.0      20652.01
SOUHY         5460.00    -300.0       5460.00
TSM         128409.00    -300.0     128409.00
UNH          77656.00    -200.0      77656.00
```

> **SUBTOTAL [IBKR]**  Amount: `   3,053,787.57`  Quantity: `    -158,847.00`  Value in USD: `   1,337,047.15`

> **SUBTOTAL [Stocks]**  Amount: `   3,117,008.01`  Quantity: `    -164,878.91`  Value in USD: `   1,399,135.46`

---

## Treasury Bills

### IBKR

```
               Amount  Quantity  Value in USD
Underlying                                   
912797TC1    73174.13  -74000.0      73174.13
912797TV9    66818.02  -68000.0      66818.02
912797UE5    97638.29 -100000.0      97638.29
912797UX3   105115.49 -108000.0     105115.49
912797VF1   256074.71 -264000.0     256074.71
912797VQ7   175891.00 -182000.0     175891.00
912797VR5    96290.76 -100000.0      96290.76
```

> **SUBTOTAL [IBKR]**  Amount: `     871,002.40`  Quantity: `    -896,000.00`  Value in USD: `     871,002.40`

> **SUBTOTAL [Treasury Bills]**  Amount: `     871,002.40`  Quantity: `    -896,000.00`  Value in USD: `     871,002.40`

---

> ### GRAND TOTAL  
> Amount: `   5,782,380.70`  Quantity: `  -1,060,794.71`  Value in USD: `   2,607,749.47`

---

