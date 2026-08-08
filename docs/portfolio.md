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

- Current refresh timestamp: **2026-08-08 12:51:38**.


# [transaction_details] START  
Status;Table;Currency;Settle Date;Ticker;Symbol;Asset Category;Transaction Price;Amount;Quantity;Value in USD;Comm/Fee;Code
Open;Trades;EUR;2026-03-04;RI;RI;Stocks;93.0;-9300.0;100.0;-10785.3536943;0.0;"A;O"
Open;Trades;EUR;2026-02-16;SAP;SAP;Stocks;220.0;-22000.0;100.0;-25513.739922;0.0;"A;O"
Open;Trades;HKD;2026-04-01;1810;1810;Stocks;31.8;-6360.0;200.0;-813.22778544;-25.18126;O
Open;Trades;HKD;2026-04-10;1810;1810;Stocks;31.0;-6200.0;200.0;-792.7692248000001;-25.1767;O
Open;Trades;HKD;2026-04-10;700;700;Stocks;502.0;-50200.0;100.0;-6418.8734008;-92.5907;O
Open;Trades;USD;2026-02-23;CPRT;CPRT;Stocks;45.0;-13500.0;300.0;-13500.0;0.0;"A;O"
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
Open;Trades;HKD;2026-05-14;16;16 29JUN26 150 C;Equity and Index Options;3.86;15440.0;-4.0;1974.25110176;-42.88;O
Open;Trades;HKD;2026-06-29;16;16 29JUN26 150 C;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-08-03;AMZN;AMZN 18SEP26 300 C;Equity and Index Options;8.03;803.0;-1.0;803.0;-1.0601118;O
Open;Trades;USD;2026-07-22;BHP;BHP 18DEC26 95 C;Equity and Index Options;4.0;1600.0;-4.0;1600.0;0.20468;O
Open;Trades;USD;2026-07-24;BRK;BRK B 28AUG26 505 C;Equity and Index Options;6.95;695.0;-1.0;695.0;-0.797667;O
Open;Trades;USD;2026-07-31;BRK;BRK B 28AUG26 505 C;Equity and Index Options;14.37;-1437.0;1.0;-1437.0;-0.68947;C
Open;Trades;USD;2026-07-31;BRK;BRK B 18SEP26 520 C;Equity and Index Options;9.38;938.0;-1.0;938.0;-0.7120828;O
Open;Trades;USD;2026-07-31;BRK;BRK B 18SEP26 495 P;Equity and Index Options;4.99;499.0;-1.0;499.0;-0.7030394;O
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
Open;Transaction Fees;HKD;2026-04-01;1810;1810;Stocks;31.8;-7.18126;0.0;-0.9182390198850401;;
Open;Transaction Fees;HKD;2026-04-10;1810;1810;Stocks;31.0;-7.1767;0.0;-0.9176559509068001;;
Open;Transaction Fees;HKD;2026-04-10;700;700;Stocks;502.0;-52.4307;0.0;-6.7041040959228;;
Open;Withholding Tax;EUR;2026-02-18;RMS;RMS;;;-3.72;;-4.314141477720001;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-11.22604556568;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;9.68;;11.22604556568;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-11.22604556568;;
Open;Withholding Tax;EUR;2026-04-30;MC;MC;;;-18.75;;-21.744664706250003;;
Open;Withholding Tax;EUR;2026-05-08;SAP;SAP;;;-61.32;;-71.11375145532;;
Open;Withholding Tax;EUR;2026-07-24;RI;RI;;;-58.75;;-68.13328274625;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.58082398228;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;90.57;;11.58082398228;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.58082398228;;
Open;Withholding Tax;HKD;2026-01-26;939;939;;;-81.86;;-10.467111087440001;;
Open;Withholding Tax;HKD;2026-02-09;3968;3968;;;-33.22;;-4.24770865288;;
Open;Withholding Tax;HKD;2026-06-16;1398;1398;;;-116.65;;-14.915569366600002;;
Open;Withholding Tax;HKD;2026-06-26;386;386;;;-25.78;;-3.29638558312;;
Open;Withholding Tax;HKD;2026-07-15;2318;2318;;;-100.56;;-12.858205362240001;;
Open;Withholding Tax;HKD;2026-07-31;857;857;;;-22.27;;-2.84757590908;;
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
Open;Dividends;HKD;2026-01-26;1398;1398;;;931.62;;119.12252664648001;;
Open;Dividends;HKD;2026-01-26;939;939;;;818.62;;104.67366819448;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;220.7;;28.2200270828;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;333.34;;42.62285377336;;
Open;Dividends;HKD;2026-03-19;16;16;;;4410.0;;563.88907764;;
Open;Dividends;HKD;2026-03-27;14;14;;;810.0;;103.57146324;;
Open;Dividends;HKD;2026-04-23;808;808;;;1142.0;;146.02297656800002;;
Open;Dividends;HKD;2026-04-30;5;5;;;8148.56;;1041.92380555424;;
Open;Dividends;HKD;2026-04-30;5;5;;;306.5;;39.190930226;;
Open;Dividends;HKD;2026-05-07;1972;1972;;;4480.0;;572.83969792;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;3.84;;0.49100545536;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;103.61;;13.24819667444;;
Open;Dividends;HKD;2026-05-29;215;215;;;2605.0;;333.09094042000004;;
Open;Dividends;HKD;2026-05-29;2800;2800;;;285.0;;36.44181114;;
Open;Dividends;HKD;2026-06-01;700;700;;;3741.8;;478.44901376720003;;
Open;Dividends;HKD;2026-06-12;1299;1299;;;864.48;;110.53760313792;;
Open;Dividends;HKD;2026-06-12;27;27;;;800.0;;102.29280320000001;;
Open;Dividends;HKD;2026-06-16;1398;1398;;;1166.46;;149.15057902584002;;
Open;Dividends;HKD;2026-06-22;66;66;;;439.66;;56.21756731864001;;
Open;Dividends;HKD;2026-06-22;66;66;;;5.34;;0.6828044613600001;;
Open;Dividends;HKD;2026-06-25;1883;1883;;;1300.0;;166.2258052;;
Open;Dividends;HKD;2026-06-26;386;386;;;257.8;;32.9638558312;;
Open;Dividends;HKD;2026-06-26;5;5;;;1661.92;;212.50306936768;;
Open;Dividends;HKD;2026-06-26;5;5;;;217.73;;27.84026505092;;
Open;Dividends;HKD;2026-06-30;2828;2828;;;280.0;;35.80248112;;
Open;Dividends;HKD;2026-07-15;2318;2318;;;1005.64;;128.58716826256;;
Open;Dividends;HKD;2026-07-31;857;857;;;222.66;;28.470644450640002;;
Open;Dividends;HKD;2026-07-31;857;857;;;316.75;;40.501556767000004;;
Open;Dividends;HKD;2026-08-07;3918;3918;;;172.04;;21.99806732816;;
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
Open;Trades;HKD;2025-03-26;1810;1810;Stocks;53.35;-10670.0;200.0;-1364.33026268;-31.90695;O
Open;Trades;HKD;2025-06-30;2888;2888;Stocks;134.1;13410.0;-100.0;1714.68311364;-33.70307;C
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
Open;Transaction Fees;HKD;2025-03-26;1810;1810;Stocks;53.35;-11.304095;0.0;-1.4454094564863802;;
Open;Transaction Fees;HKD;2025-06-30;2888;2888;Stocks;134.1;-14.382185;0.0;-1.83899252473874;;
Open;Withholding Tax;EUR;2025-12-04;MC;MC;;;-13.75;;-15.946087451250001;;
Open;Withholding Tax;HKD;2025-01-24;1398;1398;;;-93.17;;-11.913275592680002;;
Open;Withholding Tax;HKD;2025-01-27;939;939;;;-85.25;;-10.900576841000001;;
Open;Withholding Tax;HKD;2025-05-28;939;939;;;-88.71;;-11.34299321484;;
Open;Withholding Tax;HKD;2025-06-27;386;386;;;-30.46;;-3.89479848184;;
Open;Withholding Tax;HKD;2025-06-30;2318;2318;;;-88.13;;-11.26883093252;;
Open;Withholding Tax;HKD;2025-07-24;857;857;;;-54.54;;-6.97381185816;;
Open;Withholding Tax;HKD;2025-07-30;3968;3968;;;-94.82;;-12.12425449928;;
Open;Withholding Tax;HKD;2025-07-31;390;390;;;-194.8;;-24.908297579200003;;
Open;Withholding Tax;HKD;2025-08-22;1398;1398;;;-108.23;;-13.83893761292;;
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
Open;Dividends;HKD;2025-01-24;1398;1398;;;931.74;;119.13787056696;;
Open;Dividends;HKD;2025-01-27;939;939;;;852.52;;109.00832573008;;
Open;Dividends;HKD;2025-03-19;14;14;;;810.0;;103.57146324;;
Open;Dividends;HKD;2025-03-20;16;16;;;4275.0;;546.6271671000001;;
Open;Dividends;HKD;2025-04-23;808;808;;;1120.0;;143.20992448;;
Open;Dividends;HKD;2025-04-25;5;5;;;6698.42;;856.5001985136801;;
Open;Dividends;HKD;2025-05-08;1972;1972;;;4256.0;;544.197713024;;
Open;Dividends;HKD;2025-05-19;2888;2888;;;277.72;;35.51094663088001;;
Open;Dividends;HKD;2025-05-28;939;939;;;887.1;;113.42993214840001;;
Open;Dividends;HKD;2025-05-29;215;215;;;2605.0;;333.09094042000004;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;319.22;;40.817385796880004;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;10.78;;1.37839552312;;
Open;Dividends;HKD;2025-05-30;700;700;;;2727.0;;348.69059290800004;;
Open;Dividends;HKD;2025-06-12;1299;1299;;;785.88;;100.48733522352;;
Open;Dividends;HKD;2025-06-12;27;27;;;500.0;;63.933002;;
Open;Dividends;HKD;2025-06-13;66;66;;;445.0;;56.90037178;;
Open;Dividends;HKD;2025-06-20;1883;1883;;;1280.0;;163.66848512;;
Open;Dividends;HKD;2025-06-20;5;5;;;1881.53;;240.58372250612;;
Open;Dividends;HKD;2025-06-27;386;386;;;304.6;;38.9479848184;;
Open;Dividends;HKD;2025-06-30;2318;2318;;;881.33;;112.69214530532001;;
Open;Dividends;HKD;2025-07-24;857;857;;;545.4;;69.7381185816;;
Open;Dividends;HKD;2025-07-30;2828;2828;;;520.0;;66.49032208;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;133.58;;17.080340814320003;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;961.35;;122.92398294540001;;
Open;Dividends;HKD;2025-07-31;390;390;;;1948.0;;249.082975792;;
Open;Dividends;HKD;2025-08-22;1398;1398;;;1082.3;;138.3893761292;;
Open;Dividends;HKD;2025-09-09;14;14;;;270.0;;34.523821080000005;;
Open;Dividends;HKD;2025-09-16;66;66;;;36.12;;4.61852006448;;
Open;Dividends;HKD;2025-09-16;66;66;;;173.88;;22.233340775520002;;
Open;Dividends;HKD;2025-09-18;808;808;;;1170.0;;149.60322468;;
Open;Dividends;HKD;2025-09-26;5;5;;;1866.43;;238.65294584572;;
Open;Dividends;HKD;2025-09-30;2888;2888;;;26.82;;3.42936622728;;
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
Open;Trades;HKD;2024-03-24;1299;1299;Stocks;55.2;-33120.0;600.0;-4234.92205248;-61.43992;O
Open;Trades;HKD;2024-12-27;3968;3968;Stocks;39.7;-19850.0;500.0;-2538.1401794000003;-41.68725;O
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
Open;Transaction Fees;HKD;2024-03-24;1299;1299;Stocks;55.2;-34.94392;0.0;-4.46813941449568;;
Open;Transaction Fees;HKD;2024-12-27;3968;3968;Stocks;39.7;-20.565725;0.0;-2.6296570751129003;;
Open;Withholding Tax;HKD;2024-07-26;2318;2318;;;-82.34;;-10.52848676936;;
Open;Withholding Tax;HKD;2024-07-26;386;386;;;-43.88;;-5.610760255520001;;
Open;Withholding Tax;HKD;2024-07-29;857;857;;;-50.57;;-6.4661838222800005;;
Open;Withholding Tax;HKD;2024-08-02;939;939;;;-175.36;;-22.42258246144;;
Open;Withholding Tax;HKD;2024-08-08;390;390;;;-230.1;;-29.4219675204;;
Open;Withholding Tax;HKD;2024-08-19;1398;1398;;;-201.43;;-25.756049185720002;;
Open;Withholding Tax;HKD;2024-09-26;386;386;;;-31.86;;-4.0738108874400005;;
Open;Withholding Tax;HKD;2024-10-18;2318;2318;;;-51.02;;-6.523723524080001;;
Open;Withholding Tax;HKD;2024-10-28;857;857;;;-48.07;;-6.14651881228;;
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
Open;Dividends;HKD;2024-03-20;16;16;;;4275.0;;546.6271671000001;;
Open;Dividends;HKD;2024-03-22;14;14;;;810.0;;103.57146324;;
Open;Dividends;HKD;2024-04-17;808;808;;;1328.0;;169.80605331200002;;
Open;Dividends;HKD;2024-04-25;5;5;;;5119.61;;654.62407273844;;
Open;Dividends;HKD;2024-04-25;5;5;;;703.64;;89.97163505456;;
Open;Dividends;HKD;2024-04-26;27;27;;;300.0;;38.3598012;;
Open;Dividends;HKD;2024-05-02;1972;1972;;;4032.0;;515.555728128;;
Open;Dividends;HKD;2024-05-17;2888;2888;;;210.1;;26.8646474404;;
Open;Dividends;HKD;2024-05-27;215;215;;;2605.0;;333.09094042000004;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;41.55;;5.3128324662;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;183.45;;23.4570184338;;
Open;Dividends;HKD;2024-05-31;700;700;;;2060.4;;263.4551146416;;
Open;Dividends;HKD;2024-06-13;1883;1883;;;1930.0;;246.78138772;;
Open;Dividends;HKD;2024-06-14;1299;1299;;;714.42;;91.35003057768;;
Open;Dividends;HKD;2024-06-21;5;5;;;5808.32;;742.68666835328;;
Open;Dividends;HKD;2024-07-16;66;66;;;16.91;;2.16221412764;;
Open;Dividends;HKD;2024-07-16;66;66;;;428.09;;54.73815765236;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;741.05;;94.7551022642;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;823.39;;105.28358903356;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;-741.05;;-94.7551022642;;
Open;Dividends;HKD;2024-07-26;386;386;;;438.8;;56.1076025552;;
Open;Dividends;HKD;2024-07-29;857;857;;;505.74;;64.66695286296;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;36.4;;4.6543225456;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;223.6;;28.5908384944;;
Open;Dividends;HKD;2024-08-02;939;939;;;1753.57;;224.22198863428;;
Open;Dividends;HKD;2024-08-08;390;390;;;2301.0;;294.219675204;;
Open;Dividends;HKD;2024-08-19;1398;1398;;;2014.29;;257.55921319716003;;
Open;Dividends;HKD;2024-09-09;215;215;;;1140.0;;145.76724456;;
Open;Dividends;HKD;2024-09-16;808;808;;;1276.0;;163.157021104;;
Open;Dividends;HKD;2024-09-17;66;66;;;72.66;;9.29074385064;;
Open;Dividends;HKD;2024-09-17;66;66;;;137.34;;17.561116989360002;;
Open;Dividends;HKD;2024-09-20;14;14;;;270.0;;34.523821080000005;;
Open;Dividends;HKD;2024-09-25;1299;1299;;;267.0;;34.140223068000005;;
Open;Dividends;HKD;2024-09-26;386;386;;;318.6;;40.738108874400005;;
Open;Dividends;HKD;2024-09-27;1883;1883;;;600.0;;76.7196024;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;21.85;;2.7938721874000003;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;358.15;;45.7952093326;;
Open;Dividends;HKD;2024-09-27;5;5;;;1869.78;;239.08129695912;;
Open;Dividends;HKD;2024-10-09;1972;1972;;;1904.0;;243.456871616;;
Open;Dividends;HKD;2024-10-10;2888;2888;;;89.69;;11.46830189876;;
Open;Dividends;HKD;2024-10-18;2318;2318;;;510.24;;65.24234988096;;
Open;Dividends;HKD;2024-10-25;27;27;;;500.0;;63.933002;;
Open;Dividends;HKD;2024-10-28;857;857;;;480.66;;61.460073482640006;;
Open;Dividends;HKD;2024-11-21;16;16;;;12325.6;;1576.0252189024002;;
Open;Dividends;HKD;2024-11-21;16;16;;;274.4;;35.086431497599996;;
Open;Dividends;HKD;2024-11-29;2800;2800;;;930.0;;118.91538372000001;;
Open;Dividends;HKD;2024-12-19;5;5;;;1865.55;;238.5404237622;;
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
Open;Trades;HKD;2023-04-25;3918;3918;Stocks;6.1031;4.96487185;-0.8135;0.6348383238315874;0.0;C
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
Open;Withholding Tax;HKD;2023-06-30;386;386;;;-43.34;;-5.5417126133600005;;
Open;Withholding Tax;HKD;2023-07-10;2318;2318;;;-85.01;;-10.86988900004;;
Open;Withholding Tax;HKD;2023-08-04;939;939;;;-168.79;;-21.58250281516;;
Open;Withholding Tax;HKD;2023-08-10;390;390;;;-217.64;;-27.828757110559998;;
Open;Withholding Tax;HKD;2023-08-18;1398;1398;;;-197.54;;-25.25865043016;;
Open;Withholding Tax;HKD;2023-09-28;386;386;;;-31.56;;-4.03545108624;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.37539895944;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;49.86;;6.37539895944;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.37539895944;;
Open;Withholding Tax;HKD;2023-10-30;857;857;;;-0.87;;-0.11124342348;;
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
Open;Dividends;HKD;2023-03-16;16;16;;;5571.25;;712.373474785;;
Open;Dividends;HKD;2023-03-16;16;16;;;53.75;;6.872797715;;
Open;Dividends;HKD;2023-03-20;14;14;;;38.61;;4.93690641444;;
Open;Dividends;HKD;2023-03-20;14;14;;;1131.39;;144.66631826556002;;
Open;Dividends;HKD;2023-04-11;808;808;;;1500.0;;191.79900600000002;;
Open;Dividends;HKD;2023-04-27;5;5;;;4330.56;;553.7314022822401;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;3394.56;;434.04882253824;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;413.44;;52.86492069376;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;1.1;;0.14065260440000002;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;139.46;;17.83219291784;;
Open;Dividends;HKD;2023-05-29;215;215;;;2605.0;;333.09094042000004;;
Open;Dividends;HKD;2023-05-31;2800;2800;;;150.0;;19.1799006;;
Open;Dividends;HKD;2023-06-05;700;700;;;1454.4;;185.9683162176;;
Open;Dividends;HKD;2023-06-14;1883;1883;;;1850.0;;236.5521074;;
Open;Dividends;HKD;2023-06-23;5;5;;;1879.72;;240.35228503888;;
Open;Dividends;HKD;2023-06-30;386;386;;;433.4;;55.4171261336;;
Open;Dividends;HKD;2023-07-10;2318;2318;;;850.13;;108.70272598052;;
Open;Dividends;HKD;2023-07-18;66;66;;;445.0;;56.90037178;;
Open;Dividends;HKD;2023-07-28;2828;2828;;;80.0;;10.22928032;;
Open;Dividends;HKD;2023-07-28;857;857;;;485.54;;62.08405958216;;
Open;Dividends;HKD;2023-08-04;939;939;;;1687.94;;215.83014279176;;
Open;Dividends;HKD;2023-08-10;390;390;;;2176.4;;278.2875711056;;
Open;Dividends;HKD;2023-08-18;1398;1398;;;1975.41;;252.58778296164002;;
Open;Dividends;HKD;2023-09-04;215;215;;;1140.0;;145.76724456;;
Open;Dividends;HKD;2023-09-05;14;14;;;270.0;;34.523821080000005;;
Open;Dividends;HKD;2023-09-14;808;808;;;1490.0;;190.52034596000001;;
Open;Dividends;HKD;2023-09-21;5;5;;;1879.68;;240.34717039872;;
Open;Dividends;HKD;2023-09-28;2828;2828;;;568.0;;72.627890272;;
Open;Dividends;HKD;2023-09-28;386;386;;;315.6;;40.354510862400005;;
Open;Dividends;HKD;2023-09-29;1883;1883;;;600.0;;76.7196024;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;645.48;;82.53494826192001;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;1202.52;;153.76142713008;;
Open;Dividends;HKD;2023-10-13;2888;2888;;;60.04;;7.67707488016;;
Open;Dividends;HKD;2023-10-13;66;66;;;210.0;;26.85186084;;
Open;Dividends;HKD;2023-10-25;2318;2318;;;505.93;;64.69124740372;;
Open;Dividends;HKD;2023-10-27;27;27;;;200.0;;25.573200800000002;;
Open;Dividends;HKD;2023-10-30;857;857;;;8.7;;1.1124342348;;
Open;Dividends;HKD;2023-10-30;857;857;;;449.44;;57.46809683776;;
Open;Dividends;HKD;2023-11-16;16;16;;;16650.0;;2128.9689666;;
Open;Dividends;HKD;2023-11-30;2800;2800;;;825.0;;105.48945330000001;;
Open;Dividends;HKD;2023-12-21;5;5;;;1872.67;;239.45082971068;;
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
Open;Trades;HKD;2022-10-31;16;16;Stocks;85.0;-255000.0;3000.0;-32605.83102;-543.2675;"O;P"
Open;Trades;HKD;2022-01-18;200;200;Stocks;9.2;-18400.0;2000.0;-2352.7344736;-42.5244;O
Open;Trades;HKD;2022-10-31;2800;2800;Stocks;24.0;-24000.0;1000.0;-3068.7840960000003;-23.584;"A;O"
Open;Trades;HKD;2022-09-13;3918;3918;Stocks;6.0527;5.74280176;-0.9488;0.734309112815367;0.0;C
Open;Trades;HKD;2022-06-29;700;700;Stocks;485.3;-99971.8;206.0;-12782.994578687201;-220.3252263;"A;O"
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;-0.0476;0.0;0.0;O
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;0.0476;0.0;0.0;Ca
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;221.0;10.5196;-0.0476;1.3450992156784;0.0;C
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
Open;Transaction Fees;HKD;2022-01-18;200;200;Stocks;9.2;-24.5244;0.0;-3.1358370284976003;;
Open;Transaction Fees;HKD;2022-06-29;700;700;Stocks;485.3;-132.8491963;0.0;-16.986895865492585;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-225.845;0.0;-28.87789767338;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-113.4225;0.0;-14.502881838690001;;
Open;Transaction Fees;HKD;2022-10-31;2800;2800;Stocks;24.0;-0.684;0.0;-0.087460346736;;
Open;Withholding Tax;HKD;2022-06-20;2318;2318;;;-90.17;;-11.529677580680001;;
Open;Withholding Tax;HKD;2022-06-23;386;386;;;-72.32;;-9.24726940928;;
Open;Withholding Tax;HKD;2022-07-27;1398;1398;;;-205.93;;-26.331446203720002;;
Open;Withholding Tax;HKD;2022-07-29;857;857;;;-22.62;;-2.89232901048;;
Open;Withholding Tax;HKD;2022-07-29;939;939;;;-170.38;;-21.78580976152;;
Open;Withholding Tax;HKD;2022-08-09;390;390;;;-229.56;;-29.35291987824;;
Open;Withholding Tax;HKD;2022-09-29;386;386;;;-37.02;;-4.73359946808;;
Open;Withholding Tax;HKD;2022-10-20;2318;2318;;;-42.38;;-5.418961249520001;;
Open;Withholding Tax;HKD;2022-10-28;857;857;;;-46.68;;-5.968785066720001;;
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
Open;Dividends;HKD;2022-03-17;16;16;;;1875.0;;239.7487575;;
Open;Dividends;HKD;2022-03-25;14;14;;;1170.0;;149.60322468;;
Open;Dividends;HKD;2022-04-11;808;808;;;1724.0;;220.44099089600002;;
Open;Dividends;HKD;2022-04-28;5;5;;;3388.17;;433.23175877268005;;
Open;Dividends;HKD;2022-04-29;27;27;;;300.0;;38.3598012;;
Open;Dividends;HKD;2022-05-05;1972;1972;;;3584.0;;458.271758336;;
Open;Dividends;HKD;2022-05-12;2888;2888;;;90.34;;11.551414801360002;;
Open;Dividends;HKD;2022-06-06;700;700;;;640.0;;81.83424256;;
Open;Dividends;HKD;2022-06-20;2318;2318;;;901.7;;115.29677580680001;;
Open;Dividends;HKD;2022-06-23;386;386;;;723.2;;92.47269409280001;;
Open;Dividends;HKD;2022-07-19;66;66;;;510.0;;65.21166204000001;;
Open;Dividends;HKD;2022-07-27;1398;1398;;;2059.31;;263.31574069724;;
Open;Dividends;HKD;2022-07-29;857;857;;;226.22;;28.92584742488;;
Open;Dividends;HKD;2022-07-29;939;939;;;1703.8;;217.8580976152;;
Open;Dividends;HKD;2022-08-09;390;390;;;2295.6;;293.5291987824;;
Open;Dividends;HKD;2022-09-08;808;808;;;1750.0;;223.765507;;
Open;Dividends;HKD;2022-09-16;14;14;;;270.0;;34.523821080000005;;
Open;Dividends;HKD;2022-09-29;386;386;;;370.2;;47.3359946808;;
Open;Dividends;HKD;2022-09-29;5;5;;;1695.13;;216.74949936052002;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;1755.52;;224.47132734208;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;36.48;;4.664551825919999;;
Open;Dividends;HKD;2022-10-14;2888;2888;;;40.18;;5.1376560407200005;;
Open;Dividends;HKD;2022-10-14;66;66;;;210.0;;26.85186084;;
Open;Dividends;HKD;2022-10-20;2318;2318;;;531.06;;67.90452008423999;;
Open;Dividends;HKD;2022-10-28;857;857;;;466.76;;59.68273602704;;
Open;Dividends;HKD;2022-11-17;16;16;;;16650.0;;2128.9689666;;
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
Open;Trades;HKD;2021-09-19;1398;1398;Stocks;4.18;-12540.0;3000.0;-1603.43969016;-35.33858;O
Open;Trades;HKD;2021-05-10;14;14;Stocks;28.5;-28500.0;1000.0;-3644.181114;-52.5695;O
Open;Trades;HKD;2021-09-15;200;200;Stocks;9.33;-9330.0;1000.0;-1192.9898173200002;-31.25191;O
Open;Trades;HKD;2021-04-25;2318;2318;Stocks;86.3;-43150.0;500.0;-5517.4180726;-79.68505;O
Open;Trades;HKD;2021-04-25;390;390;Stocks;3.92;-39200.0;10000.0;-5012.3473568;-72.4184;"O;P"
Open;Trades;HKD;2021-05-11;3918;3918;Stocks;7.8;-15600.0;2000.0;-1994.7096624;-34.4212;O
Open;Trades;HKD;2021-04-20;700;700;Stocks;614.5;-61450.0;100.0;-7857.3659458;-112.81915;O
Open;Trades;HKD;2021-08-15;700;700;Stocks;455.8;-91160.0;200.0;-11656.26492464;-194.38932;O
Open;Trades;HKD;2021-04-26;939;939;Stocks;6.38;-25520.0;4000.0;-3263.14042208;-47.10504;O
Open;Trades;SGD;2021-09-10;C38U;C38U;Stocks;2.0;1.2144;-0.6072;0.9315493926288;0.0;C
Open;Trades;USD;2021-12-20;BABA;BABA;Stocks;230.0;-46000.0;200.0;-46000.0;0.0;"A;O"
Open;Trades;USD;2021-02-09;FCX;FCX;Stocks;31.38;15690.0;-500.0;15690.0;-2.906249;"C;P"
Open;Trades;USD;2021-01-19;MSFT;MSFT;Stocks;213.0;-21300.0;100.0;-21300.0;-1.0;O
Open;Trades;EUR;2021-03-19;MC;MC 19MAR21 360.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-08-19;700;700 30AUG21 450.0 P;Equity and Index Options;32.3;6460.0;-2.0;826.01438584;-24.0;O
Open;Trades;HKD;2021-08-30;700;700 30AUG21 450.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-09-03;700;700 29JUN22 500.0 P;Equity and Index Options;67.08;13416.0;-2.0;1715.450309664;-32.832;O
Open;Trades;HKD;2021-12-06;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.05;1025.0;-1.0;131.0626541;-19.0;O
Open;Trades;HKD;2021-12-20;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.5;1250.0;-1.0;159.832505;-19.0;O
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
Open;Transaction Fees;HKD;2021-04-20;700;700;Stocks;614.5;-63.65915;0.0;-8.1398411285366;;
Open;Transaction Fees;HKD;2021-04-25;2318;2318;Stocks;86.3;-45.16505;0.0;-5.7750744639602;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-24.63504;0.0;-3.14998412318016;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-4.10584;0.0;-0.52499735386336;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-12.31752;0.0;-1.57499206159008;;
Open;Transaction Fees;HKD;2021-04-26;939;939;Stocks;6.38;-26.68904;0.0;-3.41262089539616;;
Open;Transaction Fees;HKD;2021-05-10;14;14;Stocks;28.5;-29.7695;0.0;-3.806507006078;;
Open;Transaction Fees;HKD;2021-05-11;3918;3918;Stocks;7.8;-16.4212;0.0;-2.0997132248848;;
Open;Transaction Fees;HKD;2021-08-15;700;700;Stocks;455.8;-121.46132;0.0;-15.530773628965282;;
Open;Transaction Fees;HKD;2021-09-15;200;200;Stocks;9.33;-13.25191;0.0;-1.69446877706764;;
Open;Transaction Fees;HKD;2021-09-19;1398;1398;Stocks;4.18;-17.33858;0.0;-2.21701493963432;;
Open;Withholding Tax;HKD;2021-06-28;386;386;;;-31.38;;-4.01243520552;;
Open;Withholding Tax;HKD;2021-07-27;1398;1398;;;-95.97;;-12.27130040388;;
Open;Withholding Tax;HKD;2021-07-30;857;857;;;-21.21;;-2.71203794484;;
Open;Withholding Tax;HKD;2021-08-05;390;390;;;-216.71;;-27.70984172684;;
Open;Withholding Tax;HKD;2021-08-05;939;939;;;-156.33;;-19.989292405320004;;
Open;Withholding Tax;HKD;2021-09-30;386;386;;;-38.44;;-4.91516919376;;
Open;Withholding Tax;HKD;2021-10-25;2318;2318;;;-52.84;;-6.756439651360001;;
Open;Withholding Tax;HKD;2021-10-29;857;857;;;-31.31;;-4.00348458524;;
Open;Withholding Tax;USD;2021-03-11;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-05-03;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-06-10;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-08-02;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-09-09;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-11-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-12-09;MSFT;MSFT;;;-18.6;;-18.6;;
Open;Withholding Tax;USD;2021-12-17;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;HKD;2021-03-18;16;16;;;1875.0;;239.7487575;;
Open;Dividends;HKD;2021-04-07;808;808;;;1784.0;;228.11295113600002;;
Open;Dividends;HKD;2021-04-29;5;5;;;2796.85;;357.62203328740003;;
Open;Dividends;HKD;2021-05-06;1972;1972;;;3416.0;;436.790269664;;
Open;Dividends;HKD;2021-05-20;2888;2888;;;89.41;;11.43249941764;;
Open;Dividends;HKD;2021-06-07;700;700;;;320.0;;40.91712128;;
Open;Dividends;HKD;2021-06-28;386;386;;;313.8;;40.124352055200006;;
Open;Dividends;HKD;2021-07-05;3918;3918;;;291.4;;37.2601535656;;
Open;Dividends;HKD;2021-07-20;66;66;;;490.0;;62.654341960000004;;
Open;Dividends;HKD;2021-07-27;1398;1398;;;959.73;;122.71684001892001;;
Open;Dividends;HKD;2021-07-30;857;857;;;212.14;;27.12549408856;;
Open;Dividends;HKD;2021-08-05;390;390;;;2167.1;;277.0984172684;;
Open;Dividends;HKD;2021-08-05;939;939;;;1563.34;;199.89803869336;;
Open;Dividends;HKD;2021-09-06;14;14;;;270.0;;34.523821080000005;;
Open;Dividends;HKD;2021-09-08;808;808;;;1802.0;;230.414539208;;
Open;Dividends;HKD;2021-09-30;386;386;;;384.4;;49.1516919376;;
Open;Dividends;HKD;2021-09-30;5;5;;;1308.18;;167.27174911272002;;
Open;Dividends;HKD;2021-10-05;1972;1972;;;1736.0;;221.97538294400002;;
Open;Dividends;HKD;2021-10-19;66;66;;;125.0;;15.9832505;;
Open;Dividends;HKD;2021-10-22;2888;2888;;;29.9;;3.8231935195999998;;
Open;Dividends;HKD;2021-10-25;2318;2318;;;528.4;;67.5643965136;;
Open;Dividends;HKD;2021-10-29;857;857;;;313.06;;40.02973121224;;
Open;Dividends;HKD;2021-11-18;16;16;;;5550.0;;709.6563222;;
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
Open;Trades;HKD;2020-03-18;16;16;Stocks;97.5;-48750.0;500.0;-6233.467695;-89.31625;O
Open;Trades;HKD;2020-03-18;16;16;Stocks;96.5;-48250.0;500.0;-6169.5346930000005;-88.90275;O
Open;Trades;HKD;2020-07-20;16;16;Stocks;94.05;-47025.0;500.0;-6012.8988381;-86.889675;O
Open;Trades;HKD;2020-01-29;1972;1972;Stocks;24.65;-24650.0;1000.0;-3151.8969986;-45.38555;O
Open;Trades;HKD;2020-05-04;1972;1972;Stocks;20.9;-20900.0;1000.0;-2672.3994836;-39.5643;O
Open;Trades;HKD;2020-05-24;1972;1972;Stocks;17.78;-35560.0;2000.0;-4546.91510224;-67.40812;"O;P"
Open;Trades;HKD;2020-07-20;1972;1972;Stocks;18.2;-29120.0;1600.0;-3723.4580364800004;-55.08224;"O;P"
Open;Trades;HKD;2020-07-22;27;27;Stocks;51.5;-51500.0;1000.0;-6585.099206;-94.5905;O
Open;Trades;HKD;2020-03-18;2800;2800;Stocks;21.7;-10850.0;500.0;-1387.3461434;-18.29295;O
Open;Trades;HKD;2020-03-31;5;5;Stocks;40.8;-16320.0;400.0;-2086.7731852800002;-35.44064;O
Open;Trades;HKD;2020-05-14;5;5;Stocks;38.0;-45600.0;1200.0;-5830.689782400001;-83.7112;O
Open;Trades;HKD;2020-05-21;66;66;Stocks;40.0;-20000.0;500.0;-2557.32008;-38.54;O
Open;Trades;HKD;2020-09-29;700;700;Stocks;530.0;-53000.0;100.0;-6776.898212;-101.981;"A;O"
Open;Trades;HKD;2020-04-23;808;808;Stocks;2.38;-23800.0;10000.0;-3043.2108952000003;-48.6826;"O;P"
Open;Trades;HKD;2020-07-22;808;808;Stocks;2.29;-22900.0;10000.0;-2928.1314916;-41.9383;O
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
Open;Trades;HKD;2020-07-24;27;27 28AUG20 50.0 P;Equity and Index Options;2.18;4360.0;-2.0;557.49577744;-24.0;O
Open;Trades;HKD;2020-08-28;27;27 28AUG20 50.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2020-07-24;700;700 29SEP20 530.0 P;Equity and Index Options;38.02;3802.0;-1.0;486.146547208;-21.0;O
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
Open;Transaction Fees;HKD;2020-01-29;1972;1972;Stocks;24.65;-25.66555;0.0;-3.2817513189622;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;97.5;-50.31625;0.0;-6.433737823765;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;96.5;-50.30275;0.0;-6.432011632711001;;
Open;Transaction Fees;HKD;2020-03-18;2800;2800;Stocks;21.7;-0.29295;0.0;-0.0374583458718;;
Open;Transaction Fees;HKD;2020-03-31;5;5;Stocks;40.8;-17.44064;0.0;-2.2300649440025597;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39181468141704;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39181468141704;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39181468141704;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39181468141704;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39181468141704;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39181468141704;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39181468141704;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39181468141704;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-5.12852;0.0;-0.65576335883408;;
Open;Transaction Fees;HKD;2020-05-04;1972;1972;Stocks;20.9;-21.5643;0.0;-2.7573408700572;;
Open;Transaction Fees;HKD;2020-05-14;5;5;Stocks;38.0;-47.2312;0.0;-6.0392648081248;;
Open;Transaction Fees;HKD;2020-05-21;66;66;Stocks;40.0;-20.54;0.0;-2.62636772216;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.523740686776048;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.523740686776048;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.9670967431041921;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.9670967431041921;;
Open;Transaction Fees;HKD;2020-07-20;16;16;Stocks;94.05;-49.269675;0.0;-6.2999164606287;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.04806137374624;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.04806137374624;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-4.09828;0.0;-0.52403068687312;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-11.29484;0.0;-1.44422605661936;;
Open;Transaction Fees;HKD;2020-07-22;808;808;Stocks;2.29;-23.6183;0.0;-3.0199776422732003;;
Open;Transaction Fees;HKD;2020-07-22;27;27;Stocks;51.5;-53.3905;0.0;-6.826829886562001;;
Open;Transaction Fees;HKD;2020-09-29;700;700;Stocks;530.0;-54.431;0.0;-6.959874463724;;
Open;Withholding Tax;HKD;2020-06-19;386;386;;;-41.54;;-5.31155380616;;
Open;Withholding Tax;HKD;2020-07-21;1398;1398;;;-74.27;;-9.49660811708;;
Open;Withholding Tax;HKD;2020-07-31;857;857;;;-14.44;;-1.84638509776;;
Open;Withholding Tax;HKD;2020-11-05;386;386;;;-15.98;;-2.0432987439200003;;
Open;Withholding Tax;HKD;2020-11-13;857;857;;;-19.59;;-2.50489501836;;
Open;Withholding Tax;USD;2020-02-03;FCX;FCX;;;-16.5;;-16.5;;
Open;Withholding Tax;USD;2020-03-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;HKD;2020-05-07;1972;1972;;;590.0;;75.44094236000001;;
Open;Dividends;HKD;2020-05-29;2800;2800;;;45.0;;5.7539701800000005;;
Open;Dividends;HKD;2020-06-19;386;386;;;415.4;;53.1155380616;;
Open;Dividends;HKD;2020-07-02;200;200;;;30.1;;3.8487667204000005;;
Open;Dividends;HKD;2020-07-21;1398;1398;;;862.3;;110.2588552492;;
Open;Dividends;HKD;2020-07-30;2828;2828;;;488.0;;62.398609952;;
Open;Dividends;HKD;2020-07-31;857;857;;;144.4;;18.4638509776;;
Open;Dividends;HKD;2020-09-09;808;808;;;1798.0;;229.90307519200002;;
Open;Dividends;HKD;2020-10-06;1972;1972;;;1680.0;;214.81488672;;
Open;Dividends;HKD;2020-10-09;66;66;;;5.75;;0.735229523;;
Open;Dividends;HKD;2020-10-09;66;66;;;119.25;;15.248020977000001;;
Open;Dividends;HKD;2020-11-05;386;386;;;159.8;;20.4329874392;;
Open;Dividends;HKD;2020-11-13;857;857;;;195.86;;25.043835543440004;;
Open;Dividends;HKD;2020-11-19;16;16;;;5550.0;;709.6563222;;
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
Open;Trades;HKD;2019-03-08;1398;1398;Stocks;5.9;-17700.0;3000.0;-2263.2282708000002;-36.4779;O
Open;Trades;USD;2019-02-11;AMZN;AMZN;Stocks;1800.0;-180000.0;100.0;-180000.0;0.0;"A;O"
Open;Trades;USD;2019-02-19;AMZN;AMZN;Stocks;1628.88;81444.0;-50.0;81444.0;-2.064722;C
Open;Trades;USD;2019-10-14;FCX;FCX;Stocks;15.0;-1500.0;100.0;-1500.0;0.0;"A;O"
Open;Trades;USD;2019-02-11;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2019-03-08;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;3.8;1900.0;-5.0;1900.0;-3.9787;O
Open;Trades;USD;2019-10-14;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2019-03-08;1398;1398;Stocks;5.9;-18.4779;0.0;-2.3626952353116004;;
Open;Withholding Tax;HKD;2019-06-21;386;386;;;-60.6;;-7.7486798424000005;;
Open;Withholding Tax;HKD;2019-07-23;1398;1398;;;-85.12;;-10.883954260480001;;
Open;Withholding Tax;HKD;2019-08-02;857;857;;;-20.47;;-2.61741710188;;
Open;Withholding Tax;HKD;2019-09-26;386;386;;;-26.78;;-3.42425158712;;
Open;Withholding Tax;HKD;2019-11-01;857;857;;;-17.26;;-2.2069672290400004;;
Open;Withholding Tax;USD;2019-02-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-03-29;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-05-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-06-28;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-08-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-09-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-11-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-12-20;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;CNH;2019-07-31;3188;3188;;;390.0;;58.091925839999995;;
Open;Dividends;HKD;2019-01-04;200;200;;;24.38;;3.11737317752;;
Open;Dividends;HKD;2019-04-08;5;5;;;1318.35;;168.5721463734;;
Open;Dividends;HKD;2019-05-16;2888;2888;;;150.56;;19.251505562240002;;
Open;Dividends;HKD;2019-06-21;386;386;;;606.0;;77.486798424;;
Open;Dividends;HKD;2019-07-05;200;200;;;23.5;;3.004851094;;
Open;Dividends;HKD;2019-07-05;5;5;;;625.02;;79.91880982008;;
Open;Dividends;HKD;2019-07-23;1398;1398;;;854.32;;109.23848453728002;;
Open;Dividends;HKD;2019-07-30;2828;2828;;;640.0;;81.83424256;;
Open;Dividends;HKD;2019-08-02;857;857;;;204.72;;26.17672833888;;
Open;Dividends;HKD;2019-09-26;386;386;;;267.8;;34.242515871200006;;
Open;Dividends;HKD;2019-09-26;5;5;;;625.44;;79.97251354176001;;
Open;Dividends;HKD;2019-09-27;2828;2828;;;520.0;;66.49032208;;
Open;Dividends;HKD;2019-10-04;200;200;;;61.1;;7.8126128444;;
Open;Dividends;HKD;2019-10-21;2888;2888;;;70.24;;8.98130812096;;
Open;Dividends;HKD;2019-11-01;857;857;;;172.56;;22.06455765024;;
Open;Dividends;HKD;2019-11-20;5;5;;;626.63;;80.12467408652;;
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
Open;Trades;HKD;2018-01-01;1398;1398;Stocks;6.48;6480.0;-1000.0;828.57170592;-25.17496;C
Open;Trades;HKD;2018-02-05;215;215;Stocks;3.35;6700.0;-2000.0;856.7022268000001;-25.1809;C
Open;Trades;HKD;2018-02-19;215;215;Stocks;3.4;6800.0;-2000.0;869.4888272000001;-25.1836;C
Open;Trades;USD;2018-08-17;FCX;FCX;Stocks;21.0;-21000.0;1000.0;-21000.0;0.0;"A;O"
Open;Trades;USD;2018-10-26;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;216.21;21621.0;-1.0;21621.0;-1.367373;O
Open;Trades;USD;2018-01-18;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;3.08;3080.0;-10.0;3080.0;-5.006148;O
Open;Trades;USD;2018-08-17;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;0.0;0.0;10.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2018-01-01;1398;1398;Stocks;6.48;-7.17496;0.0;-0.9174334640598399;;
Open;Transaction Fees;HKD;2018-02-05;215;215;Stocks;3.35;-7.1809;0.0;-0.9181929881236001;;
Open;Transaction Fees;HKD;2018-02-19;215;215;Stocks;3.4;-7.1836;0.0;-0.9185382263344001;;
Open;Withholding Tax;HKD;2018-06-14;386;386;;;-98.64;;-12.61270263456;;
Open;Withholding Tax;HKD;2018-07-26;857;857;;;-14.86;;-1.90008881944;;
Open;Withholding Tax;HKD;2018-09-21;386;386;;;-36.5;;-4.6671091460000005;;
Open;Withholding Tax;HKD;2018-11-01;857;857;;;-20.39;;-2.60718782156;;
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
Open;Dividends;HKD;2018-04-06;5;5;;;1317.81;;168.50309873124;;
Open;Dividends;HKD;2018-05-17;2888;2888;;;110.45;;14.1228001418;;
Open;Dividends;HKD;2018-06-14;386;386;;;986.4;;126.1270263456;;
Open;Dividends;HKD;2018-07-04;200;200;;;40.0;;5.11464016;;
Open;Dividends;HKD;2018-07-05;5;5;;;627.87;;80.28322793148;;
Open;Dividends;HKD;2018-07-26;857;857;;;148.62;;19.003445514480003;;
Open;Dividends;HKD;2018-07-30;2828;2828;;;440.0;;56.261041760000005;;
Open;Dividends;HKD;2018-09-21;386;386;;;365.0;;46.67109146;;
Open;Dividends;HKD;2018-09-27;5;5;;;627.73;;80.26532669092;;
Open;Dividends;HKD;2018-09-28;2828;2828;;;800.0;;102.29280320000001;;
Open;Dividends;HKD;2018-10-05;200;200;;;45.0;;5.7539701800000005;;
Open;Dividends;HKD;2018-10-22;2888;2888;;;60.13;;7.688582820520001;;
Open;Dividends;HKD;2018-11-01;857;857;;;203.88;;26.06932089552;;
Open;Dividends;HKD;2018-11-21;5;5;;;626.32;;80.08503562528001;;
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
Open;Withholding Tax;HKD;2025-09-25;386;386;;;-19.34;;-2.47292851736;;
Open;Withholding Tax;HKD;2025-10-23;857;857;;;-48.11;;-6.1516334524400005;;
Open;Withholding Tax;HKD;2025-10-24;2318;2318;;;-52.02;;-6.651589528080001;;
Open;Withholding Tax;HKD;2025-12-23;390;390;;;-89.85;;-11.4887604594;;
Open;Dividends;HKD;2025-09-16;215;215;;;1140.0;;145.76724456;;
Open;Dividends;HKD;2025-09-23;1299;1299;;;294.0;;37.592605176;;
Open;Dividends;HKD;2025-09-25;386;386;;;193.4;;24.7292851736;;
Open;Dividends;HKD;2025-09-26;1883;1883;;;600.0;;76.7196024;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;26.5;;3.388449106;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;173.5;;22.184751694000003;;
Open;Dividends;HKD;2025-09-30;3918;3918;;;159.42;;20.38439835768;;
Open;Dividends;HKD;2025-10-09;1972;1972;;;1960.0;;250.61736784000001;;
Open;Dividends;HKD;2025-10-23;857;857;;;482.1;;61.644200528400006;;
Open;Dividends;HKD;2025-10-24;2318;2318;;;520.24;;66.52100992096;;
Open;Dividends;HKD;2025-10-31;27;27;;;700.0;;89.5062028;;
Open;Dividends;HKD;2025-11-20;16;16;;;12600.0;;1611.1116504000001;;
Open;Dividends;HKD;2025-11-28;2800;2800;;;825.0;;105.48945330000001;;
Open;Dividends;HKD;2025-12-18;5;5;;;1866.53;;238.66573244612002;;
Open;Dividends;HKD;2025-12-23;390;390;;;898.95;;114.9451442958;;
Open;Dividends;CNH;2022-07-29;3188;3188;;;370.0;;55.11285272;;
Open;Dividends;HKD;2022-05-26;215;215;;;2605.0;;333.09094042000004;;
Open;Dividends;HKD;2022-05-31;2800;2800;;;40.0;;5.11464016;;
Open;Dividends;HKD;2022-06-13;1883;1883;;;1700.0;;217.37220680000001;;
Open;Dividends;HKD;2022-07-28;2828;2828;;;460.0;;58.81836184;;
Open;Dividends;HKD;2022-09-02;215;215;;;1140.0;;145.76724456;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;283.5;;36.250012134;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;76.5;;9.781749306;;
Open;Dividends;HKD;2022-09-30;1883;1883;;;600.0;;76.7196024;;
Open;Dividends;HKD;2022-11-30;2800;2800;;;280.0;;35.80248112;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;243.6;;36.285110601599996;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;56.4;;8.4009861984;;
Open;Dividends;HKD;2021-05-26;215;215;;;2605.0;;333.09094042000004;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;12.22;;1.5625225688800002;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;52.78;;6.74876769112;;
Open;Dividends;HKD;2021-06-04;1883;1883;;;1600.0;;204.58560640000002;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;8.8;;1.1252208352000002;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;311.2;;39.7919004448;;
Open;Dividends;HKD;2021-09-03;215;215;;;1109.72;;141.89546195888002;;
Open;Dividends;HKD;2021-09-03;215;215;;;30.28;;3.8717826011200005;;
Open;Dividends;HKD;2021-09-03;215;215;;;9637.06;;1232.25235250824;;
Open;Dividends;HKD;2021-09-03;215;215;;;262.94;;33.62108709176;;
Open;Dividends;HKD;2021-09-29;1883;1883;;;550.0;;70.3263022;;
Open;Dividends;HKD;2021-09-29;2828;2828;;;396.0;;50.634937584;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;234.9;;30.0357243396;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;35.1;;4.4880967404000005;;
Open;Trades;HKD;2020-09-24;1883;1883;Stocks;2.45;-24500.0;10000.0;-3132.717098;-45.2615;O
Open;Trades;HKD;2020-09-02;215;215;Stocks;1.21;-60500.0;50000.0;-7735.893242;-111.0335;O
Open;Transaction Fees;HKD;2020-09-02;215;215;Stocks;1.21;-62.6335;0.0;-8.008695361534;;
Open;Transaction Fees;HKD;2020-09-24;1883;1883;Stocks;2.45;-25.6615;0.0;-3.2812334616460004;;
Open;Dividends;CNH;2020-07-31;3188;3188;;;380.0;;56.60238928;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;9.0;;1.150794036;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;591.0;;75.568808364;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;2.64;;0.33756625056;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;327.36;;41.85821506944001;;

---

# [open_positions] START  
Symbol;Account;Asset Category;Description;Currency;Current Price;Cost Price;Quantity;Value;Value in USD;Unrealized Gain in USD;Weight;Unrealized Gain in USD
912797TC1 - United States Treasury B 12/24/26;IBKR;Treasury Bills;;USD;98.574153;97.133959459;740.0;72944.87;72944.87;;0.027164592094226312;1065.7432203399994
912797TV9 - United States Treasury B 02/18/27;IBKR;Treasury Bills;;USD;98.052373;97.064044118;680.0;66675.61;66675.61;;0.02482992633044266;672.0636397600032
912797UE5 - United States Treasury B 04/15/27;IBKR;Treasury Bills;;USD;97.406609;96.90631;1000.0;97406.61;97406.61;;0.036274118083031555;500.2989999999983
912797UX3 - United States Treasury B 05/13/27;IBKR;Treasury Bills;;USD;97.128603;96.625351852;1080.0;104898.89;104898.89;;0.039064235195526646;543.5112398400042
912797VF1 - United States Treasury B 06/10/27;IBKR;Treasury Bills;;USD;96.832067;96.38057197;2640.0;255636.66;255636.66;;0.09519882060562203;1191.9468791999725
912797VQ7 - United States Treasury B 07/08/27;IBKR;Treasury Bills;;USD;96.490668;96.420521978;1820.0;175613.02;175613.02;;0.06539810208360378;127.66576004000541
AMZN;IBKR;Stocks;AMAZON.COM INC;USD;274.48;207.8670155;300.0;82344.0;82344.0;;0.03066481812095863;19983.895350000003
AMZN 18SEP26 300 C;IBKR;Equity and Index Options;;USD;3.45;8.01939888;-1.0;-345.0;-345.0;;-0.00012847763348550868;4.5693988800000005
BABA;IBKR;Stocks;ALIBABA GROUP HOLDING-SP ADR;USD;128.41;111.16845686;500.0;64205.0;64205.0;;0.023909873791121986;8620.771569999995
BHP;IBKR;Stocks;BHP GROUP LTD-SPON ADR;USD;90.41;35.102226747;1200.0;108492.0;108492.0;;0.04040230554234727;66369.32790359999
BHP 18DEC26 95 C;IBKR;Equity and Index Options;;USD;6.2576;4.0005117;-4.0;-2503.04;-2503.04;;-0.000932129436868312;-9.028353200000002
BRK B;IBKR;Stocks;BERKSHIRE HATHAWAY INC-CL B;USD;521.8;469.0869395;100.0;52180.0;52180.0;;0.01943177656601114;5271.306049999993
BRK B 18SEP26 495 P;IBKR;Equity and Index Options;;USD;2.915;4.98296961;-1.0;-291.5;-291.5;;-0.00010855429032181386;2.0679696099999996
BRK B 18SEP26 520 C;IBKR;Equity and Index Options;;USD;12.6131;9.37287917;-1.0;-1261.31;-1261.31;;-0.00046971050403364336;-3.24022083
CPRT;IBKR;Stocks;COPART INC;USD;29.66;27.171926655;1100.0;32626.0;32626.0;;0.012149887739415091;2736.8806795
CPRT 18SEP26 25 P;IBKR;Equity and Index Options;;USD;0.275;0.63450071;-5.0;-137.5;-137.5;;-5.1204853925383894e-05;1.7975035499999996
CPRT 18SEP26 32.5 C;IBKR;Equity and Index Options;;USD;0.675;0.28450792;-6.0;-405.0;-405.0;;-0.00015082156974385803;-2.34295248
CRM 09/18/2026 160.00 P;Schwab;Equity and Index Options;SALESFORCE;USD;3.6;6.94315;-2.0;-720.0;-720.0;;-0.00026812723510019203;6.6863
DNOW;IBKR;Stocks;DNOW INC;USD;16.52;26.81106924;25.0;413.0;413.0;;0.00015380076124497127;-257.276731
FAST;IBKR;Stocks;FASTENAL CO;USD;51.84;42.17242457;100.0;5184.0;5184.0;;0.0019305160927213826;966.7575430000006
FCX;IBKR;Stocks;FREEPORT-MCMORAN INC;USD;69.62;11.2079574;500.0;34810.0;34810.0;;0.012963207019219007;29206.021300000004
CASH - Schwab;Schwab;Forex;;USD;1.0;;-1.0;;70170.89;0.0;0.026131564889194048;
Forex;IBKR;Forex;;USD;0.0;;-1.0;;7772.734597392;0.0;0.0028945581066198906;
GEHC 17JUN27 57.5 P;IBKR;Equity and Index Options;;USD;3.2368;5.33945409;-1.0;-323.68;-323.68;;-0.00012053808813504188;2.10265409
GEHC 17JUN27 65 C;IBKR;Equity and Index Options;;USD;14.7569;8.8804028;1.0;1475.69;1475.69;;0.0005495453882847256;5.876497199999999
GEHC 17JUN27 80 C;IBKR;Equity and Index Options;;USD;6.6256;3.91948334;-1.0;-662.56;-662.56;;-0.0002467366401221989;-2.70611666
GOOG;IBKR;Stocks;ALPHABET INC-CL C;USD;353.47;93.120965218;400.0;141388.0;141388.0;;0.05265274099492493;104139.61391280002
GOOG 18DEC26 410 C;IBKR;Equity and Index Options;;USD;11.6374;32.055824165;-2.0;-2327.48;-2327.48;;-0.0008667510793763819;40.836848329999995
H78;IBKR;Stocks;HONGKONG LAND HOLDINGS LTD;USD;7.97;4.208;1000.0;7970.0;7970.0;;0.00296801953298407;3761.9999999999995
ICE;IBKR;Stocks;INTERCONTINENTAL EXCHANGE IN;USD;150.3;152.14704663;300.0;45090.0;45090.0;;0.016791468098149527;-554.1139889999982
IVV;IBKR;Stocks;ISHARES CORE S&P 500 ETF;USD;776.73;402.65670378;100.0;77673.0;77673.0;;0.028925342683246133;37407.329622000005
IVV;Schwab;Stocks;ISHARES CORE S&P 500 ETF;USD;735.96;531.9820000000001;10.0;7359.6;7359.6;;0.0027407072214491296;2039.7799999999995
META;IBKR;Stocks;META PLATFORMS INC-CLASS A;USD;592.1;230.10913779;200.0;118420.0;118420.0;;0.04409948219522881;72398.17244200001
META 15JAN27 460 P;IBKR;Equity and Index Options;;USD;12.175;19.71258137;-1.0;-1217.5;-1217.5;;-0.00045339570657567193;7.537581369999998
META 15JAN27 520 P;IBKR;Equity and Index Options;;USD;27.075;62.7404795;1.0;2707.5;2707.5;;0.0010082701236580139;-35.665479500000004
META 15JAN27 640 P;IBKR;Equity and Index Options;;USD;85.6409;77.2419796;-1.0;-8564.09;-8564.09;;-0.003189258017846116;-8.398920400000009
META 15JAN27 800 C;IBKR;Equity and Index Options;;USD;14.7;35.535755475;-2.0;-2940.0;-2940.0;;-0.0010948528766591176;41.671510950000005
MSFT;Schwab;Stocks;MICROSOFT CORP;USD;425.01;470.3566;100.0;42501.0;42501.0;;0.015827327248601752;-4534.660000000003
MSFT 08/21/2026 355.00 P;Schwab;Equity and Index Options;MICROSOFT CORP;USD;5.325;8.4432;-1.0;-532.5;-532.5;;-0.00019830243429285035;3.118199999999999
MSFT 15JAN27 420 P;IBKR;Equity and Index Options;;USD;10.9809;53.1437823;-2.0;-2196.18;-2196.18;;-0.0008178550988643607;84.3257646
NBIS 18DEC26 140 P;IBKR;Equity and Index Options;;USD;22.7489;24.136075115;-2.0;-4549.78;-4549.78;;-0.0016943332384918774;2.774350230000003
NBIS 18DEC26 170 P;IBKR;Equity and Index Options;;USD;38.344;39.7868947;1.0;3834.4;3834.4;;0.0014279264864835784;-1.4428946999999965
NOV;IBKR;Stocks;NOV INC;USD;19.65;62.158401437;300.0;5895.0;5895.0;;0.002195291737382822;-12752.520431100002
NVDA;Moomoo-mom;Stocks;NVIDIA;USD;191.48;0.0;0.4717;90.321116;90.321116;;3.3635487644782944e-05;90.321116
NVDA;Moomoo-me;Stocks;NVIDIA;USD;191.68;0.0;1.4431;276.613408;276.613408;;0.0001030105392759463;276.613408
NVO;IBKR;Stocks;NOVO-NORDISK A/S-SPONS ADR;USD;47.26;58.2644644;200.0;9452.0;9452.0;;0.003519914758565299;-2200.892880000001
PAYC;IBKR;Stocks;PAYCOM SOFTWARE INC;USD;214.94;215.9971074;200.0;42988.0;42988.0;;0.016008685531232022;-211.42148000000134
PAYC 21AUG26 145 P;IBKR;Equity and Index Options;;USD;0.0159;3.657044055;-2.0;-3.18;-3.18;;-1.1842286216925149e-06;7.2822881100000005
PAYC 21AUG26 200 C;IBKR;Equity and Index Options;;USD;18.1559;1.237093905;-2.0;-3631.18;-3631.18;;-0.001352247574376549;-33.83761219
PAYC 21AUG26 200 P;IBKR;Equity and Index Options;;USD;2.35;4.29295552;-2.0;-470.0;-470.0;;-0.00017502750069040314;3.885911039999999
PDD;IBKR;Stocks;PDD HOLDINGS INC;USD;91.76;96.7605204;100.0;9176.0;9176.0;;0.003417132651776892;-500.0520399999999
PDD 18DEC26 100 C;IBKR;Equity and Index Options;;USD;5.4;12.173745;2.0;1080.0;1080.0;;0.00040219085265028807;-13.54749
PDD 18DEC26 110 C;IBKR;Equity and Index Options;;USD;2.985;11.5805045;1.0;298.5;298.5;;0.00011116108288528795;-8.5955045
PDD 18DEC26 120 C;IBKR;Equity and Index Options;;USD;1.4964;5.8261019;-2.0;-299.28;-299.28;;-0.00011145155405664648;8.6594038
PDD 18DEC26 130 C;IBKR;Equity and Index Options;;USD;0.8067;6.3994626;-1.0;-80.67;-80.67;;-3.0041422299350683e-05;5.5927625999999995
PDD 18DEC26 90 P;IBKR;Equity and Index Options;;USD;6.775;8.553596777;-4.0;-2710.0;-2710.0;;-0.0010092011210021117;7.114387107999995
PDD 20NOV26 100 C;IBKR;Equity and Index Options;;USD;4.4344;10.7569825;1.0;443.44;443.44;;0.00016513658490670716;-6.322582499999999
PDD 20NOV26 130 C;IBKR;Equity and Index Options;;USD;0.5498;3.12292012;-1.0;-54.98;-54.98;;-2.0474493591400775e-05;2.57312012
SKHY 18DEC26 115 P;IBKR;Equity and Index Options;;USD;15.459;16.440827835;-2.0;-3091.8;-3091.8;;-0.0011513830353927414;1.9636556700000014
SKHY 18DEC26 130 P;IBKR;Equity and Index Options;;USD;22.9464;24.4323006;1.0;2294.64;2294.64;;0.000854521498264312;-1.4859006000000008
SMCI 18DEC26 24 P;IBKR;Equity and Index Options;;USD;2.8015;2.96300312;-4.0;-1120.6;-1120.6;;-0.00041731024951843776;0.6460124800000013
SMCI 18DEC26 27 P;IBKR;Equity and Index Options;;USD;4.1125;4.3169028;2.0;822.5;822.5;;0.0003062981262082055;-0.4088056000000009
SOUHY;IBKR;Stocks;SOUTH32 - ADR;USD;17.52;7.17;300.0;5256.0;5256.0;;0.001957328816231402;3105.0
TSM;IBKR;Stocks;TAIWAN SEMICONDUCTOR-SP ADR;USD;420.04;181.60807492;300.0;126012.0;126012.0;;0.046926734929785274;71529.57752400001
UNH;IBKR;Stocks;UNITEDHEALTH GROUP INC;USD;407.08;322.13551065;200.0;81416.0;81416.0;;0.030319231906829492;16988.897869999993
UNH 15JAN27 360 P;IBKR;Equity and Index Options;;USD;14.55;12.18938278;-1.0;-1455.0;-1455.0;;-0.0005418404542649714;-2.36061722
UNH 17JUN27 360 P;IBKR;Equity and Index Options;;USD;25.1751;21.4891912;-1.0;-2517.51;-2517.51;;-0.0009375180494959508;-3.6859088
UNH 18DEC26 440 C;IBKR;Equity and Index Options;;USD;18.3907;26.01244859;-1.0;-1839.07;-1839.07;;-0.0006848677142440419;7.621748590000003
9CI;IBKR;Stocks;CAPITALAND INVESTMENT LTD/SI;SGD;2.69;2.323057932;1200.0;3228.0;2476.154017956;;0.0009221171257177411;337.77140373058853
BS6;IBKR;Stocks;YANGZIJIANG SHIPBUILDING;SGD;4.2;0.171019587;7000.0;29400.0;22552.3321338;;0.00839846452791251;21634.024865369214
BS6;Moomoo-me;Stocks;YZJ Shipbuilding;SGD;3.87;4.015;200.0;774.0;601.398;;0.00022395997629831292;-22.245497682999936
C38U;IBKR;Stocks;CAPITALAND INTEGRATED COMMER;SGD;2.46;1.364696005;222.0;546.12;418.92107567724;;0.00015600576353685648;186.52273486950335
CGP1;IBKR;Stocks;NOBLE GROUP LTD;SGD;0.0;85.85;50.0;0.0;0.0;;0.0;-3292.7172001475
D05;IBKR;Stocks;DBS GROUP HOLDINGS LTD;SGD;76.33;18.538145455;220.0;16792.6;12881.370496260199;;0.00479700868814366;9752.892572515708
F34;IBKR;Stocks;WILMAR INTERNATIONAL LTD;SGD;3.94;3.040549529;9000.0;35460.0;27200.87406342;;0.01012957660407407;6209.60380404944
CASH - DBS;DBS;Forex;;SGD;0.763247271;;-0.777;;1043.95389;18.47775410253007;0.00038876731958026393;
CASH - Moomoo-me;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;75879.44238000001;1343.049430789266;0.028257423730963523;
CASH - Moomoo-mom;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;7038.47781;124.57950822836938;0.002621121659040771;
CASH - OCBC;OCBC;Forex;;SGD;0.763247271;;-0.777;;6154.438290000001;108.93220328133066;0.002291906280961742;
CASH - OCBC Joint;OCBC Joint;Forex;;SGD;0.763247271;;-0.777;;30062.78268;532.1046373023601;0.011195348332508808;
CASH - POSB;POSB;Forex;;SGD;0.763247271;;-0.777;;6713.0158200000005;118.81890263214063;0.0024999199597228452;
CASH - UOB;UOB;Forex;;SGD;0.763247271;;-0.777;;6898.25262;122.09755322574074;0.002568901947254522;
Forex;IBKR;Forex;;SGD;0.767086127;;-0.78224;;14246.231731;275.983814;0.005305281561985628;
Fullerton SGD Cash Fund;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0;
Fullerton SGD Cash Fund;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0;
HMN;IBKR;Stocks;CAPITALAND ASCOTT TRUST;SGD;0.86;0.527017662;68.0;58.48;44.85919670696;;1.670551719701781e-05;17.368976977076095
O39;IBKR;Stocks;OVERSEA-CHINESE BANKING CORP;SGD;30.3;8.7231;800.0;24240.0;18594.16771848;;0.006924448304646233;13241.07252293304
P40U;Moomoo-mom;Stocks;StarhillGbl Reit;SGD;0.555;0.555;1200.0;666.0000000000001;517.4820000000001;;0.00019270974704738557;0.0
P40U;Moomoo-me;Stocks;StarhillGbl Reit;SGD;0.555;0.5448;4200.0;2331.0;1811.187;;0.0006744841146658493;32.86196968068031
Z74;Moomoo-mom;Stocks;Singtel;SGD;4.59;4.29;100.0;459.0;356.64300000000003;;0.0001328134743164414;23.012583809999988
Z74;Moomoo-me;Stocks;Singtel;SGD;4.59;4.88;100.0;459.0;356.64300000000003;;0.0001328134743164414;-22.245497683000004
Z77;Moomoo-me;Stocks;Singtel 10;SGD;4.58;4.28;120.0;549.6;427.0392000000001;;0.00015902894441027494;27.615100571999985
1299;IBKR;Stocks;AIA GROUP LTD;HKD;74.15;55.302399867;600.0;44490.0;5688.75851796;;0.0021184876285740015;1445.9803883979478
1398;IBKR;Stocks;IND & COMM BK OF CHINA - H;HKD;7.14;5.051969413;6000.0;42840.0;5477.779611360001;;0.002039919307891891;1601.928764336786
14;IBKR;Stocks;HYSAN DEVELOPMENT CO;HKD;17.92;28.5525695;1000.0;17920.0;2291.35879168;;0.0008532995797717714;-1359.544174217278
16;IBKR;Stocks;SUN HUNG KAI PROPERTIES;HKD;115.4;88.851861372;4500.0;519300.0;66400.8158772;;0.024727593291042457;15275.719800001812
1810;IBKR;Stocks;XIAOMI CORP-CLASS B;HKD;27.06;38.85377485;600.0;16236.0;2076.032440944;;0.0007731122755119687;-904.8137172871197
1883;IBKR;Stocks;CITIC TELECOM INTERNATIONAL;HKD;2.655;2.45452615;10000.0;26550.0;3394.8424062000004;;0.001264235705521235;256.3379010599538
1972;IBKR;Stocks;SWIRE PROPERTIES LTD;HKD;23.38;19.720971466;5600.0;130928.0;16741.240171712;;0.006234420054707504;2620.046000121524
200;IBKR;Stocks;MELCO INTERNATIONAL DEVELOP.;HKD;3.38;11.460065577;4000.0;13520.0;1728.74837408;;0.0006437840579528097;-4132.662789555777
215;IBKR;Stocks;HUTCHISON TELECOMM HONG KONG;HKD;1.16;1.21222067;50000.0;58000.0;7416.228232;;0.0027617955148863133;-333.86241995513456
2318;IBKR;Stocks;PING AN INSURANCE GROUP CO-H;HKD;57.15;86.4593701;500.0;28575.0;3653.7710643;;0.0013606604627220068;-1873.8360172220405
27;IBKR;Stocks;GALAXY ENTERTAINMENT GROUP L;HKD;32.88;51.5945905;1000.0;32880.0;4204.23421152;;0.0015656523539562411;-2392.959903731362
2800;IBKR;Stocks;TRACKER FUND OF HONG KONG-B;HKD;26.16;21.769917967;1500.0;39240.0;5017.46199696;;0.0018684975173127402;842.013370187859
2828;IBKR;Stocks;HANGSENG CHINA ENT INDEX;HKD;87.98;92.44076825;400.0;35192.0;4499.860412768;;0.0016757432372392954;-228.15224435902925
2888;IBKR;Stocks;STANDARD CHARTERED PLC;HKD;232.2;55.6;28.0;6501.6;831.3336116064;;0.0003095877537859458;632.2718165792
3188;IBKR;Stocks;CHINAAMC ETF SERIES - CH;HKD;56.76;26.37987258;1000.0;56760.0;7257.6743870400005;;0.002702750231464606;3884.5854942062297
3690;IBKR;Stocks;MEITUAN-CLASS B;HKD;92.2;231.697888617;60.0;5532.0;707.354734128;;0.0002634181515232946;-1070.2222550335725
386;IBKR;Stocks;CHINA PETROLEUM & CHEMICAL-H;HKD;4.36;5.815174;2000.0;8720.0;1114.99155488;;0.00041522167051394224;-372.1345690093919
390;IBKR;Stocks;CHINA RAILWAY GROUP LTD-H;HKD;3.545;3.92724184;10000.0;35450.0;4532.8498418;;0.0016880284655641346;-488.75736642407395
3918;IBKR;Stocks;NAGACORP LTD;HKD;3.28;7.594013956;2036.0;6678.08;853.89940399232;;0.00031799123089744813;-1123.089617635046
3968;IBKR;Stocks;CHINA MERCHANTS BANK - H;HKD;48.68;39.7833745;500.0;24340.0;3112.25853736;;0.0011590017729712562;568.787975884751
5;IBKR;Stocks;HSBC HOLDINGS PLC;HKD;161.2;49.092166333;2400.0;386880.0;49468.79962752;;0.018422128427572706;34403.473699430295
66;IBKR;Stocks;MTR CORP;HKD;31.9;40.07708;500.0;15950.0;2039.4627638000002;;0.0007594937665937362;-522.7852719941602
700;IBKR;Stocks;TENCENT HOLDINGS LTD;HKD;478.8;452.856419642;706.0;338032.8;43222.9033569312;;0.016096163291801073;2342.0151765802284
808;IBKR;Stocks;PROSPERITY REIT;HKD;1.51;2.339531045;20000.0;30200.0;3861.5533208;;0.0014380383543028735;-2121.376398361884
857;IBKR;Stocks;PETROCHINA CO LTD-H;HKD;9.505;7.7972334;2000.0;19010.0;2430.73273604;;0.000905202288586014;436.7305818133331
939;IBKR;Stocks;CHINA CONSTRUCTION BANK-H;HKD;8.79;6.39177626;4000.0;35160.0;4495.76870064;;0.0016742194879897031;1226.6051453269395
9618;IBKR;Stocks;JD.COM INC-CLASS A;HKD;127.5;285.565311947;19.0;2422.5;309.75539469;;0.00011535257991055335;-384.0124163838507
CASH - Citic;Citic;Forex;;HKD;0.128244528;;-0.1276;;79953.53603599999;-403.8580930580793;0.029774611880300342;
CASH - StanChart;StanChart;Forex;;HKD;0.128244528;;-0.1276;;1615.37134;-8.159498895199931;0.0006015625709087392;
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;127600.0;-644.5280000000057;0.047518104442756254;
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;1276.0;-6.445279999999912;0.00047518104442756257;
CASH - StanChart Payroll;StanChart Payroll;Forex;;HKD;0.128244528;;-0.1276;;32635.340936;-164.84632463007802;0.012153366294058046;
Forex;IBKR;Forex;;HKD;0.127866004;;-0.12747;;1527.447852;-4.745243;0.0005688199573840102;
BARCl;IBKR;Stocks;;GBP;5.16;3.5479;1000.0;5160.0;6844.518347040001;;0.0025488913610894055;2138.3814006324005
Forex;IBKR;Forex;;GBP;1.326457044;;-1.3492;;2791.051453;47.047702;0.0010393845959934217;
IMB 18DEC26 30 C;IBKR;Equity and Index Options;;GBP;0.64;0.8986;-1.0;-640.0;-848.93250816;;-0.0003161415641661278;0.34302179157839996
IMBl;IBKR;Stocks;IMPERIAL BRANDS PLC;GBP;27.91;29.95515;1000.0;27910.0;37021.416098040005;;0.013786736024807232;-2712.8036235366
NWG 18SEP26 5 P;IBKR;Equity and Index Options;;GBP;0.0;0.5086;-1.0;0.0;0.0;;0.0;0.6746360525784001
RRl;IBKR;Stocks;ROLLS-ROYCE HOLDINGS PLC;GBP;15.3;11.090386;100.0;1530.0;2029.4792773200002;;0.0007557759268346494;558.3872142821017
Forex;IBKR;Forex;;EUR;1.159715451;;-1.1558;;2277.246394;-7.714524;0.0008480441378681979;
MC;IBKR;Stocks;LVMH MOET HENNESSY LOUIS VUI;EUR;480.0;619.0851265;10.0;4800.0;5566.634164800001;;0.0020730086491973485;-1612.9917020633957
MC 18DEC26 440 P;IBKR;Equity and Index Options;;EUR;17.3301;37.9386;-1.0;-1733.01;-2009.79847373751;;-0.0007484468164886451;23.899995871933502
RI;IBKR;Stocks;PERNOD-RICARD SA;EUR;70.32;83.7114;100.0;7032.0;8155.119051432001;;0.0030369576710741154;-1553.0213490521405
RMS;IBKR;Stocks;HERMES INTL;EUR;1635.5;2195.362096667;3.0;4906.5;5690.1438603315;;0.0021190035286014143;-1947.8421718019267
RMS 17DEC27 1520 P;IBKR;Equity and Index Options;;EUR;160.7809;193.044;5.0;8039.05;9323.01049636155;;0.003471879204443738;-187.08007783579055
RMS 17DEC27 1680 P;IBKR;Equity and Index Options;;EUR;237.9466;214.886;-5.0;-11897.33;-13797.51742664583;;-0.005138180831740643;-133.71867064665298
RMS 17DEC27 1920 P;IBKR;Equity and Index Options;;EUR;386.8074;388.7186;-1.0;-38680.74;-44858.65183411374;;-0.016705314286948717;2.2164481699512093
RMS 18DEC26 1520 P;IBKR;Equity and Index Options;;EUR;70.8665;130.209;5.0;3543.33;4109.25454899183;;0.0015302820285334252;-344.1020707548375
RMS 18DEC26 1600 P;IBKR;Equity and Index Options;;EUR;102.5319;110.436;-5.0;-5126.6;-5945.397231096601;;-0.0022140596127031517;45.83253448124558
SAP;IBKR;Stocks;SAP SE;EUR;177.96;210.1117;100.0;17796.0;20638.296165996002;;0.007685679566899169;-3728.682326591671
SAP 18DEC26 190 P;IBKR;Equity and Index Options;;EUR;23.35;31.7783;-2.0;-4670.0;-5415.87115617;;-0.0020168646649482533;19.5488594713266
Forex;IBKR;Forex;;CNH;0.148953656;;-0.14829;;965.3679;-4.3204;0.0003595019803909426;

---

# [pivot_summary] START  
# PORTFOLIO PIVOT SUMMARY

**Report Date :** 2026-08-08  
**Filter      :** Open Positions | Forex Balances | Cash Balances  
**Generated   :** 2026-08-08 12:51:38  

---

## Equity and Index Options

### Schwab

```
            Amount  Quantity  Value in USD
Underlying                                
CRM          720.0       2.0        -720.0
MSFT         532.5       1.0        -532.5
```

> **SUBTOTAL [Schwab]**  Amount: `       1,252.50`  Quantity: `           3.00`  Value in USD: `      -1,252.50`

### IBKR

```
              Amount  Quantity  Value in USD
Underlying                                  
AMZN         -345.00       1.0       -345.00
BHP         -2503.04       4.0      -2503.04
BRK         -1552.81       2.0      -1552.81
CPRT         -542.50      11.0       -542.50
GEHC          489.45       1.0        489.45
GOOG        -2327.48       2.0      -2327.48
IMB          -640.00       1.0       -848.93
MC          -1733.01       1.0      -2009.80
META       -10014.09       3.0     -10014.09
MSFT        -2196.18       2.0      -2196.18
NBIS         -715.38       1.0       -715.38
NWG             0.00       1.0          0.00
PAYC        -4104.36       6.0      -4104.36
PDD         -1322.99       4.0      -1322.99
RMS        -44122.29       1.0     -51169.30
SAP         -4670.00       2.0      -5415.87
SKHY         -797.16       1.0       -797.16
SMCI         -298.10       2.0       -298.10
UNH         -5811.58       3.0      -5811.58
```

> **SUBTOTAL [IBKR]**  Amount: `     -83,206.52`  Quantity: `          49.00`  Value in USD: `     -91,485.12`

> **SUBTOTAL [Equity and Index Options]**  Amount: `     -81,954.02`  Quantity: `          52.00`  Value in USD: `     -92,737.62`

---

## Forex

### Citic

```
               Amount  Quantity  Value in USD
Underlying                                   
Forex       626595.11      0.13      79953.54
```

> **SUBTOTAL [Citic]**  Amount: `     626,595.11`  Quantity: `           0.13`  Value in USD: `      79,953.54`

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
Forex       97656.94      1.55      75879.44
```

> **SUBTOTAL [Moomoo-me]**  Amount: `      97,656.94`  Quantity: `           1.55`  Value in USD: `      75,879.44`

### Moomoo-mom

```
             Amount  Quantity  Value in USD
Underlying                                 
Forex       9058.53      1.55       7038.48
```

> **SUBTOTAL [Moomoo-mom]**  Amount: `       9,058.53`  Quantity: `           1.55`  Value in USD: `       7,038.48`

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
Forex       70170.89       1.0      70170.89
```

> **SUBTOTAL [Schwab]**  Amount: `      70,170.89`  Quantity: `           1.00`  Value in USD: `      70,170.89`

### StanChart

```
              Amount  Quantity  Value in USD
Underlying                                  
Forex       12659.65      0.13       1615.37
```

> **SUBTOTAL [StanChart]**  Amount: `      12,659.65`  Quantity: `           0.13`  Value in USD: `       1,615.37`

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
Forex       255762.86      0.13      32635.34
```

> **SUBTOTAL [StanChart Payroll]**  Amount: `     255,762.86`  Quantity: `           0.13`  Value in USD: `      32,635.34`

### IBKR

```
              Amount  Quantity  Value in USD
Underlying                                  
Forex       48516.58      4.56      29580.08
```

> **SUBTOTAL [IBKR]**  Amount: `      48,516.58`  Quantity: `           4.56`  Value in USD: `      29,580.08`

### UOB

```
             Amount  Quantity  Value in USD
Underlying                                 
Forex       8878.06      0.78       6898.25
```

> **SUBTOTAL [UOB]**  Amount: `       8,878.06`  Quantity: `           0.78`  Value in USD: `       6,898.25`

> **SUBTOTAL [Forex]**  Amount: `   2,195,893.46`  Quantity: `          13.19`  Value in USD: `     476,621.58`

---

## Stocks

### Moomoo-me

```
             Amount  Quantity  Value in USD
Underlying                                 
BS6          774.00   -200.00        601.40
NVDA         276.61     -1.44        276.61
P40U        2331.00  -4200.00       1811.19
Z74          459.00   -100.00        356.64
Z77          549.60   -120.00        427.04
```

> **SUBTOTAL [Moomoo-me]**  Amount: `       4,390.21`  Quantity: `      -4,621.44`  Value in USD: `       3,472.88`

### Moomoo-mom

```
            Amount  Quantity  Value in USD
Underlying                                
NVDA         90.32     -0.47         90.32
P40U        666.00  -1200.00        517.48
Z74         459.00   -100.00        356.64
```

> **SUBTOTAL [Moomoo-mom]**  Amount: `       1,215.32`  Quantity: `      -1,300.47`  Value in USD: `         964.45`

### Schwab

```
             Amount  Quantity  Value in USD
Underlying                                 
IVV          7359.6     -10.0        7359.6
MSFT        42501.0    -100.0       42501.0
```

> **SUBTOTAL [Schwab]**  Amount: `      49,860.60`  Quantity: `        -110.00`  Value in USD: `      49,860.60`

### IBKR

```
               Amount  Quantity  Value in USD
Underlying                                   
1299         44490.00    -600.0       5688.76
1398         42840.00   -6000.0       5477.78
14           17920.00   -1000.0       2291.36
16          519300.00   -4500.0      66400.82
1810         16236.00    -600.0       2076.03
1883         26550.00  -10000.0       3394.84
1972        130928.00   -5600.0      16741.24
200          13520.00   -4000.0       1728.75
215          58000.00  -50000.0       7416.23
2318         28575.00    -500.0       3653.77
27           32880.00   -1000.0       4204.23
2800         39240.00   -1500.0       5017.46
2828         35192.00    -400.0       4499.86
2888          6501.60     -28.0        831.33
3188         56760.00   -1000.0       7257.67
3690          5532.00     -60.0        707.35
386           8720.00   -2000.0       1114.99
390          35450.00  -10000.0       4532.85
3918          6678.08   -2036.0        853.90
3968         24340.00    -500.0       3112.26
5           386880.00   -2400.0      49468.80
66           15950.00    -500.0       2039.46
700         338032.80    -706.0      43222.90
808          30200.00  -20000.0       3861.55
857          19010.00   -2000.0       2430.73
939          35160.00   -4000.0       4495.77
9618          2422.50     -19.0        309.76
9CI           3228.00   -1200.0       2476.15
AMZN         82344.00    -300.0      82344.00
BABA         64205.00    -500.0      64205.00
BARCl         5160.00   -1000.0       6844.52
BHP         108492.00   -1200.0     108492.00
BRK          52180.00    -100.0      52180.00
BS6          29400.00   -7000.0      22552.33
C38U           546.12    -222.0        418.92
CGP              0.00     -50.0          0.00
CPRT         32626.00   -1100.0      32626.00
D05          16792.60    -220.0      12881.37
DNOW           413.00     -25.0        413.00
F34          35460.00   -9000.0      27200.87
FAST          5184.00    -100.0       5184.00
FCX          34810.00    -500.0      34810.00
GOOG        141388.00    -400.0     141388.00
H78           7970.00   -1000.0       7970.00
HMN             58.48     -68.0         44.86
ICE          45090.00    -300.0      45090.00
IMB          27910.00   -1000.0      37021.42
IVV          77673.00    -100.0      77673.00
MC            4800.00     -10.0       5566.63
META        118420.00    -200.0     118420.00
NOV           5895.00    -300.0       5895.00
NVO           9452.00    -200.0       9452.00
O39          24240.00    -800.0      18594.17
PAYC         42988.00    -200.0      42988.00
PDD           9176.00    -100.0       9176.00
RI            7032.00    -100.0       8155.12
RMS           4906.50      -3.0       5690.14
RRl           1530.00    -100.0       2029.48
SAP          17796.00    -100.0      20638.30
SOUHY         5256.00    -300.0       5256.00
TSM         126012.00    -300.0     126012.00
UNH          81416.00    -200.0      81416.00
```

> **SUBTOTAL [IBKR]**  Amount: `   3,207,157.68`  Quantity: `    -159,247.00`  Value in USD: `   1,473,934.76`

> **SUBTOTAL [Stocks]**  Amount: `   3,262,623.81`  Quantity: `    -165,278.91`  Value in USD: `   1,528,232.68`

---

## Treasury Bills

### IBKR

```
               Amount  Quantity  Value in USD
Underlying                                   
912797TC1    72944.87  -74000.0      72944.87
912797TV9    66675.61  -68000.0      66675.61
912797UE5    97406.61 -100000.0      97406.61
912797UX3   104898.89 -108000.0     104898.89
912797VF1   255636.66 -264000.0     255636.66
912797VQ7   175613.02 -182000.0     175613.02
```

> **SUBTOTAL [IBKR]**  Amount: `     773,175.66`  Quantity: `    -796,000.00`  Value in USD: `     773,175.66`

> **SUBTOTAL [Treasury Bills]**  Amount: `     773,175.66`  Quantity: `    -796,000.00`  Value in USD: `     773,175.66`

---

> ### GRAND TOTAL  
> Amount: `   6,149,738.92`  Quantity: `    -961,213.72`  Value in USD: `   2,685,292.30`

---

