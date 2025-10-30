# Cleaning Summary

Input: `C:\Users\Owner\Desktop\CAS 2023\Feature Engineering\cas_data_with_engineered_features.csv`

Output: `C:\Users\Owner\Desktop\CAS 2023\Feature Engineering\cas_data_with_engineered_features_cleaned.csv`


## Column decisions

### 删除列原因说明

为确保建模/分析的稳健性与可解释性，我们依据以下通用准则删除部分列：

- 高缺失或仅在少数处置场景出现：这类列在样本中几乎恒为空或仅在止赎/回购等少量极端事件中才有记录，纳入会造成噪声大、样本外不稳定，且难以泛化。
- 可调利率（ARM）专属字段：本样本大多为固定利率（FRM），ARM 相关列基本全空，携带信息量极低。
- 强泄露风险（事后信息）：移除/结清后才产生的代码、日期、金额等，包含未来信息，用于训练会导致过拟合与失真。
- 高基数标识或非结构化长串：如唯一标识、交易批次名、还款历史长字符串；这类列不利于泛化，易导致维度爆炸或需要特定序列模型，常规建模不直接使用。
- 近似常量/低信息量：在样本内几乎单一取值，贡献有限。
- 工程特征冗余：对同一信息的“差值/比值”重复表达，保留一类以降低共线性。
- 高度共线的时间衍生：在 `Loan Age / Remaining Months To Maturity / Remaining Months to Legal Maturity / Months to Amortization` 等中择优保留，避免冗余。

据此，删除列分组说明如下：

- 违约/处置场景（高缺失）：
  - `Foreclosure Date, Disposition Date, Foreclosure Costs, Property Preservation and Repair Costs, Asset Recovery Costs, Miscellaneous Holding Expenses and Credits, Associated Taxes for Holding Property, Net Sales Proceeds, Credit Enhancement Proceeds, Repurchase Make Whole Proceeds, Other Foreclosure Proceeds, Foreclosure Principal Write-off Amount, Delinquent Accrued Interest`
  - 原因：仅在止赎、处置时才出现，绝大多数样本缺失；纳入会引入稀疏噪声，且事后属性弱于前瞻预测价值。

- 挂牌/处置价格与日期（高缺失）：
  - `Original List Start Date, Original List Price, Current List Start Date, Current List Price`
  - 原因：与处置流程相关，且整体缺失极高，对一般预付款/违约预测帮助有限。

- ARM 相关（与样本主体 FRM 不匹配）：
  - `ARM Initial Fixed-Rate Period  ? 5 YR Indicator, ARM Product Type, Initial Fixed-Rate Period, Interest Rate Adjustment Frequency, Next Interest Rate Adjustment Date, Next Payment Change Date, Index, ARM Cap Structure, Initial Interest Rate Cap Up Percent, Periodic Interest Rate Cap Up Percent, Lifetime Interest Rate Cap Up Percent, Mortgage Margin, ARM Balloon Indicator, ARM Plan Number`
  - 原因：样本以固定利率贷款为主，上述列几乎全空或无效信息。

- 强泄露/结清后信息：
  - `Zero Balance Code, Zero Balance Effective Date, UPB at the Time of Removal, Repurchase Date, Zero Balance Code Change Date, Repurchase Make Whole Proceeds Flag, Interest Bearing UPB`
  - 原因：在贷款移除/结清后才出现或更新，包含未来信息，训练会造成数据泄露。

- 高基数标识/长字符串：
  - `Loan Identifier, Deal Name, Loan Payment History`
  - 原因：前两者为高基数标识，对预测价值有限且易过拟合；`Loan Payment History` 为非结构化长序列，常规表格模型不直接使用，若需应先提取序列统计特征再替代原串。

- 近似常量/低信息量：
  - `Amortization Type, HomeReady® Program Indicator, Loan Holdback Indicator, Loan Holdback Effective Date`
  - 原因：样本内取值高度集中（如普遍为 FRM），对预测增益有限。

- 工程特征冗余（二选一保留“比值”）：
  - 删除：`Interest_Rate_Diff, UPB_Diff, Loan_Age_Ratio`
  - 保留：`Interest_Rate_Ratio, UPB_Ratio`
  - 原因：同一信息的多种表达易造成多重共线，保留尺度更稳健的“比值”表示。

- 时间相关冗余：
  - 删除：`Remaining Months to Legal Maturity, Months to Amortization`
  - 保留：`Loan Age, Remaining Months To Maturity`
  - 原因：上述时间变量高度相关，保留最具解释力且覆盖度更好的代表列。

### Dropped columns

- Loan Identifier
- Remaining Months to Legal Maturity
- Maturity Date
- Mortgage Insurance Percentage
- Amortization Type
- Interest Only First Principal And Interest Payment Date
- Months to Amortization
- Loan Payment History
- Mortgage Insurance Cancellation Indicator
- Zero Balance Code
- Zero Balance Effective Date
- UPB at the Time of Removal
- Repurchase Date
- Last Paid Installment Date
- Foreclosure Date
- Disposition Date
- Foreclosure Costs
- Property Preservation and Repair Costs
- Asset Recovery Costs
- Miscellaneous Holding Expenses and Credits
- Associated Taxes for Holding Property
- Net Sales Proceeds
- Credit Enhancement Proceeds
- Repurchase Make Whole Proceeds
- Other Foreclosure Proceeds
- Modification-Related Non-Interest Bearing UPB
- Principal Forgiveness Amount
- Original List Start Date
- Original List Price
- Current List Start Date
- Current List Price
- Current Period Modification Loss Amount
- Current Period Credit Event Net Gain or Loss
- HomeReady® Program Indicator
- Foreclosure Principal Write-off Amount
- Zero Balance Code Change Date
- Loan Holdback Indicator
- Loan Holdback Effective Date
- Delinquent Accrued Interest
- ARM Initial Fixed-Rate Period  ? 5 YR Indicator
- ARM Product Type
- Initial Fixed-Rate Period
- Interest Rate Adjustment Frequency
- Next Interest Rate Adjustment Date
- Next Payment Change Date
- Index
- ARM Cap Structure
- Initial Interest Rate Cap Up Percent
- Periodic Interest Rate Cap Up Percent
- Lifetime Interest Rate Cap Up Percent
- Mortgage Margin
- ARM Balloon Indicator
- ARM Plan Number
- Deal Name
- Repurchase Make Whole Proceeds Flag
- Alternative Delinquency Resolution
- Alternative Delinquency  Resolution Count
- Total Deferral Amount
- Payment Deferral Modification Event Indicator
- Interest Bearing UPB
- Interest_Rate_Diff
- UPB_Diff
- Loan_Age_Ratio

### Kept columns

- Reference Pool ID
- Monthly Reporting Period
- Channel
- Seller Name
- Servicer Name
- Master Servicer
- Original Interest Rate
- Current Interest Rate
- Original UPB
- UPB at Issuance
- Current Actual UPB
- Original Loan Term
- Origination Date
- First Payment Date
- Loan Age
- Remaining Months To Maturity
- Original Loan to Value Ratio (LTV)
- Original Combined Loan to Value Ratio (CLTV)
- Number of Borrowers
- Debt-To-Income (DTI)
- Borrower Credit Score at Origination
- Co-Borrower Credit Score at Origination
- First Time Home Buyer Indicator
- Loan Purpose
- Property Type
- Number of Units
- Occupancy Status
- Property State
- Metropolitan Statistical Area (MSA)
- Zip Code Short
- Prepayment Penalty Indicator
- Interest Only Loan Indicator
- Current Loan Delinquency Status
- Modification Flag
- Scheduled Principal Current
- Total Principal Current
- Unscheduled Principal Current
- Borrower Credit Score At Issuance
- Co-Borrower Credit Score At Issuance
- Borrower Credit Score Current
- Co-Borrower Credit Score Current
- Mortgage Insurance Type
- Servicing Activity Indicator
- Cumulative Modification Loss Amount
- Cumulative Credit Event Net Gain or Loss
- Relocation Mortgage Indicator
- Property Valuation Method
- High Balance Loan Indicator
- Borrower Assistance Plan
- High Loan to Value (HLTV) Refinance Option Indicator
- Interest_Rate_Ratio
- UPB_Ratio
- LTV_Category
- Credit_Score_Category

## High-missing (>=95%) candidates

- ARM Cap Structure
- ARM Initial Fixed-Rate Period  ? 5 YR Indicator
- ARM Plan Number
- ARM Product Type
- Alternative Delinquency  Resolution Count
- Asset Recovery Costs
- Associated Taxes for Holding Property
- Credit Enhancement Proceeds
- Current List Price
- Current List Start Date
- Current Period Credit Event Net Gain or Loss
- Current Period Modification Loss Amount
- Delinquent Accrued Interest
- Disposition Date
- Foreclosure Costs
- Foreclosure Date
- Foreclosure Principal Write-off Amount
- Index
- Initial Fixed-Rate Period
- Initial Interest Rate Cap Up Percent
- Interest Bearing UPB
- Interest Only First Principal And Interest Payment Date
- Interest Rate Adjustment Frequency
- Last Paid Installment Date
- Lifetime Interest Rate Cap Up Percent
- Loan Holdback Effective Date
- Loan Holdback Indicator
- Maturity Date
- Miscellaneous Holding Expenses and Credits
- Modification-Related Non-Interest Bearing UPB
- Months to Amortization
- Mortgage Insurance Cancellation Indicator
- Mortgage Insurance Percentage
- Mortgage Insurance Type
- Mortgage Margin
- Net Sales Proceeds
- Next Interest Rate Adjustment Date
- Next Payment Change Date
- Original List Price
- Original List Start Date
- Other Foreclosure Proceeds
- Payment Deferral Modification Event Indicator
- Periodic Interest Rate Cap Up Percent
- Principal Forgiveness Amount
- Property Preservation and Repair Costs
- Repurchase Date
- Repurchase Make Whole Proceeds
- Repurchase Make Whole Proceeds Flag
- Total Deferral Amount
- UPB at the Time of Removal
- Zero Balance Code
- Zero Balance Code Change Date
- Zero Balance Effective Date

## Missing rate and cardinality (top 30 by missing)

                                                 column  missing_rate  unique_count
                                          Maturity Date           1.0             0
                          Mortgage Insurance Percentage           1.0             0
Interest Only First Principal And Interest Payment Date           1.0             0
                                 Months to Amortization           1.0             0
              Mortgage Insurance Cancellation Indicator           1.0             0
                            Zero Balance Effective Date           1.0             0
                                        Repurchase Date           1.0             0
                             Last Paid Installment Date           1.0             0
                                       Foreclosure Date           1.0             0
                                       Disposition Date           1.0             0
                                      Foreclosure Costs           1.0             0
                 Property Preservation and Repair Costs           1.0             0
                                   Asset Recovery Costs           1.0             0
             Miscellaneous Holding Expenses and Credits           1.0             0
                  Associated Taxes for Holding Property           1.0             0
                                     Net Sales Proceeds           1.0             0
                            Credit Enhancement Proceeds           1.0             0
                         Repurchase Make Whole Proceeds           1.0             0
                             Other Foreclosure Proceeds           1.0             0
          Modification-Related Non-Interest Bearing UPB           1.0             0
                               Original List Start Date           1.0             0
                                    Original List Price           1.0             0
                                Current List Start Date           1.0             0
                                     Current List Price           1.0             0
                                Mortgage Insurance Type           1.0             0
                Current Period Modification Loss Amount           1.0             0
           Current Period Credit Event Net Gain or Loss           1.0             0
                          Zero Balance Code Change Date           1.0             0
                                Loan Holdback Indicator           1.0             0

                           Loan Holdback Effective Date           1.0             0
