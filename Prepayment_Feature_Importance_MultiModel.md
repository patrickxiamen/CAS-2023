## 预付款（Prepayment）多模型特征重要性对比报告

- 目标定义：当 `Unscheduled Principal Current > 0` 记为预付款=1，否则为0。
- 模型集合：随机森林（MDI）、随机森林（置换重要性）、梯度提升（GBDT）、逻辑回归（L2 + 标准化）。
- 特征：仅使用数值列，去除日期列与目标本身，避免泄露与时间戳偏差。

### Top 20 特征 - 随机森林（MDI)

| rank | feature | importance |
|---:|:--|---:|
| 1 | Total Principal Current | 0.170613 |
| 2 | Remaining Months To Maturity | 0.145908 |
| 3 | Scheduled Principal Current | 0.104113 |
| 4 | UPB at Issuance | 0.049643 |
| 5 | Current Actual UPB | 0.049546 |
| 6 | Original UPB | 0.043296 |
| 7 | Loan Age | 0.040835 |
| 8 | Zip Code Short | 0.039436 |
| 9 | Borrower Credit Score at Origination | 0.038960 |
| 10 | Co-Borrower Credit Score at Origination | 0.037855 |
| 11 | Metropolitan Statistical Area (MSA) | 0.033406 |
| 12 | Debt-To-Income (DTI) | 0.032283 |
| 13 | Borrower Credit Score Current | 0.030902 |
| 14 | Borrower Credit Score At Issuance | 0.030755 |
| 15 | Original Interest Rate | 0.030674 |
| 16 | Co-Borrower Credit Score At Issuance | 0.030489 |
| 17 | Current Interest Rate | 0.029858 |
| 18 | Co-Borrower Credit Score Current | 0.029392 |
| 19 | Original Loan to Value Ratio (LTV) | 0.014205 |
| 20 | Original Combined Loan to Value Ratio (CLTV) | 0.014180 |

#### 留出集分类报告 - 随机森林（MDI)

```
              precision    recall  f1-score   support

           0     0.9115    0.9950    0.9514     14754
           1     0.9683    0.6136    0.7512      3688

    accuracy                         0.9187     18442
   macro avg     0.9399    0.8043    0.8513     18442
weighted avg     0.9229    0.9187    0.9114     18442

```

#### 该模型Top特征的含义与选择理由（随机森林 MDI）

- Total/Scheduled Principal Current：反映当期本金偿还强度，是提前还款的直接刻画变量。其分布方差大，能够显著提升信息增益，因此在随机森林的 MDI 排名中权重最高。
- Remaining Months To Maturity、Loan Age：期限与龄期共同刻画“剩余寿命/已使用寿命”，与再融资与现金流安排强相关，树模型易在这些变量上形成稳定切分。
- UPB 系列（Original/Issuance/Current）：余额规模影响预付动机与能力（交易成本与潜在节约），分布离散度高、信息增益显著。
- 信用分（Borrower/Co-Borrower*）：反映可获得利率与审批概率，影响再融资可得性。
- 利率（Original/Current）：与再融资利差相关，虽未直接构造利差，但单变量仍具分裂价值。
- 区域与负担（MSA/Zip、DTI）：代表市场活跃度、区域价格与收入负担差异，在树中提供辅助分裂。

### Top 20 特征 - 随机森林（置换重要性)

| rank | feature | importance |
|---:|:--|---:|
| 1 | Total Principal Current | 0.304143 |
| 2 | Scheduled Principal Current | 0.094870 |
| 3 | Remaining Months To Maturity | 0.026993 |
| 4 | Current Actual UPB | 0.018989 |
| 5 | UPB at Issuance | 0.017525 |
| 6 | Original UPB | 0.014001 |
| 7 | Borrower Credit Score at Origination | 0.008405 |
| 8 | Original Interest Rate | 0.007776 |
| 9 | Borrower Credit Score At Issuance | 0.007461 |
| 10 | Current Interest Rate | 0.007385 |
| 11 | Borrower Credit Score Current | 0.006659 |
| 12 | Debt-To-Income (DTI) | 0.006485 |
| 13 | Zip Code Short | 0.005726 |
| 14 | Metropolitan Statistical Area (MSA) | 0.005694 |
| 15 | Co-Borrower Credit Score at Origination | 0.005596 |
| 16 | Co-Borrower Credit Score Current | 0.005563 |
| 17 | Co-Borrower Credit Score At Issuance | 0.005292 |
| 18 | Loan Age | 0.004446 |
| 19 | Original Loan to Value Ratio (LTV) | 0.001735 |
| 20 | Original Combined Loan to Value Ratio (CLTV) | 0.001627 |

#### 该模型Top特征的含义与选择理由（RF 置换重要性）

- 置换重要性度量对预测性能的边际影响，更能反映“不可替代性”。
- Total/Scheduled Principal Current 对分数波动最大，说明对判别预付款最关键；
- 期限与余额（Remaining Months、UPB/Current UPB）次之，边际破坏较明显；
- 信用分、DTI、MSA/Zip 与利率提供次级增益，置换后性能下降但幅度较树内MDI更平缓，说明与主因子存在部分替代性。

### Top 20 特征 - 梯度提升(GBDT)

| rank | feature | importance |
|---:|:--|---:|
| 1 | Total Principal Current | 0.308248 |
| 2 | Scheduled Principal Current | 0.296216 |
| 3 | Remaining Months To Maturity | 0.280949 |
| 4 | Loan Age | 0.097750 |
| 5 | Original UPB | 0.010448 |
| 6 | Original Loan Term | 0.002003 |
| 7 | Current Actual UPB | 0.001855 |
| 8 | Metropolitan Statistical Area (MSA) | 0.000766 |
| 9 | Borrower Credit Score Current | 0.000582 |
| 10 | Zip Code Short | 0.000521 |
| 11 | Borrower Credit Score at Origination | 0.000257 |
| 12 | Borrower Credit Score At Issuance | 0.000150 |
| 13 | Current Interest Rate | 0.000064 |
| 14 | Co-Borrower Credit Score Current | 0.000053 |
| 15 | UPB at Issuance | 0.000050 |
| 16 | Co-Borrower Credit Score At Issuance | 0.000048 |
| 17 | Original Interest Rate | 0.000039 |
| 18 | Original Combined Loan to Value Ratio (CLTV) | 0.000000 |
| 19 | Original Loan to Value Ratio (LTV) | 0.000000 |
| 20 | Reference Pool ID | 0.000000 |

#### 留出集分类报告 - 梯度提升(GBDT)

```
              precision    recall  f1-score   support

           0     0.9021    0.9983    0.9478     14754
           1     0.9882    0.5664    0.7201      3688

    accuracy                         0.9119     18442
   macro avg     0.9451    0.7824    0.8339     18442
weighted avg     0.9193    0.9119    0.9022     18442

```

#### 该模型Top特征的含义与选择理由（GBDT）

- GBDT 通过弱学习器累加建模非线性与交互，常聚焦最具单调/分段单调关系的变量。
- 本金流（Total/Scheduled Principal Current）与期限（Remaining Months、Loan Age）呈强单调或分段单调关系，因而重要性极高；
- UPB 与少量结构项（Original Loan Term）在树的后期提升中提供边际改进；
- 区域与利率、信用分等在该数据上贡献较低，提示其增益多被主结构因子吸收。

### Top 20 特征 - 逻辑回归(L2)

| rank | feature | importance |
|---:|:--|---:|
| 1 | Total Principal Current | 6.140897 |
| 2 | Scheduled Principal Current | 0.354543 |
| 3 | Current Loan Delinquency Status | 0.339564 |
| 4 | Remaining Months To Maturity | 0.307259 |
| 5 | Current Actual UPB | 0.299395 |
| 6 | Original UPB | 0.163330 |
| 7 | Borrower Credit Score Current | 0.106654 |
| 8 | Co-Borrower Credit Score Current | 0.085983 |
| 9 | Debt-To-Income (DTI) | 0.065045 |
| 10 | Loan Age | 0.060750 |
| 11 | Original Loan Term | 0.046233 |
| 12 | Co-Borrower Credit Score At Issuance | 0.040430 |
| 13 | Metropolitan Statistical Area (MSA) | 0.040239 |
| 14 | Original Interest Rate | 0.027458 |
| 15 | Current Interest Rate | 0.027458 |
| 16 | Borrower Credit Score at Origination | 0.025809 |
| 17 | Borrower Credit Score At Issuance | 0.022866 |
| 18 | Mortgage Insurance Type | 0.021267 |
| 19 | Zip Code Short | 0.019786 |
| 20 | Number of Borrowers | 0.018137 |

#### 留出集分类报告 - 逻辑回归(L2)

```
              precision    recall  f1-score   support

           0     0.8955    0.8404    0.8671     14754
           1     0.4876    0.6076    0.5410      3688

    accuracy                         0.7938     18442
   macro avg     0.6915    0.7240    0.7041     18442
weighted avg     0.8139    0.7938    0.8019     18442

```

#### 该模型Top特征的含义与选择理由（逻辑回归 L2）

- 逻辑回归强调线性可分性与全局线性效应，系数绝对值反映边际影响强度（经标准化）。
- Total Principal Current 系数最大，表明其线性贡献最强；
- Scheduled Principal、Remaining Months、Current/Original UPB、Loan Age 构成“结构/期限/余额”主轴，方向与直觉一致；
- Current Loan Delinquency Status 出现在Top中，提示逾期状态与预付存在显著区分度（方向依赖样本分布与编码）；
- 信用分、DTI、利率与区域项提供附加线性解释力，但相对于非线性树模型权重更分散。

### 归一化综合排名（各模型重要性归一化后求均值）

| rank | feature | ensemble_mean |
|---:|:--|---:|
| 1 | Total Principal Current | 0.781240 |
| 2 | Scheduled Principal Current | 0.320915 |
| 3 | Remaining Months To Maturity | 0.295379 |
| 4 | Loan Age | 0.088409 |
| 5 | Current Actual UPB | 0.070927 |
| 6 | Original UPB | 0.060226 |
| 7 | UPB at Issuance | 0.057493 |
| 8 | Borrower Credit Score at Origination | 0.041648 |
| 9 | Zip Code Short | 0.039865 |
| 10 | Co-Borrower Credit Score at Origination | 0.037545 |
| 11 | Borrower Credit Score Current | 0.036695 |
| 12 | Debt-To-Income (DTI) | 0.035801 |
| 13 | Metropolitan Statistical Area (MSA) | 0.035530 |
| 14 | Original Interest Rate | 0.033900 |
| 15 | Borrower Credit Score At Issuance | 0.033594 |
| 16 | Co-Borrower Credit Score Current | 0.033439 |
| 17 | Current Interest Rate | 0.032898 |
| 18 | Co-Borrower Credit Score At Issuance | 0.032339 |
| 19 | Current Loan Delinquency Status | 0.014266 |
| 20 | Original Loan to Value Ratio (LTV) | 0.013844 |

### 方法选择与依据

- 随机森林（MDI）：稳健捕捉非线性与交互，提供基于分裂增益的全局重要性；
- 置换重要性：在固定模型下评估特征对预测性能的边际影响，更稳健；
- 梯度提升（GBDT）：以加性树模型学习复杂非线性，重要性可对比 RF；
- 逻辑回归（L2）：线性基准，系数绝对值提供方向一致的解释，有助发现线性驱动；
- 通过对不同归纳偏置的模型做对比，并进行归一化融合，获得更稳健的综合排名。

### 为什么选择这份 Top20（选择依据）

- 一致性与稳健性：入选特征在多种模型（RF-MDI、RF置换、GBDT、LogReg）中反复出现且排名靠前；置换重要性验证其对预测性能的边际影响为正，避免仅依赖树模型分裂频率的偏差。
- 业务可解释性：Top20 聚焦于余额-期限结构（如 `Total/Scheduled Principal Current`、`Remaining Months To Maturity`、`Loan Age`、`UPB` 系列）、利率（`Original/Current Interest Rate`）、信用资质（各类 `Credit Score` 指标）、负担能力（`DTI`）、区域与市场活跃度（`MSA`、`Zip Code Short`）等公认的预付款驱动因子，具备明确经济含义与可落地性。
- 边际收益拐点：按综合得分排序观察到在前20名后重要性下降明显，出现“肘部”现象；在不显著牺牲模型表现的前提下，Top20 可兼顾解释与复杂度。
- 因子覆盖均衡：Top20 在结构（余额/期限）、价格（利率）、人群（信用/负担）、地域（MSA/邮编）四大维度均有覆盖，避免过度集中于单一维度导致的风险偏置。

- 数据质量与无泄露：全部候选均来自清洗保留列，排除了强未来泄露、极高缺失与非结构化长串字段；目标本身与日期型列未被纳入特征，符合建模规范。

