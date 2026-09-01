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

- Current refresh timestamp: **2026-09-01 18:29:34**.


# [transaction_details] START  
Status;Table;Currency;Settle Date;Ticker;Symbol;Asset Category;Transaction Price;Amount;Quantity;Value in USD;Comm/Fee;Code
Open;Trades;EUR;2026-03-04;RI;RI;Stocks;93.0;-9300.0;100.0;-10785.3536943;0.0;"A;O"
Open;Trades;EUR;2026-02-16;SAP;SAP;Stocks;220.0;-22000.0;100.0;-25513.739922;0.0;"A;O"
Open;Trades;HKD;2026-04-01;1810;1810;Stocks;31.8;-6360.0;200.0;-812.8500141599999;-25.18126;O
Open;Trades;HKD;2026-04-10;1810;1810;Stocks;31.0;-6200.0;200.0;-792.4009571999999;-25.1767;O
Open;Trades;HKD;2026-04-10;700;700;Stocks;502.0;-50200.0;100.0;-6415.8916211999995;-92.5907;O
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
Open;Trades;HKD;2026-05-14;16;16 29JUN26 150 C;Equity and Index Options;3.86;15440.0;-4.0;1973.33399664;-42.88;O
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
Open;Transaction Fees;EUR;2026-03-04;RI;RI;Stocks;93.0;-37.2;0.0;-43.141414777200005;;
Open;Transaction Fees;HKD;2026-04-01;1810;1810;Stocks;31.8;-7.18126;0.0;-0.9178124674035599;;
Open;Transaction Fees;HKD;2026-04-10;1810;1810;Stocks;31.0;-7.1767;0.0;-0.9172296692802;;
Open;Transaction Fees;HKD;2026-04-10;700;700;Stocks;502.0;-52.4307;0.0;-6.700989817204199;;
Open;Withholding Tax;EUR;2026-02-18;RMS;RMS;;;-3.72;;-4.314141477720001;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-11.22604556568;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;9.68;;11.22604556568;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-11.22604556568;;
Open;Withholding Tax;EUR;2026-04-30;MC;MC;;;-18.75;;-21.744664706250003;;
Open;Withholding Tax;EUR;2026-05-08;SAP;SAP;;;-61.32;;-71.11375145532;;
Open;Withholding Tax;EUR;2026-07-24;RI;RI;;;-58.75;;-68.13328274625;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.575444305419998;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;90.57;;11.575444305419998;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.575444305419998;;
Open;Withholding Tax;HKD;2026-01-26;939;939;;;-81.86;;-10.462248767159998;;
Open;Withholding Tax;HKD;2026-02-09;3968;3968;;;-33.22;;-4.24573545132;;
Open;Withholding Tax;HKD;2026-06-16;1398;1398;;;-116.65;;-14.9086405899;;
Open;Withholding Tax;HKD;2026-06-26;386;386;;;-25.78;;-3.2948543026799997;;
Open;Withholding Tax;HKD;2026-07-15;2318;2318;;;-100.56;;-12.852232299359999;;
Open;Withholding Tax;HKD;2026-07-31;857;857;;;-22.27;;-2.8462531156199997;;
Open;Withholding Tax;HKD;2026-08-13;390;390;;;-98.9;;-12.6400733334;;
Open;Withholding Tax;HKD;2026-08-21;3968;3968;;;-57.66;;-7.369328901959999;;
Open;Withholding Tax;HKD;2026-08-21;939;939;;;-93.42;;-11.939693132519999;;
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
Open;Dividends;EUR;2026-02-18;RMS;RMS;;;15.0;;17.395731765;;
Open;Dividends;EUR;2026-04-23;RMS;RMS;;;39.0;;45.228902589;;
Open;Dividends;EUR;2026-04-30;MC;MC;;;75.0;;86.97865882500001;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;232.5;;269.6338423575;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;17.5;;20.2950203925;;
Open;Dividends;EUR;2026-07-24;RI;RI;;;235.0;;272.533130985;;
Open;Dividends;GBP;2025-12-31;IMB;IMB;;;400.8;;531.6439832352;;
Open;Dividends;GBP;2026-03-31;IMB;IMB;;;400.8;;531.6439832352;;
Open;Dividends;GBP;2026-06-30;IMB;IMB;;;416.8;;552.8672959392001;;
Open;Dividends;HKD;2026-01-26;1398;1398;;;931.62;;119.06719028171999;;
Open;Dividends;HKD;2026-01-26;939;939;;;818.62;;104.62504380371999;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;220.7;;28.206917944199997;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;333.34;;42.60305404403999;;
Open;Dividends;HKD;2026-03-19;16;16;;;4410.0;;563.62713246;;
Open;Dividends;HKD;2026-03-27;14;14;;;810.0;;103.52335086;;
Open;Dividends;HKD;2026-04-23;808;808;;;1142.0;;145.95514405199998;;
Open;Dividends;HKD;2026-04-30;5;5;;;8148.56;;1041.43979738736;;
Open;Dividends;HKD;2026-04-30;5;5;;;306.5;;39.172724738999996;;
Open;Dividends;HKD;2026-05-07;1972;1972;;;4480.0;;572.57359488;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;3.84;;0.49077736703999997;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;103.61;;13.24204244766;;
Open;Dividends;HKD;2026-05-29;215;215;;;2605.0;;332.93620862999995;;
Open;Dividends;HKD;2026-05-29;2800;2800;;;285.0;;36.42488271;;
Open;Dividends;HKD;2026-06-01;700;700;;;3741.8;;478.2267583308;;
Open;Dividends;HKD;2026-06-12;1299;1299;;;864.48;;110.48625475488;;
Open;Dividends;HKD;2026-06-12;27;27;;;800.0;;102.2452848;;
Open;Dividends;HKD;2026-06-16;1398;1398;;;1166.46;;149.08129363476;;
Open;Dividends;HKD;2026-06-22;66;66;;;439.66;;56.19145239396;;
Open;Dividends;HKD;2026-06-22;66;66;;;5.34;;0.6824872760399999;;
Open;Dividends;HKD;2026-06-25;1883;1883;;;1300.0;;166.14858779999997;;
Open;Dividends;HKD;2026-06-26;386;386;;;257.8;;32.948543026799996;;
Open;Dividends;HKD;2026-06-26;5;5;;;1661.92;;212.40435464351998;;
Open;Dividends;HKD;2026-06-26;5;5;;;217.73;;27.827332324379995;;
Open;Dividends;HKD;2026-06-30;2828;2828;;;280.0;;35.78584968;;
Open;Dividends;HKD;2026-07-15;2318;2318;;;1005.64;;128.52743525783998;;
Open;Dividends;HKD;2026-07-31;857;857;;;222.66;;28.457418891959996;;
Open;Dividends;HKD;2026-07-31;857;857;;;316.75;;40.4827424505;;
Open;Dividends;HKD;2026-08-07;3918;3918;;;172.04;;21.987848496239998;;
Open;Dividends;HKD;2026-08-13;390;390;;;989.0;;126.400733334;;
Open;Dividends;HKD;2026-08-21;3968;3968;;;576.63;;73.69712321777999;;
Open;Dividends;HKD;2026-08-21;939;939;;;936.74;;119.72156010443999;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.96;;0.73692612384;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.26;;0.19958415854;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.36;;0.27634729644;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.86;;0.66016298594;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;0.8;;0.6141051032;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;0.4;;0.3070525516;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;9.04;;6.939387666159999;;
Open;Dividends;SGD;2026-04-17;D05;D05;;;33.0;;25.331835507;;
Open;Dividends;SGD;2026-04-17;D05;D05;;;145.2;;111.4600762308;;
Open;Dividends;SGD;2026-05-08;O39;O39;;;128.0;;98.256816512;;
Open;Dividends;SGD;2026-05-08;O39;O39;;;336.0;;257.924143344;;
Open;Dividends;SGD;2026-05-14;9CI;9CI;;;144.0;;110.538918576;;
Open;Dividends;SGD;2026-05-14;BS6;BS6;;;1400.0;;1074.6839306;;
Open;Dividends;SGD;2026-05-14;F34;F34;;;900.0;;690.8682411;;
Open;Dividends;SGD;2026-05-20;D05;D05;;;33.0;;25.331835507;;
Open;Dividends;SGD;2026-05-20;D05;D05;;;145.2;;111.4600762308;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;0.6;;0.4605788274;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;0.13;;0.09979207927;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;8.1;;6.2178141699;;
Open;Dividends;SGD;2026-08-25;D05;D05;;;145.2;;111.4600762308;;
Open;Dividends;SGD;2026-08-25;D05;D05;;;33.0;;25.331835507;;
Open;Dividends;SGD;2026-08-28;HMN;HMN;;;0.26;;0.19958415854;;
Open;Dividends;SGD;2026-08-28;HMN;HMN;;;1.22;;0.93651028238;;
Open;Dividends;SGD;2026-08-28;HMN;HMN;;;0.24;;0.18423153096;;
Open;Dividends;SGD;2026-08-28;O39;O39;;;376.0;;288.629398504;;
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
Open;Trades;EUR;2025-10-22;MC;MC;Stocks;618.7;-6187.0;10.0;-7175.159495337;-3.851265;O
Open;Trades;EUR;2025-10-22;RMS;RMS;Stocks;2194.0;-6582.0;3.0;-7633.247098482;-4.08629;O
Open;Trades;GBP;2025-05-22;IMB;IMB;Stocks;30.75;-30750.0;1000.0;-40788.554103;-153.75;"A;O"
Open;Trades;HKD;2025-03-26;1810;1810;Stocks;53.35;-10670.0;200.0;-1363.6964860199998;-31.90695;O
Open;Trades;HKD;2025-06-30;2888;2888;Stocks;134.1;13410.0;-100.0;1713.8865864599998;-33.70307;C
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
Open;Transaction Fees;HKD;2025-03-26;1810;1810;Stocks;53.35;-11.304095;0.0;-1.44473801585157;;
Open;Transaction Fees;HKD;2025-06-30;2888;2888;Stocks;134.1;-14.382185;0.0;-1.8381382517141098;;
Open;Withholding Tax;EUR;2025-12-04;MC;MC;;;-13.75;;-15.946087451250001;;
Open;Withholding Tax;HKD;2025-01-24;1398;1398;;;-93.17;;-11.907741481019999;;
Open;Withholding Tax;HKD;2025-01-27;939;939;;;-85.25;;-10.895513161499998;;
Open;Withholding Tax;HKD;2025-05-28;939;939;;;-88.71;;-11.337724018259998;;
Open;Withholding Tax;HKD;2025-06-27;386;386;;;-30.46;;-3.89298921876;;
Open;Withholding Tax;HKD;2025-06-30;2318;2318;;;-88.13;;-11.26359618678;;
Open;Withholding Tax;HKD;2025-07-24;857;857;;;-54.54;;-6.970572291239999;;
Open;Withholding Tax;HKD;2025-07-30;3968;3968;;;-94.82;;-12.118622380919998;;
Open;Withholding Tax;HKD;2025-07-31;390;390;;;-194.8;;-24.8967268488;;
Open;Withholding Tax;HKD;2025-08-22;1398;1398;;;-108.23;;-13.832508967379999;;
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
Open;Dividends;EUR;2025-12-04;MC;MC;;;55.0;;63.784349805000005;;
Open;Dividends;GBP;2025-09-30;IMB;IMB;;;400.8;;531.6439832352;;
Open;Dividends;HKD;2025-01-24;1398;1398;;;931.74;;119.08252707444;;
Open;Dividends;HKD;2025-01-27;939;939;;;852.52;;108.95768774711999;;
Open;Dividends;HKD;2025-03-19;14;14;;;810.0;;103.52335086;;
Open;Dividends;HKD;2025-03-20;16;16;;;4275.0;;546.37324065;;
Open;Dividends;HKD;2025-04-23;808;808;;;1120.0;;143.14339872;;
Open;Dividends;HKD;2025-04-25;5;5;;;6698.42;;856.10232576252;;
Open;Dividends;HKD;2025-05-08;1972;1972;;;4256.0;;543.944915136;;
Open;Dividends;HKD;2025-05-19;2888;2888;;;277.72;;35.49445061832;;
Open;Dividends;HKD;2025-05-28;939;939;;;887.1;;113.37724018259999;;
Open;Dividends;HKD;2025-05-29;215;215;;;2605.0;;332.93620862999995;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;319.22;;40.79842476732;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;10.78;;1.37775521268;;
Open;Dividends;HKD;2025-05-30;700;700;;;2727.0;;348.528614562;;
Open;Dividends;HKD;2025-06-12;1299;1299;;;785.88;;100.44065552327999;;
Open;Dividends;HKD;2025-06-12;27;27;;;500.0;;63.903302999999994;;
Open;Dividends;HKD;2025-06-13;66;66;;;445.0;;56.87393967;;
Open;Dividends;HKD;2025-06-20;1883;1883;;;1280.0;;163.59245568;;
Open;Dividends;HKD;2025-06-20;5;5;;;1881.53;;240.47196338717998;;
Open;Dividends;HKD;2025-06-27;386;386;;;304.6;;38.9298921876;;
Open;Dividends;HKD;2025-06-30;2318;2318;;;881.33;;112.63979606598;;
Open;Dividends;HKD;2025-07-24;857;857;;;545.4;;69.70572291239999;;
Open;Dividends;HKD;2025-07-30;2828;2828;;;520.0;;66.45943512;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;133.58;;17.07240642948;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;961.35;;122.86688067809999;;
Open;Dividends;HKD;2025-07-31;390;390;;;1948.0;;248.96726848799997;;
Open;Dividends;HKD;2025-08-22;1398;1398;;;1082.3;;138.3250896738;;
Open;Dividends;HKD;2025-09-09;14;14;;;270.0;;34.50778362;;
Open;Dividends;HKD;2025-09-16;66;66;;;36.12;;4.616374608719999;;
Open;Dividends;HKD;2025-09-16;66;66;;;173.88;;22.223012651279998;;
Open;Dividends;HKD;2025-09-18;808;808;;;1170.0;;149.53372901999998;;
Open;Dividends;HKD;2025-09-26;5;5;;;1866.43;;238.54208363657997;;
Open;Dividends;HKD;2025-09-30;2888;2888;;;26.82;;3.42777317292;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;1.24;;0.95186290996;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.24;;0.18423153096;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.45;;0.34543412055;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.49;;0.37613937571;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.46;;0.35311043434;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.06;;0.04605788274;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;5.33;;4.09147525007;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.24;;0.18423153096;;
Open;Dividends;SGD;2025-04-16;D05;D05;;;132.0;;101.327342028;;
Open;Dividends;SGD;2025-05-09;O39;O39;;;328.0;;251.783092312;;
Open;Dividends;SGD;2025-05-09;O39;O39;;;128.0;;98.256816512;;
Open;Dividends;SGD;2025-05-13;9CI;9CI;;;144.0;;110.538918576;;
Open;Dividends;SGD;2025-05-13;BS6;BS6;;;840.0;;644.81035836;;
Open;Dividends;SGD;2025-05-15;F34;F34;;;900.0;;690.8682411;;
Open;Dividends;SGD;2025-05-27;D05;D05;;;132.0;;101.327342028;;
Open;Dividends;SGD;2025-05-27;D05;D05;;;33.0;;25.331835507;;
Open;Dividends;SGD;2025-08-21;O39;O39;;;328.0;;251.783092312;;
Open;Dividends;SGD;2025-08-25;D05;D05;;;132.0;;101.327342028;;
Open;Dividends;SGD;2025-08-25;D05;D05;;;33.0;;25.331835507;;
Open;Dividends;SGD;2025-08-28;F34;F34;;;360.0;;276.34729644;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;0.33;;0.25331835507;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;1.18;;0.90580502722;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;0.21;;0.16120258959;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;0.73;;0.56037090667;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;0.33;;0.25331835507;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;14.41;;11.06156817139;;
Open;Dividends;SGD;2025-11-24;D05;D05;;;132.0;;101.327342028;;
Open;Dividends;SGD;2025-11-24;D05;D05;;;33.0;;25.331835507;;
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
Open;Trades;HKD;2024-03-24;1299;1299;Stocks;55.2;-33120.0;600.0;-4232.954790719999;-61.43992;O
Open;Trades;HKD;2024-12-27;3968;3968;Stocks;39.7;-19850.0;500.0;-2536.9611290999997;-41.68725;O
Open;Trades;SGD;2024-09-09;C38U;C38UNR;Stocks;0.0;0.0;-0.36;0.0;0.0;C
Open;Trades;SGD;2024-05-16;F34;F34;Stocks;3.16;-3160.0;1000.0;-2425.71515764;-3.90576;O
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
Open;Transaction Fees;HKD;2024-03-24;1299;1299;Stocks;55.2;-34.94392;0.0;-4.466063815535519;;
Open;Transaction Fees;HKD;2024-12-27;3968;3968;Stocks;39.7;-20.565725;0.0;-2.6284355121793497;;
Open;Withholding Tax;HKD;2024-07-26;2318;2318;;;-82.34;;-10.52359593804;;
Open;Withholding Tax;HKD;2024-07-26;386;386;;;-43.88;;-5.60815387128;;
Open;Withholding Tax;HKD;2024-07-29;857;857;;;-50.57;;-6.46318006542;;
Open;Withholding Tax;HKD;2024-08-02;939;939;;;-175.36;;-22.41216642816;;
Open;Withholding Tax;HKD;2024-08-08;390;390;;;-230.1;;-29.408300040599997;;
Open;Withholding Tax;HKD;2024-08-19;1398;1398;;;-201.43;;-25.74408464658;;
Open;Withholding Tax;HKD;2024-09-26;386;386;;;-31.86;;-4.07191846716;;
Open;Withholding Tax;HKD;2024-10-18;2318;2318;;;-51.02;;-6.52069303812;;
Open;Withholding Tax;HKD;2024-10-28;857;857;;;-48.07;;-6.1436635504199995;;
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
Open;Dividends;HKD;2024-03-20;16;16;;;4275.0;;546.37324065;;
Open;Dividends;HKD;2024-03-22;14;14;;;810.0;;103.52335086;;
Open;Dividends;HKD;2024-04-17;808;808;;;1328.0;;169.72717276799997;;
Open;Dividends;HKD;2024-04-25;5;5;;;5119.61;;654.3199781436599;;
Open;Dividends;HKD;2024-04-25;5;5;;;703.64;;89.92984024584;;
Open;Dividends;HKD;2024-04-26;27;27;;;300.0;;38.3419818;;
Open;Dividends;HKD;2024-05-02;1972;1972;;;4032.0;;515.316235392;;
Open;Dividends;HKD;2024-05-17;2888;2888;;;210.1;;26.852167920599996;;
Open;Dividends;HKD;2024-05-27;215;215;;;2605.0;;332.93620862999995;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;41.55;;5.3103644792999996;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;183.45;;23.446121870699997;;
Open;Dividends;HKD;2024-05-31;700;700;;;2060.4;;263.3327310024;;
Open;Dividends;HKD;2024-06-13;1883;1883;;;1930.0;;246.66674958;;
Open;Dividends;HKD;2024-06-14;1299;1299;;;714.42;;91.30759545851998;;
Open;Dividends;HKD;2024-06-21;5;5;;;5808.32;;742.3416657619199;;
Open;Dividends;HKD;2024-07-16;66;66;;;16.91;;2.16120970746;;
Open;Dividends;HKD;2024-07-16;66;66;;;428.09;;54.712729962539996;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;741.05;;94.71108537629999;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;823.39;;105.23468131434;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;-741.05;;-94.71108537629999;;
Open;Dividends;HKD;2024-07-26;386;386;;;438.8;;56.0815387128;;
Open;Dividends;HKD;2024-07-29;857;857;;;505.74;;64.63691291843999;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;36.4;;4.652160458399999;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;223.6;;28.577557101599997;;
Open;Dividends;HKD;2024-08-02;939;939;;;1753.57;;224.11783008341996;;
Open;Dividends;HKD;2024-08-08;390;390;;;2301.0;;294.083000406;;
Open;Dividends;HKD;2024-08-19;1398;1398;;;2014.29;;257.43956839974;;
Open;Dividends;HKD;2024-09-09;215;215;;;1140.0;;145.69953084;;
Open;Dividends;HKD;2024-09-16;808;808;;;1276.0;;163.081229256;;
Open;Dividends;HKD;2024-09-17;66;66;;;72.66;;9.286427991959998;;
Open;Dividends;HKD;2024-09-17;66;66;;;137.34;;17.55295926804;;
Open;Dividends;HKD;2024-09-20;14;14;;;270.0;;34.50778362;;
Open;Dividends;HKD;2024-09-25;1299;1299;;;267.0;;34.124363802;;
Open;Dividends;HKD;2024-09-26;386;386;;;318.6;;40.7191846716;;
Open;Dividends;HKD;2024-09-27;1883;1883;;;600.0;;76.6839636;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;21.85;;2.7925743411;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;358.15;;45.773935938899996;;
Open;Dividends;HKD;2024-09-27;5;5;;;1869.78;;238.97023576667996;;
Open;Dividends;HKD;2024-10-09;1972;1972;;;1904.0;;243.34377782399997;;
Open;Dividends;HKD;2024-10-10;2888;2888;;;89.69;;11.462974492139999;;
Open;Dividends;HKD;2024-10-18;2318;2318;;;510.24;;65.21204264544;;
Open;Dividends;HKD;2024-10-25;27;27;;;500.0;;63.903302999999994;;
Open;Dividends;HKD;2024-10-28;857;857;;;480.66;;61.43152323996;;
Open;Dividends;HKD;2024-11-21;16;16;;;12325.6;;1575.2931029136;;
Open;Dividends;HKD;2024-11-21;16;16;;;274.4;;35.070132686399994;;
Open;Dividends;HKD;2024-11-29;2800;2800;;;930.0;;118.86014357999998;;
Open;Dividends;HKD;2024-12-19;5;5;;;1865.55;;238.42961382329997;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;0.31;;0.23796572749;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;1.62;;1.24356283398;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;0.17;;0.13049733443;;
Open;Dividends;SGD;2024-03-28;C38U;C38U;;;10.08;;7.73772430032;;
Open;Dividends;SGD;2024-04-19;D05;D05;;;108.0;;82.904188932;;
Open;Dividends;SGD;2024-05-08;BS6;BS6;;;455.0;;349.272277445;;
Open;Dividends;SGD;2024-05-10;9CI;9CI;;;144.0;;110.538918576;;
Open;Dividends;SGD;2024-05-14;F34;F34;;;880.0;;675.51561352;;
Open;Dividends;SGD;2024-05-20;D05;D05;;;118.8;;91.1946078252;;
Open;Dividends;SGD;2024-05-21;O39;O39;;;336.0;;257.924143344;;
Open;Dividends;SGD;2024-08-23;O39;O39;;;352.0;;270.206245408;;
Open;Dividends;SGD;2024-08-26;D05;D05;;;118.8;;91.1946078252;;
Open;Dividends;SGD;2024-08-29;F34;F34;;;540.0;;414.52094466;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;1.03;;0.79066032037;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;0.39;;0.29937623781;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;0.32;;0.24564204128;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;0.11;;0.08443945169;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;0.09;;0.06908682411;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;9.84;;7.55349276936;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;3.83;;2.9400281815700002;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;0.07;;0.05373419653000001;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;0.09;;0.06908682411;;
Open;Dividends;SGD;2024-11-25;D05;D05;;;118.8;;91.1946078252;;
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
Open;Trades;HKD;2023-04-25;3918;3918;Stocks;6.1031;4.96487185;-0.8135;0.634543420373441;0.0;C
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
Open;Withholding Tax;HKD;2023-06-30;386;386;;;-43.34;;-5.53913830404;;
Open;Withholding Tax;HKD;2023-07-10;2318;2318;;;-85.01;;-10.86483957606;;
Open;Withholding Tax;HKD;2023-08-04;939;939;;;-168.79;;-21.572477026739996;;
Open;Withholding Tax;HKD;2023-08-10;390;390;;;-217.64;;-27.815829729839997;;
Open;Withholding Tax;HKD;2023-08-18;1398;1398;;;-197.54;;-25.246916949239996;;
Open;Withholding Tax;HKD;2023-09-28;386;386;;;-31.56;;-4.033576485359999;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.37243737516;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;49.86;;6.37243737516;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.37243737516;;
Open;Withholding Tax;HKD;2023-10-30;857;857;;;-0.87;;-0.11119174721999998;;
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
Open;Dividends;HKD;2023-03-16;16;16;;;5571.25;;712.0425536775;;
Open;Dividends;HKD;2023-03-16;16;16;;;53.75;;6.8696050725;;
Open;Dividends;HKD;2023-03-20;14;14;;;38.61;;4.934613057659999;;
Open;Dividends;HKD;2023-03-20;14;14;;;1131.39;;144.59911596234;;
Open;Dividends;HKD;2023-04-11;808;808;;;1500.0;;191.70990899999998;;
Open;Dividends;HKD;2023-04-27;5;5;;;4330.56;;553.47417567936;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;3394.56;;433.84719246335993;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;413.44;;52.84036318464;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;1.1;;0.1405872666;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;139.46;;17.823909272759998;;
Open;Dividends;HKD;2023-05-29;215;215;;;2605.0;;332.93620862999995;;
Open;Dividends;HKD;2023-05-31;2800;2800;;;150.0;;19.1709909;;
Open;Dividends;HKD;2023-06-05;700;700;;;1454.4;;185.8819277664;;
Open;Dividends;HKD;2023-06-14;1883;1883;;;1850.0;;236.44222109999998;;
Open;Dividends;HKD;2023-06-23;5;5;;;1879.72;;240.24063343031997;;
Open;Dividends;HKD;2023-06-30;386;386;;;433.4;;55.391383040399994;;
Open;Dividends;HKD;2023-07-10;2318;2318;;;850.13;;108.65222995877998;;
Open;Dividends;HKD;2023-07-18;66;66;;;445.0;;56.87393967;;
Open;Dividends;HKD;2023-07-28;2828;2828;;;80.0;;10.22452848;;
Open;Dividends;HKD;2023-07-28;857;857;;;485.54;;62.055219477239994;;
Open;Dividends;HKD;2023-08-04;939;939;;;1687.94;;215.72988253164;;
Open;Dividends;HKD;2023-08-10;390;390;;;2176.4;;278.1582972984;;
Open;Dividends;HKD;2023-08-18;1398;1398;;;1975.41;;252.47044755846;;
Open;Dividends;HKD;2023-09-04;215;215;;;1140.0;;145.69953084;;
Open;Dividends;HKD;2023-09-05;14;14;;;270.0;;34.50778362;;
Open;Dividends;HKD;2023-09-14;808;808;;;1490.0;;190.43184294;;
Open;Dividends;HKD;2023-09-21;5;5;;;1879.68;;240.23552116608;;
Open;Dividends;HKD;2023-09-28;2828;2828;;;568.0;;72.594152208;;
Open;Dividends;HKD;2023-09-28;386;386;;;315.6;;40.3357648536;;
Open;Dividends;HKD;2023-09-29;1883;1883;;;600.0;;76.6839636;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;645.48;;82.49660804087999;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;1202.52;;153.68999984712;;
Open;Dividends;HKD;2023-10-13;2888;2888;;;60.04;;7.673508624239999;;
Open;Dividends;HKD;2023-10-13;66;66;;;210.0;;26.83938726;;
Open;Dividends;HKD;2023-10-25;2318;2318;;;505.93;;64.66119617358;;
Open;Dividends;HKD;2023-10-27;27;27;;;200.0;;25.5613212;;
Open;Dividends;HKD;2023-10-30;857;857;;;8.7;;1.1119174721999998;;
Open;Dividends;HKD;2023-10-30;857;857;;;449.44;;57.44140100063999;;
Open;Dividends;HKD;2023-11-16;16;16;;;16650.0;;2127.9799899;;
Open;Dividends;HKD;2023-11-30;2800;2800;;;825.0;;105.44044994999999;;
Open;Dividends;HKD;2023-12-21;5;5;;;1872.67;;239.33959685802;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;0.13;;0.09979207927;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;0.2;;0.1535262758;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;9.58;;7.3539086108200005;;
Open;Dividends;SGD;2023-04-21;D05;D05;;;84.0;;64.481035836;;
Open;Dividends;SGD;2023-04-21;D05;D05;;;100.0;;76.7631379;;
Open;Dividends;SGD;2023-05-10;9CI;9CI;;;144.0;;110.538918576;;
Open;Dividends;SGD;2023-05-12;F34;F34;;;880.0;;675.51561352;;
Open;Dividends;SGD;2023-05-19;O39;O39;;;320.0;;245.64204128;;
Open;Dividends;SGD;2023-05-22;D05;D05;;;84.0;;64.481035836;;
Open;Dividends;SGD;2023-05-25;BS6;BS6;;;350.0;;268.67098265;;
Open;Dividends;SGD;2023-08-24;D05;D05;;;96.0;;73.692612384;;
Open;Dividends;SGD;2023-08-25;O39;O39;;;320.0;;245.64204128;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;1.11;;0.8520708306900001;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;0.5;;0.3838156895;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;0.28;;0.21493678612000003;;
Open;Dividends;SGD;2023-08-30;F34;F34;;;480.0;;368.46306192;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;0.04;;0.03070525516;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;0.44;;0.33775780676;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;9.32;;7.15432445228;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.3;;0.2302894137;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.08;;0.06141051032;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.1;;0.0767631379;;
Open;Dividends;SGD;2023-11-27;D05;D05;;;96.0;;73.692612384;;
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
Open;Trades;HKD;2022-10-31;16;16;Stocks;85.0;-255000.0;3000.0;-32590.68453;-543.2675;"O;P"
Open;Trades;HKD;2022-01-18;200;200;Stocks;9.2;-18400.0;2000.0;-2351.6415503999997;-42.5244;O
Open;Trades;HKD;2022-10-31;2800;2800;Stocks;24.0;-24000.0;1000.0;-3067.3585439999997;-23.584;"A;O"
Open;Trades;HKD;2022-09-13;3918;3918;Stocks;6.0527;5.74280176;-0.9488;0.7339680018764265;0.0;C
Open;Trades;HKD;2022-06-29;700;700;Stocks;485.3;-99971.8;206.0;-12777.056453710798;-220.3252263;"A;O"
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;-0.0476;0.0;0.0;O
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;0.0476;0.0;0.0;Ca
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;221.0;10.5196;-0.0476;1.3444743724776;0.0;C
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
Open;Transaction Fees;HKD;2022-01-18;200;200;Stocks;9.2;-24.5244;0.0;-3.1343803281863996;;
Open;Transaction Fees;HKD;2022-06-29;700;700;Stocks;485.3;-132.8491963;0.0;-16.979004888930756;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-225.845;0.0;-28.864482932069997;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-113.4225;0.0;-14.496144769034998;;
Open;Transaction Fees;HKD;2022-10-31;2800;2800;Stocks;24.0;-0.684;0.0;-0.087419718504;;
Open;Withholding Tax;HKD;2022-06-20;2318;2318;;;-90.17;;-11.524321663019998;;
Open;Withholding Tax;HKD;2022-06-23;386;386;;;-72.32;;-9.242973745919999;;
Open;Withholding Tax;HKD;2022-07-27;1398;1398;;;-205.93;;-26.31921437358;;
Open;Withholding Tax;HKD;2022-07-29;857;857;;;-22.62;;-2.89098542772;;
Open;Withholding Tax;HKD;2022-07-29;939;939;;;-170.38;;-21.775689530279998;;
Open;Withholding Tax;HKD;2022-08-09;390;390;;;-229.56;;-29.33928447336;;
Open;Withholding Tax;HKD;2022-09-29;386;386;;;-37.02;;-4.73140055412;;
Open;Withholding Tax;HKD;2022-10-20;2318;2318;;;-42.38;;-5.41644396228;;
Open;Withholding Tax;HKD;2022-10-28;857;857;;;-46.68;;-5.9660123680799995;;
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
Open;Dividends;HKD;2022-03-17;16;16;;;1875.0;;239.63738625;;
Open;Dividends;HKD;2022-03-25;14;14;;;1170.0;;149.53372901999998;;
Open;Dividends;HKD;2022-04-11;808;808;;;1724.0;;220.338588744;;
Open;Dividends;HKD;2022-04-28;5;5;;;3388.17;;433.03050825101997;;
Open;Dividends;HKD;2022-04-29;27;27;;;300.0;;38.3419818;;
Open;Dividends;HKD;2022-05-05;1972;1972;;;3584.0;;458.05887590399993;;
Open;Dividends;HKD;2022-05-12;2888;2888;;;90.34;;11.54604878604;;
Open;Dividends;HKD;2022-06-06;700;700;;;640.0;;81.79622784;;
Open;Dividends;HKD;2022-06-20;2318;2318;;;901.7;;115.2432166302;;
Open;Dividends;HKD;2022-06-23;386;386;;;723.2;;92.4297374592;;
Open;Dividends;HKD;2022-07-19;66;66;;;510.0;;65.18136906;;
Open;Dividends;HKD;2022-07-27;1398;1398;;;2059.31;;263.19342180186;;
Open;Dividends;HKD;2022-07-29;857;857;;;226.22;;28.912410409319996;;
Open;Dividends;HKD;2022-07-29;939;939;;;1703.8;;217.7568953028;;
Open;Dividends;HKD;2022-08-09;390;390;;;2295.6;;293.3928447336;;
Open;Dividends;HKD;2022-09-08;808;808;;;1750.0;;223.66156049999998;;
Open;Dividends;HKD;2022-09-16;14;14;;;270.0;;34.50778362;;
Open;Dividends;HKD;2022-09-29;386;386;;;370.2;;47.3140055412;;
Open;Dividends;HKD;2022-09-29;5;5;;;1695.13;;216.64881202878;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;1755.52;;224.36705296511997;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;36.48;;4.662384986879999;;
Open;Dividends;HKD;2022-10-14;2888;2888;;;40.18;;5.135269429079999;;
Open;Dividends;HKD;2022-10-14;66;66;;;210.0;;26.83938726;;
Open;Dividends;HKD;2022-10-20;2318;2318;;;531.06;;67.87297618235999;;
Open;Dividends;HKD;2022-10-28;857;857;;;466.76;;59.655011416559994;;
Open;Dividends;HKD;2022-11-17;16;16;;;16650.0;;2127.9799899;;
Open;Dividends;SGD;2022-01-28;C38U;C38U;;;0.48;;0.36846306192;;
Open;Dividends;SGD;2022-01-28;C38U;C38U;;;8.49;;6.51719040771;;
Open;Dividends;SGD;2022-03-15;C38U;C38U;;;0.19;;0.14584996201;;
Open;Dividends;SGD;2022-03-15;C38U;C38U;;;0.5;;0.3838156895;;
Open;Dividends;SGD;2022-04-22;D05;D05;;;72.0;;55.269459288;;
Open;Dividends;SGD;2022-05-06;F34;F34;;;840.0;;644.81035836;;
Open;Dividends;SGD;2022-05-20;9CI;9CI;;;36.0;;27.634729644;;
Open;Dividends;SGD;2022-05-20;9CI;9CI;;;144.0;;110.538918576;;
Open;Dividends;SGD;2022-05-20;O39;O39;;;224.0;;171.949428896;;
Open;Dividends;SGD;2022-05-25;D05;D05;;;72.0;;55.269459288;;
Open;Dividends;SGD;2022-05-27;BS6;BS6;;;350.0;;268.67098265;;
Open;Dividends;SGD;2022-08-24;F34;F34;;;480.0;;368.46306192;;
Open;Dividends;SGD;2022-08-25;O39;O39;;;224.0;;171.949428896;;
Open;Dividends;SGD;2022-08-26;D05;D05;;;72.0;;55.269459288;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;1.3;;0.9979207927;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;0.06;;0.04605788274;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;8.31;;6.379016759490001;;
Open;Dividends;SGD;2022-11-24;D05;D05;;;72.0;;55.269459288;;
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
Open;Trades;HKD;2021-09-19;1398;1398;Stocks;4.18;-12540.0;3000.0;-1602.69483924;-35.33858;O
Open;Trades;HKD;2021-05-10;14;14;Stocks;28.5;-28500.0;1000.0;-3642.4882709999997;-52.5695;O
Open;Trades;HKD;2021-09-15;200;200;Stocks;9.33;-9330.0;1000.0;-1192.43563398;-31.25191;O
Open;Trades;HKD;2021-04-25;2318;2318;Stocks;86.3;-43150.0;500.0;-5514.8550489;-79.68505;O
Open;Trades;HKD;2021-04-25;390;390;Stocks;3.92;-39200.0;10000.0;-5010.018955199999;-72.4184;"O;P"
Open;Trades;HKD;2021-05-11;3918;3918;Stocks;7.8;-15600.0;2000.0;-1993.7830536;-34.4212;O
Open;Trades;HKD;2021-04-20;700;700;Stocks;614.5;-61450.0;100.0;-7853.715938699999;-112.81915;O
Open;Trades;HKD;2021-08-15;700;700;Stocks;455.8;-91160.0;200.0;-11650.850202959999;-194.38932;O
Open;Trades;HKD;2021-04-26;939;939;Stocks;6.38;-25520.0;4000.0;-3261.6245851199997;-47.10504;O
Open;Trades;SGD;2021-09-10;C38U;C38U;Stocks;2.0;1.2144;-0.6072;0.9322115466576;0.0;C
Open;Trades;USD;2021-12-20;BABA;BABA;Stocks;230.0;-46000.0;200.0;-46000.0;0.0;"A;O"
Open;Trades;USD;2021-02-09;FCX;FCX;Stocks;31.38;15690.0;-500.0;15690.0;-2.906249;"C;P"
Open;Trades;USD;2021-01-19;MSFT;MSFT;Stocks;213.0;-21300.0;100.0;-21300.0;-1.0;O
Open;Trades;EUR;2021-03-19;MC;MC 19MAR21 360.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-08-19;700;700 30AUG21 450.0 P;Equity and Index Options;32.3;6460.0;-2.0;825.6306747599999;-24.0;O
Open;Trades;HKD;2021-08-30;700;700 30AUG21 450.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-09-03;700;700 29JUN22 500.0 P;Equity and Index Options;67.08;13416.0;-2.0;1714.6534260959997;-32.832;O
Open;Trades;HKD;2021-12-06;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.05;1025.0;-1.0;131.00177115;-19.0;O
Open;Trades;HKD;2021-12-20;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.5;1250.0;-1.0;159.75825749999998;-19.0;O
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
Open;Corporate Actions;SGD;2021-09-20;9CI;C31;Stocks;;1141.2;-1200.0;876.0209297148;;
Open;Corporate Actions;SGD;2021-09-20;C38U;C38U;Stocks;;0.0;185.6072;0.0;;
Open;Corporate Actions;USD;2021-01-04;NOV;US62955J1034) (NOV;Stocks;;0.0;300.0;0.0;;
Open;Corporate Actions;USD;2021-01-04;NOV;US62955J1034) (NOV.OLD;Stocks;;0.0;-300.0;0.0;;
Open;Transaction Fees;HKD;2021-04-20;700;700;Stocks;614.5;-63.65915;0.0;-8.136059902344899;;
Open;Transaction Fees;HKD;2021-04-25;2318;2318;Stocks;86.3;-45.16505;0.0;-5.7723917503203;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-24.63504;0.0;-3.14852085107424;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-4.10584;0.0;-0.5247534751790399;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-12.31752;0.0;-1.57426042553712;;
Open;Transaction Fees;HKD;2021-04-26;939;939;Stocks;6.38;-26.68904;0.0;-3.4110356197982394;;
Open;Transaction Fees;HKD;2021-05-10;14;14;Stocks;28.5;-29.7695;0.0;-3.8047387573169997;;
Open;Transaction Fees;HKD;2021-05-11;3918;3918;Stocks;7.8;-16.4212;0.0;-2.0987378384471995;;
Open;Transaction Fees;HKD;2021-08-15;700;700;Stocks;455.8;-121.46132;0.0;-15.52355906947992;;
Open;Transaction Fees;HKD;2021-09-15;200;200;Stocks;9.33;-13.25191;0.0;-1.69368164011746;;
Open;Transaction Fees;HKD;2021-09-19;1398;1398;Stocks;4.18;-17.33858;0.0;-2.2159850626594797;;
Open;Withholding Tax;HKD;2021-06-28;386;386;;;-31.38;;-4.010571296279999;;
Open;Withholding Tax;HKD;2021-07-27;1398;1398;;;-95.97;;-12.265599977819999;;
Open;Withholding Tax;HKD;2021-07-30;857;857;;;-21.21;;-2.71077811326;;
Open;Withholding Tax;HKD;2021-08-05;390;390;;;-216.71;;-27.69696958626;;
Open;Withholding Tax;HKD;2021-08-05;939;939;;;-156.33;;-19.98000671598;;
Open;Withholding Tax;HKD;2021-09-30;386;386;;;-38.44;;-4.912885934639999;;
Open;Withholding Tax;HKD;2021-10-25;2318;2318;;;-52.84;;-6.75330106104;;
Open;Withholding Tax;HKD;2021-10-29;857;857;;;-31.31;;-4.001624833859999;;
Open;Withholding Tax;USD;2021-03-11;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-05-03;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-06-10;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-08-02;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-09-09;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-11-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-12-09;MSFT;MSFT;;;-18.6;;-18.6;;
Open;Withholding Tax;USD;2021-12-17;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;HKD;2021-03-18;16;16;;;1875.0;;239.63738625;;
Open;Dividends;HKD;2021-04-07;808;808;;;1784.0;;228.006985104;;
Open;Dividends;HKD;2021-04-29;5;5;;;2796.85;;357.45590599109994;;
Open;Dividends;HKD;2021-05-06;1972;1972;;;3416.0;;436.587366096;;
Open;Dividends;HKD;2021-05-20;2888;2888;;;89.41;;11.427188642459999;;
Open;Dividends;HKD;2021-06-07;700;700;;;320.0;;40.89811392;;
Open;Dividends;HKD;2021-06-28;386;386;;;313.8;;40.1057129628;;
Open;Dividends;HKD;2021-07-05;3918;3918;;;291.4;;37.242844988399995;;
Open;Dividends;HKD;2021-07-20;66;66;;;490.0;;62.62523693999999;;
Open;Dividends;HKD;2021-07-27;1398;1398;;;959.73;;122.65983397637999;;
Open;Dividends;HKD;2021-07-30;857;857;;;212.14;;27.112893396839997;;
Open;Dividends;HKD;2021-08-05;390;390;;;2167.1;;276.96969586259996;;
Open;Dividends;HKD;2021-08-05;939;939;;;1563.34;;199.80517942403998;;
Open;Dividends;HKD;2021-09-06;14;14;;;270.0;;34.50778362;;
Open;Dividends;HKD;2021-09-08;808;808;;;1802.0;;230.30750401199998;;
Open;Dividends;HKD;2021-09-30;386;386;;;384.4;;49.12885934639999;;
Open;Dividends;HKD;2021-09-30;5;5;;;1308.18;;167.19404583707998;;
Open;Dividends;HKD;2021-10-05;1972;1972;;;1736.0;;221.872268016;;
Open;Dividends;HKD;2021-10-19;66;66;;;125.0;;15.975825749999998;;
Open;Dividends;HKD;2021-10-22;2888;2888;;;29.9;;3.8214175193999993;;
Open;Dividends;HKD;2021-10-25;2318;2318;;;528.4;;67.5330106104;;
Open;Dividends;HKD;2021-10-29;857;857;;;313.06;;40.011136074359996;;
Open;Dividends;HKD;2021-11-18;16;16;;;5550.0;;709.3266633;;
Open;Dividends;SGD;2021-05-06;F34;F34;;;520.0;;399.16831708;;
Open;Dividends;SGD;2021-05-06;F34;F34;;;720.0;;552.69459288;;
Open;Dividends;SGD;2021-05-18;9CI;C31;;;108.0;;82.904188932;;
Open;Dividends;SGD;2021-05-24;D05;D05;;;36.0;;27.634729644;;
Open;Dividends;SGD;2021-05-28;BS6;BS6;;;315.0;;241.803884385;;
Open;Dividends;SGD;2021-06-25;D05;D05;;;36.0;;27.634729644;;
Open;Dividends;SGD;2021-06-29;O39;O39;;;127.2;;97.6427114088;;
Open;Dividends;SGD;2021-08-26;D05;D05;;;66.0;;50.663671014;;
Open;Dividends;SGD;2021-08-26;O39;O39;;;200.0;;153.5262758;;
Open;Dividends;SGD;2021-08-27;F34;F34;;;400.0;;307.0525516;;
Open;Dividends;SGD;2021-11-26;D05;D05;;;66.0;;50.663671014;;
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
Open;Trades;HKD;2020-03-18;16;16;Stocks;97.5;-48750.0;500.0;-6230.572042499999;-89.31625;O
Open;Trades;HKD;2020-03-18;16;16;Stocks;96.5;-48250.0;500.0;-6166.6687395;-88.90275;O
Open;Trades;HKD;2020-07-20;16;16;Stocks;94.05;-47025.0;500.0;-6010.10564715;-86.889675;O
Open;Trades;HKD;2020-01-29;1972;1972;Stocks;24.65;-24650.0;1000.0;-3150.4328379;-45.38555;O
Open;Trades;HKD;2020-05-04;1972;1972;Stocks;20.9;-20900.0;1000.0;-2671.1580654;-39.5643;O
Open;Trades;HKD;2020-05-24;1972;1972;Stocks;17.78;-35560.0;2000.0;-4544.80290936;-67.40812;"O;P"
Open;Trades;HKD;2020-07-20;1972;1972;Stocks;18.2;-29120.0;1600.0;-3721.7283667199995;-55.08224;"O;P"
Open;Trades;HKD;2020-07-22;27;27;Stocks;51.5;-51500.0;1000.0;-6582.040209;-94.5905;O
Open;Trades;HKD;2020-03-18;2800;2800;Stocks;21.7;-10850.0;500.0;-1386.7016750999999;-18.29295;O
Open;Trades;HKD;2020-03-31;5;5;Stocks;40.8;-16320.0;400.0;-2085.80380992;-35.44064;O
Open;Trades;HKD;2020-05-14;5;5;Stocks;38.0;-45600.0;1200.0;-5827.9812335999995;-83.7112;O
Open;Trades;HKD;2020-05-21;66;66;Stocks;40.0;-20000.0;500.0;-2556.1321199999998;-38.54;O
Open;Trades;HKD;2020-09-29;700;700;Stocks;530.0;-53000.0;100.0;-6773.750117999999;-101.981;"A;O"
Open;Trades;HKD;2020-04-23;808;808;Stocks;2.38;-23800.0;10000.0;-3041.7972228;-48.6826;"O;P"
Open;Trades;HKD;2020-07-22;808;808;Stocks;2.29;-22900.0;10000.0;-2926.7712773999997;-41.9383;O
Open;Trades;SGD;2020-07-22;BS6;BS6;Stocks;0.935;-3085.5;3300.0;-2368.5266199045;-2.5;O
Open;Trades;SGD;2020-08-26;BS6;BS6;Stocks;0.92;-3404.0;3700.0;-2613.017214116;-2.7232;O
Open;Trades;SGD;2020-07-23;9CI;C31;Stocks;2.83;-3396.0;1200.0;-2606.876163084;-2.7168;O
Open;Trades;SGD;2020-07-22;D05;D05;Stocks;21.2;-4240.0;200.0;-3254.75704696;-3.392;O
Open;Trades;SGD;2020-09-08;F34;F34;Stocks;4.32;-4320.0;1000.0;-3316.16755728;-3.456;O
Open;Trades;SGD;2020-07-22;O39;O39;Stocks;9.13;-3652.0;400.0;-2803.389796108;-2.9216;O
Open;Trades;SGD;2020-08-26;O39;O39;Stocks;8.62;-3448.0;400.0;-2646.792994792;-2.7584;O
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
Open;Trades;HKD;2020-07-24;27;27 28AUG20 50.0 P;Equity and Index Options;2.18;4360.0;-2.0;557.2368021599999;-24.0;O
Open;Trades;HKD;2020-08-28;27;27 28AUG20 50.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2020-07-24;700;700 29SEP20 530.0 P;Equity and Index Options;38.02;3802.0;-1.0;485.92071601199996;-21.0;O
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
Open;Transaction Fees;HKD;2020-01-29;1972;1972;Stocks;24.65;-25.66555;0.0;-3.2802268366233;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;97.5;-50.31625;0.0;-6.430749139147499;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;96.5;-50.30275;0.0;-6.4290237499664995;;
Open;Transaction Fees;HKD;2020-03-18;2800;2800;Stocks;21.7;-0.29295;0.0;-0.037440945227699994;;
Open;Transaction Fees;HKD;2020-03-31;5;5;Stocks;40.8;-17.44064;0.0;-2.2290290048678396;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39163267050156;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39163267050156;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39163267050156;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39163267050156;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39163267050156;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39163267050156;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39163267050156;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39163267050156;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-5.12852;0.0;-0.6554587350031199;;
Open;Transaction Fees;HKD;2020-05-04;1972;1972;Stocks;20.9;-21.5643;0.0;-2.7560599937657995;;
Open;Transaction Fees;HKD;2020-05-14;5;5;Stocks;38.0;-47.2312;0.0;-6.0364593693072;;
Open;Transaction Fees;HKD;2020-05-21;66;66;Stocks;40.0;-20.54;0.0;-2.6251476872399997;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.523497391855272;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.523497391855272;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.9661829614210877;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.9661829614210877;;
Open;Transaction Fees;HKD;2020-07-20;16;16;Stocks;94.05;-49.269675;0.0;-6.29698994047305;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.0475745144753599;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.0475745144753599;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-4.09828;0.0;-0.5237872572376799;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-11.29484;0.0;-1.4435551657130399;;
Open;Transaction Fees;HKD;2020-07-22;808;808;Stocks;2.29;-23.6183;0.0;-3.0185747624898;;
Open;Transaction Fees;HKD;2020-07-22;27;27;Stocks;51.5;-53.3905;0.0;-6.823658597643;;
Open;Transaction Fees;HKD;2020-09-29;700;700;Stocks;530.0;-54.431;0.0;-6.956641371185999;;
Open;Withholding Tax;HKD;2020-06-19;386;386;;;-41.54;;-5.309086413239999;;
Open;Withholding Tax;HKD;2020-07-21;1398;1398;;;-74.27;;-9.492196627619998;;
Open;Withholding Tax;HKD;2020-07-31;857;857;;;-14.44;;-1.8455273906399998;;
Open;Withholding Tax;HKD;2020-11-05;386;386;;;-15.98;;-2.0423495638799998;;
Open;Withholding Tax;HKD;2020-11-13;857;857;;;-19.59;;-2.5037314115399996;;
Open;Withholding Tax;USD;2020-02-03;FCX;FCX;;;-16.5;;-16.5;;
Open;Withholding Tax;USD;2020-03-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;HKD;2020-05-07;1972;1972;;;590.0;;75.40589754;;
Open;Dividends;HKD;2020-05-29;2800;2800;;;45.0;;5.751297269999999;;
Open;Dividends;HKD;2020-06-19;386;386;;;415.4;;53.09086413239999;;
Open;Dividends;HKD;2020-07-02;200;200;;;30.1;;3.8469788406;;
Open;Dividends;HKD;2020-07-21;1398;1398;;;862.3;;110.20763635379998;;
Open;Dividends;HKD;2020-07-30;2828;2828;;;488.0;;62.36962372799999;;
Open;Dividends;HKD;2020-07-31;857;857;;;144.4;;18.4552739064;;
Open;Dividends;HKD;2020-09-09;808;808;;;1798.0;;229.79627758799998;;
Open;Dividends;HKD;2020-10-06;1972;1972;;;1680.0;;214.71509808;;
Open;Dividends;HKD;2020-10-09;66;66;;;5.75;;0.7348879844999999;;
Open;Dividends;HKD;2020-10-09;66;66;;;119.25;;15.240937765499998;;
Open;Dividends;HKD;2020-11-05;386;386;;;159.8;;20.4234956388;;
Open;Dividends;HKD;2020-11-13;857;857;;;195.86;;25.03220185116;;
Open;Dividends;HKD;2020-11-19;16;16;;;5550.0;;709.3266633;;
Open;Dividends;SGD;2020-06-24;F34;F34;;;665.0;;510.474867035;;
Open;Dividends;SGD;2020-08-27;F34;F34;;;280.0;;214.93678612;;
Open;Dividends;SGD;2020-10-05;D05;D05;;;36.0;;27.634729644;;
Open;Dividends;SGD;2020-10-07;O39;O39;;;63.6;;48.8213557044;;
Open;Dividends;SGD;2020-12-29;D05;D05;;;36.0;;27.634729644;;
Open;Dividends;USD;2020-02-03;FCX;FCX;;;55.0;;55.0;;
Open;Dividends;USD;2020-03-24;BHP;BHP;;;1560.0;;1560.0;;
Open;Dividends;USD;2020-03-27;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2020-04-02;SOUHY;SOUHY;;;11.0;;11.0;;
Open;Dividends;USD;2020-04-02;SOUHY;SOUHY;;;11.0;;11.0;;
Open;Dividends;USD;2020-05-13;H78;H78;;;32.0;;32.0;;
Open;Dividends;USD;2020-09-22;BHP;BHP;;;1320.0;;1320.0;;
Open;Dividends;USD;2020-10-08;SOUHY;SOUHY;;;15.0;;15.0;;
Open;Dividends;USD;2020-10-14;H78;H78;;;60.0;;60.0;;
Open;Trades;HKD;2019-03-08;1398;1398;Stocks;5.9;-17700.0;3000.0;-2262.1769262;-36.4779;O
Open;Trades;USD;2019-02-11;AMZN;AMZN;Stocks;1800.0;-180000.0;100.0;-180000.0;0.0;"A;O"
Open;Trades;USD;2019-02-19;AMZN;AMZN;Stocks;1628.88;81444.0;-50.0;81444.0;-2.064722;C
Open;Trades;USD;2019-10-14;FCX;FCX;Stocks;15.0;-1500.0;100.0;-1500.0;0.0;"A;O"
Open;Trades;USD;2019-02-11;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2019-03-08;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;3.8;1900.0;-5.0;1900.0;-3.9787;O
Open;Trades;USD;2019-10-14;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2019-03-08;1398;1398;Stocks;5.9;-18.4779;0.0;-2.3615976850074;;
Open;Withholding Tax;HKD;2019-06-21;386;386;;;-60.6;;-7.7450803236;;
Open;Withholding Tax;HKD;2019-07-23;1398;1398;;;-85.12;;-10.87889830272;;
Open;Withholding Tax;HKD;2019-08-02;857;857;;;-20.47;;-2.6162012248199997;;
Open;Withholding Tax;HKD;2019-09-26;386;386;;;-26.78;;-3.4226609086799997;;
Open;Withholding Tax;HKD;2019-11-01;857;857;;;-17.26;;-2.20594201956;;
Open;Withholding Tax;USD;2019-02-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-03-29;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-05-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-06-28;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-08-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-09-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-11-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-12-20;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;CNH;2019-07-31;3188;3188;;;390.0;;58.091925839999995;;
Open;Dividends;HKD;2019-01-04;200;200;;;24.38;;3.1159250542799994;;
Open;Dividends;HKD;2019-04-08;5;5;;;1318.35;;168.49383902009998;;
Open;Dividends;HKD;2019-05-16;2888;2888;;;150.56;;19.24256259936;;
Open;Dividends;HKD;2019-06-21;386;386;;;606.0;;77.450803236;;
Open;Dividends;HKD;2019-07-05;200;200;;;23.5;;3.0034552409999997;;
Open;Dividends;HKD;2019-07-05;5;5;;;625.02;;79.88168488211998;;
Open;Dividends;HKD;2019-07-23;1398;1398;;;854.32;;109.18773963792;;
Open;Dividends;HKD;2019-07-30;2828;2828;;;640.0;;81.79622784;;
Open;Dividends;HKD;2019-08-02;857;857;;;204.72;;26.16456838032;;
Open;Dividends;HKD;2019-09-26;386;386;;;267.8;;34.226609086799996;;
Open;Dividends;HKD;2019-09-26;5;5;;;625.44;;79.93536365664;;
Open;Dividends;HKD;2019-09-27;2828;2828;;;520.0;;66.45943512;;
Open;Dividends;HKD;2019-10-04;200;200;;;61.1;;7.8089836266;;
Open;Dividends;HKD;2019-10-21;2888;2888;;;70.24;;8.977136005439998;;
Open;Dividends;HKD;2019-11-01;857;857;;;172.56;;22.054307931359997;;
Open;Dividends;HKD;2019-11-20;5;5;;;626.63;;80.08745351777999;;
Open;Dividends;SGD;2019-05-16;F34;F34;;;490.0;;376.13937571;;
Open;Dividends;SGD;2019-08-30;F34;F34;;;210.0;;161.20258959;;
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
Open;Trades;HKD;2018-01-01;1398;1398;Stocks;6.48;6480.0;-1000.0;828.18680688;-25.17496;C
Open;Trades;HKD;2018-02-05;215;215;Stocks;3.35;6700.0;-2000.0;856.3042601999999;-25.1809;C
Open;Trades;HKD;2018-02-19;215;215;Stocks;3.4;6800.0;-2000.0;869.0849208;-25.1836;C
Open;Trades;USD;2018-08-17;FCX;FCX;Stocks;21.0;-21000.0;1000.0;-21000.0;0.0;"A;O"
Open;Trades;USD;2018-10-26;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;216.21;21621.0;-1.0;21621.0;-1.367373;O
Open;Trades;USD;2018-01-18;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;3.08;3080.0;-10.0;3080.0;-5.006148;O
Open;Trades;USD;2018-08-17;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;0.0;0.0;10.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2018-01-01;1398;1398;Stocks;6.48;-7.17496;0.0;-0.9170072857857599;;
Open;Transaction Fees;HKD;2018-02-05;215;215;Stocks;3.35;-7.1809;0.0;-0.9177664570253999;;
Open;Transaction Fees;HKD;2018-02-19;215;215;Stocks;3.4;-7.1836;0.0;-0.9181115348615999;;
Open;Withholding Tax;HKD;2018-06-14;386;386;;;-98.64;;-12.606843615839999;;
Open;Withholding Tax;HKD;2018-07-26;857;857;;;-14.86;;-1.8992061651599998;;
Open;Withholding Tax;HKD;2018-09-21;386;386;;;-36.5;;-4.664941119;;
Open;Withholding Tax;HKD;2018-11-01;857;857;;;-20.39;;-2.60597669634;;
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
Open;Dividends;HKD;2018-04-06;5;5;;;1317.81;;168.42482345285998;;
Open;Dividends;HKD;2018-05-17;2888;2888;;;110.45;;14.1162396327;;
Open;Dividends;HKD;2018-06-14;386;386;;;986.4;;126.06843615839999;;
Open;Dividends;HKD;2018-07-04;200;200;;;40.0;;5.11226424;;
Open;Dividends;HKD;2018-07-05;5;5;;;627.87;;80.24593370922;;
Open;Dividends;HKD;2018-07-26;857;857;;;148.62;;18.99461778372;;
Open;Dividends;HKD;2018-07-30;2828;2828;;;440.0;;56.23490664;;
Open;Dividends;HKD;2018-09-21;386;386;;;365.0;;46.649411189999995;;
Open;Dividends;HKD;2018-09-27;5;5;;;627.73;;80.22804078438;;
Open;Dividends;HKD;2018-09-28;2828;2828;;;800.0;;102.2452848;;
Open;Dividends;HKD;2018-10-05;200;200;;;45.0;;5.751297269999999;;
Open;Dividends;HKD;2018-10-22;2888;2888;;;60.13;;7.68501121878;;
Open;Dividends;HKD;2018-11-01;857;857;;;203.88;;26.057210831279995;;
Open;Dividends;HKD;2018-11-21;5;5;;;626.32;;80.04783346992;;
Open;Dividends;SGD;2018-05-16;F34;F34;;;490.0;;376.13937571;;
Open;Dividends;SGD;2018-08-31;F34;F34;;;245.0;;188.069687855;;
Open;Dividends;USD;2018-03-27;BHP;BHP;;;1320.0;;1320.0;;
Open;Dividends;USD;2018-03-30;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-04-05;SOUHY;SOUHY;;;73.0;;73.0;;
Open;Dividends;USD;2018-06-29;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-09-25;BHP;BHP;;;1512.0;;1512.0;;
Open;Dividends;USD;2018-09-28;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-10-11;SOUHY;SOUHY;;;62.0;;62.0;;
Open;Dividends;USD;2018-11-01;FCX;FCX;;;50.0;;50.0;;
Open;Dividends;USD;2018-12-21;NOV;NOV;;;15.0;;15.0;;
Open;Withholding Tax;HKD;2025-09-25;386;386;;;-19.34;;-2.47177976004;;
Open;Withholding Tax;HKD;2025-10-23;857;857;;;-48.11;;-6.14877581466;;
Open;Withholding Tax;HKD;2025-10-24;2318;2318;;;-52.02;;-6.64849964412;;
Open;Withholding Tax;HKD;2025-12-23;390;390;;;-89.85;;-11.483423549099998;;
Open;Dividends;HKD;2025-09-16;215;215;;;1140.0;;145.69953084;;
Open;Dividends;HKD;2025-09-23;1299;1299;;;294.0;;37.575142164;;
Open;Dividends;HKD;2025-09-25;386;386;;;193.4;;24.717797600399997;;
Open;Dividends;HKD;2025-09-26;1883;1883;;;600.0;;76.6839636;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;26.5;;3.386875059;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;173.5;;22.174446140999997;;
Open;Dividends;HKD;2025-09-30;3918;3918;;;159.42;;20.374929128519998;;
Open;Dividends;HKD;2025-10-09;1972;1972;;;1960.0;;250.50094775999997;;
Open;Dividends;HKD;2025-10-23;857;857;;;482.1;;61.6155647526;;
Open;Dividends;HKD;2025-10-24;2318;2318;;;520.24;;66.49010870544;;
Open;Dividends;HKD;2025-10-31;27;27;;;700.0;;89.46462419999999;;
Open;Dividends;HKD;2025-11-20;16;16;;;12600.0;;1610.3632355999998;;
Open;Dividends;HKD;2025-11-28;2800;2800;;;825.0;;105.44044994999999;;
Open;Dividends;HKD;2025-12-18;5;5;;;1866.53;;238.55486429718;;
Open;Dividends;HKD;2025-12-23;390;390;;;898.95;;114.8917484637;;
Open;Dividends;CNH;2022-07-29;3188;3188;;;370.0;;55.11285272;;
Open;Dividends;HKD;2022-05-26;215;215;;;2605.0;;332.93620862999995;;
Open;Dividends;HKD;2022-05-31;2800;2800;;;40.0;;5.11226424;;
Open;Dividends;HKD;2022-06-13;1883;1883;;;1700.0;;217.2712302;;
Open;Dividends;HKD;2022-07-28;2828;2828;;;460.0;;58.79103875999999;;
Open;Dividends;HKD;2022-09-02;215;215;;;1140.0;;145.69953084;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;283.5;;36.233172800999995;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;76.5;;9.777205359;;
Open;Dividends;HKD;2022-09-30;1883;1883;;;600.0;;76.6839636;;
Open;Dividends;HKD;2022-11-30;2800;2800;;;280.0;;35.78584968;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;243.6;;36.285110601599996;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;56.4;;8.4009861984;;
Open;Dividends;HKD;2021-05-26;215;215;;;2605.0;;332.93620862999995;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;12.22;;1.56179672532;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;52.78;;6.74563266468;;
Open;Dividends;HKD;2021-06-04;1883;1883;;;1600.0;;204.4905696;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;8.8;;1.1246981328;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;311.2;;39.773415787199994;;
Open;Dividends;HKD;2021-09-03;215;215;;;1109.72;;141.82954681031998;;
Open;Dividends;HKD;2021-09-03;215;215;;;30.28;;3.86998402968;;
Open;Dividends;HKD;2021-09-03;215;215;;;9637.06;;1231.6799304183598;;
Open;Dividends;HKD;2021-09-03;215;215;;;262.94;;33.605468981639994;;
Open;Dividends;HKD;2021-09-29;1883;1883;;;550.0;;70.2936333;;
Open;Dividends;HKD;2021-09-29;2828;2828;;;396.0;;50.611415975999996;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;234.9;;30.0217717494;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;35.1;;4.4860118706;;
Open;Trades;HKD;2020-09-24;1883;1883;Stocks;2.45;-24500.0;10000.0;-3131.2618469999998;-45.2615;O
Open;Trades;HKD;2020-09-02;215;215;Stocks;1.21;-60500.0;50000.0;-7732.299663;-111.0335;O
Open;Transaction Fees;HKD;2020-09-02;215;215;Stocks;1.21;-62.6335;0.0;-8.004975056901;;
Open;Transaction Fees;HKD;2020-09-24;1883;1883;Stocks;2.45;-25.6615;0.0;-3.279709219869;;
Open;Dividends;CNH;2020-07-31;3188;3188;;;380.0;;56.60238928;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;9.0;;1.150259454;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;591.0;;75.53370414599999;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;2.64;;0.33740943984;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;327.36;;41.83877054016;;

---

# [open_positions] START  
Symbol;Account;Asset Category;Description;Currency;Current Price;Cost Price;Quantity;Value;Value in USD;Unrealized Gain in USD;Weight;Unrealized Gain in USD
912797TC1 - United States Treasury B 12/24/26;IBKR;Treasury Bills;;USD;98.796803;97.133959459;740.0;73109.63;73109.63;;0.027693455070188906;1230.5042203400008
912797TV9 - United States Treasury B 02/18/27;IBKR;Treasury Bills;;USD;98.180338;97.064044118;680.0;66762.63;66762.63;;0.025289252513966298;759.0798397600054
912797UE5 - United States Treasury B 04/15/27;IBKR;Treasury Bills;;USD;97.58352;96.90631;1000.0;97583.52;97583.52;;0.03696400633830154;677.2099999999881
912797UX3 - United States Treasury B 05/13/27;IBKR;Treasury Bills;;USD;97.281503;96.625351852;1080.0;105064.02;105064.02;;0.03979757136458533;708.6432398400069
912797VF1 - United States Treasury B 06/10/27;IBKR;Treasury Bills;;USD;96.957345;96.38057197;2640.0;255967.39;255967.39;;0.09695879208250022;1522.6807991999954
912797VQ7 - United States Treasury B 07/08/27;IBKR;Treasury Bills;;USD;96.616642;96.420521978;1820.0;175842.29;175842.29;;0.06660792234284496;356.93844004000425
912797VR5 - United States Treasury B 08/05/27;IBKR;Treasury Bills;;USD;96.268497;96.29921;1000.0;96268.5;96268.5;;0.03646588526606523;-30.713000000005763
AMZN;IBKR;Stocks;AMAZON.COM INC;USD;259.77;207.8670155;300.0;77931.0;77931.0;;0.029519758848114693;15570.895349999992
AMZN 18SEP26 300 C;IBKR;Equity and Index Options;;USD;0.155;8.01939888;-1.0;-15.5;-15.5;;-5.871299767047488e-06;7.86439888
BABA;IBKR;Stocks;ALIBABA GROUP HOLDING-SP ADR;USD;114.02;111.16845686;500.0;57010.0;57010.0;;0.021595019336734016;1425.7715699999949
BHP;IBKR;Stocks;BHP GROUP LTD-SPON ADR;USD;93.92;35.102226747;1200.0;112704.0;112704.0;;0.042691546383569035;70581.3279036
BHP 18DEC26 95 C;IBKR;Equity and Index Options;;USD;6.05;4.0005117;-4.0;-2420.0;-2420.0;;-0.0009166803507261238;-8.1979532
BRK B;IBKR;Stocks;BERKSHIRE HATHAWAY INC-CL B;USD;504.03;469.0869395;100.0;50403.0;50403.0;;0.019092330461838357;3494.3060499999947
BRK B 18DEC26 510 P;IBKR;Equity and Index Options;;USD;16.9739;9.1593754;-1.0;-1697.39;-1697.39;;-0.0006429603555863701;-7.8145246
BRK B 18DEC26 545 C;IBKR;Equity and Index Options;;USD;4.975;18.66917949;-1.0;-497.5;-497.5;;-0.00018844978284555646;13.694179490000002
CPRT;IBKR;Stocks;COPART INC;USD;32.99;27.171926655;1100.0;36289.0;36289.0;;0.013746038532024921;6399.880679500002
CPRT 09/18/2026 25.00 P;Schwab;Equity and Index Options;SALESFORCE;USD;0.275;0.34332;-5.0;-137.5;-137.5;99.6875;-5.2084110836711585e-05;0.34159999999999996
CPRT 09/18/2026 27.50 P;Schwab;Equity and Index Options;SALESFORCE;USD;0.675;0.99332;-10.0;-675.0;-675.0;219.37499999999997;-0.00025568563501658415;3.1831999999999994
CPRT 18SEP26 25 P;IBKR;Equity and Index Options;;USD;0.0203;0.63450071;-5.0;-10.15;-10.15;;-3.844754363582709e-06;3.07100355
CPRT 18SEP26 32.5 C;IBKR;Equity and Index Options;;USD;1.5261;0.28450792;-6.0;-915.66;-915.66;;-0.0003468460867544969;-7.4495524799999995
CRM 09/18/2026 160.00 P;Schwab;Equity and Index Options;SALESFORCE;USD;2.085;6.94315;-2.0;-417.0;-417.0;;-0.0001579569034102453;9.7163
DNOW;IBKR;Stocks;DNOW INC;USD;16.03;26.81106924;25.0;400.75;400.75;;0.00015180150849317939;-269.5267309999999
FAST;IBKR;Stocks;FASTENAL CO;USD;49.31;42.17242457;100.0;4931.0;4931.0;;0.0018678309129878169;713.7575430000005
FCX;IBKR;Stocks;FREEPORT-MCMORAN INC;USD;75.74;11.2079574;500.0;37870.0;37870.0;;0.014344911108263764;32266.021299999997
CASH - Schwab;Schwab;Forex;;USD;1.0;;-1.0;;72675.14;0.0;0.027528873067880228;
Forex;IBKR;Forex;;USD;0.0;;-1.0;;123.526751292;0.0;4.679113458611952e-05;
GEHC 17JUN27 57.5 P;IBKR;Equity and Index Options;;USD;2.9009;5.33945409;-1.0;-290.09;-290.09;;-0.00010988421609179391;2.4385540900000002
GEHC 17JUN27 65 C;IBKR;Equity and Index Options;;USD;13.1389;8.8804028;1.0;1313.89;1313.89;;0.0004976930355436145;4.258497199999999
GEHC 17JUN27 80 C;IBKR;Equity and Index Options;;USD;6.1866;3.91948334;-1.0;-618.66;-618.66;;-0.0002343444073471999;-2.26711666
GOOG;IBKR;Stocks;ALPHABET INC-CL C;USD;335.41;93.120965215;200.0;67082.0;67082.0;;0.02541022780471481;48457.806957
GOOG 18DEC26 410 C;IBKR;Equity and Index Options;;USD;4.9556;32.055824165;-2.0;-991.12;-991.12;;-0.00037542984678168425;54.20044832999999
GOOG 21JAN28 250 C;IBKR;Equity and Index Options;;USD;116.2069;132.321833;2.0;23241.38;23241.38;;0.008803684450313687;-32.22986599999999
GOOG 21JAN28 250 P;IBKR;Equity and Index Options;;USD;13.0002;13.467856515;-2.0;-2600.04;-2600.04;;-0.0009848783384718805;0.9353130299999997
H78;IBKR;Stocks;HONGKONG LAND HOLDINGS LTD;USD;8.15;4.208;1000.0;8150.0;8150.0;;0.003087167296866905;3942.0
ICE;IBKR;Stocks;INTERCONTINENTAL EXCHANGE IN;USD;160.7;152.14704663;300.0;48210.0;48210.0;;0.018261636243184477;2565.886010999995
IVV;IBKR;Stocks;ISHARES CORE S&P 500 ETF;USD;770.74;402.65670378;100.0;77074.0;77074.0;;0.02919513279002697;36808.329622000005
IVV;Schwab;Stocks;ISHARES CORE S&P 500 ETF;USD;776.98;531.9820000000001;10.0;7769.8;7769.8;;0.00294314999548423;2449.9799999999996
META;IBKR;Stocks;META PLATFORMS INC-CLASS A;USD;572.34;230.10913779;200.0;114468.0;114468.0;;0.04335973817641237;68446.17244200001
META 15JAN27 460 P;IBKR;Equity and Index Options;;USD;11.325;19.71258137;-1.0;-1132.5;-1132.5;;-0.0004289836765278245;8.38758137
META 15JAN27 520 P;IBKR;Equity and Index Options;;USD;27.375;62.7404795;1.0;2737.5;2737.5;;0.0010369472975672578;-35.3654795
META 15JAN27 640 P;IBKR;Equity and Index Options;;USD;92.8037;77.2419796;-1.0;-9280.37;-9280.37;;-0.003515344143168677;-15.561720400000013
META 15JAN27 800 C;IBKR;Equity and Index Options;;USD;8.0962;35.535755475;-2.0;-1619.24;-1619.24;;-0.0006133576409544499;54.879110950000005
MSFT;Schwab;Stocks;MICROSOFT CORP;USD;499.45;470.3566;100.0;49945.0;49945.0;;0.018918843023560436;2909.3399999999974
MSFT 09/18/2026 520.00 C;Schwab;Equity and Index Options;MICROSOFT CORP;USD;11.325;13.4931;-1.0;-1132.5;-1132.5;;-0.0004289836765278245;2.168100000000001
MSFT 15JAN27 420 P;IBKR;Equity and Index Options;;USD;6.885;53.1437823;-2.0;-1377.0;-1377.0;;-0.0005215986954338316;92.5175646
NBIS 18DEC26 140 P;IBKR;Equity and Index Options;;USD;9.5456;24.136075115;-2.0;-1909.12;-1909.12;;-0.0007231623104042386;29.18095023
NBIS 18DEC26 170 P;IBKR;Equity and Index Options;;USD;18.9022;15.93624404;-1.0;-1890.22;-1890.22;;-0.0007160031126237743;-2.9659559600000005
NBIS 18DEC26 190 P;IBKR;Equity and Index Options;;USD;28.35;22.3868947;1.0;2835.0;2835.0;;0.0010738796670696534;5.963105300000002
NOV;IBKR;Stocks;NOV INC;USD;21.29;62.158401437;300.0;6387.0;6387.0;;0.0024193542975569225;-12260.520431100002
NVDA;Moomoo-mom;Stocks;NVIDIA;USD;223.96;0.0;0.4717;105.641932;105.641932;;4.001648069303526e-05;105.64193200000001
NVDA;Moomoo-me;Stocks;NVIDIA;USD;223.96;0.0;1.4431;323.196676;323.196676;;0.00012242481087156918;323.196676
NVO;IBKR;Stocks;NOVO-NORDISK A/S-SPONS ADR;USD;45.33;58.2644644;200.0;9066.0;9066.0;;0.0034341421734227435;-2586.892880000001
PDD;IBKR;Stocks;PDD HOLDINGS INC;USD;84.0;96.7605204;100.0;8400.0;8400.0;;0.0031818656802063802;-1276.0520400000005
PDD 18DEC26 100 C;IBKR;Equity and Index Options;;USD;1.845;12.173745;2.0;369.0;369.0;;0.000139774813809066;-20.65749
PDD 18DEC26 110 C;IBKR;Equity and Index Options;;USD;0.9225;11.5805045;1.0;92.25;92.25;;3.49437034522665e-05;-10.6580045
PDD 18DEC26 120 C;IBKR;Equity and Index Options;;USD;0.4587;5.8261019;-2.0;-91.74;-91.74;;-3.4750518750253966e-05;10.7348038
PDD 18DEC26 130 C;IBKR;Equity and Index Options;;USD;0.3627;6.3994626;-1.0;-36.27;-36.27;;-1.3738841454891121e-05;6.036762599999999
PDD 18DEC26 90 P;IBKR;Equity and Index Options;;USD;9.3135;8.553596778;-4.0;-3725.4;-3725.4;;-0.0014111574291715296;-3.0396128880000006
PDD 20NOV26 100 C;IBKR;Equity and Index Options;;USD;1.2956;10.7569825;1.0;129.56;129.56;;4.907649018184984e-05;-9.4613825
PDD 20NOV26 130 C;IBKR;Equity and Index Options;;USD;0.3272;3.12292012;-1.0;-32.72;-32.72;;-1.2394124411470567e-05;2.79572012
SKHY 18DEC26 115 P;IBKR;Equity and Index Options;;USD;3.5262;16.440827835;-2.0;-705.24;-705.24;;-0.00026714035146532713;25.829255670000002
SKHY 18DEC26 130 P;IBKR;Equity and Index Options;;USD;7.1253;24.4323006;1.0;712.53;712.53;;0.0002699017563235062;-17.307000600000002
SMCI 18DEC26 24 P;IBKR;Equity and Index Options;;USD;0.9;2.96300312;-4.0;-360.0;-360.0;;-0.00013636567200884487;8.252012480000001
SMCI 18DEC26 27 P;IBKR;Equity and Index Options;;USD;1.605;4.3169028;2.0;321.0;321.0;;0.00012159272420788668;-5.423805600000001
SOUHY;IBKR;Stocks;SOUTH32 - ADR;USD;18.37;7.17;300.0;5511.0;5511.0;;0.0020875311623354004;3360.0000000000005
TSM;IBKR;Stocks;TAIWAN SEMICONDUCTOR-SP ADR;USD;415.32;181.60807492;300.0;124596.0;124596.0;;0.047196159082261206;70113.577524
UNH;IBKR;Stocks;UNITEDHEALTH GROUP INC;USD;389.41;322.13551065;200.0;77882.0;77882.0;;0.029501197964980155;13454.897870000003
UNH 15JAN27 360 P;IBKR;Equity and Index Options;;USD;16.525;12.18938278;-1.0;-1652.5;-1652.5;;-0.0006259563138739338;-4.335617219999998
UNH 17JUN27 360 P;IBKR;Equity and Index Options;;USD;27.4962;21.4891912;-1.0;-2749.62;-2749.62;;-0.0010415382751915556;-6.007008800000001
UNH 18DEC26 440 C;IBKR;Equity and Index Options;;USD;9.5254;26.01244859;-1.0;-952.54;-952.54;;-0.00036081599226473637;16.48704859
9CI;IBKR;Stocks;CAPITALAND INVESTMENT LTD/SI;SGD;2.65;2.323057932;1200.0;3180.0;2441.06778522;;0.0009246606915296331;301.16518861434184
BS6;IBKR;Stocks;YANGZIJIANG SHIPBUILDING;SGD;4.84;0.171019587;7000.0;33880.0;26007.35112052;;0.009851416424221373;25088.391110686258
BS6;Moomoo-me;Stocks;YZJ Shipbuilding;SGD;4.2;4.015;200.0;840.0;652.6800000000001;;0.00024723096335203576;28.402361023000076
C38U;IBKR;Stocks;CAPITALAND INTEGRATED COMMER;SGD;2.37;1.364696005;222.0;526.14;403.88157374706;;0.0001529877283777991;171.31804202312514
CGP1;IBKR;Stocks;NOBLE GROUP LTD;SGD;0.0;85.85;50.0;0.0;0.0;;0.0;-3295.0576943575
D05;IBKR;Stocks;DBS GROUP HOLDINGS LTD;SGD;77.4;18.388145455;220.0;17028.0;13071.227121612;;0.004951296306719054;9965.85728197967
F34;IBKR;Stocks;WILMAR INTERNATIONAL LTD;SGD;3.84;3.040549529;9000.0;34560.0;26529.34045824;;0.010049142609831484;5523.149407463364
CASH - DBS;DBS;Forex;;SGD;0.763247271;;-0.777;;1043.95389;18.47775410253007;0.000395442982655827;
CASH - Moomoo-me;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;75899.04609;1343.3964121419413;0.028750067846925388;
CASH - Moomoo-mom;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;7040.27268;124.6112770323598;0.002666809764260309;
CASH - OCBC;OCBC;Forex;;SGD;0.763247271;;-0.777;;6154.438290000001;108.93220328133066;0.002331261425702267;
CASH - OCBC Joint;OCBC Joint;Forex;;SGD;0.763247271;;-0.777;;30062.78268;532.1046373023601;0.011387587673927951;
CASH - POSB;POSB;Forex;;SGD;0.763247271;;-0.777;;6713.0158200000005;118.81890263214063;0.002542846981945297;
CASH - UOB;UOB;Forex;;SGD;0.763247271;;-0.777;;6898.25262;122.09755322574074;0.002613013483925208;
Forex;IBKR;Forex;;SGD;0.767631379;;-0.78674;;14765.550675;358.630945;0.005593095112158411;
Fullerton SGD Cash Fund;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0;
Fullerton SGD Cash Fund;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0;
HMN;IBKR;Stocks;CAPITALAND ASCOTT TRUST;SGD;0.855;0.505252956;68.0;58.14;44.63008837506;;1.690558886966442e-05;18.25642278670877
O39;IBKR;Stocks;OVERSEA-CHINESE BANKING CORP;SGD;31.52;8.7231;800.0;25216.0;19356.592852864;;0.007332152200506675;13999.69262714008
P40U;Moomoo-mom;Stocks;StarhillGbl Reit;SGD;0.53;0.555;1200.0;636.0;494.172;;0.00018718915796654136;-23.028941370000023
P40U;Moomoo-me;Stocks;StarhillGbl Reit;SGD;0.53;0.5448;4200.0;2226.0;1729.602;;0.0006551620528828947;-47.71596651863976
Z74;Moomoo-mom;Stocks;Singtel;SGD;4.3;4.29;100.0;430.0;334.11;;0.0001265587074302088;0.7676313789999837
Z74;Moomoo-me;Stocks;Singtel;SGD;4.3;4.88;100.0;430.0;334.11;;0.0001265587074302088;-44.52261998200001
Z77;Moomoo-me;Stocks;Singtel 10;SGD;4.29;4.28;120.0;514.8;399.9996;;0.00015151726182574763;0.9211576547999804
1299;IBKR;Stocks;AIA GROUP LTD;HKD;76.25;55.302399867;600.0;45750.0;5847.1522245;;0.002214863451199874;1606.3450061063272
1398;IBKR;Stocks;IND & COMM BK OF CHINA - H;HKD;7.57;5.051969413;6000.0;45420.0;5804.97604452;;0.002198887386961711;1930.9256587719462
14;IBKR;Stocks;HYSAN DEVELOPMENT CO;HKD;15.93;28.5525695;1000.0;15930.0;2035.9592335799998;;0.0007712081918604151;-1613.247766794117
16;IBKR;Stocks;SUN HUNG KAI PROPERTIES;HKD;116.2;88.851861372;4500.0;522900.0;66830.0742774;;0.02531479997010741;15728.727503079794
1810;IBKR;Stocks;XIAOMI CORP-CLASS B;HKD;27.62;38.85377485;600.0;16572.0;2118.0110746319997;;0.0008022888986510231;-861.4503816879953
1883;IBKR;Stocks;CITIC TELECOM INTERNATIONAL;HKD;2.44;2.45452615;10000.0;24400.0;3118.4811864;;0.0011812605073065995;-18.56537929746902
1972;IBKR;Stocks;SWIRE PROPERTIES LTD;HKD;23.98;19.720971466;5600.0;134288.0;17162.893506528;;0.0065011930739831405;3048.259098011094
200;IBKR;Stocks;MELCO INTERNATIONAL DEVELOP.;HKD;3.085;11.460065577;4000.0;12340.0;1577.13351804;;0.0005974079778755507;-4281.554825695207
215;IBKR;Stocks;HUTCHISON TELECOMM HONG KONG;HKD;1.155;1.21222067;50000.0;57750.0;7380.831496499999;;0.0027958112416785292;-365.6589812873008
2318;IBKR;Stocks;PING AN INSURANCE GROUP CO-H;HKD;56.55;86.4593701;500.0;28275.0;3613.7317846499996;;0.0013688582313153318;-1911.3075400394405
27;IBKR;Stocks;GALAXY ENTERTAINMENT GROUP L;HKD;34.16;51.5945905;1000.0;34160.0;4365.87366096;;0.0016537647102292394;-2228.2558388048437
2800;IBKR;Stocks;TRACKER FUND OF HONG KONG-B;HKD;26.08;21.769917967;1500.0;39120.0;4999.794426719999;;0.0018938897969604167;826.2854343289644
2828;IBKR;Stocks;HANGSENG CHINA ENT INDEX;HKD;87.5;92.44076825;400.0;35000.0;4473.23121;;0.001694431055562745;-252.58512842602403
2888;IBKR;Stocks;STANDARD CHARTERED PLC;HKD;230.8;55.6;28.0;6462.4;825.9374106143999;;0.00031285975009910525;626.9680863936
3188;IBKR;Stocks;CHINAAMC ETF SERIES - CH;HKD;55.78;26.37987258;1000.0;55780.0;7129.052482679999;;0.0027004389794082835;3757.5305015177364
3690;IBKR;Stocks;MEITUAN-CLASS B;HKD;78.9;231.697888617;60.0;4734.0;605.0364728039999;;0.00022918390334382955;-1171.714772886288
386;IBKR;Stocks;CHINA PETROLEUM & CHEMICAL-H;HKD;4.785;5.815174;2000.0;9570.0;1223.1092194199998;;0.0004633058629067277;-263.32608505888794
390;IBKR;Stocks;CHINA RAILWAY GROUP LTD-H;HKD;3.445;3.92724184;10000.0;34450.0;4402.937576699999;;0.001667804281832473;-616.3369284159509
3918;IBKR;Stocks;NAGACORP LTD;HKD;3.3;7.594013956;2036.0;6718.8;858.7070243927999;;0.0003252726678889992;-1117.3636202599744
3968;IBKR;Stocks;CHINA MERCHANTS BANK - H;HKD;50.95;39.7833745;500.0;25475.0;3255.8732878499995;;0.0012333037468703122;713.5842528140265
5;IBKR;Stocks;HSBC HOLDINGS PLC;HKD;161.0;49.092166333;2400.0;386400.0;49384.4725584;;0.018706518853412707;34326.14497390033
66;IBKR;Stocks;MTR CORP;HKD;32.34;40.07708;500.0;16170.0;2066.6328190199997;;0.0007828271476699882;-494.4249675752399
700;IBKR;Stocks;TENCENT HOLDINGS LTD;HKD;453.0;452.856419642;706.0;319818.0;40874.85311770799;;0.015483130037941883;12.955465880437686
808;IBKR;Stocks;PROSPERITY REIT;HKD;1.47;2.339531045;20000.0;29400.0;3757.5142164;;0.001423322086672706;-2222.636233461666
857;IBKR;Stocks;PETROCHINA CO LTD-H;HKD;10.17;7.7972334;2000.0;20340.0;2599.5863660399996;;0.0009847065048613208;606.5104919523193
939;IBKR;Stocks;CHINA CONSTRUCTION BANK-H;HKD;9.5;6.39177626;4000.0;38000.0;4856.651027999999;;0.0018396680031824088;1589.0061075921053
9618;IBKR;Stocks;JD.COM INC-CLASS A;HKD;112.2;285.565311947;19.0;2131.8;272.4581226708;;0.00010320537497853314;-420.9874102434691
CASH - Citic;Citic;Forex;;HKD;0.128244528;;-0.1276;;56493.2093;-285.35623200400005;0.021399262361474512;
CASH - StanChart;StanChart;Forex;;HKD;0.128244528;;-0.1276;;1614.1055479999998;-8.153105177440239;0.0006114127437395133;
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;127600.0;-644.5280000000057;0.04833405485646835;
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;1276.0;-6.445279999999912;0.0004833405485646835;
CASH - StanChart Payroll;StanChart Payroll;Forex;;HKD;0.128244528;;-0.1276;;14971.866888;-75.62529327264019;0.005671246359469703;
Forex;IBKR;Forex;;HKD;0.127806606;;-0.12757;;1815.983525;-3.368128;0.0006878828159544888;
BARCl;IBKR;Stocks;;GBP;4.9665;3.5479;1000.0;4966.5;6587.848909026;;0.0024954345654779613;1881.7119626184003
Forex;IBKR;Forex;;GBP;1.326457044;;-1.3549;;2802.84288;58.839129;0.0010616987579622394;
IMB 18DEC26 30 C;IBKR;Equity and Index Options;;GBP;0.14;0.8986;-1.0;-140.0;-185.70398616;;-7.03434690761934e-05;1.0062503135784
IMBl;IBKR;Stocks;IMPERIAL BRANDS PLC;GBP;25.27;29.95515;1000.0;25270.0;33519.56950188;;0.012696996168252912;-6214.650219696601
NWG 18SEP26 5 P;IBKR;Equity and Index Options;;GBP;0.0;0.5086;-1.0;0.0;0.0;;0.0;0.6746360525784001
RRl;IBKR;Stocks;ROLLS-ROYCE HOLDINGS PLC;GBP;15.302;11.090386;100.0;1530.2;2029.7445687288002;;0.0007688541170027941;558.6525056909015
Forex;IBKR;Forex;;EUR;1.159715451;;-1.1617;;2288.871029;3.910112;0.0008670095444754479;
MC;IBKR;Stocks;LVMH MOET HENNESSY LOUIS VUI;EUR;453.3;619.0851265;10.0;4533.0;5256.9901393830005;;0.0019913138697245387;-1922.6357274803954
MC 18DEC26 440 P;IBKR;Equity and Index Options;;EUR;24.2586;37.9386;-1.0;-2425.86;-2813.30732396286;;-0.001065662621665557;15.864907369680001
RI;IBKR;Stocks;PERNOD-RICARD SA;EUR;64.5;83.7114;100.0;6450.0;7480.164658950001;;0.0028334380012625796;-2227.97574153414
RMS;IBKR;Stocks;HERMES INTL;EUR;1581.0;2195.362096667;3.0;4743.0;5500.530384093;;0.0020835653395330877;-2137.4556480404267
RMS 17DEC27 1520 P;IBKR;Equity and Index Options;;EUR;178.0733;193.044;5.0;8903.67;10325.723669605171;;0.003911317353287069;-86.80876051142863
RMS 17DEC27 1680 P;IBKR;Equity and Index Options;;EUR;260.9422;214.886;-5.0;-13047.11;-15130.935057896611;;-0.0057315003535896155;-267.06043377173114
RMS 17DEC27 1920 P;IBKR;Equity and Index Options;;EUR;418.3073;388.7186;-1.0;-41830.73;-48511.743907609234;;-0.018375934884117;-34.31447256500372
RMS 18DEC26 1520 P;IBKR;Equity and Index Options;;EUR;79.1074;130.209;5.0;3955.37;4587.10370342187;;0.0017375652196982897;-296.31657545410803
RMS 18DEC26 1600 P;IBKR;Equity and Index Options;;EUR;116.9082;110.436;-5.0;-5845.41;-6779.01229442991;;-0.0025678460196837667;-37.529551709810924
SAP;IBKR;Stocks;SAP SE;EUR;190.9;210.1117;100.0;19090.0;22138.96795959;;0.008386097898310489;-2228.010532997671
SAP 18DEC26 190 P;IBKR;Equity and Index Options;;EUR;14.74;31.7783;-2.0;-2948.0;-3418.841149548;;-0.0012950349190266799;39.5191595375466
Forex;IBKR;Forex;;CNH;0.148953656;;-0.14885;;969.0135;-0.6748;0.00036705604753650776;

---

# [pivot_summary] START  
# PORTFOLIO PIVOT SUMMARY

**Report Date :** 2026-09-01  
**Filter      :** Open Positions | Forex Balances | Cash Balances  
**Generated   :** 2026-09-01 18:29:34  

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
AMZN          -15.50       1.0        -15.50
BHP         -2420.00       4.0      -2420.00
BRK         -2194.89       2.0      -2194.89
CPRT         -925.81      11.0       -925.81
GEHC          405.14       1.0        405.14
GOOG        19650.22       2.0      19650.22
IMB          -140.00       1.0       -185.70
MC          -2425.86       1.0      -2813.31
META        -9294.61       3.0      -9294.61
MSFT        -1377.00       2.0      -1377.00
NBIS         -964.34       2.0       -964.34
NWG             0.00       1.0          0.00
PDD         -3295.32       4.0      -3295.32
RMS        -47864.21       1.0     -55508.86
SAP         -2948.00       2.0      -3418.84
SKHY            7.29       1.0          7.29
SMCI          -39.00       2.0        -39.00
UNH         -5354.66       3.0      -5354.66
```

> **SUBTOTAL [IBKR]**  Amount: `     -59,196.55`  Quantity: `          44.00`  Value in USD: `     -67,745.20`

> **SUBTOTAL [Equity and Index Options]**  Amount: `     -56,834.55`  Quantity: `          62.00`  Value in USD: `     -70,107.20`

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
Forex       43675.69      4.58      22765.79
```

> **SUBTOTAL [IBKR]**  Amount: `      43,675.69`  Quantity: `           4.58`  Value in USD: `      22,765.79`

### UOB

```
             Amount  Quantity  Value in USD
Underlying                                 
Forex       8878.06      0.78       6898.25
```

> **SUBTOTAL [UOB]**  Amount: `       8,878.06`  Quantity: `           0.78`  Value in USD: `       6,898.25`

> **SUBTOTAL [Forex]**  Amount: `   1,871,287.60`  Quantity: `          13.21`  Value in USD: `     431,207.87`

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
1299         45750.00    -600.0       5847.15
1398         45420.00   -6000.0       5804.98
14           15930.00   -1000.0       2035.96
16          522900.00   -4500.0      66830.07
1810         16572.00    -600.0       2118.01
1883         24400.00  -10000.0       3118.48
1972        134288.00   -5600.0      17162.89
200          12340.00   -4000.0       1577.13
215          57750.00  -50000.0       7380.83
2318         28275.00    -500.0       3613.73
27           34160.00   -1000.0       4365.87
2800         39120.00   -1500.0       4999.79
2828         35000.00    -400.0       4473.23
2888          6462.40     -28.0        825.94
3188         55780.00   -1000.0       7129.05
3690          4734.00     -60.0        605.04
386           9570.00   -2000.0       1223.11
390          34450.00  -10000.0       4402.94
3918          6718.80   -2036.0        858.71
3968         25475.00    -500.0       3255.87
5           386400.00   -2400.0      49384.47
66           16170.00    -500.0       2066.63
700         319818.00    -706.0      40874.85
808          29400.00  -20000.0       3757.51
857          20340.00   -2000.0       2599.59
939          38000.00   -4000.0       4856.65
9618          2131.80     -19.0        272.46
9CI           3180.00   -1200.0       2441.07
AMZN         77931.00    -300.0      77931.00
BABA         57010.00    -500.0      57010.00
BARCl         4966.50   -1000.0       6587.85
BHP         112704.00   -1200.0     112704.00
BRK          50403.00    -100.0      50403.00
BS6          33880.00   -7000.0      26007.35
C38U           526.14    -222.0        403.88
CGP              0.00     -50.0          0.00
CPRT         36289.00   -1100.0      36289.00
D05          17028.00    -220.0      13071.23
DNOW           400.75     -25.0        400.75
F34          34560.00   -9000.0      26529.34
FAST          4931.00    -100.0       4931.00
FCX          37870.00    -500.0      37870.00
GOOG         67082.00    -200.0      67082.00
H78           8150.00   -1000.0       8150.00
HMN             58.14     -68.0         44.63
ICE          48210.00    -300.0      48210.00
IMB          25270.00   -1000.0      33519.57
IVV          77074.00    -100.0      77074.00
MC            4533.00     -10.0       5256.99
META        114468.00    -200.0     114468.00
NOV           6387.00    -300.0       6387.00
NVO           9066.00    -200.0       9066.00
O39          25216.00    -800.0      19356.59
PDD           8400.00    -100.0       8400.00
RI            6450.00    -100.0       7480.16
RMS           4743.00      -3.0       5500.53
RRl           1530.20    -100.0       2029.74
SAP          19090.00    -100.0      22138.97
SOUHY         5511.00    -300.0       5511.00
TSM         124596.00    -300.0     124596.00
UNH          77882.00    -200.0      77882.00
```

> **SUBTOTAL [IBKR]**  Amount: `   3,072,750.73`  Quantity: `    -158,847.00`  Value in USD: `   1,346,173.62`

> **SUBTOTAL [Stocks]**  Amount: `   3,135,971.17`  Quantity: `    -164,878.91`  Value in USD: `   1,408,261.93`

---

## Treasury Bills

### IBKR

```
               Amount  Quantity  Value in USD
Underlying                                   
912797TC1    73109.63  -74000.0      73109.63
912797TV9    66762.63  -68000.0      66762.63
912797UE5    97583.52 -100000.0      97583.52
912797UX3   105064.02 -108000.0     105064.02
912797VF1   255967.39 -264000.0     255967.39
912797VQ7   175842.29 -182000.0     175842.29
912797VR5    96268.50 -100000.0      96268.50
```

> **SUBTOTAL [IBKR]**  Amount: `     870,597.98`  Quantity: `    -896,000.00`  Value in USD: `     870,597.98`

> **SUBTOTAL [Treasury Bills]**  Amount: `     870,597.98`  Quantity: `    -896,000.00`  Value in USD: `     870,597.98`

---

> ### GRAND TOTAL  
> Amount: `   5,821,022.19`  Quantity: `  -1,060,803.70`  Value in USD: `   2,639,960.59`

---

