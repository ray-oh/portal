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

- Current refresh timestamp: **2026-07-30 15:29:44**.


# [transaction_details] START  
Status;Table;Currency;Settle Date;Ticker;Symbol;Asset Category;Transaction Price;Amount;Quantity;Value in USD;Comm/Fee;Code
Open;Trades;EUR;2026-03-04;RI;RI;Stocks;93.0;-9300.0;100.0;-10834.8378504;0.0;"A;O"
Open;Trades;EUR;2026-02-16;SAP;SAP;Stocks;220.0;-22000.0;100.0;-25630.799216;0.0;"A;O"
Open;Trades;HKD;2026-04-01;1810;1810;Stocks;31.8;-6360.0;200.0;-813.47145612;-25.18126;O
Open;Trades;HKD;2026-04-10;1810;1810;Stocks;31.0;-6200.0;200.0;-793.0067654;-25.1767;O
Open;Trades;HKD;2026-04-10;700;700;Stocks;502.0;-50200.0;100.0;-6420.796713399999;-92.5907;O
Open;Trades;USD;2026-02-23;CPRT;CPRT;Stocks;45.0;-13500.0;300.0;-13500.0;0.0;"A;O"
Open;Trades;USD;2026-02-23;NVO;NVO;Stocks;65.0;-13000.0;200.0;-13000.0;0.0;"A;O"
Open;Trades;USD;2026-03-20;PDD;PDD;Stocks;100.0;-10000.0;100.0;-10000.0;0.0;"A;O"
Open;Trades;USD;2026-01-02;TSM;TSM;Stocks;317.21;63442.0;-200.0;63442.0;-0.7795145;"C;P"
Open;Trades;USD;2026-01-02;TSM;TSM;Stocks;318.5;63700.0;-200.0;63700.0;-0.1395145;C
Open;Trades;USD;2026-01-29;UNH;UNH;Stocks;350.0;-70000.0;200.0;-70000.0;0.0;"A;O"
Open;Trades;EUR;2026-03-30;RMS;RMS 18DEC26 1760 C;Equity and Index Options;130.095;6504.75;-5.0;7578.270054558;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1760 C;Equity and Index Options;181.5;-9075.0;5.0;-10572.7046766;-5.7;C
Open;Trades;EUR;2026-03-30;RMS;RMS 17DEC27 1760 C;Equity and Index Options;226.53;11326.5;-5.0;13195.783969092001;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 17DEC27 1760 C;Equity and Index Options;284.1;-14205.0;5.0;-16549.34103924;-5.7;C
Open;Trades;EUR;2026-02-18;SAP;SAP 17APR26 194 C;Equity and Index Options;2.2;220.0;-1.0;256.30799216;-1.17;O
Open;Trades;EUR;2026-04-17;SAP;SAP 17APR26 194 C;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2026-02-18;SAP;SAP 18SEP26 175 C;Equity and Index Options;15.79;-1579.0;1.0;-1839.5923619120001;-1.17;O
Open;Trades;EUR;2026-02-18;SAP;SAP 18SEP26 195 C;Equity and Index Options;8.52;1704.0;-2.0;1985.221902912;-2.34;O
Open;Trades;EUR;2026-03-10;RMS;RMS 19JUN26 2040 P;Equity and Index Options;180.57;-18057.0;1.0;-21037.060974696;-1.14;C
Open;Trades;EUR;2026-02-18;RMS;RMS 18DEC26 1480 P;Equity and Index Options;30.1;3010.0;-1.0;3506.75934728;-1.14;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1480 P;Equity and Index Options;73.05;-7305.0;1.0;-8510.59037604;-1.14;C
Open;Trades;EUR;2026-03-10;RMS;RMS 18DEC26 1960 P;Equity and Index Options;198.32;19832.0;-1.0;23105.000456896;-1.14;O
Open;Trades;EUR;2026-03-24;RMS;RMS 18DEC26 1960 P;Equity and Index Options;359.93;-35993.0;1.0;-41933.152553704;-1.14;C
Open;Trades;EUR;2026-03-24;RMS;RMS 17DEC27 1920 P;Equity and Index Options;388.73;38873.0;-1.0;45288.457178344;-1.14;O
Open;Trades;EUR;2026-03-30;RMS;RMS 18DEC26 1520 P;Equity and Index Options;130.095;-6504.75;5.0;-7578.270054558;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 18DEC26 1600 P;Equity and Index Options;110.55;5527.5;-5.0;6439.73830302;-5.7;O
Open;Trades;EUR;2026-03-30;RMS;RMS 17DEC27 1520 P;Equity and Index Options;192.93;-9646.5;5.0;-11238.522938052;-5.7;O
Open;Trades;EUR;2026-04-09;RMS;RMS 17DEC27 1680 P;Equity and Index Options;215.0;10750.0;-5.0;12524.140526;-5.7;O
Open;Trades;EUR;2026-03-17;MC;MC 18DEC26 440 P;Equity and Index Options;37.95;3795.0;-1.0;4421.31286476;-1.14;O
Open;Trades;EUR;2026-03-04;RI;RI 20MAR26 93 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-02-16;SAP;SAP 20MAR26 220 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;EUR;2026-03-10;SAP;SAP 20MAR26 240 P;Equity and Index Options;71.23;-7123.0;1.0;-8298.553764344;-1.17;C
Open;Trades;EUR;2026-03-10;SAP;SAP 18DEC26 190 P;Equity and Index Options;31.79;6358.0;-2.0;7407.300973424;-2.34;O
Open;Trades;GBP;2026-03-20;NWG;NWG 20MAR26 5 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2026-05-14;16;16 29JUN26 150 C;Equity and Index Options;3.86;15440.0;-4.0;1974.8426544799997;-42.88;O
Open;Trades;HKD;2026-06-29;16;16 29JUN26 150 C;Equity and Index Options;0.0;0.0;4.0;0.0;0.0;"C;Ep"
Open;Trades;USD;2026-07-22;BHP;BHP 18DEC26 95 C;Equity and Index Options;4.0;1600.0;-4.0;1600.0;0.20468;O
Open;Trades;USD;2026-07-24;BRK;BRK B 28AUG26 505 C;Equity and Index Options;6.95;695.0;-1.0;695.0;-0.797667;O
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
Open;Trades;USD;2026-01-23;912797RG4;912797RG4 3.60505781%;Treasury Bills;98.1389333;-167817.58;171000.0;-167817.58;-5.0;O
Open;Trades;USD;2026-02-20;912797TC1;912797TC1 3.55127091%;Treasury Bills;97.1272;-71874.13;74000.0;-71874.13;-5.0;O
Open;Trades;USD;2026-04-17;912797TV9;912797TV9 3.64107797%;Treasury Bills;97.0566889;-65998.55;68000.0;-65998.55;-5.0;O
Open;Trades;USD;2026-06-12;912797UE5;912797UE5 3.8394375%;Treasury Bills;96.9013111;-96901.31;100000.0;-96901.31;-5.0;O
Open;Trades;USD;2026-06-12;912797UX3;912797UX3 3.815%;Treasury Bills;96.61841;-104347.88;108000.0;-104347.88;-7.5;O
Open;Trades;USD;2026-06-22;912797VF1;912797VF1 4.043%;Treasury Bills;96.212;-31749.96;33000.0;-31749.96;-12.5;"O;P"
Open;Trades;USD;2026-06-23;912797VF1;912797VF1 4.03054553%;Treasury Bills;96.268675;-84716.43;88000.0;-84716.43;-5.0;O
Open;Trades;USD;2026-07-10;912797VF1;912797VF1 3.986%;Treasury Bills;96.470857091;-137953.32;143000.0;-137953.32;-7.5;"O;P"
Open;Transaction Fees;EUR;2026-03-04;RI;RI;Stocks;93.0;-37.2;0.0;-43.339351401600005;;
Open;Transaction Fees;HKD;2026-04-01;1810;1810;Stocks;31.8;-7.18126;0.0;-0.91851415549942;;
Open;Transaction Fees;HKD;2026-04-10;1810;1810;Stocks;31.0;-7.1767;0.0;-0.9179309118139;;
Open;Transaction Fees;HKD;2026-04-10;700;700;Stocks;502.0;-52.4307;0.0;-6.706112873331899;;
Open;Withholding Tax;EUR;2026-02-18;RMS;RMS;;;-3.72;;-4.33393514016;;
Open;Withholding Tax;EUR;2026-04-23;RMS;RMS;;;-9.68;;-11.27755165504;;
Open;Withholding Tax;EUR;2026-04-30;MC;MC;;;-18.75;;-21.844431150000002;;
Open;Withholding Tax;EUR;2026-05-08;SAP;SAP;;;-61.32;;-71.44002763296;;
Open;Withholding Tax;EUR;2026-07-24;RI;RI;;;-58.75;;-68.44588427000001;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.584293990689998;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;90.57;;11.584293990689998;;
Open;Withholding Tax;HKD;2026-01-26;1398;1398;;;-90.57;;-11.584293990689998;;
Open;Withholding Tax;HKD;2026-01-26;939;939;;;-81.86;;-10.470247389619999;;
Open;Withholding Tax;HKD;2026-02-09;3968;3968;;;-33.22;;-4.24898141074;;
Open;Withholding Tax;HKD;2026-06-16;1398;1398;;;-116.65;;-14.920038578049999;;
Open;Withholding Tax;HKD;2026-06-26;386;386;;;-25.78;;-3.29737329226;;
Open;Withholding Tax;HKD;2026-07-15;2318;2318;;;-100.56;;-12.862058117519998;;
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
Open;Dividends;EUR;2026-02-18;RMS;RMS;;;15.0;;17.47554492;;
Open;Dividends;EUR;2026-04-23;RMS;RMS;;;39.0;;45.436416792;;
Open;Dividends;EUR;2026-04-30;MC;MC;;;75.0;;87.37772460000001;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;232.5;;270.87094626;;
Open;Dividends;EUR;2026-05-08;SAP;SAP;;;17.5;;20.38813574;;
Open;Dividends;EUR;2026-07-24;RI;RI;;;235.0;;273.78353708000003;;
Open;Dividends;HKD;2026-01-26;1398;1398;;;931.62;;119.15821980353999;;
Open;Dividends;HKD;2026-01-26;939;939;;;818.62;;104.70503198253999;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;220.7;;28.228482761899997;;
Open;Dividends;HKD;2026-02-09;3968;3968;;;333.34;;42.63562502877999;;
Open;Dividends;HKD;2026-03-19;16;16;;;4410.0;;564.05803797;;
Open;Dividends;HKD;2026-03-27;14;14;;;810.0;;103.60249676999999;;
Open;Dividends;HKD;2026-04-23;808;808;;;1142.0;;146.066730014;;
Open;Dividends;HKD;2026-04-30;5;5;;;8148.56;;1042.2360013335199;;
Open;Dividends;HKD;2026-04-30;5;5;;;306.5;;39.2026731605;;
Open;Dividends;HKD;2026-05-07;1972;1972;;;4480.0;;573.0113401599999;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;3.84;;0.49115257727999995;;
Open;Dividends;HKD;2026-05-14;2888;2888;;;103.61;;13.252166284369999;;
Open;Dividends;HKD;2026-05-29;215;215;;;2605.0;;333.190745785;;
Open;Dividends;HKD;2026-05-29;2800;2800;;;285.0;;36.452730345;;
Open;Dividends;HKD;2026-06-01;700;700;;;3741.8;;478.5923733506;;
Open;Dividends;HKD;2026-06-12;1299;1299;;;864.48;;110.57072396016;;
Open;Dividends;HKD;2026-06-12;27;27;;;800.0;;102.3234536;;
Open;Dividends;HKD;2026-06-16;1398;1398;;;1166.46;;149.19526960782;;
Open;Dividends;HKD;2026-06-22;66;66;;;439.66;;56.23441201222;;
Open;Dividends;HKD;2026-06-22;66;66;;;5.34;;0.6830090527799999;;
Open;Dividends;HKD;2026-06-25;1883;1883;;;1300.0;;166.2756121;;
Open;Dividends;HKD;2026-06-26;386;386;;;257.8;;32.9737329226;;
Open;Dividends;HKD;2026-06-26;5;5;;;1661.92;;212.56674250864;;
Open;Dividends;HKD;2026-06-26;5;5;;;217.73;;27.848606940409997;;
Open;Dividends;HKD;2026-06-30;2828;2828;;;280.0;;35.813208759999995;;
Open;Dividends;HKD;2026-07-15;2318;2318;;;1005.64;;128.62569734788;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.96;;0.73640210784;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.26;;0.19944223754;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.36;;0.27615079044;;
Open;Dividends;SGD;2026-02-27;HMN;HMN;;;0.86;;0.65969355494;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;0.8;;0.6136684232;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;0.4;;0.3068342116;;
Open;Dividends;SGD;2026-03-24;C38U;C38U;;;9.04;;6.9344531821599995;;
Open;Dividends;SGD;2026-04-17;D05;D05;;;33.0;;25.313822457;;
Open;Dividends;SGD;2026-04-17;D05;D05;;;145.2;;111.3808188108;;
Open;Dividends;SGD;2026-05-08;O39;O39;;;128.0;;98.186947712;;
Open;Dividends;SGD;2026-05-08;O39;O39;;;336.0;;257.740737744;;
Open;Dividends;SGD;2026-05-14;9CI;9CI;;;144.0;;110.460316176;;
Open;Dividends;SGD;2026-05-14;BS6;BS6;;;1400.0;;1073.9197406;;
Open;Dividends;SGD;2026-05-14;F34;F34;;;900.0;;690.3769761000001;;
Open;Dividends;SGD;2026-05-20;D05;D05;;;33.0;;25.313822457;;
Open;Dividends;SGD;2026-05-20;D05;D05;;;145.2;;111.3808188108;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;0.6;;0.4602513174;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;0.13;;0.09972111877;;
Open;Dividends;SGD;2026-06-08;C38U;C38U;;;8.1;;6.2133927849;;
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
Open;Trades;EUR;2025-10-22;MC;MC;Stocks;618.7;-6187.0;10.0;-7208.079761336;-3.851265;O
Open;Trades;EUR;2025-10-22;RMS;RMS;Stocks;2194.0;-6582.0;3.0;-7668.269110896;-4.08629;O
Open;Trades;HKD;2025-03-26;1810;1810;Stocks;53.35;-10670.0;200.0;-1364.7390623899998;-31.90695;O
Open;Trades;HKD;2025-06-30;2888;2888;Stocks;134.1;13410.0;-100.0;1715.19689097;-33.70307;C
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
Open;Trades;EUR;2025-09-03;RMS;RMS 19JUN26 2040 P;Equity and Index Options;170.0;17000.0;-1.0;19805.617576;-1.14;O
Open;Trades;EUR;2025-07-15;RI;RI 20MAR26 93 P;Equity and Index Options;9.3;930.0;-1.0;1083.48378504;-1.14;O
Open;Trades;EUR;2025-03-26;SAP;SAP 16MAY25 235 P;Equity and Index Options;5.35;535.0;-1.0;623.2944354800001;-1.17;O
Open;Trades;EUR;2025-05-16;SAP;SAP 16MAY25 235 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;EUR;2025-08-15;SAP;SAP 20MAR26 220 P;Equity and Index Options;9.9;990.0;-1.0;1153.38596472;-1.17;O
Open;Trades;EUR;2025-08-08;SAP;SAP 20MAR26 240 P;Equity and Index Options;13.8;1380.0;-1.0;1607.75013264;-1.17;O
Open;Trades;EUR;2025-03-21;SAP;SAP 21MAR25 220 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;GBP;2025-03-27;NWG;NWG 17APR25 4.5 P;Equity and Index Options;0.0725;72.5;-1.0;96.03211416250001;-1.4;O
Open;Trades;GBP;2025-04-17;NWG;NWG 17APR25 4.5 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;GBP;2025-05-07;NWG;NWG 20JUN25 4.6 P;Equity and Index Options;0.1;200.0;-2.0;264.916177;-2.8;O
Open;Trades;GBP;2025-06-20;NWG;NWG 20JUN25 4.6 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;GBP;2025-09-19;NWG;NWG 20MAR26 5 P;Equity and Index Options;0.3475;347.5;-1.0;460.2918575375;-1.4;O
Open;Trades;GBP;2025-09-19;NWG;NWG 18SEP26 5 P;Equity and Index Options;0.51;510.0;-1.0;675.53625135;-1.4;O
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
Open;Transaction Fees;EUR;2025-10-22;MC;MC;Stocks;618.7;-24.75;0.0;-28.834649118;;
Open;Transaction Fees;EUR;2025-10-22;RMS;RMS;Stocks;2194.0;-26.33;0.0;-30.67540651624;;
Open;Transaction Fees;HKD;2025-03-26;1810;1810;Stocks;53.35;-11.304095;0.0;-1.445842550278115;;
Open;Transaction Fees;HKD;2025-06-30;2888;2888;Stocks;134.1;-14.382185;0.0;-1.8395435493926449;;
Open;Withholding Tax;EUR;2025-12-04;MC;MC;;;-13.75;;-16.01924951;;
Open;Withholding Tax;HKD;2025-01-24;1398;1398;;;-93.17;;-11.91684521489;;
Open;Withholding Tax;HKD;2025-01-27;939;939;;;-85.25;;-10.90384302425;;
Open;Withholding Tax;HKD;2025-05-28;939;939;;;-88.71;;-11.346391961069997;;
Open;Withholding Tax;HKD;2025-06-27;386;386;;;-30.46;;-3.8959654958199996;;
Open;Withholding Tax;HKD;2025-06-30;2318;2318;;;-88.13;;-11.272207457209998;;
Open;Withholding Tax;HKD;2025-07-24;857;857;;;-54.54;;-6.975901449179999;;
Open;Withholding Tax;HKD;2025-07-30;3968;3968;;;-94.82;;-12.127887337939999;;
Open;Withholding Tax;HKD;2025-07-31;390;390;;;-194.8;;-24.9157609516;;
Open;Withholding Tax;HKD;2025-08-22;1398;1398;;;-108.23;;-13.84308422891;;
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
Open;Dividends;CNH;2025-07-31;3188;3188;;;530.0;;78.98767868;;
Open;Dividends;EUR;2025-12-04;MC;MC;;;55.0;;64.07699804;;
Open;Dividends;HKD;2025-01-24;1398;1398;;;931.74;;119.17356832157999;;
Open;Dividends;HKD;2025-01-27;939;939;;;852.52;;109.04098832883999;;
Open;Dividends;HKD;2025-03-19;14;14;;;810.0;;103.60249676999999;;
Open;Dividends;HKD;2025-03-20;16;16;;;4275.0;;546.7909551749999;;
Open;Dividends;HKD;2025-04-23;808;808;;;1120.0;;143.25283503999998;;
Open;Dividends;HKD;2025-04-25;5;5;;;6698.42;;856.7568350791399;;
Open;Dividends;HKD;2025-05-08;1972;1972;;;4256.0;;544.360773152;;
Open;Dividends;HKD;2025-05-19;2888;2888;;;277.72;;35.52158691724;;
Open;Dividends;HKD;2025-05-28;939;939;;;887.1;;113.46391961069999;;
Open;Dividends;HKD;2025-05-29;215;215;;;2605.0;;333.190745785;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;319.22;;40.82961607274;;
Open;Dividends;HKD;2025-05-30;2800;2800;;;10.78;;1.3788085372599999;;
Open;Dividends;HKD;2025-05-30;700;700;;;2727.0;;348.795072459;;
Open;Dividends;HKD;2025-06-12;1299;1299;;;785.88;;100.51744464395999;;
Open;Dividends;HKD;2025-06-12;27;27;;;500.0;;63.952158499999996;;
Open;Dividends;HKD;2025-06-13;66;66;;;445.0;;56.91742106499999;;
Open;Dividends;HKD;2025-06-20;1883;1883;;;1280.0;;163.71752576;;
Open;Dividends;HKD;2025-06-20;5;5;;;1881.53;;240.65580956500997;;
Open;Dividends;HKD;2025-06-27;386;386;;;304.6;;38.9596549582;;
Open;Dividends;HKD;2025-06-30;2318;2318;;;881.33;;112.72591170161;;
Open;Dividends;HKD;2025-07-24;857;857;;;545.4;;69.75901449179999;;
Open;Dividends;HKD;2025-07-30;2828;2828;;;520.0;;66.51024484;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;133.58;;17.08545866486;;
Open;Dividends;HKD;2025-07-30;3968;3968;;;961.35;;122.96081514794999;;
Open;Dividends;HKD;2025-07-31;390;390;;;1948.0;;249.15760951599998;;
Open;Dividends;HKD;2025-08-22;1398;1398;;;1082.3;;138.43084228909999;;
Open;Dividends;HKD;2025-09-09;14;14;;;270.0;;34.53416559;;
Open;Dividends;HKD;2025-09-16;66;66;;;36.12;;4.6199039300399996;;
Open;Dividends;HKD;2025-09-16;66;66;;;173.88;;22.240002639959997;;
Open;Dividends;HKD;2025-09-18;808;808;;;1170.0;;149.64805088999998;;
Open;Dividends;HKD;2025-09-26;5;5;;;1866.43;;238.72445437830999;;
Open;Dividends;HKD;2025-09-30;2888;2888;;;26.82;;3.43039378194;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;1.24;;0.95118605596;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.24;;0.18410052696;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.45;;0.34518848805;;
Open;Dividends;SGD;2025-02-28;HMN;HMN;;;0.49;;0.37587190921;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.46;;0.35285934334;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.06;;0.04602513174;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;5.33;;4.08856586957;;
Open;Dividends;SGD;2025-03-21;C38U;C38U;;;0.24;;0.18410052696;;
Open;Dividends;SGD;2025-04-16;D05;D05;;;132.0;;101.255289828;;
Open;Dividends;SGD;2025-05-09;O39;O39;;;328.0;;251.604053512;;
Open;Dividends;SGD;2025-05-09;O39;O39;;;128.0;;98.186947712;;
Open;Dividends;SGD;2025-05-13;9CI;9CI;;;144.0;;110.460316176;;
Open;Dividends;SGD;2025-05-13;BS6;BS6;;;840.0;;644.3518443600001;;
Open;Dividends;SGD;2025-05-15;F34;F34;;;900.0;;690.3769761000001;;
Open;Dividends;SGD;2025-05-27;D05;D05;;;132.0;;101.255289828;;
Open;Dividends;SGD;2025-05-27;D05;D05;;;33.0;;25.313822457;;
Open;Dividends;SGD;2025-08-21;O39;O39;;;328.0;;251.604053512;;
Open;Dividends;SGD;2025-08-25;D05;D05;;;132.0;;101.255289828;;
Open;Dividends;SGD;2025-08-25;D05;D05;;;33.0;;25.313822457;;
Open;Dividends;SGD;2025-08-28;F34;F34;;;360.0;;276.15079044000004;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;0.33;;0.25313822457;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;1.18;;0.90516092422;;
Open;Dividends;SGD;2025-08-29;HMN;HMN;;;0.21;;0.16108796109;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;0.73;;0.55997243617;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;0.33;;0.25313822457;;
Open;Dividends;SGD;2025-09-18;C38U;C38U;;;14.41;;11.05370247289;;
Open;Dividends;SGD;2025-11-24;D05;D05;;;132.0;;101.255289828;;
Open;Dividends;SGD;2025-11-24;D05;D05;;;33.0;;25.313822457;;
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
Open;Trades;HKD;2024-03-24;1299;1299;Stocks;55.2;-33120.0;600.0;-4236.19097904;-61.43992;O
Open;Trades;HKD;2024-12-27;3968;3968;Stocks;39.7;-19850.0;500.0;-2538.90069245;-41.68725;O
Open;Trades;SGD;2024-09-09;C38U;C38UNR;Stocks;0.0;0.0;-0.36;0.0;0.0;C
Open;Trades;SGD;2024-05-16;F34;F34;Stocks;3.16;-3160.0;1000.0;-2423.99027164;-3.90576;O
Open;Trades;USD;2024-12-13;AMZN;AMZN;Stocks;200.0;120000.0;-600.0;120000.0;-3.4356;"A;C"
Open;Trades;USD;2024-12-13;AMZN;AMZN;Stocks;205.0;-61500.0;300.0;-61500.0;0.0;"Ex;O"
Open;Trades;USD;2024-05-03;BABA;BABA;Stocks;78.0;15600.0;-200.0;15600.0;-0.1248;"A;C"
Open;Trades;USD;2024-05-10;BABA;BABA;Stocks;80.0;8000.0;-100.0;8000.0;0.0;"A;C"
Open;Trades;USD;2024-09-19;BABA;BABA;Stocks;87.83;43915.0;-500.0;43915.0;-2.58112325;"C;P"
Open;Trades;EUR;2024-11-15;SAP;SAP 21MAR25 220 P;Equity and Index Options;12.7;1270.0;-1.0;1479.59613656;-1.17;O
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
Open;Transaction Fees;HKD;2024-03-24;1299;1299;Stocks;55.2;-34.94392;0.0;-4.46947822090264;;
Open;Transaction Fees;HKD;2024-12-27;3968;3968;Stocks;39.7;-20.565725;0.0;-2.630445009734825;;
Open;Withholding Tax;HKD;2024-07-26;2318;2318;;;-82.34;;-10.53164146178;;
Open;Withholding Tax;HKD;2024-07-26;386;386;;;-43.88;;-5.61244142996;;
Open;Withholding Tax;HKD;2024-07-29;857;857;;;-50.57;;-6.468121310689999;;
Open;Withholding Tax;HKD;2024-08-02;939;939;;;-175.36;;-22.42930102912;;
Open;Withholding Tax;HKD;2024-08-08;390;390;;;-230.1;;-29.430783341699996;;
Open;Withholding Tax;HKD;2024-08-19;1398;1398;;;-201.43;;-25.763766573309997;;
Open;Withholding Tax;HKD;2024-09-26;386;386;;;-31.86;;-4.075031539619999;;
Open;Withholding Tax;HKD;2024-10-18;2318;2318;;;-51.02;;-6.52567825334;;
Open;Withholding Tax;HKD;2024-10-28;857;857;;;-48.07;;-6.14836051819;;
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
Open;Dividends;CNH;2024-07-31;3188;3188;;;480.0;;71.53601088;;
Open;Dividends;HKD;2024-03-20;16;16;;;4275.0;;546.7909551749999;;
Open;Dividends;HKD;2024-03-22;14;14;;;810.0;;103.60249676999999;;
Open;Dividends;HKD;2024-04-17;808;808;;;1328.0;;169.856932976;;
Open;Dividends;HKD;2024-04-25;5;5;;;5119.61;;654.8202203563699;;
Open;Dividends;HKD;2024-04-25;5;5;;;703.64;;89.99859361387999;;
Open;Dividends;HKD;2024-04-26;27;27;;;300.0;;38.3712951;;
Open;Dividends;HKD;2024-05-02;1972;1972;;;4032.0;;515.7102061439999;;
Open;Dividends;HKD;2024-05-17;2888;2888;;;210.1;;26.872697001699997;;
Open;Dividends;HKD;2024-05-27;215;215;;;2605.0;;333.190745785;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;41.55;;5.3144243713499995;;
Open;Dividends;HKD;2024-05-31;2800;2800;;;183.45;;23.464046953649998;;
Open;Dividends;HKD;2024-05-31;700;700;;;2060.4;;263.5340547468;;
Open;Dividends;HKD;2024-06-13;1883;1883;;;1930.0;;246.85533180999997;;
Open;Dividends;HKD;2024-06-14;1299;1299;;;714.42;;91.37740215113999;;
Open;Dividends;HKD;2024-06-21;5;5;;;5808.32;;742.9092025174399;;
Open;Dividends;HKD;2024-07-16;66;66;;;16.91;;2.1628620004699997;;
Open;Dividends;HKD;2024-07-16;66;66;;;428.09;;54.75455906452999;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;741.05;;94.78349411285;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;823.39;;105.31513557462999;;
Open;Dividends;HKD;2024-07-26;2318;2318;;;-741.05;;-94.78349411285;;
Open;Dividends;HKD;2024-07-26;386;386;;;438.8;;56.1244142996;;
Open;Dividends;HKD;2024-07-29;857;857;;;505.74;;64.68632927958;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;36.4;;4.655717138799999;;
Open;Dividends;HKD;2024-07-30;2828;2828;;;223.6;;28.599405281199996;;
Open;Dividends;HKD;2024-08-02;939;939;;;1753.57;;224.28917316168997;;
Open;Dividends;HKD;2024-08-08;390;390;;;2301.0;;294.307833417;;
Open;Dividends;HKD;2024-08-19;1398;1398;;;2014.29;;257.63638668992996;;
Open;Dividends;HKD;2024-09-09;215;215;;;1140.0;;145.81092138;;
Open;Dividends;HKD;2024-09-16;808;808;;;1276.0;;163.205908492;;
Open;Dividends;HKD;2024-09-17;66;66;;;72.66;;9.293527673219998;;
Open;Dividends;HKD;2024-09-17;66;66;;;137.34;;17.566378896779998;;
Open;Dividends;HKD;2024-09-20;14;14;;;270.0;;34.53416559;;
Open;Dividends;HKD;2024-09-25;1299;1299;;;267.0;;34.150452638999994;;
Open;Dividends;HKD;2024-09-26;386;386;;;318.6;;40.7503153962;;
Open;Dividends;HKD;2024-09-27;1883;1883;;;600.0;;76.7425902;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;21.85;;2.79470932645;;
Open;Dividends;HKD;2024-09-27;2828;2828;;;358.15;;45.80893113354999;;
Open;Dividends;HKD;2024-09-27;5;5;;;1869.78;;239.15293384025998;;
Open;Dividends;HKD;2024-10-09;1972;1972;;;1904.0;;243.529819568;;
Open;Dividends;HKD;2024-10-10;2888;2888;;;89.69;;11.47173819173;;
Open;Dividends;HKD;2024-10-18;2318;2318;;;510.24;;65.26189870607999;;
Open;Dividends;HKD;2024-10-25;27;27;;;500.0;;63.952158499999996;;
Open;Dividends;HKD;2024-10-28;857;857;;;480.66;;61.47848900922;;
Open;Dividends;HKD;2024-11-21;16;16;;;12325.6;;1576.4974496152;;
Open;Dividends;HKD;2024-11-21;16;16;;;274.4;;35.09694458479999;;
Open;Dividends;HKD;2024-11-29;2800;2800;;;930.0;;118.95101480999999;;
Open;Dividends;HKD;2024-12-19;5;5;;;1865.55;;238.61189857934997;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;0.31;;0.23779651399;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;1.62;;1.24267855698;;
Open;Dividends;SGD;2024-02-29;HMN;HMN;;;0.17;;0.13040453993;;
Open;Dividends;SGD;2024-03-28;C38U;C38U;;;10.08;;7.73222213232;;
Open;Dividends;SGD;2024-04-19;D05;D05;;;108.0;;82.84523713200001;;
Open;Dividends;SGD;2024-05-08;BS6;BS6;;;455.0;;349.023915695;;
Open;Dividends;SGD;2024-05-10;9CI;9CI;;;144.0;;110.460316176;;
Open;Dividends;SGD;2024-05-14;F34;F34;;;880.0;;675.03526552;;
Open;Dividends;SGD;2024-05-20;D05;D05;;;118.8;;91.1297608452;;
Open;Dividends;SGD;2024-05-21;O39;O39;;;336.0;;257.740737744;;
Open;Dividends;SGD;2024-08-23;O39;O39;;;352.0;;270.01410620800004;;
Open;Dividends;SGD;2024-08-26;D05;D05;;;118.8;;91.1297608452;;
Open;Dividends;SGD;2024-08-29;F34;F34;;;540.0;;414.22618566;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;1.03;;0.79009809487;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;0.39;;0.29916335631;;
Open;Dividends;SGD;2024-08-29;HMN;HMN;;;0.32;;0.24546736928000001;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;0.11;;0.08437940819;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;0.09;;0.06903769761;;
Open;Dividends;SGD;2024-09-26;C38U;C38U;;;9.84;;7.54812160536;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;3.83;;2.9379375760700004;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;0.07;;0.05369598703000001;;
Open;Dividends;SGD;2024-10-17;C38U;C38U;;;0.09;;0.06903769761;;
Open;Dividends;SGD;2024-11-25;D05;D05;;;118.8;;91.1297608452;;
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
Open;Trades;HKD;2023-04-25;3918;3918;Stocks;6.1031;4.96487185;-0.8135;0.6350285429667764;0.0;C
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
Open;Withholding Tax;HKD;2023-06-30;386;386;;;-43.34;;-5.54337309878;;
Open;Withholding Tax;HKD;2023-07-10;2318;2318;;;-85.01;;-10.87314598817;;
Open;Withholding Tax;HKD;2023-08-04;939;939;;;-168.79;;-21.588969666429996;;
Open;Withholding Tax;HKD;2023-08-10;390;390;;;-217.64;;-27.837095551879997;;
Open;Withholding Tax;HKD;2023-08-18;1398;1398;;;-197.54;;-25.266218780179997;;
Open;Withholding Tax;HKD;2023-09-28;386;386;;;-31.56;;-4.036660244519999;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.377309245619999;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;49.86;;6.377309245619999;;
Open;Withholding Tax;HKD;2023-10-25;2318;2318;;;-49.86;;-6.377309245619999;;
Open;Withholding Tax;HKD;2023-10-30;857;857;;;-0.87;;-0.11127675578999999;;
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
Open;Dividends;CNH;2023-07-31;3188;3188;;;430.0;;64.08434308;;
Open;Dividends;HKD;2023-03-16;16;16;;;5571.25;;712.58692608625;;
Open;Dividends;HKD;2023-03-16;16;16;;;53.75;;6.874857038749999;;
Open;Dividends;HKD;2023-03-20;14;14;;;38.61;;4.93838567937;;
Open;Dividends;HKD;2023-03-20;14;14;;;1131.39;;144.70966521063;;
Open;Dividends;HKD;2023-04-11;808;808;;;1500.0;;191.8564755;;
Open;Dividends;HKD;2023-04-27;5;5;;;4330.56;;553.89731902752;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;3394.56;;434.17887831551997;;
Open;Dividends;HKD;2023-05-04;1972;1972;;;413.44;;52.88076082047999;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;1.1;;0.1406947487;;
Open;Dividends;HKD;2023-05-11;2888;2888;;;139.46;;17.83753604882;;
Open;Dividends;HKD;2023-05-29;215;215;;;2605.0;;333.190745785;;
Open;Dividends;HKD;2023-05-31;2800;2800;;;150.0;;19.18564755;;
Open;Dividends;HKD;2023-06-05;700;700;;;1454.4;;186.0240386448;;
Open;Dividends;HKD;2023-06-14;1883;1883;;;1850.0;;236.62298644999998;;
Open;Dividends;HKD;2023-06-23;5;5;;;1879.72;;240.42430275124;;
Open;Dividends;HKD;2023-06-30;386;386;;;433.4;;55.43373098779999;;
Open;Dividends;HKD;2023-07-10;2318;2318;;;850.13;;108.73529701121;;
Open;Dividends;HKD;2023-07-18;66;66;;;445.0;;56.91742106499999;;
Open;Dividends;HKD;2023-07-28;2828;2828;;;80.0;;10.23234536;;
Open;Dividends;HKD;2023-07-28;857;857;;;485.54;;62.102662076179996;;
Open;Dividends;HKD;2023-08-04;939;939;;;1687.94;;215.89481283697998;;
Open;Dividends;HKD;2023-08-10;390;390;;;2176.4;;278.3709555188;;
Open;Dividends;HKD;2023-08-18;1398;1398;;;1975.41;;252.66346684497;;
Open;Dividends;HKD;2023-09-04;215;215;;;1140.0;;145.81092138;;
Open;Dividends;HKD;2023-09-05;14;14;;;270.0;;34.53416559;;
Open;Dividends;HKD;2023-09-14;808;808;;;1490.0;;190.57743233;;
Open;Dividends;HKD;2023-09-21;5;5;;;1879.68;;240.41918657856;;
Open;Dividends;HKD;2023-09-28;2828;2828;;;568.0;;72.649652056;;
Open;Dividends;HKD;2023-09-28;386;386;;;315.6;;40.3666024452;;
Open;Dividends;HKD;2023-09-29;1883;1883;;;600.0;;76.7425902;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;645.48;;82.55967853716;;
Open;Dividends;HKD;2023-10-12;1972;1972;;;1202.52;;153.80749927884;;
Open;Dividends;HKD;2023-10-13;2888;2888;;;60.04;;7.679375192679999;;
Open;Dividends;HKD;2023-10-13;66;66;;;210.0;;26.859906569999996;;
Open;Dividends;HKD;2023-10-25;2318;2318;;;505.93;;64.71063109981;;
Open;Dividends;HKD;2023-10-27;27;27;;;200.0;;25.5808634;;
Open;Dividends;HKD;2023-10-30;857;857;;;8.7;;1.1127675578999998;;
Open;Dividends;HKD;2023-10-30;857;857;;;449.44;;57.485316232479995;;
Open;Dividends;HKD;2023-11-16;16;16;;;16650.0;;2129.60687805;;
Open;Dividends;HKD;2023-11-30;2800;2800;;;825.0;;105.521061525;;
Open;Dividends;HKD;2023-12-21;5;5;;;1872.67;;239.52257731639;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;0.13;;0.09972111877;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;0.2;;0.1534171058;;
Open;Dividends;SGD;2023-03-17;C38U;C38U;;;9.58;;7.348679367820001;;
Open;Dividends;SGD;2023-04-21;D05;D05;;;84.0;;64.435184436;;
Open;Dividends;SGD;2023-04-21;D05;D05;;;100.0;;76.7085529;;
Open;Dividends;SGD;2023-05-10;9CI;9CI;;;144.0;;110.460316176;;
Open;Dividends;SGD;2023-05-12;F34;F34;;;880.0;;675.03526552;;
Open;Dividends;SGD;2023-05-19;O39;O39;;;320.0;;245.46736928;;
Open;Dividends;SGD;2023-05-22;D05;D05;;;84.0;;64.435184436;;
Open;Dividends;SGD;2023-05-25;BS6;BS6;;;350.0;;268.47993515;;
Open;Dividends;SGD;2023-08-24;D05;D05;;;96.0;;73.640210784;;
Open;Dividends;SGD;2023-08-25;O39;O39;;;320.0;;245.46736928;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;1.11;;0.8514649371900002;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;0.5;;0.3835427645;;
Open;Dividends;SGD;2023-08-29;HMN;HMN;;;0.28;;0.21478394812000004;;
Open;Dividends;SGD;2023-08-30;F34;F34;;;480.0;;368.20105392000005;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;0.04;;0.030683421160000002;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;0.44;;0.33751763276;;
Open;Dividends;SGD;2023-09-15;C38U;C38U;;;9.32;;7.14923713028;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.3;;0.2301256587;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.08;;0.061366842320000003;;
Open;Dividends;SGD;2023-10-11;HMN;HMN;;;0.1;;0.0767085529;;
Open;Dividends;SGD;2023-11-27;D05;D05;;;96.0;;73.640210784;;
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
Open;Trades;HKD;2022-10-31;16;16;Stocks;85.0;-255000.0;3000.0;-32615.600834999997;-543.2675;"O;P"
Open;Trades;HKD;2022-01-18;200;200;Stocks;9.2;-18400.0;2000.0;-2353.4394328;-42.5244;O
Open;Trades;HKD;2022-10-31;2800;2800;Stocks;24.0;-24000.0;1000.0;-3069.703608;-23.584;"A;O"
Open;Trades;HKD;2022-09-13;3918;3918;Stocks;6.0527;5.74280176;-0.9488;0.7345291367791978;0.0;C
Open;Trades;HKD;2022-06-29;700;700;Stocks;485.3;-99971.8;206.0;-12786.824798260599;-220.3252263;"A;O"
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;-0.0476;0.0;0.0;O
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;0.0;0.0;0.0476;0.0;0.0;Ca
Open;Trades;HKD;2022-01-19;9618;9618.SPO;Stocks;221.0;10.5196;-0.0476;1.3455022531132;0.0;C
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
Open;Trades;EUR;2022-10-24;MC;MC 16DEC22 720 P;Equity and Index Options;87.32;-8732.0;1.0;-10173.097216096001;-1.1;C
Open;Trades;EUR;2022-10-24;MC;MC 15DEC23 720 P;Equity and Index Options;124.72;12472.0;-1.0;14530.333082816;-1.1;O
Open;Trades;EUR;2022-01-21;MC;MC 16DEC22 720.0 P;Equity and Index Options;94.1;9410.0;-1.0;10962.99184648;-1.1;O
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
Open;Transaction Fees;HKD;2022-01-18;200;200;Stocks;9.2;-24.5244;0.0;-3.1367766318348;;
Open;Transaction Fees;HKD;2022-06-29;700;700;Stocks;485.3;-132.8491963;0.0;-16.991985716750424;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-225.845;0.0;-28.886550472864997;;
Open;Transaction Fees;HKD;2022-10-31;16;16;Stocks;85.0;-113.4225;0.0;-14.5072273949325;;
Open;Transaction Fees;HKD;2022-10-31;2800;2800;Stocks;24.0;-0.684;0.0;-0.087486552828;;
Open;Withholding Tax;HKD;2022-06-20;2318;2318;;;-90.17;;-11.53313226389;;
Open;Withholding Tax;HKD;2022-06-23;386;386;;;-72.32;;-9.250040205439998;;
Open;Withholding Tax;HKD;2022-07-27;1398;1398;;;-205.93;;-26.33933599981;;
Open;Withholding Tax;HKD;2022-07-29;857;857;;;-22.62;;-2.89319565054;;
Open;Withholding Tax;HKD;2022-07-29;939;939;;;-170.38;;-21.79233753046;;
Open;Withholding Tax;HKD;2022-08-09;390;390;;;-229.56;;-29.361715010519998;;
Open;Withholding Tax;HKD;2022-09-29;386;386;;;-37.02;;-4.73501781534;;
Open;Withholding Tax;HKD;2022-10-20;2318;2318;;;-42.38;;-5.42058495446;;
Open;Withholding Tax;HKD;2022-10-28;857;857;;;-46.68;;-5.970573517559999;;
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
Open;Dividends;HKD;2022-03-17;16;16;;;1875.0;;239.82059437499998;;
Open;Dividends;HKD;2022-03-25;14;14;;;1170.0;;149.64805088999998;;
Open;Dividends;HKD;2022-04-11;808;808;;;1724.0;;220.50704250799998;;
Open;Dividends;HKD;2022-04-28;5;5;;;3388.17;;433.36156972989;;
Open;Dividends;HKD;2022-04-29;27;27;;;300.0;;38.3712951;;
Open;Dividends;HKD;2022-05-05;1972;1972;;;3584.0;;458.40907212799993;;
Open;Dividends;HKD;2022-05-12;2888;2888;;;90.34;;11.55487599778;;
Open;Dividends;HKD;2022-06-06;700;700;;;640.0;;81.85876288;;
Open;Dividends;HKD;2022-06-20;2318;2318;;;901.7;;115.33132263889999;;
Open;Dividends;HKD;2022-06-23;386;386;;;723.2;;92.5004020544;;
Open;Dividends;HKD;2022-07-19;66;66;;;510.0;;65.23120166999999;;
Open;Dividends;HKD;2022-07-27;1398;1398;;;2059.31;;263.39463904126995;;
Open;Dividends;HKD;2022-07-29;857;857;;;226.22;;28.934514591739998;;
Open;Dividends;HKD;2022-07-29;939;939;;;1703.8;;217.92337530459997;;
Open;Dividends;HKD;2022-08-09;390;390;;;2295.6;;293.6171501052;;
Open;Dividends;HKD;2022-09-08;808;808;;;1750.0;;223.83255474999999;;
Open;Dividends;HKD;2022-09-16;14;14;;;270.0;;34.53416559;;
Open;Dividends;HKD;2022-09-29;386;386;;;370.2;;47.350178153399995;;
Open;Dividends;HKD;2022-09-29;5;5;;;1695.13;;216.81444487621;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;1755.52;;224.53858657983997;;
Open;Dividends;HKD;2022-10-06;1972;1972;;;36.48;;4.6659494841599995;;
Open;Dividends;HKD;2022-10-14;2888;2888;;;40.18;;5.13919545706;;
Open;Dividends;HKD;2022-10-14;66;66;;;210.0;;26.859906569999996;;
Open;Dividends;HKD;2022-10-20;2318;2318;;;531.06;;67.92486658601999;;
Open;Dividends;HKD;2022-10-28;857;857;;;466.76;;59.70061900291999;;
Open;Dividends;HKD;2022-11-17;16;16;;;16650.0;;2129.60687805;;
Open;Dividends;SGD;2022-01-28;C38U;C38U;;;0.48;;0.36820105392;;
Open;Dividends;SGD;2022-01-28;C38U;C38U;;;8.49;;6.51255614121;;
Open;Dividends;SGD;2022-03-15;C38U;C38U;;;0.19;;0.14574625051;;
Open;Dividends;SGD;2022-03-15;C38U;C38U;;;0.5;;0.3835427645;;
Open;Dividends;SGD;2022-04-22;D05;D05;;;72.0;;55.230158088;;
Open;Dividends;SGD;2022-05-06;F34;F34;;;840.0;;644.3518443600001;;
Open;Dividends;SGD;2022-05-20;9CI;9CI;;;36.0;;27.615079044;;
Open;Dividends;SGD;2022-05-20;9CI;9CI;;;144.0;;110.460316176;;
Open;Dividends;SGD;2022-05-20;O39;O39;;;224.0;;171.827158496;;
Open;Dividends;SGD;2022-05-25;D05;D05;;;72.0;;55.230158088;;
Open;Dividends;SGD;2022-05-27;BS6;BS6;;;350.0;;268.47993515;;
Open;Dividends;SGD;2022-08-24;F34;F34;;;480.0;;368.20105392000005;;
Open;Dividends;SGD;2022-08-25;O39;O39;;;224.0;;171.827158496;;
Open;Dividends;SGD;2022-08-26;D05;D05;;;72.0;;55.230158088;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;1.3;;0.9972111877000001;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;0.06;;0.04602513174;;
Open;Dividends;SGD;2022-09-09;C38U;C38U;;;8.31;;6.374480745990001;;
Open;Dividends;SGD;2022-11-24;D05;D05;;;72.0;;55.230158088;;
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
Open;Trades;HKD;2021-09-19;1398;1398;Stocks;4.18;-12540.0;3000.0;-1603.9201351799998;-35.33858;O
Open;Trades;HKD;2021-05-10;14;14;Stocks;28.5;-28500.0;1000.0;-3645.2730344999995;-52.5695;O
Open;Trades;HKD;2021-09-15;200;200;Stocks;9.33;-9330.0;1000.0;-1193.34727761;-31.25191;O
Open;Trades;HKD;2021-04-25;2318;2318;Stocks;86.3;-43150.0;500.0;-5519.07127855;-79.68505;O
Open;Trades;HKD;2021-04-25;390;390;Stocks;3.92;-39200.0;10000.0;-5013.8492264;-72.4184;"O;P"
Open;Trades;HKD;2021-05-11;3918;3918;Stocks;7.8;-15600.0;2000.0;-1995.3073451999999;-34.4212;O
Open;Trades;HKD;2021-04-20;700;700;Stocks;614.5;-61450.0;100.0;-7859.72027965;-112.81915;O
Open;Trades;HKD;2021-08-15;700;700;Stocks;455.8;-91160.0;200.0;-11659.75753772;-194.38932;O
Open;Trades;HKD;2021-04-26;939;939;Stocks;6.38;-25520.0;4000.0;-3264.1181698399996;-47.10504;O
Open;Trades;SGD;2021-09-10;C38U;C38U;Stocks;2.0;1.2144;-0.6072;0.9315486664176;0.0;C
Open;Trades;USD;2021-12-20;BABA;BABA;Stocks;230.0;-46000.0;200.0;-46000.0;0.0;"A;O"
Open;Trades;USD;2021-02-09;FCX;FCX;Stocks;31.38;15690.0;-500.0;15690.0;-2.906249;"C;P"
Open;Trades;USD;2021-01-19;MSFT;MSFT;Stocks;213.0;-21300.0;100.0;-21300.0;-1.0;O
Open;Trades;EUR;2021-03-19;MC;MC 19MAR21 360.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-08-19;700;700 30AUG21 450.0 P;Equity and Index Options;32.3;6460.0;-2.0;826.26188782;-24.0;O
Open;Trades;HKD;2021-08-30;700;700 30AUG21 450.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2021-09-03;700;700 29JUN22 500.0 P;Equity and Index Options;67.08;13416.0;-2.0;1715.964316872;-32.832;O
Open;Trades;HKD;2021-12-06;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.05;1025.0;-1.0;131.101924925;-19.0;O
Open;Trades;HKD;2021-12-20;2800;2800 29DEC22 24.0 P;Equity and Index Options;2.5;1250.0;-1.0;159.88039625;-19.0;O
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
Open;Corporate Actions;SGD;2021-09-20;9CI;C31;Stocks;;1141.2;-1200.0;875.3980056948001;;
Open;Corporate Actions;SGD;2021-09-20;C38U;C38U;Stocks;;0.0;185.6072;0.0;;
Open;Corporate Actions;USD;2021-01-04;NOV;US62955J1034) (NOV;Stocks;;0.0;300.0;0.0;;
Open;Corporate Actions;USD;2021-01-04;NOV;US62955J1034) (NOV.OLD;Stocks;;0.0;-300.0;0.0;;
Open;Transaction Fees;HKD;2021-04-20;700;700;Stocks;614.5;-63.65915;0.0;-8.142280101550549;;
Open;Transaction Fees;HKD;2021-04-25;2318;2318;Stocks;86.3;-45.16505;0.0;-5.77680487252085;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-24.63504;0.0;-3.15092796546768;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-4.10584;0.0;-0.5251546609112799;;
Open;Transaction Fees;HKD;2021-04-25;390;390;Stocks;3.92;-12.31752;0.0;-1.57546398273384;;
Open;Transaction Fees;HKD;2021-04-26;939;939;Stocks;6.38;-26.68904;0.0;-3.4136434325856797;;
Open;Transaction Fees;HKD;2021-05-10;14;14;Stocks;28.5;-29.7695;0.0;-3.8076475649314996;;
Open;Transaction Fees;HKD;2021-05-11;3918;3918;Stocks;7.8;-16.4212;0.0;-2.1003423703203996;;
Open;Transaction Fees;HKD;2021-08-15;700;700;Stocks;455.8;-121.46132;0.0;-15.53542717651844;;
Open;Transaction Fees;HKD;2021-09-15;200;200;Stocks;9.33;-13.25191;0.0;-1.69497649749547;;
Open;Transaction Fees;HKD;2021-09-19;1398;1398;Stocks;4.18;-17.33858;0.0;-2.2176792326498598;;
Open;Withholding Tax;HKD;2021-06-28;386;386;;;-31.38;;-4.01363746746;;
Open;Withholding Tax;HKD;2021-07-27;1398;1398;;;-95.97;;-12.274977302489999;;
Open;Withholding Tax;HKD;2021-07-30;857;857;;;-21.21;;-2.71285056357;;
Open;Withholding Tax;HKD;2021-08-05;390;390;;;-216.71;;-27.71814453707;;
Open;Withholding Tax;HKD;2021-08-05;939;939;;;-156.33;;-19.99528187661;;
Open;Withholding Tax;HKD;2021-09-30;386;386;;;-38.44;;-4.916641945479999;;
Open;Withholding Tax;HKD;2021-10-25;2318;2318;;;-52.84;;-6.75846411028;;
Open;Withholding Tax;HKD;2021-10-29;857;857;;;-31.31;;-4.00468416527;;
Open;Withholding Tax;USD;2021-03-11;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-05-03;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-06-10;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-08-02;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-09-09;MSFT;MSFT;;;-16.8;;-16.8;;
Open;Withholding Tax;USD;2021-11-01;FCX;FCX;;;-22.5;;-22.5;;
Open;Withholding Tax;USD;2021-12-09;MSFT;MSFT;;;-18.6;;-18.6;;
Open;Withholding Tax;USD;2021-12-17;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;HKD;2021-03-18;16;16;;;1875.0;;239.82059437499998;;
Open;Dividends;HKD;2021-04-07;808;808;;;1784.0;;228.18130152799998;;
Open;Dividends;HKD;2021-04-29;5;5;;;2796.85;;357.72918900145;;
Open;Dividends;HKD;2021-05-06;1972;1972;;;3416.0;;436.92114687199995;;
Open;Dividends;HKD;2021-05-20;2888;2888;;;89.41;;11.435924982969999;;
Open;Dividends;HKD;2021-06-07;700;700;;;320.0;;40.92938144;;
Open;Dividends;HKD;2021-06-28;386;386;;;313.8;;40.1363746746;;
Open;Dividends;HKD;2021-07-05;3918;3918;;;291.4;;37.271317973799995;;
Open;Dividends;HKD;2021-07-20;66;66;;;490.0;;62.673115329999995;;
Open;Dividends;HKD;2021-07-27;1398;1398;;;959.73;;122.75361015441;;
Open;Dividends;HKD;2021-07-30;857;857;;;212.14;;27.133621808379996;;
Open;Dividends;HKD;2021-08-05;390;390;;;2167.1;;277.1814453707;;
Open;Dividends;HKD;2021-08-05;939;939;;;1563.34;;199.95793493877997;;
Open;Dividends;HKD;2021-09-06;14;14;;;270.0;;34.53416559;;
Open;Dividends;HKD;2021-09-08;808;808;;;1802.0;;230.483579234;;
Open;Dividends;HKD;2021-09-30;386;386;;;384.4;;49.16641945479999;;
Open;Dividends;HKD;2021-09-30;5;5;;;1308.18;;167.32186941306;;
Open;Dividends;HKD;2021-10-05;1972;1972;;;1736.0;;222.04189431199998;;
Open;Dividends;HKD;2021-10-19;66;66;;;125.0;;15.988039624999999;;
Open;Dividends;HKD;2021-10-22;2888;2888;;;29.9;;3.8243390782999995;;
Open;Dividends;HKD;2021-10-25;2318;2318;;;528.4;;67.58464110279999;;
Open;Dividends;HKD;2021-10-29;857;857;;;313.06;;40.04172548002;;
Open;Dividends;HKD;2021-11-18;16;16;;;5550.0;;709.86895935;;
Open;Dividends;SGD;2021-05-06;F34;F34;;;520.0;;398.88447508;;
Open;Dividends;SGD;2021-05-06;F34;F34;;;720.0;;552.3015808800001;;
Open;Dividends;SGD;2021-05-18;9CI;C31;;;108.0;;82.84523713200001;;
Open;Dividends;SGD;2021-05-24;D05;D05;;;36.0;;27.615079044;;
Open;Dividends;SGD;2021-05-28;BS6;BS6;;;315.0;;241.631941635;;
Open;Dividends;SGD;2021-06-25;D05;D05;;;36.0;;27.615079044;;
Open;Dividends;SGD;2021-06-29;O39;O39;;;127.2;;97.57327928880001;;
Open;Dividends;SGD;2021-08-26;D05;D05;;;66.0;;50.627644914;;
Open;Dividends;SGD;2021-08-26;O39;O39;;;200.0;;153.4171058;;
Open;Dividends;SGD;2021-08-27;F34;F34;;;400.0;;306.8342116;;
Open;Dividends;SGD;2021-11-26;D05;D05;;;66.0;;50.627644914;;
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
Open;Trades;HKD;2020-03-18;16;16;Stocks;97.5;-48750.0;500.0;-6235.33545375;-89.31625;O
Open;Trades;HKD;2020-03-18;16;16;Stocks;96.5;-48250.0;500.0;-6171.38329525;-88.90275;O
Open;Trades;HKD;2020-07-20;16;16;Stocks;94.05;-47025.0;500.0;-6014.700506925;-86.889675;O
Open;Trades;HKD;2020-01-29;1972;1972;Stocks;24.65;-24650.0;1000.0;-3152.84141405;-45.38555;O
Open;Trades;HKD;2020-05-04;1972;1972;Stocks;20.9;-20900.0;1000.0;-2673.2002252999996;-39.5643;O
Open;Trades;HKD;2020-05-24;1972;1972;Stocks;17.78;-35560.0;2000.0;-4548.27751252;-67.40812;"O;P"
Open;Trades;HKD;2020-07-20;1972;1972;Stocks;18.2;-29120.0;1600.0;-3724.5737110399996;-55.08224;"O;P"
Open;Trades;HKD;2020-07-22;27;27;Stocks;51.5;-51500.0;1000.0;-6587.072325499999;-94.5905;O
Open;Trades;HKD;2020-03-18;2800;2800;Stocks;21.7;-10850.0;500.0;-1387.7618394499998;-18.29295;O
Open;Trades;HKD;2020-03-31;5;5;Stocks;40.8;-16320.0;400.0;-2087.3984534399997;-35.44064;O
Open;Trades;HKD;2020-05-14;5;5;Stocks;38.0;-45600.0;1200.0;-5832.4368552;-83.7112;O
Open;Trades;HKD;2020-05-21;66;66;Stocks;40.0;-20000.0;500.0;-2558.08634;-38.54;O
Open;Trades;HKD;2020-09-29;700;700;Stocks;530.0;-53000.0;100.0;-6778.928800999999;-101.981;"A;O"
Open;Trades;HKD;2020-04-23;808;808;Stocks;2.38;-23800.0;10000.0;-3044.1227446;-48.6826;"O;P"
Open;Trades;HKD;2020-07-22;808;808;Stocks;2.29;-22900.0;10000.0;-2929.0088592999996;-41.9383;O
Open;Trades;SGD;2020-07-22;BS6;BS6;Stocks;0.935;-3085.5;3300.0;-2366.8423997295004;-2.5;O
Open;Trades;SGD;2020-08-26;BS6;BS6;Stocks;0.92;-3404.0;3700.0;-2611.1591407160004;-2.7232;O
Open;Trades;SGD;2020-07-23;9CI;C31;Stocks;2.83;-3396.0;1200.0;-2605.022456484;-2.7168;O
Open;Trades;SGD;2020-07-22;D05;D05;Stocks;21.2;-4240.0;200.0;-3252.4426429600003;-3.392;O
Open;Trades;SGD;2020-09-08;F34;F34;Stocks;4.32;-4320.0;1000.0;-3313.80948528;-3.456;O
Open;Trades;SGD;2020-07-22;O39;O39;Stocks;9.13;-3652.0;400.0;-2801.3963519080003;-2.9216;O
Open;Trades;SGD;2020-08-26;O39;O39;Stocks;8.62;-3448.0;400.0;-2644.910903992;-2.7584;O
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
Open;Trades;EUR;2020-05-04;MC;MC 19MAR21 360.0 P;Equity and Index Options;50.8;5080.0;-1.0;5918.38454624;-1.1;O
Open;Trades;HKD;2020-07-24;27;27 28AUG20 50.0 P;Equity and Index Options;2.18;4360.0;-2.0;557.66282212;-24.0;O
Open;Trades;HKD;2020-08-28;27;27 28AUG20 50.0 P;Equity and Index Options;0.0;0.0;2.0;0.0;0.0;"C;Ep"
Open;Trades;HKD;2020-07-24;700;700 29SEP20 530.0 P;Equity and Index Options;38.02;3802.0;-1.0;486.292213234;-21.0;O
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
Open;Transaction Fees;HKD;2020-01-29;1972;1972;Stocks;24.65;-25.66555;0.0;-3.28273464317935;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;97.5;-50.31625;0.0;-6.435665590251249;;
Open;Transaction Fees;HKD;2020-03-18;16;16;Stocks;96.5;-50.30275;0.0;-6.43393888197175;;
Open;Transaction Fees;HKD;2020-03-18;2800;2800;Stocks;21.7;-0.29295;0.0;-0.03746956966515;;
Open;Transaction Fees;HKD;2020-03-31;5;5;Stocks;40.8;-17.44064;0.0;-2.2307331472428795;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39193208241042;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39193208241042;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39193208241042;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39193208241042;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39193208241042;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39193208241042;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39193208241042;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-3.06426;0.0;-0.39193208241042;;
Open;Transaction Fees;HKD;2020-04-23;808;808;Stocks;2.38;-5.12852;0.0;-0.6559598478208399;;
Open;Transaction Fees;HKD;2020-05-04;1972;1972;Stocks;20.9;-21.5643;0.0;-2.7581670630831;;
Open;Transaction Fees;HKD;2020-05-14;5;5;Stocks;38.0;-47.2312;0.0;-6.0410743770904;;
Open;Transaction Fees;HKD;2020-05-21;66;66;Stocks;40.0;-20.54;0.0;-2.6271546711799996;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.5238976172838039;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-4.096012;0.0;-0.5238976172838039;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.9676861521352158;;
Open;Transaction Fees;HKD;2020-05-24;1972;1972;Stocks;17.78;-15.384048;0.0;-1.9676861521352158;;
Open;Transaction Fees;HKD;2020-07-20;16;16;Stocks;94.05;-49.269675;0.0;-6.301804129686975;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.0483754085495198;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-8.19656;0.0;-1.0483754085495198;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-4.09828;0.0;-0.5241877042747599;;
Open;Transaction Fees;HKD;2020-07-20;1972;1972;Stocks;18.2;-11.29484;0.0;-1.44465879582428;;
Open;Transaction Fees;HKD;2020-07-22;808;808;Stocks;2.29;-23.6183;0.0;-3.0208825302010998;;
Open;Transaction Fees;HKD;2020-07-22;27;27;Stocks;51.5;-53.3905;0.0;-6.8288754367885;;
Open;Transaction Fees;HKD;2020-09-29;700;700;Stocks;530.0;-54.431;0.0;-6.961959878626999;;
Open;Withholding Tax;HKD;2020-06-19;386;386;;;-41.54;;-5.313145328179999;;
Open;Withholding Tax;HKD;2020-07-21;1398;1398;;;-74.27;;-9.499453623589998;;
Open;Withholding Tax;HKD;2020-07-31;857;857;;;-14.44;;-1.8469383374799997;;
Open;Withholding Tax;HKD;2020-11-05;386;386;;;-15.98;;-2.0439109856599997;;
Open;Withholding Tax;HKD;2020-11-13;857;857;;;-19.59;;-2.50564557003;;
Open;Withholding Tax;USD;2020-02-03;FCX;FCX;;;-16.5;;-16.5;;
Open;Withholding Tax;USD;2020-03-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;HKD;2020-05-07;1972;1972;;;590.0;;75.46354703;;
Open;Dividends;HKD;2020-05-29;2800;2800;;;45.0;;5.755694265;;
Open;Dividends;HKD;2020-06-19;386;386;;;415.4;;53.13145328179999;;
Open;Dividends;HKD;2020-07-02;200;200;;;30.1;;3.8499199417;;
Open;Dividends;HKD;2020-07-21;1398;1398;;;862.3;;110.29189254909998;;
Open;Dividends;HKD;2020-07-30;2828;2828;;;488.0;;62.417306696;;
Open;Dividends;HKD;2020-07-31;857;857;;;144.4;;18.4693833748;;
Open;Dividends;HKD;2020-09-09;808;808;;;1798.0;;229.97196196599998;;
Open;Dividends;HKD;2020-10-06;1972;1972;;;1680.0;;214.87925255999997;;
Open;Dividends;HKD;2020-10-09;66;66;;;5.75;;0.7354498227499999;;
Open;Dividends;HKD;2020-10-09;66;66;;;119.25;;15.252589802249998;;
Open;Dividends;HKD;2020-11-05;386;386;;;159.8;;20.4391098566;;
Open;Dividends;HKD;2020-11-13;857;857;;;195.86;;25.051339527619998;;
Open;Dividends;HKD;2020-11-19;16;16;;;5550.0;;709.86895935;;
Open;Dividends;SGD;2020-06-24;F34;F34;;;665.0;;510.11187678500005;;
Open;Dividends;SGD;2020-08-27;F34;F34;;;280.0;;214.78394812000002;;
Open;Dividends;SGD;2020-10-05;D05;D05;;;36.0;;27.615079044;;
Open;Dividends;SGD;2020-10-07;O39;O39;;;63.6;;48.786639644400005;;
Open;Dividends;SGD;2020-12-29;D05;D05;;;36.0;;27.615079044;;
Open;Dividends;USD;2020-02-03;FCX;FCX;;;55.0;;55.0;;
Open;Dividends;USD;2020-03-24;BHP;BHP;;;1560.0;;1560.0;;
Open;Dividends;USD;2020-03-27;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2020-04-02;SOUHY;SOUHY;;;11.0;;11.0;;
Open;Dividends;USD;2020-04-02;SOUHY;SOUHY;;;11.0;;11.0;;
Open;Dividends;USD;2020-05-13;H78;H78;;;32.0;;32.0;;
Open;Dividends;USD;2020-09-22;BHP;BHP;;;1320.0;;1320.0;;
Open;Dividends;USD;2020-10-08;SOUHY;SOUHY;;;15.0;;15.0;;
Open;Dividends;USD;2020-10-14;H78;H78;;;60.0;;60.0;;
Open;Trades;HKD;2019-03-08;1398;1398;Stocks;5.9;-17700.0;3000.0;-2263.9064108999996;-36.4779;O
Open;Trades;USD;2019-02-11;AMZN;AMZN;Stocks;1800.0;-180000.0;100.0;-180000.0;0.0;"A;O"
Open;Trades;USD;2019-02-19;AMZN;AMZN;Stocks;1628.88;81444.0;-50.0;81444.0;-2.064722;C
Open;Trades;USD;2019-10-14;FCX;FCX;Stocks;15.0;-1500.0;100.0;-1500.0;0.0;"A;O"
Open;Trades;USD;2019-02-11;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Trades;USD;2019-03-08;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;3.8;1900.0;-5.0;1900.0;-3.9787;O
Open;Trades;USD;2019-10-14;FCX;FCX 17JAN20 15.0 P;Equity and Index Options;0.0;0.0;1.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2019-03-08;1398;1398;Stocks;5.9;-18.4779;0.0;-2.3634031790943;;
Open;Withholding Tax;HKD;2019-06-21;386;386;;;-60.6;;-7.7510016101999994;;
Open;Withholding Tax;HKD;2019-07-23;1398;1398;;;-85.12;;-10.88721546304;;
Open;Withholding Tax;HKD;2019-08-02;857;857;;;-20.47;;-2.6182013689899994;;
Open;Withholding Tax;HKD;2019-09-26;386;386;;;-26.78;;-3.4252776092599997;;
Open;Withholding Tax;HKD;2019-11-01;857;857;;;-17.26;;-2.20762851142;;
Open;Withholding Tax;USD;2019-02-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-03-29;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-05-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-06-28;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-08-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-09-27;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2019-11-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2019-12-20;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;CNH;2019-07-31;3188;3188;;;390.0;;58.123008840000004;;
Open;Dividends;HKD;2019-01-04;200;200;;;24.38;;3.1183072484599994;;
Open;Dividends;HKD;2019-04-08;5;5;;;1318.35;;168.62265631694999;;
Open;Dividends;HKD;2019-05-16;2888;2888;;;150.56;;19.25727396752;;
Open;Dividends;HKD;2019-06-21;386;386;;;606.0;;77.510016102;;
Open;Dividends;HKD;2019-07-05;200;200;;;23.5;;3.0057514495;;
Open;Dividends;HKD;2019-07-05;5;5;;;625.02;;79.94275621134;;
Open;Dividends;HKD;2019-07-23;1398;1398;;;854.32;;109.27121609944;;
Open;Dividends;HKD;2019-07-30;2828;2828;;;640.0;;81.85876288;;
Open;Dividends;HKD;2019-08-02;857;857;;;204.72;;26.18457177624;;
Open;Dividends;HKD;2019-09-26;386;386;;;267.8;;34.2527760926;;
Open;Dividends;HKD;2019-09-26;5;5;;;625.44;;79.99647602448;;
Open;Dividends;HKD;2019-09-27;2828;2828;;;520.0;;66.51024484;;
Open;Dividends;HKD;2019-10-04;200;200;;;61.1;;7.8149537687;;
Open;Dividends;HKD;2019-10-21;2888;2888;;;70.24;;8.983999226079998;;
Open;Dividends;HKD;2019-11-01;857;857;;;172.56;;22.07116894152;;
Open;Dividends;HKD;2019-11-20;5;5;;;626.63;;80.14868216171;;
Open;Dividends;SGD;2019-05-16;F34;F34;;;490.0;;375.87190921;;
Open;Dividends;SGD;2019-08-30;F34;F34;;;210.0;;161.08796109000002;;
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
Open;Trades;HKD;2018-01-01;1398;1398;Stocks;6.48;6480.0;-1000.0;828.8199741599999;-25.17496;C
Open;Trades;HKD;2018-02-05;215;215;Stocks;3.35;6700.0;-2000.0;856.9589239;-25.1809;C
Open;Trades;HKD;2018-02-19;215;215;Stocks;3.4;6800.0;-2000.0;869.7493556;-25.1836;C
Open;Trades;USD;2018-08-17;FCX;FCX;Stocks;21.0;-21000.0;1000.0;-21000.0;0.0;"A;O"
Open;Trades;USD;2018-10-26;AMZN;AMZN 15FEB19 1800.0 P;Equity and Index Options;216.21;21621.0;-1.0;21621.0;-1.367373;O
Open;Trades;USD;2018-01-18;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;3.08;3080.0;-10.0;3080.0;-5.006148;O
Open;Trades;USD;2018-08-17;FCX;FCX 17AUG18 21.0 P;Equity and Index Options;0.0;0.0;10.0;0.0;0.0;"A;C"
Open;Transaction Fees;HKD;2018-01-01;1398;1398;Stocks;6.48;-7.17496;0.0;-0.9177083583023199;;
Open;Transaction Fees;HKD;2018-02-05;215;215;Stocks;3.35;-7.1809;0.0;-0.9184681099452999;;
Open;Transaction Fees;HKD;2018-02-19;215;215;Stocks;3.4;-7.1836;0.0;-0.9188134516012;;
Open;Withholding Tax;HKD;2018-06-14;386;386;;;-98.64;;-12.61648182888;;
Open;Withholding Tax;HKD;2018-07-26;857;857;;;-14.86;;-1.9006581506199998;;
Open;Withholding Tax;HKD;2018-09-21;386;386;;;-36.5;;-4.668507570499999;;
Open;Withholding Tax;HKD;2018-11-01;857;857;;;-20.39;;-2.60796902363;;
Open;Withholding Tax;USD;2018-03-30;NOV;NOV;;;-1.5;;-1.5;;
Open;Withholding Tax;USD;2018-03-30;NOV;NOV;;;1.5;;1.5;;
Open;Withholding Tax;USD;2018-03-30;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2018-06-29;NOV;NOV;;;-1.5;;-1.5;;
Open;Withholding Tax;USD;2018-06-29;NOV;NOV;;;1.5;;1.5;;
Open;Withholding Tax;USD;2018-06-29;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2018-09-28;NOV;NOV;;;-4.5;;-4.5;;
Open;Withholding Tax;USD;2018-11-01;FCX;FCX;;;-15.0;;-15.0;;
Open;Withholding Tax;USD;2018-12-21;NOV;NOV;;;-4.5;;-4.5;;
Open;Dividends;CNH;2018-07-26;3188;3188;;;350.0;;52.161674600000005;;
Open;Dividends;HKD;2018-04-06;5;5;;;1317.81;;168.55358798576998;;
Open;Dividends;HKD;2018-05-17;2888;2888;;;110.45;;14.12703181265;;
Open;Dividends;HKD;2018-06-14;386;386;;;986.4;;126.16481828879999;;
Open;Dividends;HKD;2018-07-04;200;200;;;40.0;;5.11617268;;
Open;Dividends;HKD;2018-07-05;5;5;;;627.87;;80.30728351479;;
Open;Dividends;HKD;2018-07-26;857;857;;;148.62;;19.00913959254;;
Open;Dividends;HKD;2018-07-30;2828;2828;;;440.0;;56.277899479999995;;
Open;Dividends;HKD;2018-09-21;386;386;;;365.0;;46.685075704999996;;
Open;Dividends;HKD;2018-09-27;5;5;;;627.73;;80.28937691041;;
Open;Dividends;HKD;2018-09-28;2828;2828;;;800.0;;102.3234536;;
Open;Dividends;HKD;2018-10-05;200;200;;;45.0;;5.755694265;;
Open;Dividends;HKD;2018-10-22;2888;2888;;;60.13;;7.69088658121;;
Open;Dividends;HKD;2018-11-01;857;857;;;203.88;;26.077132149959997;;
Open;Dividends;HKD;2018-11-21;5;5;;;626.32;;80.10903182344;;
Open;Dividends;SGD;2018-05-16;F34;F34;;;490.0;;375.87190921;;
Open;Dividends;SGD;2018-08-31;F34;F34;;;245.0;;187.935954605;;
Open;Dividends;USD;2018-03-27;BHP;BHP;;;1320.0;;1320.0;;
Open;Dividends;USD;2018-03-30;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-04-05;SOUHY;SOUHY;;;73.0;;73.0;;
Open;Dividends;USD;2018-06-29;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-09-25;BHP;BHP;;;1512.0;;1512.0;;
Open;Dividends;USD;2018-09-28;NOV;NOV;;;15.0;;15.0;;
Open;Dividends;USD;2018-10-11;SOUHY;SOUHY;;;62.0;;62.0;;
Open;Dividends;USD;2018-11-01;FCX;FCX;;;50.0;;50.0;;
Open;Dividends;USD;2018-12-21;NOV;NOV;;;15.0;;15.0;;
Open;Withholding Tax;HKD;2025-09-25;386;386;;;-19.34;;-2.47366949078;;
Open;Withholding Tax;HKD;2025-10-23;857;857;;;-48.11;;-6.15347669087;;
Open;Withholding Tax;HKD;2025-10-24;2318;2318;;;-52.02;;-6.65358257034;;
Open;Withholding Tax;HKD;2025-12-23;390;390;;;-89.85;;-11.492202882449998;;
Open;Dividends;HKD;2025-09-16;215;215;;;1140.0;;145.81092138;;
Open;Dividends;HKD;2025-09-23;1299;1299;;;294.0;;37.603869198;;
Open;Dividends;HKD;2025-09-25;386;386;;;193.4;;24.7366949078;;
Open;Dividends;HKD;2025-09-26;1883;1883;;;600.0;;76.7425902;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;26.5;;3.3894644004999996;;
Open;Dividends;HKD;2025-09-29;2828;2828;;;173.5;;22.1913989995;;
Open;Dividends;HKD;2025-09-30;3918;3918;;;159.42;;20.390506216139997;;
Open;Dividends;HKD;2025-10-09;1972;1972;;;1960.0;;250.69246131999998;;
Open;Dividends;HKD;2025-10-23;857;857;;;482.1;;61.662671225699995;;
Open;Dividends;HKD;2025-10-24;2318;2318;;;520.24;;66.54094187608;;
Open;Dividends;HKD;2025-10-31;27;27;;;700.0;;89.5330219;;
Open;Dividends;HKD;2025-11-20;16;16;;;12600.0;;1611.5943942;;
Open;Dividends;HKD;2025-11-28;2800;2800;;;825.0;;105.521061525;;
Open;Dividends;HKD;2025-12-18;5;5;;;1866.53;;238.73724481000997;;
Open;Dividends;HKD;2025-12-23;390;390;;;898.95;;114.97958576715;;
Open;Dividends;CNH;2022-07-29;3188;3188;;;370.0;;55.142341720000005;;
Open;Dividends;HKD;2022-05-26;215;215;;;2605.0;;333.190745785;;
Open;Dividends;HKD;2022-05-31;2800;2800;;;40.0;;5.11617268;;
Open;Dividends;HKD;2022-06-13;1883;1883;;;1700.0;;217.4373389;;
Open;Dividends;HKD;2022-07-28;2828;2828;;;460.0;;58.83598582;;
Open;Dividends;HKD;2022-09-02;215;215;;;1140.0;;145.81092138;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;283.5;;36.260873869499996;;
Open;Dividends;HKD;2022-09-29;2828;2828;;;76.5;;9.7846802505;;
Open;Dividends;HKD;2022-09-30;1883;1883;;;600.0;;76.7425902;;
Open;Dividends;HKD;2022-11-30;2800;2800;;;280.0;;35.813208759999995;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;243.6;;36.3045255216;;
Open;Dividends;CNH;2021-07-30;3188;3188;;;56.4;;8.4054812784;;
Open;Dividends;HKD;2021-05-26;215;215;;;2605.0;;333.190745785;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;12.22;;1.5629907537399998;;
Open;Dividends;HKD;2021-05-31;2800;2800;;;52.78;;6.7507898512599995;;
Open;Dividends;HKD;2021-06-04;1883;1883;;;1600.0;;204.6469072;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;8.8;;1.1255579896;;
Open;Dividends;HKD;2021-07-29;2828;2828;;;311.2;;39.803823450399996;;
Open;Dividends;HKD;2021-09-03;215;215;;;1109.72;;141.93797866123998;;
Open;Dividends;HKD;2021-09-03;215;215;;;30.28;;3.8729427187599996;;
Open;Dividends;HKD;2021-09-03;215;215;;;9637.06;;1232.6215771880197;;
Open;Dividends;HKD;2021-09-03;215;215;;;262.94;;33.63116111198;;
Open;Dividends;HKD;2021-09-29;1883;1883;;;550.0;;70.34737435;;
Open;Dividends;HKD;2021-09-29;2828;2828;;;396.0;;50.650109531999995;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;234.9;;30.0447240633;;
Open;Dividends;HKD;2021-11-30;2800;2800;;;35.1;;4.489441526699999;;
Open;Trades;HKD;2020-09-24;1883;1883;Stocks;2.45;-24500.0;10000.0;-3133.6557664999996;-45.2615;O
Open;Trades;HKD;2020-09-02;215;215;Stocks;1.21;-60500.0;50000.0;-7738.2111785;-111.0335;O
Open;Transaction Fees;HKD;2020-09-02;215;215;Stocks;1.21;-62.6335;0.0;-8.0110950388195;;
Open;Transaction Fees;HKD;2020-09-24;1883;1883;Stocks;2.45;-25.6615;0.0;-3.2822166306955;;
Open;Dividends;CNH;2020-07-31;3188;3188;;;380.0;;56.63267528;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;9.0;;1.151138853;;
Open;Dividends;HKD;2020-09-29;2828;2828;;;591.0;;75.59145134699999;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;2.64;;0.33766739687999997;;
Open;Dividends;HKD;2020-11-30;2800;2800;;;327.36;;41.87075721312;;

---

# [open_positions] START  
Symbol;Account;Asset Category;Description;Currency;Current Price;Cost Price;Quantity;Value;Value in USD;Unrealized Gain in USD;Weight;Unrealized Gain in USD
912797RG4 - United States Treasury B 08/06/26;IBKR;Treasury Bills;;USD;99.898481;98.141859649;1710.0;170826.4;170826.4;;0.0656261610010795;3003.822510210012
912797TC1 - United States Treasury B 12/24/26;IBKR;Treasury Bills;;USD;98.374882;97.133959459;740.0;72797.41;72797.41;;0.027966488488439696;918.2826803400025
912797TV9 - United States Treasury B 02/18/27;IBKR;Treasury Bills;;USD;97.814438;97.064044118;680.0;66513.82;66513.82;;0.025552529703352773;510.2678397599982
912797UE5 - United States Treasury B 04/15/27;IBKR;Treasury Bills;;USD;97.178631;96.90631;1000.0;97178.63;97178.63;;0.03733299079208093;272.32099999999093
912797UX3 - United States Treasury B 05/13/27;IBKR;Treasury Bills;;USD;96.882897;96.625351852;1080.0;104633.53;104633.53;;0.0401969302513621;278.14875984000594
912797VF1 - United States Treasury B 06/10/27;IBKR;Treasury Bills;;USD;96.560568;96.38057197;2640.0;254919.9;254919.9;;0.09793225402969966;475.18951919999495
AMZN;IBKR;Stocks;AMAZON.COM INC;USD;231.39;207.8670155;300.0;69417.0;69417.0;;0.026667840674579196;7056.895349999993
BABA;IBKR;Stocks;ALIBABA GROUP HOLDING-SP ADR;USD;115.0;111.16845686;500.0;57500.0;57500.0;;0.022089701928753817;1915.771569999997
BHP;IBKR;Stocks;BHP GROUP LTD-SPON ADR;USD;83.93;35.102226747;1200.0;100716.0;100716.0;;0.03869193772967599;58593.3279036
BHP 18DEC26 95 C;IBKR;Equity and Index Options;;USD;3.6532;4.0005117;-4.0;-1461.28;-1461.28;;-0.0005613780805991196;1.3892467999999987
BRK B;IBKR;Stocks;BERKSHIRE HATHAWAY INC-CL B;USD;497.18;469.0869395;100.0;49718.0;49718.0;;0.019100100878152736;2809.306049999998
BRK B 28AUG26 505 C;IBKR;Equity and Index Options;;USD;6.7;6.94202333;-1.0;-670.0;-670.0;;-0.00025739304856113143;0.24202332999999943
CPRT;IBKR;Stocks;COPART INC;USD;29.79;27.171926655;1100.0;32769.0;32769.0;;0.012588825087014501;2879.880679499999
CPRT 18SEP26 25 P;IBKR;Equity and Index Options;;USD;0.325;0.63450071;-5.0;-162.5;-162.5;;-6.242741849430426e-05;1.5475035499999998
CPRT 18SEP26 32.5 C;IBKR;Equity and Index Options;;USD;0.8;0.28450792;-6.0;-480.0;-480.0;;-0.0001844009900139449;-3.09295248
CRM 09/18/2026 160.00 P;Schwab;Equity and Index Options;SALESFORCE;USD;3.6;-6.94315;2.0;720.0;720.0;;0.0002766014850209174;21.0863
DNOW;IBKR;Stocks;DNOW INC;USD;14.19;26.81106924;25.0;354.75;354.75;;0.00013628385668218115;-315.526731
FAST;IBKR;Stocks;FASTENAL CO;USD;47.64;42.17242457;100.0;4764.0;4764.0;;0.0018301798258884032;546.7575430000004
FCX;IBKR;Stocks;FREEPORT-MCMORAN INC;USD;62.72;11.2079574;500.0;31360.0;31360.0;;0.012047531347577734;25756.0213
CASH - Schwab;Schwab;Forex;;USD;1.0;;-1.0;;70170.89;0.0;0.026957461637832556;
Forex;IBKR;Forex;;USD;0.0;;-1.0;;7505.028857792;0.0;0.002883197398875148;
GEHC 17JUN27 57.5 P;IBKR;Equity and Index Options;;USD;5.6003;5.33945409;-1.0;-560.03;-560.03;;-0.00021514601341147824;-0.26084590999999957
GEHC 17JUN27 65 C;IBKR;Equity and Index Options;;USD;7.8462;8.8804028;1.0;784.62;784.62;;0.0003014264683015447;-1.034202800000001
GEHC 17JUN27 80 C;IBKR;Equity and Index Options;;USD;3.076;3.91948334;-1.0;-307.6;-307.6;;-0.00011817030110060304;0.8434833400000001
GOOG;IBKR;Stocks;ALPHABET INC-CL C;USD;326.57;93.120965218;400.0;130628.0;130628.0;;0.05018319275737832;93379.6139128
GOOG 18DEC26 410 C;IBKR;Equity and Index Options;;USD;6.0466;32.055824165;-2.0;-1209.32;-1209.32;;-0.00046458292759096634;52.01844833
H78;IBKR;Stocks;HONGKONG LAND HOLDINGS LTD;USD;7.96;4.208;1000.0;7960.0;7960.0;;0.00305798308439792;3752.0
ICE;IBKR;Stocks;INTERCONTINENTAL EXCHANGE IN;USD;148.73;152.14704663;300.0;44619.0;44619.0;;0.017141224527983768;-1025.1139890000047
IVV;IBKR;Stocks;ISHARES CORE S&P 500 ETF;USD;742.55;402.65670378;100.0;74255.0;74255.0;;0.02852644898642808;33989.329622
IVV;Schwab;Stocks;ISHARES CORE S&P 500 ETF;USD;735.96;531.9820000000001;10.0;7359.6;7359.6;;0.0028273281793888103;2039.7799999999995
META;IBKR;Stocks;META PLATFORMS INC-CLASS A;USD;593.87;230.10913779;200.0;118774.0;118774.0;;0.045629256641492276;72752.17244200001
META 15JAN27 460 P;IBKR;Equity and Index Options;;USD;15.275;19.71258137;-1.0;-1527.5;-1527.5;;-0.0005868177338464601;4.437581369999998
META 15JAN27 520 P;IBKR;Equity and Index Options;;USD;31.575;62.7404795;1.0;3157.5;3157.5;;0.0012130127624354814;-31.1654795
META 15JAN27 640 P;IBKR;Equity and Index Options;;USD;90.4578;77.2419796;-1.0;-9045.78;-9045.78;;-0.0034751058071840475;-13.215820400000013
META 15JAN27 800 C;IBKR;Equity and Index Options;;USD;18.7613;35.535755475;-2.0;-3752.26;-3752.26;;-0.001441500955811927;33.54891095000001
MSFT;Schwab;Stocks;MICROSOFT CORP;USD;425.01;470.3566;100.0;42501.0;42501.0;;0.016327555159547234;-4534.660000000003
MSFT 08/21/2026 355.00 P;Schwab;Equity and Index Options;MICROSOFT CORP;USD;5.325;-8.4432;1.0;532.5;532.5;;0.00020456984829672013;13.7682
MSFT 15JAN27 420 P;IBKR;Equity and Index Options;;USD;53.3451;53.1437823;-2.0;-10669.02;-10669.02;;-0.004098703855163705;-0.4026354000000083
NOV;IBKR;Stocks;NOV INC;USD;20.47;62.158401437;300.0;6141.0;6141.0;;0.002359180165990908;-12506.520431100002
NVDA;Moomoo-me;Stocks;NVIDIA;USD;191.48;0.0;0.4717;90.321116;90.321116;;3.4698548353259086e-05;90.321116
NVDA;Moomoo-mom;Stocks;NVIDIA;USD;191.68;0.0;1.4431;276.613408;276.613408;;0.0001062662214298568;276.613408
NVO;IBKR;Stocks;NOVO-NORDISK A/S-SPONS ADR;USD;49.67;58.2644644;200.0;9934.0;9934.0;;0.0038163321558302684;-1718.8928799999999
PAYC;IBKR;Stocks;PAYCOM SOFTWARE INC;USD;151.3;215.9971074;200.0;30260.0;30260.0;;0.011624945745462443;-12939.421479999999
PDD;IBKR;Stocks;PDD HOLDINGS INC;USD;84.85;96.7605204;100.0;8485.0;8485.0;;0.003259671667225672;-1191.052040000001
PDD 18DEC26 100 C;IBKR;Equity and Index Options;;USD;4.4901;12.173745;2.0;898.02;898.02;;0.00034499120219233916;-15.36729
PDD 18DEC26 110 C;IBKR;Equity and Index Options;;USD;2.385;11.5805045;1.0;238.5;238.5;;9.162424191317888e-05;-9.1955045
PDD 18DEC26 120 C;IBKR;Equity and Index Options;;USD;1.3167;5.8261019;-2.0;-263.34;-263.34;;-0.00010116699314640051;9.0188038
PDD 18DEC26 130 C;IBKR;Equity and Index Options;;USD;0.762;6.3994626;-1.0;-76.2;-76.2;;-2.9273657164713756e-05;5.637462599999999
PDD 18DEC26 90 P;IBKR;Equity and Index Options;;USD;11.2601;8.553596777;-4.0;-4504.04;-4504.04;;-0.0017303113230466842;-10.826012892000001
PDD 20NOV26 100 C;IBKR;Equity and Index Options;;USD;3.375;10.7569825;1.0;337.5;337.5;;0.000129656946103555;-7.3819824999999994
PDD 20NOV26 130 C;IBKR;Equity and Index Options;;USD;0.4654;3.12292012;-1.0;-46.54;-46.54;;-1.787921265676874e-05;2.65752012
SOUHY;IBKR;Stocks;SOUTH32 - ADR;USD;15.525;7.17;300.0;4657.5;4657.5;;0.0017892658562290592;2506.5
TSM;IBKR;Stocks;TAIWAN SEMICONDUCTOR-SP ADR;USD;399.09;181.60807492;300.0;119727.0;119727.0;;0.045995369440415795;65244.57752399999
UNH;IBKR;Stocks;UNITEDHEALTH GROUP INC;USD;417.64;322.13551065;200.0;83528.0;83528.0;;0.03208884561225998;19100.897869999993
UNH 15JAN27 360 P;IBKR;Equity and Index Options;;USD;14.5968;12.18938278;-1.0;-1459.68;-1459.68;;-0.0005607634106324065;-2.4074172199999992
UNH 17JUN27 360 P;IBKR;Equity and Index Options;;USD;24.0799;21.4891912;-1.0;-2407.99;-2407.99;;-0.0009250744582159983;-2.590708799999998
UNH 18DEC26 440 C;IBKR;Equity and Index Options;;USD;25.1001;26.01244859;-1.0;-2510.01;-2510.01;;-0.0009642673519685457;0.9123485900000006
9CI;IBKR;Stocks;CAPITALAND INVESTMENT LTD/SI;SGD;2.51;2.323057932;1200.0;3012.0;2310.4616133480004;;0.000887607101855391;172.08066614896038
BS6;IBKR;Stocks;YANGZIJIANG SHIPBUILDING;SGD;3.97;0.171019587;7000.0;27790.0;21317.30685091;;0.00818944268278928;20399.000298367206
C38U;IBKR;Stocks;CAPITALAND INTEGRATED COMMER;SGD;2.44;1.364696005;222.0;541.68;415.51488934872;;0.00015962782700299736;183.1167297125662
CGP1;IBKR;Stocks;NOBLE GROUP LTD;SGD;0.0;85.85;50.0;0.0;0.0;;0.0;-3292.7146332325
D05;IBKR;Stocks;DBS GROUP HOLDINGS LTD;SGD;74.38;18.538145455;220.0;16363.6;12552.2807623444;;0.004822193748977714;9423.805277478325
F34;IBKR;Stocks;WILMAR INTERNATIONAL LTD;SGD;3.85;3.040549529;9000.0;34650.0;26579.51357985;;0.01021101795461132;5588.259684717007
CASH - DBS;DBS;Forex;;SGD;0.763247271;;-0.777;;1043.95389;18.47775410253007;0.00040105443926022696;
CASH - Moomoo-me;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;7038.47781;124.57950822836938;0.0027039630757399644;
CASH - Moomoo-mom;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;75879.44238000001;1343.049430789266;0.029150508951204353;
CASH - OCBC;OCBC;Forex;;SGD;0.763247271;;-0.777;;6154.438290000001;108.93220328133066;0.0023643427367827717;
CASH - OCBC Joint;OCBC Joint;Forex;;SGD;0.763247271;;-0.777;;30062.78268;532.1046373023601;0.011549181018262659;
CASH - POSB;POSB;Forex;;SGD;0.763247271;;-0.777;;6713.0158200000005;118.81890263214063;0.0025789307566401547;
CASH - UOB;UOB;Forex;;SGD;0.763247271;;-0.777;;6898.25262;122.09755322574074;0.0026500929426964363;
Forex;IBKR;Forex;;SGD;0.767085529;;-0.77442;;14103.185845;133.570165;0.005418002983684696;
Fullerton SGD Cash Fund;Moomoo-me;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0;
Fullerton SGD Cash Fund;Moomoo-mom;Forex;;SGD;0.763247271;;-0.777;;0.0;0.0;0.0;
HMN;IBKR;Stocks;CAPITALAND ASCOTT TRUST;SGD;0.915;0.527017662;68.0;62.22;47.72806161438;;1.8335628777371322e-05;20.237863315142306
O39;IBKR;Stocks;OVERSEA-CHINESE BANKING CORP;SGD;29.29;8.7231;800.0;23432.0;17974.348115528002;;0.006905182473663851;12621.257093112079
BS6;Moomoo-mom;Stocks;YZJ Shipbuilding;SGD;3.87;4.015;200.0;774.0;601.398;;0.00023103830540084676;-22.245480340999936
P40U;Moomoo-me;Stocks;StarhillGbl Reit;SGD;0.555;0.555;1200.0;666.0000000000001;517.4820000000001;;0.00019880040232165885;0.0
P40U;Moomoo-mom;Stocks;StarhillGbl Reit;SGD;0.555;0.5448;4200.0;2331.0;1811.187;;0.0006958014081258058;32.861944062360315
Z74;Moomoo-me;Stocks;Singtel;SGD;4.59;4.29;100.0;459.0;356.64300000000003;;0.00013701108808654867;23.01256586999999
Z74;Moomoo-mom;Stocks;Singtel;SGD;4.59;4.88;100.0;459.0;356.64300000000003;;0.00013701108808654867;-22.245480341000004
Z77;Moomoo-mom;Stocks;Singtel 10;SGD;4.58;4.28;120.0;549.6;427.0392000000001;;0.00016405510678075633;27.615079043999984
1299;IBKR;Stocks;AIA GROUP LTD;HKD;77.95;55.302399867;600.0;46770.0;5982.084906089999;;0.0022981299563134844;1738.0354960202847
1398;IBKR;Stocks;IND & COMM BK OF CHINA - H;HKD;7.29;5.051969413;6000.0;43740.0;5594.53482558;;0.0021492453343842596;1717.5226419320643
14;IBKR;Stocks;HYSAN DEVELOPMENT CO;HKD;18.05;28.5525695;1000.0;18050.0;2308.67292185;;0.000886919942515681;-1343.3239786425313
16;IBKR;Stocks;SUN HUNG KAI PROPERTIES;HKD;121.5;88.851861372;4500.0;546750.0;69931.68531974999;;0.02686556667980324;18791.270426410454
1810;IBKR;Stocks;XIAOMI CORP-CLASS B;HKD;28.68;38.85377485;600.0;17208.0;2200.977486936;;0.0008455467241445893;-780.7618341006165
1883;IBKR;Stocks;CITIC TELECOM INTERNATIONAL;HKD;2.68;2.45452615;10000.0;26800.0;3427.8356956;;0.0013168672830703738;288.39078785610474
1972;IBKR;Stocks;SWIRE PROPERTIES LTD;HKD;23.44;19.720971466;5600.0;131264.0;16789.232266688;;0.006449898024065282;2663.8069054507746
200;IBKR;Stocks;MELCO INTERNATIONAL DEVELOP.;HKD;3.58;11.460065577;4000.0;14320.0;1831.58981944;;0.0007036395333420804;-4031.5776221655833
215;IBKR;Stocks;HUTCHISON TELECOMM HONG KONG;HKD;1.14;1.21222067;50000.0;57000.0;7290.546068999999;;0.002800799818470571;-461.8667734816201
2318;IBKR;Stocks;PING AN INSURANCE GROUP CO-H;HKD;56.65;86.4593701;500.0;28325.0;3622.889779025;;0.0013918009624241919;-1906.3735614203608
27;IBKR;Stocks;GALAXY ENTERTAINMENT GROUP L;HKD;33.6;51.5945905;1000.0;33600.0;4297.5850512;;0.001650997787730021;-2301.5858075971887
2800;IBKR;Stocks;TRACKER FUND OF HONG KONG-B;HKD;25.7;21.769917967;1500.0;38550.0;4930.71142035;;0.0018942251403866758;754.0116872772543
2828;IBKR;Stocks;HANGSENG CHINA ENT INDEX;HKD;85.96;92.44076825;400.0;34384.0;4397.862035728;;0.0016895210694437214;-331.56729466061466
2888;IBKR;Stocks;STANDARD CHARTERED PLC;HKD;226.0;55.6;28.0;6328.0;809.3785179759999;;0.00031093791668915395;610.2570772703999
3188;IBKR;Stocks;CHINAAMC ETF SERIES - CH;HKD;56.9;26.37987258;1000.0;56900.0;7277.7556373;;0.0027958861345785176;3903.6560524080714
3690;IBKR;Stocks;MEITUAN-CLASS B;HKD;89.3;231.697888617;60.0;5358.0;685.311330486;;0.0002632751829362337;-1092.7982811479674
386;IBKR;Stocks;CHINA PETROLEUM & CHEMICAL-H;HKD;4.31;5.815174;2000.0;8620.0;1102.53521254;;0.00042355955149502326;-385.036504872316
390;IBKR;Stocks;CHINA RAILWAY GROUP LTD-H;HKD;3.59;3.92724184;10000.0;35900.0;4591.7649802999995;;0.0017640125172472545;-431.3468720902332
3918;IBKR;Stocks;NAGACORP LTD;HKD;3.48;7.594013956;2036.0;7085.28;906.2378991537599;;0.0003481482620668977;-1071.3434955674397
3968;IBKR;Stocks;CHINA MERCHANTS BANK - H;HKD;48.96;39.7833745;500.0;24480.0;3131.0976801599995;;0.0012028698167747295;586.8650084711417
5;IBKR;Stocks;HSBC HOLDINGS PLC;HKD;162.8;49.092166333;2400.0;390720.0;49974.77473824;;0.01919874570303196;34904.934726545376
66;IBKR;Stocks;MTR CORP;HKD;32.8;40.07708;500.0;16400.0;2097.6307988;;0.0008058441582967959;-465.3849735771803
700;IBKR;Stocks;TENCENT HOLDINGS LTD;HKD;443.0;452.856419642;706.0;312758.0;40003.098376286;;0.015367939467109104;-890.0391073970278
808;IBKR;Stocks;PROSPERITY REIT;HKD;1.5;2.339531045;20000.0;30000.0;3837.1295099999998;;0.0014741051676160901;-2147.592898220426
857;IBKR;Stocks;PETROCHINA CO LTD-H;HKD;9.85;7.7972334;2000.0;19700.0;2519.7150448999996;;0.0009679957267345658;525.1154198668244
939;IBKR;Stocks;CHINA CONSTRUCTION BANK-H;HKD;8.99;6.39177626;4000.0;35960.0;4599.43923932;;0.0017669607275824868;1329.2961315115422
9618;IBKR;Stocks;JD.COM INC-CLASS A;HKD;119.2;285.565311947;19.0;2264.8;289.6776971416;;0.0001112851127872307;-404.2979903443865
CASH - Citic;Citic;Forex;;HKD;0.128244528;;-0.1276;;79953.53603599999;-403.8580930580793;0.030715648333654207;
CASH - StanChart;StanChart;Forex;;HKD;0.128244528;;-0.1276;;1615.37134;-8.159498895199931;0.000620575154866985;
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;127600.0;-644.5280000000057;0.04901992984537369;
CASH - StanChart AsiaMiles TD;StanChart AsiaMiles TD;Forex;;HKD;0.128244528;;-0.1276;;1276.0;-6.445279999999912;0.0004901992984537368;
CASH - StanChart Payroll;StanChart Payroll;Forex;;HKD;0.128244528;;-0.1276;;32635.340936;-164.84632463007802;0.012537477454252132;
Forex;IBKR;Forex;;HKD;0.127904317;;-0.12752;;1383.731332;-4.170258;0.000531586307362739;
BARCl;IBKR;Stocks;;GBP;5.304;3.5479;1000.0;5304.0;7025.57701404;;0.002699006993350398;2326.0964921485006
Forex;IBKR;Forex;;GBP;1.324580885;;-1.3288;;1554.790876;4.936665;0.000597302034998018;
IMBl;IBKR;Stocks;IMPERIAL BRANDS PLC;GBP;28.18;29.95515;1000.0;28180.0;37326.6893393;;0.014339746808562256;-2351.32975800775
NWG 18SEP26 5 P;IBKR;Equity and Index Options;;GBP;0.01;0.5086;-1.0;-10.0;-13.24580885;;-5.0886255530739025e-06;0.6604360292610001
RRl;IBKR;Stocks;ROLLS-ROYCE HOLDINGS PLC;GBP;14.136;11.090386;100.0;1413.6;1872.4275390359999;;0.0007193281081825268;403.41620874883887
Forex;IBKR;Forex;;EUR;1.165036328;;-1.1368;;1891.393295;-46.979242;0.0007266141585494529;
MC;IBKR;Stocks;LVMH MOET HENNESSY LOUIS VUI;EUR;466.8;619.0851265;10.0;4668.0;5438.389579104;;0.0020892592134756226;-1774.1770458657547
MC 18DEC26 440 P;IBKR;Equity and Index Options;;EUR;24.5368;37.9386;-1.0;-2453.68;-2858.62633728704;;-0.0010981948472409736;15.613583860590403
RI;IBKR;Stocks;PERNOD-RICARD SA;EUR;64.8;83.7114;100.0;6480.0;7549.43540544;;0.0029002570058530492;-2203.2468013339203
RMS;IBKR;Stocks;HERMES INTL;EUR;1659.0;2195.362096667;3.0;4977.0;5798.385804456;;0.002227558505884356;-1874.6439827379083
RMS 17DEC27 1520 P;IBKR;Equity and Index Options;;EUR;155.4529;193.044;5.0;7772.65;9055.4196148292;;0.0034788090457629167;-218.97498554740406
RMS 17DEC27 1680 P;IBKR;Equity and Index Options;;EUR;229.4001;214.886;-5.0;-11470.01;-13362.97833252328;;-0.005133638404275391;-84.54726884112408
RMS 17DEC27 1920 P;IBKR;Equity and Index Options;;EUR;372.7844;388.7186;-1.0;-37278.44;-43430.73685116832;;-0.016684730984147;18.563921857617572
RMS 18DEC26 1520 P;IBKR;Equity and Index Options;;EUR;73.6986;130.209;5.0;3684.93;4293.07731613704;;0.0016492660568793328;-329.183344549056
RMS 18DEC26 1600 P;IBKR;Equity and Index Options;;EUR;103.4619;110.436;-5.0;-5173.1;-6026.8494283768005;;-0.002315327085953458;40.62539927552404
SAP;IBKR;Stocks;SAP SE;EUR;151.28;210.1117;100.0;15128.0;17624.669569984;;0.006770846911195204;-6854.106773799761
SAP 18DEC26 190 P;IBKR;Equity and Index Options;;EUR;41.42;31.7783;-2.0;-8284.0;-9651.160941152;;-0.003707674234025719;-22.4658615273552
SAP 18SEP26 175 C;IBKR;Equity and Index Options;;EUR;2.12;15.8017;1.0;212.0;246.987701536;;9.488495142605655e-05;-15.9396775287976
SAP 18SEP26 195 C;IBKR;Equity and Index Options;;EUR;0.62;8.5083;-2.0;-124.0;-144.464504672;;-5.549874517373119e-05;18.3803121323248
Forex;IBKR;Forex;;CNH;0.149033356;;-0.14781;;880.9476;-7.2912;0.0003384325199800182;

---

# [pivot_summary] START  
# PORTFOLIO PIVOT SUMMARY

**Report Date :** 2026-07-30  
**Filter      :** Open Positions | Forex Balances | Cash Balances  
**Generated   :** 2026-07-30 15:29:44  

---

## Equity and Index Options

### Schwab

```
            Amount  Quantity  Value in USD
Underlying                                
CRM          720.0      -2.0         720.0
MSFT         532.5      -1.0         532.5
```

> **SUBTOTAL [Schwab]**  Amount: `       1,252.50`  Quantity: `          -3.00`  Value in USD: `       1,252.50`

### IBKR

```
              Amount  Quantity  Value in USD
Underlying                                  
BHP         -1461.28       4.0      -1461.28
BRK          -670.00       1.0       -670.00
CPRT         -642.50      11.0       -642.50
GEHC          -83.01       1.0        -83.01
GOOG        -1209.32       2.0      -1209.32
MC          -2453.68       1.0      -2858.63
META       -11168.04       3.0     -11168.04
MSFT       -10669.02       2.0     -10669.02
NWG           -10.00       1.0        -13.25
PDD         -3416.10       4.0      -3416.10
RMS        -42463.97       1.0     -49472.07
SAP         -8196.00       3.0      -9548.64
UNH         -6377.68       3.0      -6377.68
```

> **SUBTOTAL [IBKR]**  Amount: `     -88,820.60`  Quantity: `          37.00`  Value in USD: `     -97,589.53`

> **SUBTOTAL [Equity and Index Options]**  Amount: `     -87,568.10`  Quantity: `          34.00`  Value in USD: `     -96,337.03`

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
Forex       9058.53      1.55       7038.48
```

> **SUBTOTAL [Moomoo-me]**  Amount: `       9,058.53`  Quantity: `           1.55`  Value in USD: `       7,038.48`

### Moomoo-mom

```
              Amount  Quantity  Value in USD
Underlying                                  
Forex       97656.94      1.55      75879.44
```

> **SUBTOTAL [Moomoo-mom]**  Amount: `      97,656.94`  Quantity: `           1.55`  Value in USD: `      75,879.44`

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
Forex       45361.27      4.52      27319.08
```

> **SUBTOTAL [IBKR]**  Amount: `      45,361.27`  Quantity: `           4.52`  Value in USD: `      27,319.08`

### UOB

```
             Amount  Quantity  Value in USD
Underlying                                 
Forex       8878.06      0.78       6898.25
```

> **SUBTOTAL [UOB]**  Amount: `       8,878.06`  Quantity: `           0.78`  Value in USD: `       6,898.25`

> **SUBTOTAL [Forex]**  Amount: `   2,192,738.15`  Quantity: `          13.15`  Value in USD: `     474,360.58`

---

## Stocks

### Moomoo-me

```
            Amount  Quantity  Value in USD
Underlying                                
NVDA         90.32     -0.47         90.32
P40U        666.00  -1200.00        517.48
Z74         459.00   -100.00        356.64
```

> **SUBTOTAL [Moomoo-me]**  Amount: `       1,215.32`  Quantity: `      -1,300.47`  Value in USD: `         964.45`

### Moomoo-mom

```
             Amount  Quantity  Value in USD
Underlying                                 
NVDA         276.61     -1.44        276.61
P40U        3105.00  -4400.00       2412.58
Z74         1008.60   -220.00        783.68
```

> **SUBTOTAL [Moomoo-mom]**  Amount: `       4,390.21`  Quantity: `      -4,621.44`  Value in USD: `       3,472.88`

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
1299         46770.00    -600.0       5982.08
1398         43740.00   -6000.0       5594.53
14           18050.00   -1000.0       2308.67
16          546750.00   -4500.0      69931.69
1810         17208.00    -600.0       2200.98
1883         26800.00  -10000.0       3427.84
1972        131264.00   -5600.0      16789.23
200          14320.00   -4000.0       1831.59
215          57000.00  -50000.0       7290.55
2318         28325.00    -500.0       3622.89
27           33600.00   -1000.0       4297.59
2800         38550.00   -1500.0       4930.71
2828         34384.00    -400.0       4397.86
2888          6328.00     -28.0        809.38
3188         56900.00   -1000.0       7277.76
3690          5358.00     -60.0        685.31
386           8620.00   -2000.0       1102.54
390          35900.00  -10000.0       4591.76
3918          7085.28   -2036.0        906.24
3968         24480.00    -500.0       3131.10
5           390720.00   -2400.0      49974.77
66           16400.00    -500.0       2097.63
700         312758.00    -706.0      40003.10
808          30000.00  -20000.0       3837.13
857          19700.00   -2000.0       2519.72
939          35960.00   -4000.0       4599.44
9618          2264.80     -19.0        289.68
9CI           3012.00   -1200.0       2310.46
AMZN         69417.00    -300.0      69417.00
BABA         57500.00    -500.0      57500.00
BARCl         5304.00   -1000.0       7025.58
BHP         100716.00   -1200.0     100716.00
BRK          49718.00    -100.0      49718.00
BS6          27790.00   -7000.0      21317.31
C38U           541.68    -222.0        415.51
CGP              0.00     -50.0          0.00
CPRT         32769.00   -1100.0      32769.00
D05          16363.60    -220.0      12552.28
DNOW           354.75     -25.0        354.75
F34          34650.00   -9000.0      26579.51
FAST          4764.00    -100.0       4764.00
FCX          31360.00    -500.0      31360.00
GOOG        130628.00    -400.0     130628.00
H78           7960.00   -1000.0       7960.00
HMN             62.22     -68.0         47.73
ICE          44619.00    -300.0      44619.00
IMB          28180.00   -1000.0      37326.69
IVV          74255.00    -100.0      74255.00
MC            4668.00     -10.0       5438.39
META        118774.00    -200.0     118774.00
NOV           6141.00    -300.0       6141.00
NVO           9934.00    -200.0       9934.00
O39          23432.00    -800.0      17974.35
PAYC         30260.00    -200.0      30260.00
PDD           8485.00    -100.0       8485.00
RI            6480.00    -100.0       7549.44
RMS           4977.00      -3.0       5798.39
RRl           1413.60    -100.0       1872.43
SAP          15128.00    -100.0      17624.67
SOUHY         4657.50    -300.0       4657.50
TSM         119727.00    -300.0     119727.00
UNH          83528.00    -200.0      83528.00
```

> **SUBTOTAL [IBKR]**  Amount: `   3,146,804.43`  Quantity: `    -159,247.00`  Value in USD: `   1,403,831.73`

> **SUBTOTAL [Stocks]**  Amount: `   3,202,270.56`  Quantity: `    -165,278.91`  Value in USD: `   1,458,129.66`

---

## Treasury Bills

### IBKR

```
               Amount  Quantity  Value in USD
Underlying                                   
912797RG4   170826.40 -171000.0     170826.40
912797TC1    72797.41  -74000.0      72797.41
912797TV9    66513.82  -68000.0      66513.82
912797UE5    97178.63 -100000.0      97178.63
912797UX3   104633.53 -108000.0     104633.53
912797VF1   254919.90 -264000.0     254919.90
```

> **SUBTOTAL [IBKR]**  Amount: `     766,869.69`  Quantity: `    -785,000.00`  Value in USD: `     766,869.69`

> **SUBTOTAL [Treasury Bills]**  Amount: `     766,869.69`  Quantity: `    -785,000.00`  Value in USD: `     766,869.69`

---

> ### GRAND TOTAL  
> Amount: `   6,074,310.30`  Quantity: `    -950,231.77`  Value in USD: `   2,603,022.90`

---

