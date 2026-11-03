# ApisTox 蜜蜂急性毒性分类

这是一个面向生物学机器学习学习与作品展示的公开数据项目。项目使用分子结构特征预测化合物是否具有蜜蜂急性毒性，并比较逻辑回归、随机森林和多层感知机（MLP）的表现。

> 本项目是基于公开 ApisTox 数据集完成的练习与作品集案例，不声称提出新的毒理学机制。模型输出是统计预测评分，不能替代实验毒理评价或监管决策。

## 项目目标

- 理解并检查公开毒理数据的含义与质量；
- 避免标识符泄漏、来源偏差和标签泄漏；
- 比较二维分子描述符与 Morgan 指纹；
- 在类别不平衡条件下合理评价模型；
- 使用独立测试集、分组交叉验证和骨架交叉验证；
- 保存并重新加载模型，对单个 SMILES 进行预测；
- 提供基础的最高 Tanimoto 结构相似度提示。

## 数据集

项目使用公开的 [ApisTox 数据集](https://www.nature.com/articles/s41597-024-04232-w)，共包含 1,035 个化合物：

- 无毒（`label=0`）：739 个；
- 有毒（`label=1`）：296 个。

第一版模型只使用分子结构，不使用名称、CID、CAS、数据来源、年份、毒性类型、农药类别或 `ppdb_level` 等可能引入标识符泄漏、来源偏差或标签泄漏的字段。

ApisTox 数据集及其相关文件受原作者许可条款约束。官方仓库标明数据采用 [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/) 许可。本仓库仅用于非商业学习与作品展示，使用者应同时遵守原始数据许可并引用原始论文。

## 特征与模型

### 分子特征

- 10 个二维分子描述符；
- Morgan 指纹：
  - `radius=2`；
  - `fpSize=2048`；
  - `includeChirality=True`。

### 比较模型

- 逻辑回归；
- 随机森林；
- 多层感知机（MLP）。

最终选择的模型为 **Morgan 指纹＋随机森林**，分类阈值为 **0.30**。阈值只使用训练集折外预测确定，没有使用最终测试集调节。

## 验证设计

- 固定训练集：827 个样本；
- 独立测试集：208 个样本；
- 相同 Morgan 指纹的样本不能跨训练集和测试集；
- 在训练集内部进行分组交叉验证；
- 使用 Murcko 骨架交叉验证补充评价新骨架外推能力；
- 测试集只在模型、超参数和分类阈值确定后使用。

## 独立测试集结果

| 指标 | 结果 |
|---|---:|
| Accuracy | 0.803 |
| Balanced Accuracy | 0.792 |
| 有毒类 Precision | 0.630 |
| 有毒类 Recall | 0.767 |
| 无毒类 Recall | 0.818 |
| ROC-AUC | 0.832 |
| Average Precision / PR-AUC | 0.755 |
| MCC | 0.555 |

混淆矩阵：`TN=121`、`FP=27`、`FN=14`、`TP=46`。

这些结果来自一次固定独立测试，不代表模型对任意化合物都具有相同表现。

## 单个 SMILES 预测

`04_model_application.ipynb` 完成以下流程：

1. 加载保存的模型包；
2. 读取分类阈值、Morgan 参数和特征顺序；
3. 接收并验证一个 SMILES；
4. 使用训练时相同的参数生成 Morgan 指纹；
5. 输出有毒类概率评分和预测类别；
6. 报告与训练集分子的最高 Tanimoto 相似度；
7. 在结构相似度较低时显示谨慎解释提示。

当前使用 `0.40` 作为展示性的相似度谨慎提示线。它不是经过性能验证的正式适用域阈值，也不能解释为预测正确率。

## 仓库结构

```text
apis-tox-ml/
├── notebooks/
│   ├── 02_feature_engineering.ipynb
│   ├── 03_modeling.ipynb
│   └── 04_model_application.ipynb
├── processed_data/
│   ├── sample_metadata.csv
│   ├── descriptor_features.csv
│   ├── morgan_features.csv
│   └── random_grouped_split.csv
├── saved_models/
│   └── apis_tox_morgan_random_forest.joblib
├── reports/
│   ├── ApisTox_蜜蜂急性毒性分类_分析报告.docx
│   └── ApisTox_蜜蜂急性毒性分类_分析报告.pdf
├── figures/
├── .gitignore
└── README.md
```

## 运行方法

建议使用与模型保存时一致的软件版本：

- Python 3.10.21；
- scikit-learn 1.7.1；
- RDKit 2024.09.6；
- pandas 2.3.3；
- NumPy 2.2.5。

安装依赖后，从仓库根目录启动 Jupyter：

```bash
jupyter notebook
```

然后打开：

```text
notebooks/04_model_application.ipynb
```

按照 Notebook 提示输入一个 SMILES。Notebook 支持从仓库根目录或 `notebooks` 目录启动。

## 模型文件安全说明

`joblib` 文件只能从可信来源加载，因为反序列化可能执行代码。不同 Python、scikit-learn 和 RDKit 版本之间也可能存在兼容性问题。

## 重要限制

- 模型测试集仍来自 ApisTox，不属于真正独立的外部数据验证；
- 当前最高 Tanimoto 相似度仅是基础结构接近程度提示，不是正式验证的适用域；
- 模型概率是预测评分，不是实验测得的毒性值；
- 标签整合了不同急性暴露途径，不能解释为单一毒理机制；
- 预测关联不能证明分子结构导致蜜蜂毒性的生物学因果关系；
- 本模型不能替代实验毒理评价、风险评估或监管决策。

## 报告

完整中文分析报告见：

- [PDF 报告](reports/ApisTox_蜜蜂急性毒性分类_分析报告.pdf)
- [Word 报告](reports/ApisTox_蜜蜂急性毒性分类_分析报告.docx)

## 引用与致谢

数据来源：

- Adamczyk J, Poziemski J, Siedlecki P. *ApisTox: a new benchmark dataset for the classification of small molecules toxicity on honey bees*. Scientific Data, 2025. <https://doi.org/10.1038/s41597-024-04232-w>
- [ApisTox 官方数据仓库](https://github.com/j-adamczyk/ApisTox_dataset)

相关模型比较研究：

- Adamczyk J, Poziemski J, Siedlecki P. *Evaluating machine learning models for predicting pesticide toxicity to honey bees*. Ecotoxicology and Environmental Safety, 2026. <https://doi.org/10.1016/j.ecoenv.2026.119869>

## 项目定位

本仓库用于记录从数据理解、质量检查、特征工程、建模验证到模型应用的完整学习过程，并作为生物学数据分析与机器学习能力的作品集展示。
