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

- Current refresh timestamp: **2026-11-27 20:52:53**.


# [transaction_details] START  
Status;Table;Currency;Settle Date;Ticker;Symbol;Asset Category;Transaction Price;Amount;Quantity;Value in USD;Comm/Fee;Code
Open;Trades;EUR;2026-11-20;MC;MC;Stocks;410.0;-82000.0;200.0;-89945.8;0.0;"A;O"
Open;Trades;EUR;2026-11-20;MC;MC;Stocks;430.0;43000.0;-100.0;47166.7;0.0;"C;Ex"
Open;Trades;EUR;2026-03-04;RI;RI;Stocks;93.0;-9300.0;100.0;-10201.17;0.0;"A;O"
Open;Trades;EUR;2026-11-17;RMS;RMS;Stocks;1600.0;-32000.0;20.0;-35100.8;0.0;"A;O"
Open;Trades;EUR;2026-02-16;SAP;SAP;Stocks;220.0;-22000.0;100.0;-24131.8;0.0;"A;O"
Open;Trades;HKD;2026-04-01;1810;1810;Stocks;31.8;-6360.0;200.0;-811.52183628;-25.18126;O
Open;Trades;HKD;2026-04-10;1810;1810;Stocks;31.0;-6200.0;200.0;-791.1061926;-25.1767;O
Open;Trades;HKD;2026-04-10;700;700;Stocks;502.0;-50200.0;100.0;-6405.4082046;-92.5907;O
Open;Trades;USD;2026-02-23;CPRT;CPRT;Stocks;45.0;-13500.0;300.0;-13500.0;0.0;"A;O"
Open;Trades;USD;2026-11-24;GLD;GLD;Stocks;390.0;-78000.0;200.0;-78000.0;0.0;"A;O"
Open;Trades;USD;2026-08-10;GOOG;GOOG;Stocks;352.84;70568.0;-200.0;70568.0;-2.4338153;"C;P"
Open;Trades;USD;2026-02-23;NVO;NVO;Stocks;65.0;-13000.0;200.0;-13000.0;0.0;"A;O"
Open;Trades;USD;2026-03-20;PDD;PDD;Stocks;100.0;-10000.0;100.0;-10000.0;0.0;"A;O"
Open;Trades;USD;2026-01-02;TSM;TSM;Stocks;317.21;63442.0;-200.0;63442.0;-0.7795145;"C;P"
Open;Trades;USD;2026-01-02;TSM;TSM;Stocks;318.5;63700.0;-200.0;63700.0;-0.1395145;C
Open;Trades;USD;2026-01-29;UNH;UNH;Stocks;350.0;-70000.0;200.0;-70000.0;0.0;"A;O"
Open;Trades;EUR;2026-03-30;RMS;RMS 18DEC26 1760 C;Equity and Index Options;130.095;6504.75;-5.0;7135.060275;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1760 C;Equity and Index Options;181.5;-9075.0;5.0;-9954.3675;-5.7;C
Open;Trades;EUR;2026-03-30;RMS;RMS 17DEC27 1760 C;Equity and Index Options;226.53;11326.5;-5.0;12424.03785;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 17DEC27 1760 C;Equity and Index Options;284.1;-14205.0;5.0;-15581.4645;-5.7;C
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 440 C;Equity and Index Options;26.975;-2697.5;1.0;-2958.88775;-1.14;O
Open;Trades;EUR;2026-11-20;MC;MC 20NOV26 440 C;Equity and Index Options;0.0;0.0;-1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 450 C;Equity and Index Options;22.175;2217.5;-1.0;2432.37575;-1.14;O
Open;Trades;EUR;2026-11-20;MC;MC 20NOV26 450 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2026-02-18;SAP;SAP 17APR26 194 C;Equity and Index Options;2.2;220.0;-1.0;241.31799999999998;-1.17;O
Open;Trades;EUR;2026-04-17;SAP;SAP 17APR26 194 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2026-02-18;SAP;SAP 18SEP26 175 C;Equity and Index Options;15.79;-1579.0;1.0;-1732.0051;-1.17;O
Open;Trades;EUR;2026-08-06;SAP;SAP 18SEP26 175 C;Equity and Index Options;7.5;750.0;-1.0;822.675;-1.17;C
Open;Trades;EUR;2026-02-18;SAP;SAP 18SEP26 195 C;Equity and Index Options;8.52;1704.0;-2.0;1869.1176;-2.34;O
Open;Trades;EUR;2026-08-06;SAP;SAP 18SEP26 195 C;Equity and Index Options;2.2;-440.0;2.0;-482.63599999999997;-2.34;C
Open;Trades;EUR;2026-11-26;SAP;SAP 15JAN27 205 C;Equity and Index Options;4.72;472.0;-1.0;517.7368;-1.17;O
Open;Trades;EUR;2026-03-10;RMS;RMS 19JUN26 2040 P;Equity and Index Options;180.57;-18057.0;1.0;-19806.7233;-1.14;C
Open;Trades;EUR;2026-02-18;RMS;RMS 18DEC26 1480 P;Equity and Index Options;30.1;3010.0;-1.0;3301.669;-1.14;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1480 P;Equity and Index Options;73.05;-7305.0;1.0;-8012.8544999999995;-1.14;C
Open;Trades;EUR;2026-03-10;RMS;RMS 18DEC26 1960 P;Equity and Index Options;198.32;19832.0;-1.0;21753.7208;-1.14;O
Open;Trades;EUR;2026-03-24;RMS;RMS 18DEC26 1960 P;Equity and Index Options;359.93;-35993.0;1.0;-39480.7217;-1.14;C
Open;Trades;EUR;2026-03-24;RMS;RMS 17DEC27 1920 P;Equity and Index Options;388.73;38873.0;-1.0;42639.7937;-1.14;O
Open;Trades;EUR;2026-03-30;RMS;RMS 18DEC26 1520 P;Equity and Index Options;130.095;-6504.75;5.0;-7135.060275;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1600 P;Equity and Index Options;110.55;5527.5;-5.0;6063.11475;-5.7;O
Open;Trades;EUR;2026-11-17;RMS;RMS 18DEC26 1600 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-09-04;RMS;RMS 18JUN27 1360 P;Equity and Index Options;108.1375;10813.75;-10.0;11861.602375;-11.4;O
Open;Trades;EUR;2026-09-04;RMS;RMS 18JUN27 1440 P;Equity and Index Options;146.275;-7313.75;5.0;-8022.452375;-5.7;O
Open;Trades;EUR;2026-03-30;RMS;RMS 17DEC27 1520 P;Equity and Index Options;192.93;-9646.5;5.0;-10581.24585;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 17DEC27 1680 P;Equity and Index Options;215.0;10750.0;-5.0;11791.675;-5.7;O
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 410 P;Equity and Index Options;12.5;2500.0;-2.0;2742.25;-2.28;O
Open;Trades;EUR;2026-11-20;MC;MC 20NOV26 410 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-09-02;MC;MC 20NOV26 430 P;Equity and Index Options;20.2;-2020.0;1.0;-2215.738;-1.14;O
Open;Trades;EUR;2026-11-20;MC;MC 20NOV26 430 P;Equity and Index Options;0.0;0.0;-1.0;0.0;0.0;"C;Ex"
Open;Trades;EUR;2026-03-17;MC;MC 18DEC26 440 P;Equity and Index Options;37.95;3795.0;-1.0;4162.7355;-1.14;O
Open;Trades;EUR;2026-11-26;MC;MC 18DEC26 440 P;Equity and Index Options;44.975;-4497.5;1.0;-4933.30775;-1.14;C
Open;Trades;EUR;2026-11-26;MC;MC 18JUN27 415 P;Equity and Index Options;45.975;4597.5;-1.0;5042.9977499999995;-1.14;O
Open;Trades;EUR;2026-03-04;RI;RI 20MAR26 93 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-02-16;SAP;SAP 20MAR26 220 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-03-10;SAP;SAP 20MAR26 240 P;Equity and Index Options;71.23;-7123.0;1.0;-7813.2187;-1.17;C
Open;Trades;EUR;2026-03-10;SAP;SAP 18DEC26 190 P;Equity and Index Options;31.79;6358.0;-2.0;6974.0902;-2.34;O
Open;Trades;EUR;2026-11-26;SAP;SAP 18DEC26 190 P;Equity and Index Options;4.865;-973.0;2.0;-1067.2837;-2.34;C
Open;Trades;EUR;2026-11-26;SAP;SAP 19MAR27 185 P;Equity and Index Options;11.11;1111.0;-1.0;1218.6559;-1.17;O
Open;Trades;GBP;2026-07-28;IMB;IMB 18DEC26 30 C;Equity and Index Options;0.9;900.0;-1.0;1195.702767;-1.4;O
Open;Trades;GBP;2026-02-27;IMB;IMB 20MAR26 34.5 C;Equity and Index Options;0.23;230.0;-1.0;305.56848490000004;-1.4;O
Open;Trades;GBP;2026-03-20;IMB;IMB 20MAR26 34.5 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;GBP;2026-10-23;RR.;RR. 15JAN27 11 P;Equity and Index Options;0.465;465.0;-1.0;617.7797629500001;-1.4;O
Open;Trades;HKD;2026-05-14;16;16 29JUN26 150 C;Equity and Index Options;3.86;15440.0;-4.0;1970.10961512;-42.88;O
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
Open;Trades;USD;2026-11-25;BRK;BRK B 18DEC26 510 P;Equity and Index Options;0.72;-72.0;1.0;-72.0;-0.5473;C
Open;Trades;USD;2026-11-25;BRK;BRK B 18DEC26 540 P;Equity and Index Options;2.08;208.0;-1.0;208.0;-0.5515848;O
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
Open;Trades;USD;2026-11-25;GLD;GLD 15JAN27 315 P;Equity and Index Options;15.22;-1522.0;1.0;-1522.0;-1.04028;O
Open;Trades;USD;2026-09-04;GLD;GLD 15JAN27 390 P;Equity and Index Options;13.75;2750.0;-2.0;2750.0;-0.45783;O
Open;Trades;USD;2026-11-24;GLD;GLD 15JAN27 390 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"A;C"
Open;Trades;USD;2026-09-04;GLD;GLD 15JAN27 405 P;Equity and Index Options;20.5;-2050.0;1.0;-2050.0;-0.5473;O
Open;Trades;USD;2026-11-25;GLD;GLD 15JAN27 405 P;Equity and Index Options;90.65;9065.0;-1.0;9065.0;-1.227019;C
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
Open;Trades;USD;2026-06-12;912797UE5;912797UE5 3.8394375%;Treasury Bills;96.9013111;-96901.31;100000.0;-96901.31;-5.0;O
Open;Trades;USD;2026-06-12;912797UX3;912797UX3 3.815%;Treasury Bills;96.61841;-104347.88;108000.0;-104347.88;-7.5;O
Open;Trades;USD;2026-06-22;912797VF1;912797VF1 4.043%;Treasury Bills;96.212;-31749.96;33000.0;-31749.96;-12.5;"O;P"
Open;Trades;USD;2026-06-23;912797VF1;912797VF1 4.03054553%;Treasury Bills;96.268675;-84716.43;88000.0;-84716.43;-5.0;O
Open;Trades;USD;2026-07-10;912797VF1;912797VF1 3.986%;Treasury Bills;96.470857091;-137953.32;143000.0;-137953.32;-7.5;"O;P"
Open;Trades;USD;2026-08-06;912797VQ7;912797VQ7 4.05724169%;Treasury Bills;96.4099167;-96409.92;100000.0;-96409.92;-5.0;O
Open;Trades;USD;2026-08-06;912797VQ7;912797VQ7 4.011%;Treasury Bills;96.41821;-79062.93;82000.0;-79062.93;-7.5;O
Open;Trades;USD;2026-08-31;912797VR5;912797VR5 4.1558258%;Treasury Bills;96.2942056;-96294.21;100000.0;-96294.21;-5.0;O
Open;Corporate Actions;EUR;2026-11-25;RI;RI;Stocks;;0.0;100.0;0.0;;
Open;Transaction Fees;EUR;2026-03-04;RI;RI;Stocks;93.0;-37.2;0.0;-40.804680000000005;;
Open;Transaction Fees;EUR;2026-11-17;RMS;RMS;Stocks;1600.0;-128.0;0.0;-140.4032;;
Open;Transaction Fees;EUR;2026-11-20;MC;MC;Stocks;410.0;-164.0;0.0;-179.8916;;
Open;Transaction Fees;HKD;2026-04-01;1810;1810;Stocks;31.8;-7.18126;0.0;-0.91631278333398;;
Open;Transaction Fees;HKD;2026-04-10;1810;1810;Stocks;31.0;-7.1767;0.0;-0.9157309374891001;;
Open;Transaction Fees;HKD;2026-04-10;700;700;Stocks;502.0;-52.4307;0.0;-6.6900405568311;;
Open;Withholding Tax;EUR;2026-02-18;RMS;RMS;;;-3.72;;-4.080468;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-10.617992;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;9.68;;10.617992;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-10.617992;;
Open;Withholding Tax;EUR;2026-04-30;MC;MC;;;-18.75;;-20.566875;;
Open;Withholding Tax;EUR;2026-05-08;SAP;SAP;;;-61.32;;-67.261908;;
Open;Withholding Tax;EUR;2026-07-24;RI;RI;;;-58.75;;-64.442875;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.55653030061;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;90.57;;11.55653030061;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.55653030061;;
Open;Withholding Tax;HKD;2026-01-26;939;939;;;-81.86;;-10.44515369778;;
Open;Withholding Tax;HKD;2026-02-09;3968;3968;;;-33.22;;-4.23879801906;;
Open;Withholding Tax;HKD;2026-06-16;1398;1398;;;-116.65;;-14.88428022045;;
Open;Withholding Tax;HKD;2026-06-26;386;386;;;-25.78;;-3.28947058794;;
Open;Withholding Tax;HKD;2026-07-15;2318;2318;;;-100.56;;-12.83123205288;;
Open;Withholding Tax;HKD;2026-07-31;857;857;;;-22.27;;-2.8416024047099997;;
Open;Withholding Tax;HKD;2026-08-13;390;390;;;-98.9;;-12.6194197497;;
Open;Withholding Tax;HKD;2026-08-21;3968;3968;;;-57.66;;-7.3572875911799995;;
Open;Withholding Tax;HKD;2026-08-21;939;939;;;-93.42;;-11.92018395366;;
Open;Withholding Tax;HKD;2026-10-14;386;386;;;-24.46;;-3.12104152758;;
Open;Withholding Tax;HKD;2026-10-14;390;390;;;-0.35;;-0.044659220549999996;;
Open;Withholding Tax;HKD;2026-10-16;2318;2318;;;-56.67;;-7.23096579591;;
Open;Withholding Tax;HKD;2026-10-26;857;857;;;-36.57;;-4.66625055861;;
Open;Withholding Tax;HKD;2026-11-25;1398;1398;;;-103.54;;-13.21147341642;;
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
Open;Withholding Tax;USD;2026-11-25;FAST;FAST;;;-6.86;;-6.86;;
Open;Withholding Tax;USD;2026-11-25;FAST;FAST;;;-0.94;;-0.94;;
Open;Dividends;CNH;2026-07-31;3188;3188;;;548.9;;81.76066177839999;;
Open;Dividends;CNH;2026-07-31;3188;3188;;;1.1;;0.1638490216;;
Open;Dividends;EUR;2026-02-18;RMS;RMS;;;15.0;;16.4535;;
Open;Dividends;EUR;2026-04-23;RMS;RMS;;;39.0;;42.7791;;
Open;Dividends;EUR;2026-04-30;MC;MC;;;75.0;;82.2675;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;232.5;;255.02925;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;17.5;;19.19575;;
Open;Dividends;EUR;2026-07-24;RI;RI;;;235.0;;257.7715;;
Open;Dividends;GBP;2025-12-31;IMB;IMB;;;400.8;;532.486298904;;
Open;Dividends;GBP;2026-03-31;IMB;IMB;;;400.8;;532.486298904;;
Open;Dividends;GBP;2026-06-03;RR.;RR.;;;5.0;;6.64279315;;
Open;Dividends;GBP;2026-06-30;IMB;IMB;;;416.8;;553.7432369840001;;
Open;Dividends;GBP;2026-09-23;RR.;RR.;;;6.0;;7.971351780000001;;
Open;Dividends;GBP;2026-09-30;IMB;IMB;;;416.8;;553.7432369840001;;
Open;Dividends;HKD;2026-01-26;1398;1398;;;931.62;;118.87263728226;;
Open;Dividends;HKD;2026-01-26;939;939;;;818.62;;104.45408893326;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;220.7;;28.1608285011;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;333.34;;42.53344165182;;
Open;Dividends;HKD;2026-03-19;16;16;;;4410.0;;562.70617893;;
Open;Dividends;HKD;2026-03-27;14;14;;;810.0;;103.35419612999999;;
Open;Dividends;HKD;2026-04-23;808;808;;;1142.0;;145.716656766;;
Open;Dividends;HKD;2026-04-30;5;5;;;8148.56;;1039.73810915688;;
Open;Dividends;HKD;2026-04-30;5;5;;;306.5;;39.1087174245;;
Open;Dividends;HKD;2026-05-07;1972;1972;;;4480.0;;571.63802304;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;3.84;;0.48997544831999995;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;103.61;;13.220405260529999;;
Open;Dividends;HKD;2026-05-29;215;215;;;2605.0;;332.39219866499997;;
Open;Dividends;HKD;2026-05-29;2800;2800;;;285.0;;36.365365305;;
Open;Dividends;HKD;2026-06-01;700;700;;;3741.8;;477.44534701140003;;
Open;Dividends;HKD;2026-06-12;1299;1299;;;864.48;;110.30572280304;;
Open;Dividends;HKD;2026-06-12;27;27;;;800.0;;102.0782184;;
Open;Dividends;HKD;2026-06-16;1398;1398;;;1166.46;;148.83769829358;;
Open;Dividends;HKD;2026-06-22;66;66;;;439.66;;56.099636877180004;;
Open;Dividends;HKD;2026-06-22;66;66;;;5.34;;0.68137210782;;
Open;Dividends;HKD;2026-06-25;1883;1883;;;1300.0;;165.8771049;;
Open;Dividends;HKD;2026-06-26;386;386;;;257.8;;32.8947058794;;
Open;Dividends;HKD;2026-06-26;5;5;;;1661.92;;212.05729090416;;
Open;Dividends;HKD;2026-06-26;5;5;;;217.73;;27.781863115289998;;
Open;Dividends;HKD;2026-06-30;2828;2828;;;280.0;;35.72737644;;
Open;Dividends;HKD;2026-07-15;2318;2318;;;1005.64;;128.31742443971999;;
Open;Dividends;HKD;2026-07-31;857;857;;;222.66;;28.41092013618;;
Open;Dividends;HKD;2026-07-31;857;857;;;316.75;;40.41659459775;;
Open;Dividends;HKD;2026-08-07;3918;3918;;;172.04;;21.95192086692;;
Open;Dividends;HKD;2026-08-13;390;390;;;989.0;;126.194197497;;
Open;Dividends;HKD;2026-08-21;3968;3968;;;576.63;;73.57670384498999;;
Open;Dividends;HKD;2026-08-21;939;939;;;936.74;;119.52593788002;;
Open;Dividends;HKD;2026-09-11;14;14;;;270.0;;34.45139871;;
Open;Dividends;HKD;2026-09-15;27;27;;;900.0;;114.8379957;;
Open;Dividends;HKD;2026-09-15;66;66;;;0.84;;0.10718212932;;
Open;Dividends;HKD;2026-09-15;66;66;;;209.16;;26.68835020068;;
Open;Dividends;HKD;2026-09-16;215;215;;;1140.0;;145.46146122;;
Open;Dividends;HKD;2026-09-18;808;808;;;1126.0;;143.675092398;;
Open;Dividends;HKD;2026-09-22;1299;1299;;;323.4;;41.265119788199996;;
Open;Dividends;HKD;2026-09-25;5;5;;;1881.81;;240.11476520912998;;
Open;Dividends;HKD;2026-09-28;3918;3918;;;154.74;;19.74447939402;;
Open;Dividends;HKD;2026-09-29;2888;2888;;;31.98;;4.08057678054;;
Open;Dividends;HKD;2026-09-29;2888;2888;;;12.79;;1.6319755166699998;;
Open;Dividends;HKD;2026-09-30;1883;1883;;;500.0;;63.7988865;;
Open;Dividends;HKD;2026-09-30;2828;2828;;;260.0;;33.17542098;;
Open;Dividends;HKD;2026-10-08;1972;1972;;;2072.0;;264.382585656;;
Open;Dividends;HKD;2026-10-14;386;386;;;244.6;;31.2104152758;;
Open;Dividends;HKD;2026-10-14;390;390;;;3.6;;0.4593519828;;
Open;Dividends;HKD;2026-10-14;390;390;;;659.69;;84.17497487037001;;
Open;Dividends;HKD;2026-10-16;2318;2318;;;566.73;;72.31348589229;;
Open;Dividends;HKD;2026-10-26;857;857;;;207.41;;26.46505409793;;
Open;Dividends;HKD;2026-10-26;857;857;;;370.0;;47.21117601;;
Open;Dividends;HKD;2026-11-19;16;16;;;13185.0;;1682.376637005;;
Open;Dividends;HKD;2026-11-25;1398;1398;;;1061.13;;135.39782486349;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.96;;0.7374287452799999;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.26;;0.19972028518;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.36;;0.27653577947999997;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.86;;0.66061325098;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;0.8;;0.6145239544000001;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;0.4;;0.30726197720000004;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;9.04;;6.944120684719999;;
Open;Dividends;SGD;2026-04-17;D05;D05;;;33.0;;25.349113119;;
Open;Dividends;SGD;2026-04-17;D05;D05;;;145.2;;111.5360977236;;
Open;Dividends;SGD;2026-05-08;O39;O39;;;128.0;;98.323832704;;
Open;Dividends;SGD;2026-05-08;O39;O39;;;336.0;;258.100060848;;
Open;Dividends;SGD;2026-05-14;9CI;9CI;;;144.0;;110.614311792;;
Open;Dividends;SGD;2026-05-14;BS6;BS6;;;1400.0;;1075.4169202;;
Open;Dividends;SGD;2026-05-14;F34;F34;;;900.0;;691.3394486999999;;
Open;Dividends;SGD;2026-05-20;D05;D05;;;33.0;;25.349113119;;
Open;Dividends;SGD;2026-05-20;D05;D05;;;145.2;;111.5360977236;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;0.6;;0.46089296579999994;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;0.13;;0.09986014259;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;8.1;;6.2220550383;;
Open;Dividends;SGD;2026-08-25;D05;D05;;;145.2;;111.5360977236;;
Open;Dividends;SGD;2026-08-25;D05;D05;;;33.0;;25.349113119;;
Open;Dividends;SGD;2026-08-28;HMN;HMN;;;0.26;;0.19972028518;;
Open;Dividends;SGD;2026-08-28;HMN;HMN;;;1.22;;0.93714903046;;
Open;Dividends;SGD;2026-08-28;HMN;HMN;;;0.24;;0.18435718631999998;;
Open;Dividends;SGD;2026-08-28;O39;O39;;;376.0;;288.826258568;;
Open;Dividends;SGD;2026-09-02;F34;F34;;;450.0;;345.66972434999997;;
Open;Dividends;SGD;2026-09-25;C38U;C38U;;;0.42;;0.32262507605999996;;
Open;Dividends;SGD;2026-09-25;C38U;C38U;;;4.0;;3.072619772;;
Open;Dividends;SGD;2026-09-25;C38U;C38U;;;0.11;;0.08449704373;;
Open;Dividends;SGD;2026-11-24;D05;D05;;;33.0;;25.349113119;;
Open;Dividends;SGD;2026-11-24;D05;D05;;;145.2;;111.5360977236;;
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
Open;Dividends;USD;2026-11-25;FAST;FAST;;;22.88;;22.88;;
Open;Dividends;USD;2026-11-25;FAST;FAST;;;3.12;;3.12;;
Open;Trades;EUR;2025-10-22;MC;MC;Stocks;618.7;-6187.0;10.0;-6786.5203;-3.851265;O
Open;Trades;EUR;2025-10-22;RMS;RMS;Stocks;2194.0;-6582.0;3.0;-7219.7958;-4.08629;O
Open;Trades;GBP;2025-05-22;IMB;IMB;Stocks;30.75;-30750.0;1000.0;-40853.177872500004;-153.75;"A;O"
Open;Trades;GBP;2025-10-17;RR.;RR.;Stocks;11.025;-1102.5;100.0;-1464.735889575;-6.538600281;O
Open;Trades;HKD;2025-03-26;1810;1810;Stocks;53.35;-10670.0;200.0;-1361.46823791;-31.90695;O
Open;Trades;HKD;2025-06-30;2888;2888;Stocks;134.1;13410.0;-100.0;1711.08613593;-33.70307;C
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
Open;Trades;EUR;2025-09-03;RMS;RMS 19JUN26 2040 P;Equity and Index Options;170.0;17000.0;-1.0;18647.3;-1.14;O
Open;Trades;EUR;2025-07-15;RI;RI 20MAR26 93 P;Equity and Index Options;9.3;930.0;-1.0;1020.117;-1.14;O
Open;Trades;EUR;2025-03-26;SAP;SAP 16MAY25 235 P;Equity and Index Options;5.35;535.0;-1.0;586.8415;-1.17;O
Open;Trades;EUR;2025-05-16;SAP;SAP 16MAY25 235 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2025-08-15;SAP;SAP 20MAR26 220 P;Equity and Index Options;9.9;990.0;-1.0;1085.931;-1.17;O
Open;Trades;EUR;2025-08-08;SAP;SAP 20MAR26 240 P;Equity and Index Options;13.8;1380.0;-1.0;1513.722;-1.17;O
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
Open;Transaction Fees;EUR;2025-10-22;MC;MC;Stocks;618.7;-24.75;0.0;-27.148274999999998;;
Open;Transaction Fees;EUR;2025-10-22;RMS;RMS;Stocks;2194.0;-26.33;0.0;-28.881376999999997;;
Open;Transaction Fees;GBP;2025-05-22;IMB;IMB;Stocks;30.75;-153.75;0.0;-204.26588936250002;;
Open;Transaction Fees;GBP;2025-05-22;IMB;IMB;Stocks;0.0;-1.5;0.0;-1.9928379450000002;;
Open;Transaction Fees;GBP;2025-10-17;RR.;RR.;Stocks;11.025;-5.5125;0.0;-7.3236794478750005;;
Open;Transaction Fees;HKD;2025-03-26;1810;1810;Stocks;53.35;-11.304095;0.0;-1.442377347780435;;
Open;Transaction Fees;HKD;2025-06-30;2888;2888;Stocks;134.1;-14.382185;0.0;-1.835134776874005;;
Open;Withholding Tax;EUR;2025-12-04;MC;MC;;;-13.75;;-15.082374999999999;;
Open;Withholding Tax;HKD;2025-01-24;1398;1398;;;-93.17;;-11.88828451041;;
Open;Withholding Tax;HKD;2025-01-27;939;939;;;-85.25;;-10.87771014825;;
Open;Withholding Tax;HKD;2025-05-28;939;939;;;-88.71;;-11.319198442829999;;
Open;Withholding Tax;HKD;2025-06-27;386;386;;;-30.46;;-3.88662816558;;
Open;Withholding Tax;HKD;2025-06-30;2318;2318;;;-88.13;;-11.24519173449;;
Open;Withholding Tax;HKD;2025-07-24;857;857;;;-54.54;;-6.9591825394199995;;
Open;Withholding Tax;HKD;2025-07-30;3968;3968;;;-94.82;;-12.09882083586;;
Open;Withholding Tax;HKD;2025-07-31;390;390;;;-194.8;;-24.8560461804;;
Open;Withholding Tax;HKD;2025-08-22;1398;1398;;;-108.23;;-13.80990697179;;
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
Open;Dividends;EUR;2025-12-04;MC;MC;;;55.0;;60.329499999999996;;
Open;Dividends;GBP;2025-09-30;IMB;IMB;;;400.8;;532.486298904;;
Open;Dividends;HKD;2025-01-24;1398;1398;;;931.74;;118.88794901502;;
Open;Dividends;HKD;2025-01-27;939;939;;;852.52;;108.77965343796;;
Open;Dividends;HKD;2025-03-19;14;14;;;810.0;;103.35419612999999;;
Open;Dividends;HKD;2025-03-20;16;16;;;4275.0;;545.480479575;;
Open;Dividends;HKD;2025-04-23;808;808;;;1120.0;;142.90950576;;
Open;Dividends;HKD;2025-04-25;5;5;;;6698.42;;854.70347461866;;
Open;Dividends;HKD;2025-05-08;1972;1972;;;4256.0;;543.056121888;;
Open;Dividends;HKD;2025-05-19;2888;2888;;;277.72;;35.436453517560004;;
Open;Dividends;HKD;2025-05-28;939;939;;;887.1;;113.1919844283;;
Open;Dividends;HKD;2025-05-29;215;215;;;2605.0;;332.39219866499997;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;319.22;;40.73176109706;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;10.78;;1.37550399294;;
Open;Dividends;HKD;2025-05-30;700;700;;;2727.0;;347.959126971;;
Open;Dividends;HKD;2025-06-12;1299;1299;;;785.88;;100.27653784524;;
Open;Dividends;HKD;2025-06-12;27;27;;;500.0;;63.7988865;;
Open;Dividends;HKD;2025-06-13;66;66;;;445.0;;56.781008985;;
Open;Dividends;HKD;2025-06-20;1883;1883;;;1280.0;;163.32514944;;
Open;Dividends;HKD;2025-06-20;5;5;;;1881.53;;240.07903783269;;
Open;Dividends;HKD;2025-06-27;386;386;;;304.6;;38.8662816558;;
Open;Dividends;HKD;2025-06-30;2318;2318;;;881.33;;112.45574527809;;
Open;Dividends;HKD;2025-07-24;857;857;;;545.4;;69.5918253942;;
Open;Dividends;HKD;2025-07-30;2828;2828;;;520.0;;66.35084196;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;133.58;;17.04451051734;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;961.35;;122.66611907355;;
Open;Dividends;HKD;2025-07-31;390;390;;;1948.0;;248.560461804;;
Open;Dividends;HKD;2025-08-22;1398;1398;;;1082.3;;138.0990697179;;
Open;Dividends;HKD;2025-09-09;14;14;;;270.0;;34.45139871;;
Open;Dividends;HKD;2025-09-16;66;66;;;36.12;;4.60883156076;;
Open;Dividends;HKD;2025-09-16;66;66;;;173.88;;22.186700769239998;;
Open;Dividends;HKD;2025-09-18;808;808;;;1170.0;;149.28939441;;
Open;Dividends;HKD;2025-09-26;5;5;;;1866.43;;238.15231146039;;
Open;Dividends;HKD;2025-09-30;2888;2888;;;26.82;;3.42217227186;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;1.24;;0.95251212932;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.24;;0.18435718631999998;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.45;;0.34566972435;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.49;;0.37639592207;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.46;;0.35335127378000003;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.06;;0.046089296579999994;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;5.33;;4.09426584619;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.24;;0.18435718631999998;;
Open;Dividends;SGD;2025-04-16;D05;D05;;;132.0;;101.396452476;;
Open;Dividends;SGD;2025-05-09;O39;O39;;;328.0;;251.954821304;;
Open;Dividends;SGD;2025-05-09;O39;O39;;;128.0;;98.323832704;;
Open;Dividends;SGD;2025-05-13;9CI;9CI;;;144.0;;110.614311792;;
Open;Dividends;SGD;2025-05-13;BS6;BS6;;;840.0;;645.2501521199999;;
Open;Dividends;SGD;2025-05-15;F34;F34;;;900.0;;691.3394486999999;;
Open;Dividends;SGD;2025-05-27;D05;D05;;;132.0;;101.396452476;;
Open;Dividends;SGD;2025-05-27;D05;D05;;;33.0;;25.349113119;;
Open;Dividends;SGD;2025-08-21;O39;O39;;;328.0;;251.954821304;;
Open;Dividends;SGD;2025-08-25;D05;D05;;;132.0;;101.396452476;;
Open;Dividends;SGD;2025-08-25;D05;D05;;;33.0;;25.349113119;;
Open;Dividends;SGD;2025-08-28;F34;F34;;;360.0;;276.53577948;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;0.33;;0.25349113119;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;1.18;;0.9064228327399999;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;0.21;;0.16131253802999998;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;0.73;;0.5607531083899999;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;0.33;;0.25349113119;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;14.41;;11.06911272863;;
Open;Dividends;SGD;2025-11-24;D05;D05;;;132.0;;101.396452476;;
Open;Dividends;SGD;2025-11-24;D05;D05;;;33.0;;25.349113119;;
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
Open;Trades;HKD;2024-03-24;1299;1299;Stocks;55.2;-33120.0;600.0;-4226.03824176;-61.43992;O
Open;Trades;HKD;2024-12-27;3968;3968;Stocks;39.7;-19850.0;500.0;-2532.81579405;-41.68725;O
Open;Trades;SGD;2024-09-09;C38U;C38UNR;Stocks;0.0;0.0;-0.36;0.0;0.0;C
Open;Trades;SGD;2024-05-16;F34;F34;Stocks;3.16;-3160.0;1000.0;-2427.36961988;-3.90576;O
Open;Trades;USD;2024-12-13;AMZN;AMZN;Stocks;200.0;120000.0;-600.0;120000.0;-3.4356;"A;C"
Open;Trades;USD;2024-12-13;AMZN;AMZN;Stocks;205.0;-61500.0;300.0;-61500.0;0.0;"Ex;O"
Open;Trades;USD;2024-05-03;BABA;BABA;Stocks;78.0;15600.0;-200.0;15600.0;-0.1248;"A;C"
Open;Trades;USD;2024-05-10;BABA;BABA;Stocks;80.0;8000.0;-100.0;8000.0;0.0;"A;C"
Open;Trades;USD;2024-09-19;BABA;BABA;Stocks;87.83;43915.0;-500.0;43915.0;-2.58112325;"C;P"
Open;Trades;EUR;2024-11-15;SAP;SAP 21MAR25 220 P;Equity and Index Options;12.7;1270.0;-1.0;1393.0629999999999;-1.17;O
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
Open;Transaction Fees;HKD;2024-03-24;1299;1299;Stocks;55.2;-34.94392;0.0;-4.458766371890159;;
Open;Transaction Fees;HKD;2024-12-27;3968;3968;Stocks;39.7;-20.565725;0.0;-2.624140710130425;;
Open;Withholding Tax;HKD;2024-07-26;2318;2318;;;-82.34;;-10.50640062882;;
Open;Withholding Tax;HKD;2024-07-26;386;386;;;-43.88;;-5.598990279240001;;
Open;Withholding Tax;HKD;2024-07-29;857;857;;;-50.57;;-6.45261938061;;
Open;Withholding Tax;HKD;2024-08-02;939;939;;;-175.36;;-22.375545473280003;;
Open;Withholding Tax;HKD;2024-08-08;390;390;;;-230.1;;-29.3602475673;;
Open;Withholding Tax;HKD;2024-08-19;1398;1398;;;-201.43;;-25.70201941539;;
Open;Withholding Tax;HKD;2024-09-26;386;386;;;-31.86;;-4.06526504778;;
Open;Withholding Tax;HKD;2024-10-18;2318;2318;;;-51.02;;-6.51003837846;;
Open;Withholding Tax;HKD;2024-10-28;857;857;;;-48.07;;-6.13362494811;;
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
Open;Dividends;HKD;2024-03-20;16;16;;;4275.0;;545.480479575;;
Open;Dividends;HKD;2024-03-22;14;14;;;810.0;;103.35419612999999;;
Open;Dividends;HKD;2024-04-17;808;808;;;1328.0;;169.449842544;;
Open;Dividends;HKD;2024-04-25;5;5;;;5119.61;;653.2508346285299;;
Open;Dividends;HKD;2024-04-25;5;5;;;703.64;;89.78289699372;;
Open;Dividends;HKD;2024-04-26;27;27;;;300.0;;38.2793319;;
Open;Dividends;HKD;2024-05-02;1972;1972;;;4032.0;;514.474220736;;
Open;Dividends;HKD;2024-05-17;2888;2888;;;210.1;;26.808292107299998;;
Open;Dividends;HKD;2024-05-27;215;215;;;2605.0;;332.39219866499997;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;41.55;;5.30168746815;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;183.45;;23.40781145685;;
Open;Dividends;HKD;2024-05-31;700;700;;;2060.4;;262.9024514892;;
Open;Dividends;HKD;2024-06-13;1883;1883;;;1930.0;;246.26370189;;
Open;Dividends;HKD;2024-06-14;1299;1299;;;714.42;;91.15840098666;;
Open;Dividends;HKD;2024-06-21;5;5;;;5808.32;;741.1286968713599;;
Open;Dividends;HKD;2024-07-16;66;66;;;16.91;;2.15767834143;;
Open;Dividends;HKD;2024-07-16;66;66;;;428.09;;54.62333064356999;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;741.05;;94.55632968165;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;823.39;;105.06273031047;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;-741.05;;-94.55632968165;;
Open;Dividends;HKD;2024-07-26;386;386;;;438.8;;55.9899027924;;
Open;Dividends;HKD;2024-07-29;857;857;;;505.74;;64.53129771702;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;36.4;;4.644558937199999;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;223.6;;28.5308620428;;
Open;Dividends;HKD;2024-08-02;939;939;;;1753.57;;223.75162679961;;
Open;Dividends;HKD;2024-08-08;390;390;;;2301.0;;293.602475673;;
Open;Dividends;HKD;2024-08-19;1398;1398;;;2014.29;;257.01891817617;;
Open;Dividends;HKD;2024-09-09;215;215;;;1140.0;;145.46146122;;
Open;Dividends;HKD;2024-09-16;808;808;;;1276.0;;162.814758348;;
Open;Dividends;HKD;2024-09-17;66;66;;;72.66;;9.27125418618;;
Open;Dividends;HKD;2024-09-17;66;66;;;137.34;;17.52427814382;;
Open;Dividends;HKD;2024-09-20;14;14;;;270.0;;34.45139871;;
Open;Dividends;HKD;2024-09-25;1299;1299;;;267.0;;34.068605391;;
Open;Dividends;HKD;2024-09-26;386;386;;;318.6;;40.6526504778;;
Open;Dividends;HKD;2024-09-27;1883;1883;;;600.0;;76.5586638;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;21.85;;2.78801134005;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;358.15;;45.69914239995;;
Open;Dividends;HKD;2024-09-27;5;5;;;1869.78;;238.57976399993998;;
Open;Dividends;HKD;2024-10-09;1972;1972;;;1904.0;;242.946159792;;
Open;Dividends;HKD;2024-10-10;2888;2888;;;89.69;;11.444244260369999;;
Open;Dividends;HKD;2024-10-18;2318;2318;;;510.24;;65.10548769552;;
Open;Dividends;HKD;2024-10-25;27;27;;;500.0;;63.7988865;;
Open;Dividends;HKD;2024-10-28;857;857;;;480.66;;61.33114557018;;
Open;Dividends;HKD;2024-11-21;16;16;;;12325.6;;1572.7191108888;;
Open;Dividends;HKD;2024-11-21;16;16;;;274.4;;35.012828911199996;;
Open;Dividends;HKD;2024-11-29;2800;2800;;;930.0;;118.66592889;;
Open;Dividends;HKD;2024-12-19;5;5;;;1865.55;;238.04002542015;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;0.31;;0.23812803233;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;1.62;;1.2444110076600001;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;0.17;;0.13058634031;;
Open;Dividends;SGD;2024-03-28;C38U;C38U;;;10.08;;7.7430018254399995;;
Open;Dividends;SGD;2024-04-19;D05;D05;;;108.0;;82.960733844;;
Open;Dividends;SGD;2024-05-08;BS6;BS6;;;455.0;;349.510499065;;
Open;Dividends;SGD;2024-05-10;9CI;9CI;;;144.0;;110.614311792;;
Open;Dividends;SGD;2024-05-14;F34;F34;;;880.0;;675.97634984;;
Open;Dividends;SGD;2024-05-20;D05;D05;;;118.8;;91.25680722839999;;
Open;Dividends;SGD;2024-05-21;O39;O39;;;336.0;;258.100060848;;
Open;Dividends;SGD;2024-08-23;O39;O39;;;352.0;;270.390539936;;
Open;Dividends;SGD;2024-08-26;D05;D05;;;118.8;;91.25680722839999;;
Open;Dividends;SGD;2024-08-29;F34;F34;;;540.0;;414.80366921999996;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;1.03;;0.79119959129;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;0.39;;0.29958042777;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;0.32;;0.24580958176;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;0.11;;0.08449704373;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;0.09;;0.06913394486999999;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;9.84;;7.55864463912;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;3.83;;2.94203343169;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;0.07;;0.053770846010000005;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;0.09;;0.06913394486999999;;
Open;Dividends;SGD;2024-11-25;D05;D05;;;118.8;;91.25680722839999;;
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
Open;Trades;HKD;2023-04-25;3918;3918;Stocks;6.1031;4.96487185;-0.8135;0.63350659129039;0.0;C
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
Open;Withholding Tax;HKD;2023-06-30;386;386;;;-43.34;;-5.530087481820001;;
Open;Withholding Tax;HKD;2023-07-10;2318;2318;;;-85.01;;-10.84708668273;;
Open;Withholding Tax;HKD;2023-08-04;939;939;;;-168.79;;-21.53722810467;;
Open;Withholding Tax;HKD;2023-08-10;390;390;;;-217.64;;-27.770379315719996;;
Open;Withholding Tax;HKD;2023-08-18;1398;1398;;;-197.54;;-25.20566407842;;
Open;Withholding Tax;HKD;2023-09-28;386;386;;;-31.56;;-4.0269857158799995;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.3620249617799995;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;49.86;;6.3620249617799995;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.3620249617799995;;
Open;Withholding Tax;HKD;2023-10-30;857;857;;;-0.87;;-0.11101006251;;
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
Open;Dividends;HKD;2023-03-16;16;16;;;5571.25;;710.87909282625;;
Open;Dividends;HKD;2023-03-16;16;16;;;53.75;;6.85838029875;;
Open;Dividends;HKD;2023-03-20;14;14;;;38.61;;4.92655001553;;
Open;Dividends;HKD;2023-03-20;14;14;;;1131.39;;144.36284439447002;;
Open;Dividends;HKD;2023-04-11;808;808;;;1500.0;;191.3966595;;
Open;Dividends;HKD;2023-04-27;5;5;;;4330.56;;552.5698118428801;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;3394.56;;433.13829631488;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;413.44;;52.75402326912;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;1.1;;0.14035755030000002;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;139.46;;17.794785422580002;;
Open;Dividends;HKD;2023-05-29;215;215;;;2605.0;;332.39219866499997;;
Open;Dividends;HKD;2023-05-31;2800;2800;;;150.0;;19.13966595;;
Open;Dividends;HKD;2023-06-05;700;700;;;1454.4;;185.5782010512;;
Open;Dividends;HKD;2023-06-14;1883;1883;;;1850.0;;236.05588004999998;;
Open;Dividends;HKD;2023-06-23;5;5;;;1879.72;;239.84808586356;;
Open;Dividends;HKD;2023-06-30;386;386;;;433.4;;55.30087481819999;;
Open;Dividends;HKD;2023-07-10;2318;2318;;;850.13;;108.47469476049;;
Open;Dividends;HKD;2023-07-18;66;66;;;445.0;;56.781008985;;
Open;Dividends;HKD;2023-07-28;2828;2828;;;80.0;;10.20782184;;
Open;Dividends;HKD;2023-07-28;857;857;;;485.54;;61.95382270242;;
Open;Dividends;HKD;2023-08-04;939;939;;;1687.94;;215.37738495762;;
Open;Dividends;HKD;2023-08-10;390;390;;;2176.4;;277.7037931572;;
Open;Dividends;HKD;2023-08-18;1398;1398;;;1975.41;;252.05791676193002;;
Open;Dividends;HKD;2023-09-04;215;215;;;1140.0;;145.46146122;;
Open;Dividends;HKD;2023-09-05;14;14;;;270.0;;34.45139871;;
Open;Dividends;HKD;2023-09-14;808;808;;;1490.0;;190.12068177;;
Open;Dividends;HKD;2023-09-21;5;5;;;1879.68;;239.84298195264;;
Open;Dividends;HKD;2023-09-28;2828;2828;;;568.0;;72.475535064;;
Open;Dividends;HKD;2023-09-28;386;386;;;315.6;;40.2698571588;;
Open;Dividends;HKD;2023-09-29;1883;1883;;;600.0;;76.5586638;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;645.48;;82.36181051604;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;1202.52;;153.43887398796;;
Open;Dividends;HKD;2023-10-13;2888;2888;;;60.04;;7.66097029092;;
Open;Dividends;HKD;2023-10-13;66;66;;;210.0;;26.79553233;;
Open;Dividends;HKD;2023-10-25;2318;2318;;;505.93;;64.55554129389;;
Open;Dividends;HKD;2023-10-27;27;27;;;200.0;;25.5195546;;
Open;Dividends;HKD;2023-10-30;857;857;;;8.7;;1.1101006250999998;;
Open;Dividends;HKD;2023-10-30;857;857;;;449.44;;57.347543097119996;;
Open;Dividends;HKD;2023-11-16;16;16;;;16650.0;;2124.50292045;;
Open;Dividends;HKD;2023-11-30;2800;2800;;;825.0;;105.268162725;;
Open;Dividends;HKD;2023-12-21;5;5;;;1872.67;;238.94852156391;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;0.13;;0.09986014259;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;0.2;;0.15363098860000002;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;9.58;;7.35892435394;;
Open;Dividends;SGD;2023-04-21;D05;D05;;;84.0;;64.525015212;;
Open;Dividends;SGD;2023-04-21;D05;D05;;;100.0;;76.8154943;;
Open;Dividends;SGD;2023-05-10;9CI;9CI;;;144.0;;110.614311792;;
Open;Dividends;SGD;2023-05-12;F34;F34;;;880.0;;675.97634984;;
Open;Dividends;SGD;2023-05-19;O39;O39;;;320.0;;245.80958176;;
Open;Dividends;SGD;2023-05-22;D05;D05;;;84.0;;64.525015212;;
Open;Dividends;SGD;2023-05-25;BS6;BS6;;;350.0;;268.85423005;;
Open;Dividends;SGD;2023-08-24;D05;D05;;;96.0;;73.742874528;;
Open;Dividends;SGD;2023-08-25;O39;O39;;;320.0;;245.80958176;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;1.11;;0.8526519867300001;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;0.5;;0.3840774715;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;0.28;;0.21508338404000002;;
Open;Dividends;SGD;2023-08-30;F34;F34;;;480.0;;368.71437263999997;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;0.04;;0.03072619772;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;0.44;;0.33798817492;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;9.32;;7.15920406876;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.3;;0.23044648289999997;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.08;;0.06145239544;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.1;;0.07681549430000001;;
Open;Dividends;SGD;2023-11-27;D05;D05;;;96.0;;73.742874528;;
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
Open;Trades;HKD;2022-10-31;16;16;Stocks;85.0;-255000.0;3000.0;-32537.432115;-543.2675;"O;P"
Open;Trades;HKD;2022-01-18;200;200;Stocks;9.2;-18400.0;2000.0;-2347.7990231999997;-42.5244;O
Open;Trades;HKD;2022-10-31;2800;2800;Stocks;24.0;-24000.0;1000.0;-3062.346552;-23.584;"A;O"
Open;Trades;HKD;2022-09-13;3918;3918;Stocks;6.0527;5.74280176;-0.9488;0.7327687153564805;0.0;C
Open;Trades;HKD;2022-06-29;700;700;Stocks;485.3;-99971.8;206.0;-12756.179042801401;-220.3252263;"A;O"
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;-0.0476;0.0;0.0;O
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;0.0476;0.0;0.0;Ca
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;221.0;10.5196;-0.0476;1.3422775328508;0.0;C
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
Open;Trades;EUR;2022-10-24;MC;MC 16DEC22 720 P;Equity and Index Options;87.32;-8732.0;1.0;-9578.130799999999;-1.1;C
Open;Trades;EUR;2022-10-24;MC;MC 15DEC23 720 P;Equity and Index Options;124.72;12472.0;-1.0;13680.5368;-1.1;O
Open;Trades;EUR;2022-01-21;MC;MC 16DEC22 720.0 P;Equity and Index Options;94.1;9410.0;-1.0;10321.829;-1.1;O
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
Open;Transaction Fees;HKD;2022-01-18;200;200;Stocks;9.2;-24.5244;0.0;-3.1292588241612;;
Open;Transaction Fees;HKD;2022-06-29;700;700;Stocks;485.3;-132.8491963;0.0;-16.95126159271984;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-225.845;0.0;-28.817319043184998;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-113.4225;0.0;-14.4724584080925;;
Open;Transaction Fees;HKD;2022-10-31;2800;2800;Stocks;24.0;-0.684;0.0;-0.087276876732;;
Open;Withholding Tax;HKD;2022-06-20;2318;2318;;;-90.17;;-11.50549119141;;
Open;Withholding Tax;HKD;2022-06-23;386;386;;;-72.32;;-9.22787094336;;
Open;Withholding Tax;HKD;2022-07-27;1398;1398;;;-205.93;;-26.27620939389;;
Open;Withholding Tax;HKD;2022-07-29;857;857;;;-22.62;;-2.88626162526;;
Open;Withholding Tax;HKD;2022-07-29;939;939;;;-170.38;;-21.740108563739998;;
Open;Withholding Tax;HKD;2022-08-09;390;390;;;-229.56;;-29.29134476988;;
Open;Withholding Tax;HKD;2022-09-29;386;386;;;-37.02;;-4.72366955646;;
Open;Withholding Tax;HKD;2022-10-20;2318;2318;;;-42.38;;-5.40759361974;;
Open;Withholding Tax;HKD;2022-10-28;857;857;;;-46.68;;-5.95626404364;;
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
Open;Dividends;HKD;2022-03-17;16;16;;;1875.0;;239.24582437499998;;
Open;Dividends;HKD;2022-03-25;14;14;;;1170.0;;149.28939441;;
Open;Dividends;HKD;2022-04-11;808;808;;;1724.0;;219.978560652;;
Open;Dividends;HKD;2022-04-28;5;5;;;3388.17;;432.32294654541;;
Open;Dividends;HKD;2022-04-29;27;27;;;300.0;;38.2793319;;
Open;Dividends;HKD;2022-05-05;1972;1972;;;3584.0;;457.310418432;;
Open;Dividends;HKD;2022-05-12;2888;2888;;;90.34;;11.52718281282;;
Open;Dividends;HKD;2022-06-06;700;700;;;640.0;;81.66257472;;
Open;Dividends;HKD;2022-06-20;2318;2318;;;901.7;;115.0549119141;;
Open;Dividends;HKD;2022-06-23;386;386;;;723.2;;92.2787094336;;
Open;Dividends;HKD;2022-07-19;66;66;;;510.0;;65.07486423;;
Open;Dividends;HKD;2022-07-27;1398;1398;;;2059.31;;262.76336991663;;
Open;Dividends;HKD;2022-07-29;857;857;;;226.22;;28.86516820806;;
Open;Dividends;HKD;2022-07-29;939;939;;;1703.8;;217.4010856374;;
Open;Dividends;HKD;2022-08-09;390;390;;;2295.6;;292.9134476988;;
Open;Dividends;HKD;2022-09-08;808;808;;;1750.0;;223.29610275;;
Open;Dividends;HKD;2022-09-16;14;14;;;270.0;;34.45139871;;
Open;Dividends;HKD;2022-09-29;386;386;;;370.2;;47.2366955646;;
Open;Dividends;HKD;2022-09-29;5;5;;;1695.13;;216.29481294549;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;1755.52;;224.00044245696;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;36.48;;4.654766759039999;;
Open;Dividends;HKD;2022-10-14;2888;2888;;;40.18;;5.12687851914;;
Open;Dividends;HKD;2022-10-14;66;66;;;210.0;;26.79553233;;
Open;Dividends;HKD;2022-10-20;2318;2318;;;531.06;;67.76207332937999;;
Open;Dividends;HKD;2022-10-28;857;857;;;466.76;;59.557536525479996;;
Open;Dividends;HKD;2022-11-17;16;16;;;16650.0;;2124.50292045;;
Open;Dividends;SGD;2022-01-28;C38U;C38U;;;0.48;;0.36871437263999995;;
Open;Dividends;SGD;2022-01-28;C38U;C38U;;;8.49;;6.52163546607;;
Open;Dividends;SGD;2022-03-15;C38U;C38U;;;0.19;;0.14594943917;;
Open;Dividends;SGD;2022-03-15;C38U;C38U;;;0.5;;0.3840774715;;
Open;Dividends;SGD;2022-04-22;D05;D05;;;72.0;;55.307155896;;
Open;Dividends;SGD;2022-05-06;F34;F34;;;840.0;;645.2501521199999;;
Open;Dividends;SGD;2022-05-20;9CI;9CI;;;36.0;;27.653577948;;
Open;Dividends;SGD;2022-05-20;9CI;9CI;;;144.0;;110.614311792;;
Open;Dividends;SGD;2022-05-20;O39;O39;;;224.0;;172.066707232;;
Open;Dividends;SGD;2022-05-25;D05;D05;;;72.0;;55.307155896;;
Open;Dividends;SGD;2022-05-27;BS6;BS6;;;350.0;;268.85423005;;
Open;Dividends;SGD;2022-08-24;F34;F34;;;480.0;;368.71437263999997;;
Open;Dividends;SGD;2022-08-25;O39;O39;;;224.0;;172.066707232;;
Open;Dividends;SGD;2022-08-26;D05;D05;;;72.0;;55.307155896;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;1.3;;0.9986014259;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;0.06;;0.046089296579999994;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;8.31;;6.38336757633;;
Open;Dividends;SGD;2022-11-24;D05;D05;;;72.0;;55.307155896;;
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
Open;Trades;HKD;2021-09-19;1398;1398;Stocks;4.18;-12540.0;3000.0;-1600.07607342;-35.33858;O
Open;Trades;HKD;2021-05-10;14;14;Stocks;28.5;-28500.0;1000.0;-3636.5365305;-52.5695;O
Open;Trades;HKD;2021-09-15;200;200;Stocks;9.33;-9330.0;1000.0;-1190.48722209;-31.25191;O
Open;Trades;HKD;2021-04-25;2318;2318;Stocks;86.3;-43150.0;500.0;-5505.84390495;-79.68505;O
Open;Trades;HKD;2021-04-25;390;390;Stocks;3.92;-39200.0;10000.0;-5001.8327016;-72.4184;"O;P"
Open;Trades;HKD;2021-05-11;3918;3918;Stocks;7.8;-15600.0;2000.0;-1990.5252588;-34.4212;O
Open;Trades;HKD;2021-04-20;700;700;Stocks;614.5;-61450.0;100.0;-7840.88315085;-112.81915;O
Open;Trades;HKD;2021-08-15;700;700;Stocks;455.8;-91160.0;200.0;-11631.812986679999;-194.38932;O
Open;Trades;HKD;2021-04-26;939;939;Stocks;6.38;-25520.0;4000.0;-3256.2951669599997;-47.10504;O
Open;Trades;SGD;2021-09-10;C38U;C38U;Stocks;2.0;1.2144;-0.6072;0.9328473627791999;0.0;C
Open;Trades;USD;2021-12-20;BABA;BABA;Stocks;230.0;-46000.0;200.0;-46000.0;0.0;"A;O"
Open;Trades;USD;2021-02-09;FCX;FCX;Stocks;31.38;15690.0;-500.0;15690.0;-2.906249;"C;P"
Open;Trades;USD;2021-01-19;MSFT;MSFT;Stocks;213.0;-21300.0;100.0;-21300.0;-1.0;O
Open;Trades;EUR;2021-03-19;MC;MC 19MAR21 360.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-08-19;700;700 30AUG21 450.0 P;Equity and Index Options;32.3;6460.0;-2.0;824.28161358;-24.0;O
Open;Trades;HKD;2021-08-30;700;700 30AUG21 450.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-09-03;700;700 29JUN22 500.0 P;Equity and Index Options;67.08;13416.0;-2.0;1711.851722568;-32.832;O
Open;Trades;HKD;2021-12-06;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.05;1025.0;-1.0;130.787717325;-19.0;O
Open;Trades;HKD;2021-12-20;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.5;1250.0;-1.0;159.49721625;-19.0;O
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
Open;Corporate Actions;SGD;2021-09-20;9CI;C31;Stocks;;1141.2;-1200.0;876.6184209516;;
Open;Corporate Actions;SGD;2021-09-20;C38U;C38U;Stocks;;0.0;185.6072;0.0;;
Open;Corporate Actions;USD;2021-01-04;NOV;US62955J1034) (NOV;Stocks;;0.0;300.0;0.0;;
Open;Corporate Actions;USD;2021-01-04;NOV;US62955J1034) (NOV.OLD;Stocks;;0.0;-300.0;0.0;;
Open;Transaction Fees;HKD;2021-04-20;700;700;Stocks;614.5;-63.65915;0.0;-8.122765771072949;;
Open;Transaction Fees;HKD;2021-04-25;2318;2318;Stocks;86.3;-45.16505;0.0;-5.76295979743365;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-24.63504;0.0;-3.14337624176592;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-4.10584;0.0;-0.5238960402943199;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-12.31752;0.0;-1.57168812088296;;
Open;Transaction Fees;HKD;2021-04-26;939;939;Stocks;6.38;-26.68904;0.0;-3.4054620675079197;;
Open;Transaction Fees;HKD;2021-05-10;14;14;Stocks;28.5;-29.7695;0.0;-3.7985219033235;;
Open;Transaction Fees;HKD;2021-05-11;3918;3918;Stocks;7.8;-16.4212;0.0;-2.0953085499875996;;
Open;Transaction Fees;HKD;2021-08-15;700;700;Stocks;455.8;-121.46132;0.0;-15.49819393764036;;
Open;Transaction Fees;HKD;2021-09-15;200;200;Stocks;9.33;-13.25191;0.0;-1.69091420399643;;
Open;Transaction Fees;HKD;2021-09-19;1398;1398;Stocks;4.18;-17.33858;0.0;-2.21236419498234;;
Open;Withholding Tax;HKD;2021-06-28;386;386;;;-31.38;;-4.00401811674;;
Open;Withholding Tax;HKD;2021-07-27;1398;1398;;;-95.97;;-12.24555827481;;
Open;Withholding Tax;HKD;2021-07-30;857;857;;;-21.21;;-2.70634876533;;
Open;Withholding Tax;HKD;2021-08-05;390;390;;;-216.71;;-27.65171338683;;
Open;Withholding Tax;HKD;2021-08-05;939;939;;;-156.33;;-19.947359853090003;;
Open;Withholding Tax;HKD;2021-09-30;386;386;;;-38.44;;-4.90485839412;;
Open;Withholding Tax;HKD;2021-10-25;2318;2318;;;-52.84;;-6.74226632532;;
Open;Withholding Tax;HKD;2021-10-29;857;857;;;-31.31;;-3.9950862726299996;;
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
Open;Dividends;HKD;2021-03-18;16;16;;;1875.0;;239.24582437499998;;
Open;Dividends;HKD;2021-04-07;808;808;;;1784.0;;227.634427032;;
Open;Dividends;HKD;2021-04-29;5;5;;;2796.85;;356.87183141505;;
Open;Dividends;HKD;2021-05-06;1972;1972;;;3416.0;;435.873992568;;
Open;Dividends;HKD;2021-05-20;2888;2888;;;89.41;;11.40851688393;;
Open;Dividends;HKD;2021-06-07;700;700;;;320.0;;40.83128736;;
Open;Dividends;HKD;2021-06-28;386;386;;;313.8;;40.0401811674;;
Open;Dividends;HKD;2021-07-05;3918;3918;;;291.4;;37.1819910522;;
Open;Dividends;HKD;2021-07-20;66;66;;;490.0;;62.52290877;;
Open;Dividends;HKD;2021-07-27;1398;1398;;;959.73;;122.45941068129;;
Open;Dividends;HKD;2021-07-30;857;857;;;212.14;;27.068591564219997;;
Open;Dividends;HKD;2021-08-05;390;390;;;2167.1;;276.5171338683;;
Open;Dividends;HKD;2021-08-05;939;939;;;1563.34;;199.47870244182;;
Open;Dividends;HKD;2021-09-06;14;14;;;270.0;;34.45139871;;
Open;Dividends;HKD;2021-09-08;808;808;;;1802.0;;229.931186946;;
Open;Dividends;HKD;2021-09-30;386;386;;;384.4;;49.04858394119999;;
Open;Dividends;HKD;2021-09-30;5;5;;;1308.18;;166.92085468314;;
Open;Dividends;HKD;2021-10-05;1972;1972;;;1736.0;;221.509733928;;
Open;Dividends;HKD;2021-10-19;66;66;;;125.0;;15.949721625;;
Open;Dividends;HKD;2021-10-22;2888;2888;;;29.9;;3.8151734126999997;;
Open;Dividends;HKD;2021-10-25;2318;2318;;;528.4;;67.4226632532;;
Open;Dividends;HKD;2021-10-29;857;857;;;313.06;;39.945758815379996;;
Open;Dividends;HKD;2021-11-18;16;16;;;5550.0;;708.16764015;;
Open;Dividends;SGD;2021-05-06;F34;F34;;;520.0;;399.44057036;;
Open;Dividends;SGD;2021-05-06;F34;F34;;;720.0;;553.07155896;;
Open;Dividends;SGD;2021-05-18;9CI;C31;;;108.0;;82.960733844;;
Open;Dividends;SGD;2021-05-24;D05;D05;;;36.0;;27.653577948;;
Open;Dividends;SGD;2021-05-28;BS6;BS6;;;315.0;;241.968807045;;
Open;Dividends;SGD;2021-06-25;D05;D05;;;36.0;;27.653577948;;
Open;Dividends;SGD;2021-06-29;O39;O39;;;127.2;;97.7093087496;;
Open;Dividends;SGD;2021-08-26;D05;D05;;;66.0;;50.698226238;;
Open;Dividends;SGD;2021-08-26;O39;O39;;;200.0;;153.6309886;;
Open;Dividends;SGD;2021-08-27;F34;F34;;;400.0;;307.2619772;;
Open;Dividends;SGD;2021-11-26;D05;D05;;;66.0;;50.698226238;;
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
Open;Trades;HKD;2020-03-18;16;16;Stocks;97.5;-48750.0;500.0;-6220.39143375;-89.31625;O
Open;Trades;HKD;2020-03-18;16;16;Stocks;96.5;-48250.0;500.0;-6156.59254725;-88.90275;O
Open;Trades;HKD;2020-07-20;16;16;Stocks;94.05;-47025.0;500.0;-6000.285275325;-86.889675;O
Open;Trades;HKD;2020-01-29;1972;1972;Stocks;24.65;-24650.0;1000.0;-3145.28510445;-45.38555;O
Open;Trades;HKD;2020-05-04;1972;1972;Stocks;20.9;-20900.0;1000.0;-2666.7934557;-39.5643;O
Open;Trades;HKD;2020-05-24;1972;1972;Stocks;17.78;-35560.0;2000.0;-4537.37680788;-67.40812;"O;P"
Open;Trades;HKD;2020-07-20;1972;1972;Stocks;18.2;-29120.0;1600.0;-3715.64714976;-55.08224;"O;P"
Open;Trades;HKD;2020-07-22;27;27;Stocks;51.5;-51500.0;1000.0;-6571.2853095;-94.5905;O
Open;Trades;HKD;2020-03-18;2800;2800;Stocks;21.7;-10850.0;500.0;-1384.43583705;-18.29295;O
Open;Trades;HKD;2020-03-31;5;5;Stocks;40.8;-16320.0;400.0;-2082.39565536;-35.44064;O
Open;Trades;HKD;2020-05-14;5;5;Stocks;38.0;-45600.0;1200.0;-5818.4584488;-83.7112;O
Open;Trades;HKD;2020-05-21;66;66;Stocks;40.0;-20000.0;500.0;-2551.95546;-38.54;O
Open;Trades;HKD;2020-09-29;700;700;Stocks;530.0;-53000.0;100.0;-6762.681969;-101.981;"A;O"
Open;Trades;HKD;2020-04-23;808;808;Stocks;2.38;-23800.0;10000.0;-3036.8269974;-48.6826;"O;P"
Open;Trades;HKD;2020-07-22;808;808;Stocks;2.29;-22900.0;10000.0;-2921.9890017;-41.9383;O
Open;Trades;SGD;2020-07-22;BS6;BS6;Stocks;0.935;-3085.5;3300.0;-2370.1420766265;-2.5;O
Open;Trades;SGD;2020-08-26;BS6;BS6;Stocks;0.92;-3404.0;3700.0;-2614.799425972;-2.7232;O
Open;Trades;SGD;2020-07-23;9CI;C31;Stocks;2.83;-3396.0;1200.0;-2608.654186428;-2.7168;O
Open;Trades;SGD;2020-07-22;D05;D05;Stocks;21.2;-4240.0;200.0;-3256.97695832;-3.392;O
Open;Trades;SGD;2020-09-08;F34;F34;Stocks;4.32;-4320.0;1000.0;-3318.4293537599997;-3.456;O
Open;Trades;SGD;2020-07-22;O39;O39;Stocks;9.13;-3652.0;400.0;-2805.301851836;-2.9216;O
Open;Trades;SGD;2020-08-26;O39;O39;Stocks;8.62;-3448.0;400.0;-2648.598243464;-2.7584;O
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
Open;Trades;EUR;2020-05-04;MC;MC 19MAR21 360.0 P;Equity and Index Options;50.8;5080.0;-1.0;5572.2519999999995;-1.1;O
Open;Trades;HKD;2020-07-24;27;27 28AUG20 50.0 P;Equity and Index Options;2.18;4360.0;-2.0;556.32629028;-24.0;O
Open;Trades;HKD;2020-08-28;27;27 28AUG20 50.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2020-07-24;700;700 29SEP20 530.0 P;Equity and Index Options;38.02;3802.0;-1.0;485.126732946;-21.0;O
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
Open;Transaction Fees;HKD;2020-01-29;1972;1972;Stocks;24.65;-25.66555;0.0;-3.2748670228201497;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;97.5;-50.31625;0.0;-6.420241445711249;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;96.5;-50.30275;0.0;-6.41851887577575;;
Open;Transaction Fees;HKD;2020-03-18;2800;2800;Stocks;21.7;-0.29295;0.0;-0.03737976760035;;
Open;Transaction Fees;HKD;2020-03-31;5;5;Stocks;40.8;-17.44064;0.0;-2.22538682369472;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099275189298;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099275189298;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099275189298;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099275189298;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099275189298;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099275189298;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099275189298;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39099275189298;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-5.12852;0.0;-0.65438773078596;;
Open;Transaction Fees;HKD;2020-05-04;1972;1972;Stocks;20.9;-21.5643;0.0;-2.7515566563039;;
Open;Transaction Fees;HKD;2020-05-14;5;5;Stocks;38.0;-47.2312;0.0;-6.0265959361176;;
Open;Transaction Fees;HKD;2020-05-21;66;66;Stocks;40.0;-20.54;0.0;-2.6208582574199997;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.522642009381276;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.522642009381276;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.962970264525104;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.962970264525104;;
Open;Transaction Fees;HKD;2020-07-20;16;16;Stocks;94.05;-49.269675;0.0;-6.286700806433775;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.04586280226088;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.04586280226088;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-4.09828;0.0;-0.52293140113044;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-11.29484;0.0;-1.44119643039132;;
Open;Transaction Fees;HKD;2020-07-22;808;808;Stocks;2.29;-23.6183;0.0;-3.0136424820459;;
Open;Transaction Fees;HKD;2020-07-22;27;27;Stocks;51.5;-53.3905;0.0;-6.812508899356501;;
Open;Transaction Fees;HKD;2020-09-29;700;700;Stocks;530.0;-54.431;0.0;-6.945274382162999;;
Open;Withholding Tax;HKD;2020-06-19;386;386;;;-41.54;;-5.30041149042;;
Open;Withholding Tax;HKD;2020-07-21;1398;1398;;;-74.27;;-9.47668660071;;
Open;Withholding Tax;HKD;2020-07-31;857;857;;;-14.44;;-1.84251184212;;
Open;Withholding Tax;HKD;2020-11-05;386;386;;;-15.98;;-2.03901241254;;
Open;Withholding Tax;HKD;2020-11-13;857;857;;;-19.59;;-2.49964037307;;
Open;Withholding Tax;USD;2020-02-03;FCX;FCX;;;-16.5;;-16.5;;
Open;Withholding Tax;USD;2020-03-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2020-09-15;MCD;MCD;;;-37.5;;-37.5;;
Open;Withholding Tax;USD;2020-12-15;MCD;MCD;;;-38.7;;-38.7;;
Open;Dividends;HKD;2020-05-07;1972;1972;;;590.0;;75.28268607;;
Open;Dividends;HKD;2020-05-29;2800;2800;;;45.0;;5.741899785;;
Open;Dividends;HKD;2020-06-19;386;386;;;415.4;;53.004114904199994;;
Open;Dividends;HKD;2020-07-02;200;200;;;30.1;;3.8406929673000003;;
Open;Dividends;HKD;2020-07-21;1398;1398;;;862.3;;110.02755965789999;;
Open;Dividends;HKD;2020-07-30;2828;2828;;;488.0;;62.267713224;;
Open;Dividends;HKD;2020-07-31;857;857;;;144.4;;18.4251184212;;
Open;Dividends;HKD;2020-09-09;808;808;;;1798.0;;229.420795854;;
Open;Dividends;HKD;2020-10-06;1972;1972;;;1680.0;;214.36425864;;
Open;Dividends;HKD;2020-10-09;66;66;;;5.75;;0.73368719475;;
Open;Dividends;HKD;2020-10-09;66;66;;;119.25;;15.21603443025;;
Open;Dividends;HKD;2020-11-05;386;386;;;159.8;;20.3901241254;;
Open;Dividends;HKD;2020-11-13;857;857;;;195.86;;24.99129981978;;
Open;Dividends;HKD;2020-11-19;16;16;;;5550.0;;708.16764015;;
Open;Dividends;SGD;2020-06-24;F34;F34;;;665.0;;510.82303709499996;;
Open;Dividends;SGD;2020-08-27;F34;F34;;;280.0;;215.08338404;;
Open;Dividends;SGD;2020-10-05;D05;D05;;;36.0;;27.653577948;;
Open;Dividends;SGD;2020-10-07;O39;O39;;;63.6;;48.8546543748;;
Open;Dividends;SGD;2020-12-29;D05;D05;;;36.0;;27.653577948;;
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
Open;Trades;HKD;2019-03-08;1398;1398;Stocks;5.9;-17700.0;3000.0;-2258.4805821;-36.4779;O
Open;Trades;USD;2019-02-11;AMZN;AMZN;Stocks;1800.0;-180000.0;100.0;-180000.0;0.0;"A;O"
Open;Trades;USD;2019-02-19;AMZN;AMZN;Stocks;1628.88;81444.0;-50.0;81444.0;-2.064722;C
Open;Trades;USD;2019-10-14;FCX;FCX;Stocks;15.0;-1500.0;100.0;-1500.0;0.0;"A;O"
Open;Trades;USD;2019-02-11;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2019-03-08;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;3.8;1900.0;-5.0;1900.0;-3.9787;O
Open;Trades;USD;2019-10-14;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2019-03-08;1398;1398;Stocks;5.9;-18.4779;0.0;-2.3577388897167;;
Open;Withholding Tax;HKD;2019-06-21;386;386;;;-60.6;;-7.7324250438;;
Open;Withholding Tax;HKD;2019-07-23;1398;1398;;;-85.12;;-10.86112243776;;
Open;Withholding Tax;HKD;2019-08-02;857;857;;;-20.47;;-2.61192641331;;
Open;Withholding Tax;HKD;2019-09-26;386;386;;;-26.78;;-3.41706836094;;
Open;Withholding Tax;HKD;2019-11-01;857;857;;;-17.26;;-2.2023375619800003;;
Open;Withholding Tax;USD;2019-02-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-03-29;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-05-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-06-28;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-08-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-09-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-11-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-12-20;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;CNH;2019-07-31;3188;3188;;;390.0;;58.091925839999995;;
Open;Dividends;HKD;2019-01-04;200;200;;;24.38;;3.1108337057399997;;
Open;Dividends;HKD;2019-04-08;5;5;;;1318.35;;168.21852403454997;;
Open;Dividends;HKD;2019-05-16;2888;2888;;;150.56;;19.21112070288;;
Open;Dividends;HKD;2019-06-21;386;386;;;606.0;;77.32425043799999;;
Open;Dividends;HKD;2019-07-05;200;200;;;23.5;;2.9985476655;;
Open;Dividends;HKD;2019-07-05;5;5;;;625.02;;79.75116008046;;
Open;Dividends;HKD;2019-07-23;1398;1398;;;854.32;;109.00932942936001;;
Open;Dividends;HKD;2019-07-30;2828;2828;;;640.0;;81.66257472;;
Open;Dividends;HKD;2019-08-02;857;857;;;204.72;;26.12181608856;;
Open;Dividends;HKD;2019-09-26;386;386;;;267.8;;34.1706836094;;
Open;Dividends;HKD;2019-09-26;5;5;;;625.44;;79.80475114512001;;
Open;Dividends;HKD;2019-09-27;2828;2828;;;520.0;;66.35084196;;
Open;Dividends;HKD;2019-10-04;200;200;;;61.1;;7.7962239303;;
Open;Dividends;HKD;2019-10-21;2888;2888;;;70.24;;8.96246757552;;
Open;Dividends;HKD;2019-11-01;857;857;;;172.56;;22.01827170888;;
Open;Dividends;HKD;2019-11-20;5;5;;;626.63;;79.95659249498999;;
Open;Dividends;SGD;2019-05-16;F34;F34;;;490.0;;376.39592207;;
Open;Dividends;SGD;2019-08-30;F34;F34;;;210.0;;161.31253802999998;;
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
Open;Trades;HKD;2018-01-01;1398;1398;Stocks;6.48;6480.0;-1000.0;826.8335690399999;-25.17496;C
Open;Trades;HKD;2018-02-05;215;215;Stocks;3.35;6700.0;-2000.0;854.9050791;-25.1809;C
Open;Trades;HKD;2018-02-19;215;215;Stocks;3.4;6800.0;-2000.0;867.6648564;-25.1836;C
Open;Trades;USD;2018-08-17;FCX;FCX;Stocks;21.0;-21000.0;1000.0;-21000.0;0.0;"A;O"
Open;Trades;USD;2018-10-26;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;216.21;21621.0;-1.0;21621.0;-1.367373;O
Open;Trades;USD;2018-01-18;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;3.08;3080.0;-10.0;3080.0;-5.006148;O
Open;Trades;USD;2018-08-17;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;0.0;0.0;10.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2018-01-01;1398;1398;Stocks;6.48;-7.17496;0.0;-0.9155089173640799;;
Open;Transaction Fees;HKD;2018-02-05;215;215;Stocks;3.35;-7.1809;0.0;-0.9162668481357;;
Open;Transaction Fees;HKD;2018-02-19;215;215;Stocks;3.4;-7.1836;0.0;-0.9166113621228;;
Open;Withholding Tax;HKD;2018-06-14;386;386;;;-98.64;;-12.58624432872;;
Open;Withholding Tax;HKD;2018-07-26;857;857;;;-14.86;;-1.89610290678;;
Open;Withholding Tax;HKD;2018-09-21;386;386;;;-36.5;;-4.6573187145;;
Open;Withholding Tax;HKD;2018-11-01;857;857;;;-20.39;;-2.60171859147;;
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
Open;Dividends;HKD;2018-04-06;5;5;;;1317.81;;168.14962123713;;
Open;Dividends;HKD;2018-05-17;2888;2888;;;110.45;;14.09317402785;;
Open;Dividends;HKD;2018-06-14;386;386;;;986.4;;125.8624432872;;
Open;Dividends;HKD;2018-07-04;200;200;;;40.0;;5.10391092;;
Open;Dividends;HKD;2018-07-05;5;5;;;627.87;;80.11481373351;;
Open;Dividends;HKD;2018-07-26;857;857;;;148.62;;18.96358102326;;
Open;Dividends;HKD;2018-07-30;2828;2828;;;440.0;;56.143020119999996;;
Open;Dividends;HKD;2018-09-21;386;386;;;365.0;;46.573187145;;
Open;Dividends;HKD;2018-09-27;5;5;;;627.73;;80.09695004529;;
Open;Dividends;HKD;2018-09-28;2828;2828;;;800.0;;102.0782184;;
Open;Dividends;HKD;2018-10-05;200;200;;;45.0;;5.741899785;;
Open;Dividends;HKD;2018-10-22;2888;2888;;;60.13;;7.6724540904900005;;
Open;Dividends;HKD;2018-11-01;857;857;;;203.88;;26.014633959239998;;
Open;Dividends;HKD;2018-11-21;5;5;;;626.32;;79.91703718536;;
Open;Dividends;SGD;2018-05-16;F34;F34;;;490.0;;376.39592207;;
Open;Dividends;SGD;2018-08-31;F34;F34;;;245.0;;188.197961035;;
Open;Dividends;USD;2018-03-27;BHP;BHP;;;1320.0;;1320.0;;
Open;Dividends;USD;2018-03-30;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-04-05;SOUHY;SOUHY;;;73.0;;73.0;;
Open;Dividends;USD;2018-06-29;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-09-25;BHP;BHP;;;1512.0;;1512.0;;
Open;Dividends;USD;2018-09-28;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-10-11;SOUHY;SOUHY;;;62.0;;62.0;;
Open;Dividends;USD;2018-11-01;FCX;FCX;;;50.0;;50.0;;
Open;Dividends;USD;2018-12-21;NOV;NOV;;;15.0;;15.0;;
Open;Withholding Tax;HKD;2025-09-25;386;386;;;-19.34;;-2.46774092982;;
Open;Withholding Tax;HKD;2025-10-23;857;857;;;-48.11;;-6.1387288590299995;;
Open;Withholding Tax;HKD;2025-10-24;2318;2318;;;-52.02;;-6.637636151460001;;
Open;Withholding Tax;HKD;2025-12-23;390;390;;;-89.85;;-11.464659904049999;;
Open;Dividends;HKD;2025-09-16;215;215;;;1140.0;;145.46146122;;
Open;Dividends;HKD;2025-09-23;1299;1299;;;294.0;;37.513745262;;
Open;Dividends;HKD;2025-09-25;386;386;;;193.4;;24.6774092982;;
Open;Dividends;HKD;2025-09-26;1883;1883;;;600.0;;76.5586638;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;26.5;;3.3813409845;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;173.5;;22.1382136155;;
Open;Dividends;HKD;2025-09-30;3918;3918;;;159.42;;20.341636971659998;;
Open;Dividends;HKD;2025-10-09;1972;1972;;;1960.0;;250.09163508;;
Open;Dividends;HKD;2025-10-23;857;857;;;482.1;;61.5148863633;;
Open;Dividends;HKD;2025-10-24;2318;2318;;;520.24;;66.38146542552;;
Open;Dividends;HKD;2025-10-31;27;27;;;700.0;;89.3184411;;
Open;Dividends;HKD;2025-11-20;16;16;;;12600.0;;1607.7319398;;
Open;Dividends;HKD;2025-11-28;2800;2800;;;825.0;;105.268162725;;
Open;Dividends;HKD;2025-12-18;5;5;;;1866.53;;238.16507123769;;
Open;Dividends;HKD;2025-12-23;390;390;;;898.95;;114.70401803835;;
Open;Dividends;CNH;2022-07-29;3188;3188;;;370.0;;55.11285272;;
Open;Dividends;HKD;2022-05-26;215;215;;;2605.0;;332.39219866499997;;
Open;Dividends;HKD;2022-05-31;2800;2800;;;40.0;;5.10391092;;
Open;Dividends;HKD;2022-06-13;1883;1883;;;1700.0;;216.9162141;;
Open;Dividends;HKD;2022-07-28;2828;2828;;;460.0;;58.69497558;;
Open;Dividends;HKD;2022-09-02;215;215;;;1140.0;;145.46146122;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;283.5;;36.1739686455;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;76.5;;9.7612296345;;
Open;Dividends;HKD;2022-09-30;1883;1883;;;600.0;;76.5586638;;
Open;Dividends;HKD;2022-11-30;2800;2800;;;280.0;;35.72737644;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;243.6;;36.285110601599996;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;56.4;;8.4009861984;;
Open;Dividends;HKD;2021-05-26;215;215;;;2605.0;;332.39219866499997;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;12.22;;1.55924478606;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;52.78;;6.73461045894;;
Open;Dividends;HKD;2021-06-04;1883;1883;;;1600.0;;204.1564368;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;8.8;;1.1228604024000002;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;311.2;;39.7084269576;;
Open;Dividends;HKD;2021-09-03;215;215;;;1109.72;;141.59780065356;;
Open;Dividends;HKD;2021-09-03;215;215;;;30.28;;3.86366056644;;
Open;Dividends;HKD;2021-09-03;215;215;;;9637.06;;1229.66739426738;;
Open;Dividends;HKD;2021-09-03;215;215;;;262.94;;33.55055843262;;
Open;Dividends;HKD;2021-09-29;1883;1883;;;550.0;;70.17877514999999;;
Open;Dividends;HKD;2021-09-29;2828;2828;;;396.0;;50.528718108;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;234.9;;29.9727168777;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;35.1;;4.4786818323;;
Open;Trades;HKD;2020-09-24;1883;1883;Stocks;2.45;-24500.0;10000.0;-3126.1454384999997;-45.2615;O
Open;Trades;HKD;2020-09-02;215;215;Stocks;1.21;-60500.0;50000.0;-7719.6652665;-111.0335;O
Open;Transaction Fees;HKD;2020-09-02;215;215;Stocks;1.21;-62.6335;0.0;-7.9918951151955;;
Open;Transaction Fees;HKD;2020-09-24;1883;1883;Stocks;2.45;-25.6615;0.0;-3.2743502518395;;
Open;Dividends;CNH;2020-07-31;3188;3188;;;380.0;;56.60238928;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;9.0;;1.148379957;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;591.0;;75.410283843;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;2.64;;0.33685812072;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;327.36;;41.77040696928;;
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
912797UE5 - United States Treasury B 04/15/27;IBKR;Treasury Bills;;USD;98.357908;96.90631;1000.0;98357.91;98357.91;1451.59799999999;0.040035909418071974
912797UX3 - United States Treasury B 05/13/27;IBKR;Treasury Bills;;USD;98.008618;96.625351852;1080.0;105849.31;105849.31;1493.9274398400044;0.04308523216003085
912797VF1 - United States Treasury B 06/10/27;IBKR;Treasury Bills;;USD;97.694558;96.38057197;2640.0;257913.63;257913.63;3468.9231191999875;0.10498196564329326
912797VQ7 - United States Treasury B 07/08/27;IBKR;Treasury Bills;;USD;97.377458;96.420521978;1820.0;177226.97;177226.97;1741.6235600400141;0.07213901675380617
912797VR5 - United States Treasury B 08/05/27;IBKR;Treasury Bills;;USD;97.01812;96.29921;1000.0;97018.12;97018.12;718.9099999999939;0.03949055713192398
AMZN;IBKR;Stocks;AMAZON.COM INC;USD;244.43;207.8670155;300.0;73329.0;73329.0;10968.895349999999;0.029848064092840118
BABA;IBKR;Stocks;ALIBABA GROUP HOLDING-SP ADR;USD;86.26;111.16845686;500.0;43130.0;43130.0;-12454.228430000001;0.017555769263513675
BABA 15JAN27 100 C;IBKR;Equity and Index Options;;USD;1.665;2.72447076;-1.0;-166.5;-166.5;105.947076;-6.777267754173492e-05
BABA 15JAN27 110 C;IBKR;Equity and Index Options;;USD;0.635;1.06450496;-1.0;-63.5;-63.5;42.950496;-2.5847237380781784e-05
BABA 15JAN27 75 P;IBKR;Equity and Index Options;;USD;1.88;2.38447777;-1.0;-188.0;-188.0;50.44777700000003;-7.652410437144843e-05
BABA 15JAN27 90 C;IBKR;Equity and Index Options;;USD;4.4;6.005473;1.0;440.0;440.0;-160.5473;0.00017909896767785802
BABA 18DEC26 90 P;IBKR;Equity and Index Options;;USD;6.1318;3.44559593;-2.0;-1226.36;-1226.36;-537.240814;-0.000499181386366859
BHP;IBKR;Stocks;BHP GROUP LTD-SPON ADR;USD;70.77;35.102226747;1200.0;84924.0;84924.0;42801.327903599995;0.034567728934260036
BHP 18DEC26 95 C;IBKR;Equity and Index Options;;USD;0.0117;4.0005117;-4.0;-4.68;-4.68;1595.52468;-1.9049617471190353e-06
BRK B;IBKR;Stocks;BERKSHIRE HATHAWAY INC-CL B;USD;579.32;469.0869395;100.0;57932.0;57932.0;11023.306050000003;0.023580821353440164
BRK B 18DEC26 540 P;IBKR;Equity and Index Options;;USD;1.7091;2.07448415;-1.0;-170.91;-170.91;36.53841499999999;-6.956773764959708e-05
BRK B 18DEC26 545 C;IBKR;Equity and Index Options;;USD;38.1204;18.66917949;-1.0;-3812.04;-3812.04;-1945.1220509999996;-0.001551664610787959
CASH - Schwab;Schwab;Forex;;USD;1.0;;1.0;;78478.7;;0.031944213987955265
CPRT;IBKR;Stocks;COPART INC;USD;33.165;27.171926655;1100.0;36481.5;36481.5;6592.380679499999;0.014849543157590404
DNOW;IBKR;Stocks;DNOW INC;USD;13.25;26.81106924;25.0;331.25;331.25;-339.026731;0.000134833029643842
FAST;IBKR;Stocks;FASTENAL CO;USD;52.93;42.17242457;100.0;5293.0;5293.0;1075.7575430000002;0.0021544791725429603
FCX;IBKR;Stocks;FREEPORT-MCMORAN INC;USD;48.55;11.2079574;500.0;24275.0;24275.0;18671.0213;0.0098809714554091
Forex;IBKR;Forex;;USD;0.0;;-1.0;;10795.067627292;0.0;0.00439405788195605
GEHC 17JUN27 57.5 P;IBKR;Equity and Index Options;;USD;1.3402;5.33945409;-1.0;-134.02;-134.02;399.925409;-5.4551917382242125e-05
GEHC 17JUN27 65 C;IBKR;Equity and Index Options;;USD;21.7955;8.8804028;1.0;2179.55;2179.55;1291.50972;0.0008871708068233534
GEHC 17JUN27 80 C;IBKR;Equity and Index Options;;USD;10.7165;3.91948334;-1.0;-1071.65;-1071.65;-679.7016659999999;-0.00043620774707267405
GLD;IBKR;Stocks;SPDR GOLD SHARES;USD;327.45;376.25228915;200.0;65490.0;65490.0;-9760.457830000007;0.026657253166415734
GLD 15JAN27 315 P;IBKR;Equity and Index Options;;USD;9.2378;15.2304028;1.0;923.78;923.78;-599.26028;0.0003760182826396629
GOOG;IBKR;Stocks;ALPHABET INC-CL C;USD;270.98;93.120965215;200.0;54196.0;54196.0;35571.806957;0.022060108300611805
GOOG 18DEC26 410 C;IBKR;Equity and Index Options;;USD;0.0194;32.055824165;-2.0;-3.88;-3.88;6407.284833;-1.5793272604320207e-06
GOOG 21JAN28 250 C;IBKR;Equity and Index Options;;USD;62.5415;132.321833;2.0;12508.3;12508.3;-13956.0666;0.005091417312283981
GOOG 21JAN28 250 P;IBKR;Equity and Index Options;;USD;29.2147;13.467856515;-2.0;-5842.94;-5842.94;-3149.3686970000003;-0.002378328459553781
H78;IBKR;Stocks;HONGKONG LAND HOLDINGS LTD;USD;8.53;4.208;1000.0;8530.0;8530.0;4321.999999999999;0.003472077714300293
ICE;IBKR;Stocks;INTERCONTINENTAL EXCHANGE IN;USD;177.81;152.14704663;300.0;53343.0;53343.0;7698.886010999999;0.021712900529181775
IVV;IBKR;Stocks;ISHARES CORE S&P 500 ETF;USD;720.01;402.65670378;100.0;72001.0;72001.0;31735.329622;0.029307510844939672
IVV;Schwab;Stocks;ISHARES CORE S&P 500 ETF;USD;693.71;455.0;10.0;6937.1;6937.1;2387.1000000000004;0.0028236987469956116
LULU 15JAN27 85 P;IBKR;Equity and Index Options;;USD;1.6854;4.59553934;-2.0;-337.08;-337.08;582.027868;-0.0001372060909655736
MCD 18DEC26 245 P;IBKR;Equity and Index Options;;USD;0.252;6.4943602;-1.0;-25.2;-25.2;624.23602;-1.0257486330640959e-05
MCD 18DEC26 275 C;IBKR;Equity and Index Options;;USD;10.7939;6.505473;1.0;1079.39;1079.39;428.84270000000004;0.0004393582607313709
META;IBKR;Stocks;META PLATFORMS INC-CLASS A;USD;609.89;230.10913779;200.0;121978.0;121978.0;75956.172442;0.04965030427138584
META 15JAN27 460 P;IBKR;Equity and Index Options;;USD;4.1959;19.71258137;-1.0;-419.59;-419.59;1551.668137;-0.00017079121783625557
META 15JAN27 520 P;IBKR;Equity and Index Options;;USD;10.9612;62.7404795;1.0;1096.12;1096.12;-5177.92795;0.000446168091934213
META 15JAN27 640 P;IBKR;Equity and Index Options;;USD;54.7603;77.2419796;-1.0;-5476.03;-5476.03;2248.1679599999993;-0.0022289802726658658
META 15JAN27 800 C;IBKR;Equity and Index Options;;USD;1.2717;35.535755475;-2.0;-254.34;-254.34;6852.811095;-0.00010352734417996913
MSFT;Schwab;Stocks;MICROSOFT CORP;USD;484.52;470.3566;100.0;48452.0;48452.0;1416.3399999999967;0.01972205268619904
MSFT 15JAN27 420 P;IBKR;Equity and Index Options;;USD;5.7865;53.1437823;-2.0;-1157.3;-1157.3;9471.45646;-0.0004710709893036025
MU 15JAN27 700 P;IBKR;Equity and Index Options;;USD;134.2407;55.29338782;-1.0;-13424.07;-13424.07;-7894.731218000001;-0.00546417517962569
MU 15JAN27 760 C;IBKR;Equity and Index Options;;USD;25.977;86.755473;1.0;2597.7;2597.7;-6077.847299999999;0.0010573758825835721
MU 15JAN27 800 C;IBKR;Equity and Index Options;;USD;18.8956;68.94310663;-1.0;-1889.56;-1889.56;5004.750663;-0.0007691323758303941
NBIS 18DEC26 140 P;IBKR;Equity and Index Options;;USD;55.4894;24.136075115;-2.0;-11097.88;-11097.88;-6270.664977;-0.004517315571392607
NOV;IBKR;Stocks;NOV INC;USD;17.05;62.158401437;300.0;5115.0;5115.0;-13532.5204311;0.0020820254992550994
NVDA;Moomoo-mom;Stocks;NVIDIA;USD;223.96;0.0;0.4717;105.641932;105.641932;105.64193200000001;4.3000820374305626e-05
NVDA;Moomoo-me;Stocks;NVIDIA;USD;223.96;0.0;1.4431;323.196676;323.196676;323.196676;0.00013155497961026173
NVDA 01/15/2027 150.00 P;Schwab;Equity and Index Options;PUT NVIDIA CORP $150 EXP 01/15/27;USD;28.146;9.5432;-1.0;-2814.6;-2814.6;-1860.2800000000002;-0.001145663532786589
NVDA 15JAN27 145 P;IBKR;Equity and Index Options;;USD;12.075;7.59437044;-1.0;-1207.5;-1207.5;-448.062956;-0.0004915045533432126
NVDA 15JAN27 165 C;IBKR;Equity and Index Options;;USD;8.35;12.435473;1.0;835.0;835.0;-408.54730000000006;0.0003398809954795715
NVDA 15JAN27 185 C;IBKR;Equity and Index Options;;USD;3.675;5.84440649;-1.0;-367.5;-367.5;216.940649;-0.00014958834232184733
NVO;IBKR;Stocks;NOVO-NORDISK A/S-SPONS ADR;USD;40.97;58.2644644;200.0;8194.0;8194.0;-3458.8928800000003;0.0033353112298917472
PDD;IBKR;Stocks;PDD HOLDINGS INC;USD;87.42;96.7605204;100.0;8742.0;8742.0;-934.0520400000003;0.003558370853272352
PDD 18DEC26 120 C;IBKR;Equity and Index Options;;USD;0.0019;5.8261019;-2.0;-0.38;-0.38;1164.84038;-1.5467638117633194e-07
PDD 18DEC26 90 P;IBKR;Equity and Index Options;;USD;4.2134;8.553596777;-4.0;-1685.36;-1685.36;1736.0787107999997;-0.0006860141731035337
PLTR 18DEC26 180 P;IBKR;Equity and Index Options;;USD;19.6823;10.19781688;-2.0;-3936.46;-3936.46;-1896.8966240000004;-0.001602308914329957
PLTR 18DEC26 195 P;IBKR;Equity and Index Options;;USD;32.1576;16.655473;1.0;3215.76;3215.76;1550.2127;0.0013089529461357927
SKHY 15JAN27 125 P;IBKR;Equity and Index Options;;USD;22.3037;11.39193216;-1.0;-2230.37;-2230.37;-1091.176784;-0.0009078567375901459
SKHY 18DEC26 115 P;IBKR;Equity and Index Options;;USD;12.5516;16.440827835;-2.0;-2510.32;-2510.32;777.845567;-0.0010218084557751831
SKHY 18DEC26 130 P;IBKR;Equity and Index Options;;USD;22.8424;24.4323006;1.0;2284.24;2284.24;-158.99006;0.0009297841498374327
SMCI 18DEC26 24 P;IBKR;Equity and Index Options;;USD;2.5935;2.96300312;-4.0;-1037.4;-1037.4;147.80124800000004;-0.0004222665206113862
SMCI 18DEC26 27 P;IBKR;Equity and Index Options;;USD;4.544;4.3169028;2.0;908.8;908.8;45.41943999999987;0.00036992077687644857
SOUHY;IBKR;Stocks;SOUTH32 - ADR;USD;14.13;7.17;300.0;4239.0;4239.0;2088.0000000000005;0.0017254557363328186
TSM;IBKR;Stocks;TAIWAN SEMICONDUCTOR-SP ADR;USD;342.34;181.60807492;300.0;102702.0;102702.0;48219.57752399999;0.041804141314662216
TSM 01/15/2027 390.00 P;Schwab;Equity and Index Options;PUT TAIWAN SEMICONDUCTOR$390 EXP 01/15/27;USD;75.5268;23.6229;-1.0;-7552.68;-7552.68;-5190.389999999999;-0.003074266343639102
TSM 19MAR27 310 P;IBKR;Equity and Index Options;;USD;22.647;17.84415929;-1.0;-2264.7;-2264.7;-480.2840709999998;-0.0009218305275001024
TSM 19MAR27 360 C;IBKR;Equity and Index Options;;USD;30.8;38.055473;1.0;3080.0;3080.0;-725.5472999999998;0.0012536927737450063
TSM 19MAR27 400 C;IBKR;Equity and Index Options;;USD;16.975;21.19409028;-1.0;-1697.5;-1697.5;421.909028;-0.0006909556764390091
UNH;IBKR;Stocks;UNITEDHEALTH GROUP INC;USD;346.19;322.13551065;200.0;69238.0;69238.0;4810.897869999997;0.028182850736544397
UNH 15JAN27 360 P;IBKR;Equity and Index Options;;USD;26.2185;12.18938278;-1.0;-2621.85;-2621.85;-1402.9117219999998;-0.0010672059736504364
UNH 17JUN27 360 P;IBKR;Equity and Index Options;;USD;42.5545;21.4891912;-1.0;-4255.45;-4255.45;-2106.53088;-0.0017321515954653202
UNH 18DEC26 440 C;IBKR;Equity and Index Options;;USD;0.0911;26.01244859;-1.0;-9.11;-9.11;2592.1348590000002;-3.7081627171483787e-06
9CI;IBKR;Stocks;CAPITALAND INVESTMENT LTD/SI;SGD;2.29;2.323057932;1200.0;2748.0;2110.889783364;-30.472336645389614;0.0008592231388232494
BS6;IBKR;Stocks;YANGZIJIANG SHIPBUILDING;SGD;4.18;0.171019587;7000.0;29260.0;22476.21363218;21556.626844452916;0.009148787860978268
BS6;Moomoo-me;Stocks;YZJ Shipbuilding;SGD;4.2;4.015;200.0;840.0;652.6800000000001;28.421732891000076;0.0002656688959636009
C38U;IBKR;Stocks;CAPITALAND INTEGRATED COMMER;SGD;2.33;1.362308617;222.0;517.26;397.33582581618;165.02079605129026;0.000161732809602516
CGP1;IBKR;Stocks;NOBLE GROUP LTD;SGD;0.0;85.85;50.0;0.0;0.0;-3297.3050928274997;0.0
D05;IBKR;Stocks;DBS GROUP HOLDINGS LTD;SGD;65.85;18.238145455;220.0;14487.0;11128.260659241;8046.1219111105265;0.004529681809364052
F34;IBKR;Stocks;WILMAR INTERNATIONAL LTD;SGD;2.82;3.040549529;9000.0;25380.0;19495.77245334;-1524.745897899049;0.007935619819262763
CASH - DBS;DBS;Forex;;SGD;0.763247271;;-0.777;;1043.95389;18.47775410253007;0.00042493423636882764
CASH - Moomoo-me;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;75899.04609;1343.3964121419413;0.030894183641939018
CASH - Moomoo-mom;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;7040.27268;124.6112770323598;0.002865694475360516
CASH - OCBC;OCBC;Forex;;SGD;0.763247271;;-0.777;;6154.438290000001;108.93220328133066;0.002505121691763823
CASH - OCBC Joint;OCBC Joint;Forex;;SGD;0.763247271;;-0.777;;30062.78268;532.1046373023601;0.01223684850798134
CASH - POSB;POSB;Forex;;SGD;0.763247271;;-0.777;;6713.0158200000005;118.81890263214063;0.002732486825834386
CASH - UOB;UOB;Forex;;SGD;0.763247271;;-0.777;;6898.25262;122.09755322574074;0.002807886188688817
Forex;IBKR;Forex;;SGD;0.768154943;;-0.77492;;15038.506868;131.286274;0.006121320580625405
Fullerton SGD Cash Fund;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0
Fullerton SGD Cash Fund;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0
HMN;IBKR;Stocks;CAPITALAND ASCOTT TRUST;SGD;0.815;0.505252956;68.0;55.42;42.57114694106;16.179493159120216;1.7328291977286933e-05
O39;IBKR;Stocks;OVERSEA-CHINESE BANKING CORP;SGD;26.61;8.7231;800.0;21288.0;16352.482426584;10991.928519957357;0.006656165276298884
P40U;Moomoo-mom;Stocks;StarhillGbl Reit;SGD;0.53;0.555;1200.0;636.0;494.172;-23.044648290000023;0.00020114930694386924
P40U;Moomoo-me;Stocks;StarhillGbl Reit;SGD;0.53;0.5448;4200.0;2226.0;1729.602;-47.74851125687976;0.0007040225743035423
Z74;Moomoo-mom;Stocks;Singtel;SGD;4.3;4.29;100.0;430.0;334.11;0.7681549429999837;0.00013599717293374806
Z74;Moomoo-me;Stocks;Singtel;SGD;4.3;4.88;100.0;430.0;334.11;-44.552986694000005;0.00013599717293374806
Z77;Moomoo-me;Stocks;Singtel 10;SGD;4.29;4.28;120.0;514.8;399.9996;0.9217859315999803;0.00016281708052626396
1299;IBKR;Stocks;AIA GROUP LTD;HKD;74.0;55.302399867;600.0;44400.0;5665.3411212;1431.4632824491825;0.0023060380598859975
1398;IBKR;Stocks;IND & COMM BK OF CHINA - H;HKD;7.84;5.051969413;6000.0;47040.0;6002.19924192;2134.4789637424965;0.002443153836419759
14;IBKR;Stocks;HYSAN DEVELOPMENT CO;HKD;15.11;28.5525695;1000.0;15110.0;1928.00235003;-1715.2419315977236;0.0007847800694792211
16;IBKR;Stocks;SUN HUNG KAI PROPERTIES;HKD;106.7;88.851861372;4500.0;480150.0;61266.07070595;10248.22233507634;0.024937931857077964
1810;IBKR;Stocks;XIAOMI CORP-CLASS B;HKD;26.6;38.85377485;600.0;15960.0;2036.46045708;-938.1326290220454;0.0008289271944995611
1883;IBKR;Stocks;CITIC TELECOM INTERNATIONAL;HKD;2.335;2.45452615;10000.0;23350.0;2979.40799955;-152.51270555263952;0.0012127474932058116
1972;IBKR;Stocks;SWIRE PROPERTIES LTD;HKD;25.36;19.720971466;5600.0;142016.0;18120.925330367998;4029.353903802385;0.00737599777281013
200;IBKR;Stocks;MELCO INTERNATIONAL DEVELOP.;HKD;1.94;11.460065577;4000.0;7760.0;990.15871848;-4858.95666575664;0.00040303728253863374
215;IBKR;Stocks;HUTCHISON TELECOMM HONG KONG;HKD;1.08;1.21222067;50000.0;54000.0;6890.279742;-843.5531518283951;0.0028046408836451316
2318;IBKR;Stocks;PING AN INSURANCE GROUP CO-H;HKD;50.25;86.4593701;500.0;25125.0;3205.894046625;-2310.1174932463937;0.0013049370778071099
27;IBKR;Stocks;GALAXY ENTERTAINMENT GROUP L;HKD;32.84;51.5945905;1000.0;32840.0;4190.31086532;-2393.0439813269563;0.0017056371596093727
2800;IBKR;Stocks;TRACKER FUND OF HONG KONG-B;HKD;22.4;21.769917967;1500.0;33600.0;4287.2851728;120.59559632716822;0.0017451098831569706
2828;IBKR;Stocks;HANGSENG CHINA ENT INDEX;HKD;77.0;92.44076825;400.0;30800.0;3930.0114083999997;-788.0830568436431;0.0015996840595605564
2888;IBKR;Stocks;STANDARD CHARTERED PLC;HKD;195.5;55.6;28.0;5474.0;698.470209402;499.82599639560004;0.0002843074851309898
3188;IBKR;Stocks;CHINAAMC ETF SERIES - CH;HKD;47.98;26.37987258;1000.0;47980.0;6122.141148539999;2756.1281553082354;0.002491975362912841
3690;IBKR;Stocks;MEITUAN-CLASS B;HKD;62.7;231.697888617;60.0;3762.0;480.022822026;-1293.825253753875;0.00019538998156061084
386;IBKR;Stocks;CHINA PETROLEUM & CHEMICAL-H;HKD;4.455;5.815174;2000.0;8910.0;1136.89615743;-347.1103465850039;0.0004627657458014467
390;IBKR;Stocks;CHINA RAILWAY GROUP LTD-H;HKD;2.62;3.92724184;10000.0;26200.0;3343.0616526;-1668.0114755642232;0.0013607702065093047
3918;IBKR;Stocks;NAGACORP LTD;HKD;1.94;7.594013956;2036.0;3949.84;503.99078770632;-1468.8510038077145;0.00020514597681216458
3968;IBKR;Stocks;CHINA MERCHANTS BANK - H;HKD;53.35;39.7833745;500.0;26675.0;3403.670594775;865.5356004625057;0.0013854406587265535
5;IBKR;Stocks;HSBC HOLDINGS PLC;HKD;128.4;49.092166333;2400.0;308160.0;39320.52972768;24286.8070976727;0.016005150642668216
66;IBKR;Stocks;MTR CORP;HKD;32.3;40.07708;500.0;16150.0;2060.70403395;-496.16904422142034;0.0008387953753864607
700;IBKR;Stocks;TENCENT HOLDINGS LTD;HKD;341.0;452.856419642;706.0;240746.0;30718.653458658;-10076.476809703294;0.01250381618840798
808;IBKR;Stocks;PROSPERITY REIT;HKD;1.34;2.339531045;20000.0;26800.0;3419.6203164;-2550.758707727256;0.0013919328829942504
857;IBKR;Stocks;PETROCHINA CO LTD-H;HKD;9.775;7.7972334;2000.0;19550.0;2494.53646215;504.7172273475637;0.0010153838754678208
939;IBKR;Stocks;CHINA CONSTRUCTION BANK-H;HKD;10.43;6.39177626;4000.0;41720.0;5323.37908956;2061.0734243989236;0.002166844771586572
9618;IBKR;Stocks;JD.COM INC-CLASS A;HKD;100.8;285.565311947;19.0;1915.2;244.3752548496;-447.9372043096623;9.947126333994734e-05
CASH - Citic;Citic;Forex;;HKD;0.128244528;;-0.1276;;56493.2093;-285.35623200400005;0.022995171514634476
CASH - StanChart;StanChart;Forex;;HKD;0.128244528;;-0.1276;;1614.1055479999998;-8.153105177440239;0.000657010539477053
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;127600.0;-644.5280000000057;0.05193870062657883
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;1276.0;-6.445279999999912;0.0005193870062657883
CASH - StanChart Payroll;StanChart Payroll;Forex;;HKD;0.128244528;;-0.1276;;14971.866888;-75.62529327264019;0.006094195236025239
Forex;IBKR;Forex;;HKD;0.127597773;;-0.1276;;5049.645289;0.088123;0.0020554233145437706
BARCl;IBKR;Stocks;;GBP;4.015;3.5479;1000.0;4015.0;5334.162899450001;620.5697360729998;0.002171234247084148
Forex;IBKR;Forex;;GBP;1.32855863;;-1.3108;;3950.844791;-53.52578;0.001608164144371688
IMB 18DEC26 30 C;IBKR;Equity and Index Options;;GBP;4.69;0.8986;-1.0;-4690.0;-6230.939974700001;-5037.097189782001;-0.002536261175298793
IMBl;IBKR;Stocks;IMPERIAL BRANDS PLC;GBP;34.32;29.95515;1000.0;34320.0;45596.132181600005;5798.959136155501;0.018559591372335732
RR. 15JAN27 11 P;IBKR;Equity and Index Options;;GBP;0.095;0.4636;-1.0;-95.0;-126.21306985000001;489.7067110180001;-5.137416026724634e-05
RRl;IBKR;Stocks;ROLLS-ROYCE HOLDINGS PLC;GBP;12.944;11.090386;100.0;1294.4;1719.6862906720003;246.26348763888205;0.0006999864531570913
Forex;IBKR;Forex;;EUR;1.0969;;-1.0969;;1317.19833;0.0;0.0005361565025681786
MC;IBKR;Stocks;LVMH MOET HENNESSY LOUIS VUI;EUR;401.05;397.5114;110.0;44115.5;48390.29195;426.9639374000037;0.01969693484971628
MC 18JUN27 415 P;IBKR;Equity and Index Options;;EUR;46.2375;45.9636;-1.0;-4623.75;-5071.791375;-30.044090999999735;-0.0020644377262271914
RI;IBKR;Stocks;PERNOD-RICARD SA;EUR;70.94;83.7114;100.0;7094.0;7781.4086;-1400.8948659999999;0.003167368743953651
RI.DRTS;IBKR;Stocks;PERNOD-RICARD SA - DIVIDEND RIGHTS;EUR;2.35;0.0;100.0;235.0;257.7715;257.7715;0.00010492411260630223
RMS;IBKR;Stocks;HERMES INTL;EUR;1272.5;1581.624621304;23.0;29267.5;32103.52075;-7798.812333492223;0.013067516875340215
RMS 17DEC27 1520 P;IBKR;Equity and Index Options;;EUR;312.8217;193.044;5.0;15641.09;17156.711621;6569.2079565;0.006983521227426841
RMS 17DEC27 1680 P;IBKR;Equity and Index Options;;EUR;439.2292;214.886;-5.0;-21961.46;-24089.525474;-12304.102804;-0.00980547532782469
RMS 17DEC27 1920 P;IBKR;Equity and Index Options;;EUR;653.1463;388.7186;-1.0;-65314.63;-71643.61764699999;-29005.074413;-0.029162040821101978
RMS 18DEC26 1520 P;IBKR;Equity and Index Options;;EUR;247.5943;130.209;5.0;12379.72;13579.314868;6437.9967785;0.005527366533253157
RMS 18DEC26 1600 P;IBKR;Equity and Index Options;;EUR;327.5;110.436;-3.0;-9825.0;-10777.0425;-7142.925048;-0.004386720878114551
RMS 18JUN27 1360 P;IBKR;Equity and Index Options;;EUR;176.366;108.0235;-10.0;-17636.6;-19345.586539999997;-7496.488825000002;-0.007874487678265147
RMS 18JUN27 1440 P;IBKR;Equity and Index Options;;EUR;229.399;146.389;5.0;11469.95;12581.388155;4552.6834499999995;0.0051211673420793885
SAP;IBKR;Stocks;SAP SE;EUR;194.14;210.1117;100.0;19414.0;21295.2166;-1751.935773000003;0.00866807115803724
SAP 15JAN27 205 C;IBKR;Equity and Index Options;;EUR;4.8549;4.7083;-1.0;-485.49;-532.533981;-16.080553999999935;-0.00021676428693290925
SAP 19MAR27 185 P;IBKR;Equity and Index Options;;EUR;10.5794;11.0983;-1.0;-1057.94;-1160.454386;56.91814100000004;-0.00047235496038600594
Forex;IBKR;Forex;;CNH;0.148953656;;-0.15012;;977.2812;7.5929;0.0003977955773885871

---

# [pivot_summary] START  
# PORTFOLIO PIVOT SUMMARY

**Report Date :** 2026-11-27  
**Filter      :** Open Positions | Forex Balances | Cash Balances  
**Generated   :** 2026-11-27 20:52:53  

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
BABA        -1204.36       4.0      -1204.36
BHP            -4.68       4.0         -4.68
BRK         -3982.95       2.0      -3982.95
GEHC          973.88       1.0        973.88
GLD           923.78      -1.0        923.78
GOOG         6661.48       2.0       6661.48
IMB         -4690.00       1.0      -6230.94
LULU         -337.08       2.0       -337.08
MC          -4623.75       1.0      -5071.79
MCD          1054.19       0.0       1054.19
META        -5053.84       3.0      -5053.84
MSFT        -1157.30       2.0      -1157.30
MU         -12715.93       1.0     -12715.93
NBIS       -11097.88       2.0     -11097.88
NVDA         -740.00       1.0       -740.00
PDD         -1685.74       6.0      -1685.74
PLTR         -720.70       1.0       -720.70
RMS        -75246.93       4.0     -82538.36
RR.           -95.00       1.0       -126.21
SAP         -1543.43       2.0      -1692.99
SKHY        -2456.45       2.0      -2456.45
SMCI         -128.60       2.0       -128.60
TSM          -882.20       1.0       -882.20
UNH         -6886.41       3.0      -6886.41
```

> **SUBTOTAL [IBKR]**  Amount: `    -125,639.90`  Quantity: `          47.00`  Value in USD: `    -135,101.08`

> **SUBTOTAL [Equity and Index Options]**  Amount: `    -136,007.18`  Quantity: `          49.00`  Value in USD: `    -145,468.36`

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
Forex       80500.53      4.46      37128.54
```

> **SUBTOTAL [IBKR]**  Amount: `      80,500.53`  Quantity: `           4.46`  Value in USD: `      37,128.54`

### UOB

```
             Amount  Quantity  Value in USD
Underlying                                 
Forex       8878.06      0.78       6898.25
```

> **SUBTOTAL [UOB]**  Amount: `       8,878.06`  Quantity: `           0.78`  Value in USD: `       6,898.25`

> **SUBTOTAL [Forex]**  Amount: `   1,913,916.00`  Quantity: `          11.09`  Value in USD: `     451,374.19`

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
1299         44400.00    -600.0       5665.34
1398         47040.00   -6000.0       6002.20
14           15110.00   -1000.0       1928.00
16          480150.00   -4500.0      61266.07
1810         15960.00    -600.0       2036.46
1883         23350.00  -10000.0       2979.41
1972        142016.00   -5600.0      18120.93
200           7760.00   -4000.0        990.16
215          54000.00  -50000.0       6890.28
2318         25125.00    -500.0       3205.89
27           32840.00   -1000.0       4190.31
2800         33600.00   -1500.0       4287.29
2828         30800.00    -400.0       3930.01
2888          5474.00     -28.0        698.47
3188         47980.00   -1000.0       6122.14
3690          3762.00     -60.0        480.02
386           8910.00   -2000.0       1136.90
390          26200.00  -10000.0       3343.06
3918          3949.84   -2036.0        503.99
3968         26675.00    -500.0       3403.67
5           308160.00   -2400.0      39320.53
66           16150.00    -500.0       2060.70
700         240746.00    -706.0      30718.65
808          26800.00  -20000.0       3419.62
857          19550.00   -2000.0       2494.54
939          41720.00   -4000.0       5323.38
9618          1915.20     -19.0        244.38
9CI           2748.00   -1200.0       2110.89
AMZN         73329.00    -300.0      73329.00
BABA         43130.00    -500.0      43130.00
BARCl         4015.00   -1000.0       5334.16
BHP          84924.00   -1200.0      84924.00
BRK          57932.00    -100.0      57932.00
BS6          29260.00   -7000.0      22476.21
C38U           517.26    -222.0        397.34
CGP              0.00     -50.0          0.00
CPRT         36481.50   -1100.0      36481.50
D05          14487.00    -220.0      11128.26
DNOW           331.25     -25.0        331.25
F34          25380.00   -9000.0      19495.77
FAST          5293.00    -100.0       5293.00
FCX          24275.00    -500.0      24275.00
GLD          65490.00    -200.0      65490.00
GOOG         54196.00    -200.0      54196.00
H78           8530.00   -1000.0       8530.00
HMN             55.42     -68.0         42.57
ICE          53343.00    -300.0      53343.00
IMB          34320.00   -1000.0      45596.13
IVV          72001.00    -100.0      72001.00
MC           44115.50    -110.0      48390.29
META        121978.00    -200.0     121978.00
NOV           5115.00    -300.0       5115.00
NVO           8194.00    -200.0       8194.00
O39          21288.00    -800.0      16352.48
PDD           8742.00    -100.0       8742.00
RI            7094.00    -100.0       7781.41
RI.DRTS        235.00    -100.0        257.77
RMS          29267.50     -23.0      32103.52
RRl           1294.40    -100.0       1719.69
SAP          19414.00    -100.0      21295.22
SOUHY         4239.00    -300.0       4239.00
TSM         102702.00    -300.0     102702.00
UNH          69238.00    -200.0      69238.00
```

> **SUBTOTAL [IBKR]**  Amount: `   2,863,097.87`  Quantity: `    -159,267.00`  Value in USD: `   1,354,707.87`

> **SUBTOTAL [Stocks]**  Amount: `   2,923,992.61`  Quantity: `    -165,298.91`  Value in USD: `   1,414,470.48`

---

## Treasury Bills

### IBKR

```
               Amount  Quantity  Value in USD
Underlying                                   
912797UE5    98357.91 -100000.0      98357.91
912797UX3   105849.31 -108000.0     105849.31
912797VF1   257913.63 -264000.0     257913.63
912797VQ7   177226.97 -182000.0     177226.97
912797VR5    97018.12 -100000.0      97018.12
```

> **SUBTOTAL [IBKR]**  Amount: `     736,365.94`  Quantity: `    -754,000.00`  Value in USD: `     736,365.94`

> **SUBTOTAL [Treasury Bills]**  Amount: `     736,365.94`  Quantity: `    -754,000.00`  Value in USD: `     736,365.94`

---

> ### GRAND TOTAL  
> Amount: `   5,438,267.37`  Quantity: `    -919,238.82`  Value in USD: `   2,456,742.25`

---

