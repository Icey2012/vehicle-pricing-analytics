[🇬🇧 English](README.md)

# 车型竞品拆解与定价分析 🚗

**一个用数据分析将传统车型竞品分析自动化的实践。**

传统竞品分析依赖咨询顾问手动拉 Excel 做对标，耗时长、难以量化。这个项目尝试用代码构建一个自动化框架——输入车型参数，自动匹配竞品、量化配置溢价、输出分析结论。

> ⚠️ 项目因数据量不足未达成原定商业目标，但分析框架本身已证明其可复用性，且为小样本分析场景提供了可借鉴的方法论。

---

## 关键决策

| 阶段 | 关键决策/事件 | 判断依据 |
| :--- | :--- | :--- |
| 启动前 | 手头有243条/80+品牌的现成车型数据，能不能做定价分析？ | 直觉认为样本量不够，但参考AI建议"随机森林对200条不敏感"，未独立验证就启动了项目 |
| 数据清洗 | 轴距缺失，怎么填充？ | 根据汽车设计知识（轴距与车长高度线性相关），用 length_mm 线性回归预测填充 |
| 特征工程 | 80+个品牌类别怎么编码？ | 选目标编码（当时条件下的合理尝试）。后期复盘发现小样本下目标编码不稳定，模型过度依赖品牌编码走捷径 |
| 建模诊断 | 回归 R² 异常低，第一步做什么？ | 检查训练集和测试集的价格分布是否一致，发现两者均值相差6万元→用 StratifiedShuffleSplit 分层采样校准。观察分类模型后发现 价格分段 与车型 segment 高度重叠——模型学的不是定价逻辑而是尺寸规律，继续调参无意义，转为系统性复盘 |
| 系统复盘 | 小样本 ML 到底能不能成功？ | 总结了三类"先天友好"场景（强物理规律、预训练迁移、显式规则）和6种补救技术，输出可复用的可行性判断框架。本项目若想成功，至少需要3000+条有效数据，每品牌≥50条，且需要同品牌同级别仅单一配置差异的成对样本。给出4条数据采集路线 |


---

## 结果摘要

在 **243 条样本、80+ 品牌**（平均每个品牌不足 3 辆车）的数据约束下：

- 低价段（<15 万）配置溢价可量化，MAE 1-3 万元
- 高价段（>50 万）样本不足 5 条，不具备统计意义
- 项目已归档，不重启；框架代码保留在作品集中

<details>
<summary>详细技术指标（点击展开）</summary>

| 方案 | 指标 | 说明 |
|------|------|------|
| 回归模型 | R² ≈ 0.40, MAE ≈ 7.4 万 | 数据不足，预测力有限 |
| 分类模型（6 档价格） | 准确率 63% | 作为小样本分类演示留存 |
| 低价段（<15 万）MAE | 1-3 万元 | 配置溢价可量化 |
| 高价段（>50 万）MAE | 55.4 万元 | 仅 3 条样本 |

</details>

---

## 关键教训

- 启动前必须完成可行性论证——适用任何项目，AI 的乐观建议需要独立验证
- 高价车和低价车定价逻辑不同，不应纳入同一模型。对业务的理解远比调参重要

---

## 项目结构

```
.
├── Project Proposal.txt          # 英文方案书（目标、方法、预期指标）
├── Project Proposal_CN           # 中文方案书
├── EDA_Insight                   # EDA 关键发现摘要
├── post-mortem.ipynb             # 英文复盘报告（失败诊断 + 方法论总结）
├── post-mortem_CN.ipynb          # 中文复盘报告
├── README.md                     # English README
├── README_CN.md                  # 中文 README
│
├── data/
│   ├── raw/                      # 原始数据（手工整理 → 清洗共4个版本）
│   │   ├── 01_hand_made_original_data.xlsx
│   │   ├── 02_rough_machining.xlsx
│   │   ├── 03_hand_AI_cleaned_data.xlsx
│   │   ├── 04_cleaned_data.xlsx
│   │   ├── kaggle_original_data.csv
│   │   └── kaggle_engineered_data.csv
│   └── processed/                # 特征工程后的训练/测试数据
│       ├── X_train_df.csv
│       ├── X_test_df.csv
│       ├── y_train_df.csv
│       ├── y_test_df.csv
│       ├── final_before_feng.csv
│       └── best_features.txt     # 特征重要性筛选结果
│
├── notebooks/
│   ├── 00_download_kaggle_data.ipynb   # Kaggle 数据补充
│   ├── 00_kaggle_EDA.ipynb             # Kaggle 数据的探索性分析
│   ├── 01_raw_data_obsevation.ipynb    # 原始数据初步观察
│   ├── 02_raw_data_cleansing.ipynb     # 数据清洗（缺失值处理、格式统一）
│   ├── 03_EDA_insight.ipynb            # 探索性数据分析
│   ├── 04_feature_engineering.ipynb    # 特征工程与构造
│   ├── 05_model_training_regression.ipynb  # 回归模型训练
│   ├── kaggle_data_report.html         # Kaggle 数据 profiling 报告
│   └── vehicle_data_profile.html       # 车辆数据 profiling 报告
```

---

## 相关文档
