# 🧾 CAS 2023 Loan Data Cleaning Report

**Project:** Credit Risk & Prepayment Prediction (CAS 2023 Dataset)\
**Prepared by:** \[Your Name\]\
**Date:** October 2025

------------------------------------------------------------------------

## 1. Objective（研究目标）

本次数据清洗旨在为后续的贷款违约与提前还款（Prepayment）建模提供高质量的输入数据。原始文件
`cas_data_with_engineered_features.csv`
含有大量与预测无关或噪声较高的变量，通过系统性清洗，生成结构化、可解释、无泄露风险的特征数据集
`cas_data_with_engineered_features_cleaned.csv`。

------------------------------------------------------------------------

## 2. Data Overview（数据概览）

-   **输入文件路径：**\
    `C:\Users\Owner\Desktop\CAS 2023\Feature Engineering\cas_data_with_engineered_features.csv`

-   **输出文件路径：**\
    `C:\Users\Owner\Desktop\CAS 2023\Feature Engineering\cas_data_with_engineered_features_cleaned.csv`

-   **原始数据特征数：** 约 110 列\

-   **清洗后保留特征数：** 约 60 列

------------------------------------------------------------------------

## 3. Cleaning Principles（清洗原则）

在清洗过程中，我们遵循以下七项通用准则，以确保数据的稳健性与建模的有效性：

  ------------------------------------------------------------------------------------------------------
  准则类别                                      说明
  --------------------------------------------- --------------------------------------------------------
  **① 高缺失率剔除**                            缺失率超过 95% 的列删除，防止稀疏噪声与不稳定性。

  **② 可调利率（ARM）排除**                     样本以固定利率（FRM）为主，ARM 字段几乎无效。

  **③ 防止数据泄露**                            删除在贷款结清后才出现的事后信息（如 Zero Balance
                                                Code）。

  **④ 高基数/非结构化字段删除**                 去除唯一标识或长字符串字段，避免过拟合。

  **⑤ 低信息量字段剔除**                        几乎恒定取值的变量无助于预测。

  **⑥ 工程特征冗余消除**                        同一指标的差值与比值类变量中仅保留信息更稳定的"比值"。

  **⑦ 时间变量去重**                            在多个高度相关的时间特征中保留解释力更强者（如 Loan
                                                Age）。
  ------------------------------------------------------------------------------------------------------

------------------------------------------------------------------------

## 4. Column Removal Decisions（删除字段说明）

### 4.1 违约/处置类（高缺失、仅极端场景）

-   例：`Foreclosure Date`, `Disposition Date`, `Net Sales Proceeds`,
    `Delinquent Accrued Interest`\
-   **原因：**
    仅在止赎或处置情境下出现，绝大多数样本为空，对预测前瞻性弱。

### 4.2 ARM 专属变量（样本不匹配）

-   例：`ARM Product Type`, `Index`, `ARM Cap Structure`,
    `Mortgage Margin`\
-   **原因：** 样本以固定利率为主，ARM 信息无效或全空。

### 4.3 未来信息（强泄露风险）

-   例：`Zero Balance Code`, `Repurchase Date`,
    `UPB at the Time of Removal`\
-   **原因：** 包含贷款结束后的状态或金额，若用于训练将造成数据泄露。

### 4.4 标识与长字符串

-   例：`Loan Identifier`, `Deal Name`, `Loan Payment History`\
-   **原因：** 高基数或非结构化文本不适合常规模型输入。

### 4.5 常量或低方差列

-   例：`Amortization Type`, `HomeReady Program Indicator`\
-   **原因：** 几乎无变化，贡献极低。

### 4.6 工程特征冗余

-   删除：`Interest_Rate_Diff`, `UPB_Diff`, `Loan_Age_Ratio`\
-   保留：`Interest_Rate_Ratio`, `UPB_Ratio`\
-   **原因：** 避免共线性，仅保留比例表达。

### 4.7 时间变量冗余

-   删除：`Remaining Months to Legal Maturity`,
    `Months to Amortization`\
-   保留：`Loan Age`, `Remaining Months To Maturity`

------------------------------------------------------------------------

## 5. Columns Summary（字段汇总）

### 5.1 删除字段（部分展示）

`Loan Identifier`, `Foreclosure Date`, `Disposition Date`,
`ARM Product Type`,\
`Repurchase Date`, `Zero Balance Code`, `Deal Name`,
`Interest_Rate_Diff`,\
`UPB_Diff`, `Loan_Age_Ratio` 等共 **约 50+ 列**。

### 5.2 保留字段（核心建模变量）

`Original Interest Rate`, `Current Interest Rate`, `Original UPB`,\
`Loan Age`, `Remaining Months To Maturity`,\
`Borrower Credit Score`, `DTI`, `LTV`,\
`Property Type`, `Occupancy Status`, `Current Loan Delinquency Status`,\
`Interest_Rate_Ratio`, `UPB_Ratio`, `LTV_Category`,
`Credit_Score_Category` 等。

------------------------------------------------------------------------

## 6. Missing Value & Cardinality Analysis（缺失与唯一值分析）

  -----------------------------------------------------------------------
  指标                                说明
  ----------------------------------- -----------------------------------
  **缺失率 100% 的字段数**            约 30 个（如 Maturity Date,
                                      Mortgage Insurance Percentage 等）

  **缺失率 ≥95% 的字段数**            约 40 个，主要集中在 ARM 和
                                      Foreclosure 模块

  **高基数字段数**                    2 个（Loan Identifier, Deal Name）

  **低信息量字段数**                  3 个（HomeReady Indicator, Loan
                                      Holdback Indicator 等）
  -----------------------------------------------------------------------

> 总体而言，清洗后数据的缺失率显著下降，特征稳定性提升。

------------------------------------------------------------------------

## 7. Data Integrity Check（数据完整性校验）

  项目                      状态          说明
  ------------------------- ------------- ----------------------
  缺失率 ≤ 10% 的字段比例   ✅ 约 90%     清洗后数据可直接建模
  含未来信息字段            ❌ 全部移除   防止标签泄露
  共线性特征                ⚠️ 部分保留   将在建模前进一步处理
  非结构化文本              ❌ 已移除     无长字符串变量

------------------------------------------------------------------------

## 8. Recommendations（后续建议）

1.  **特征筛选与降维**：对保留特征进行 VIF
    检测，进一步剔除潜在多重共线变量。\
2.  **缺失值填充策略**：对于关键数值字段（如 DTI、Credit
    Score），建议用中位数或基于群组的均值填补。\
3.  **标准化与编码**：对连续变量进行标准化（Z-score），分类变量采用
    one-hot 或 target encoding。\
4.  **时间序列一致化**：可在后续加入时间衍生特征，如月度变化率或累计逾期期数。

------------------------------------------------------------------------

## 9. Summary（总结）

  指标               数值
  ------------------ ----------
  原始列数           ≈110
  删除列数           ≈50
  保留列数           ≈60
  缺失率 ≥95% 的列   40
  数据泄露风险列     全部删除
  可用于建模特征     60+

> 清洗后数据集兼顾稳健性与解释性，适用于后续的违约率与提前还款预测建模。
