# Part I 数据交接说明（预处理部分）

这一包只包含**数据清洗**的结果。是否加权、DK 删不删等**分析阶段的决定还没定**，所以这里没有代际分析结果和图。

## 文件和用途

| 文件 | 内容 | 给谁 |
|---|---|---|
| `data_quality_report.txt` | 数据质量评估，按 Week 1 课件五个问题组织：structure / granularity / scope / temporality / faithfulness，以及缺失分析 | B：写数据质量部分 |
| `educ_mapping.csv` | 24 国每个教育选项 → ISCED 统一等级，附原文标签和人数，Excel 可直接打开 | B：写教育映射理由 |
| `educ_labels.txt` | 各国教育选项原文（含德国、以色列的补充列） | B：核对 |
| `clean.csv` | 清洗后的数据，28,333 行，Excel 可直接打开 | 所有人：查数 |
| `clean.py` | 清洗代码；开头是小组决定 | 存档 / 复现 |

运行方法：把 Pew 的 .sav 改名为 `data.sav` 放在同一文件夹，然后执行 `pip install pyreadstat pandas numpy`，再执行 `python clean.py`。

## 已定的预处理决定

1. **缺失编码**：Q30、Q31 的 8/9、年龄的 98/99、教育的 DK/拒答都设为缺失（作业要求）。
2. **Q32 的 "Not sure"（8）保留为有效回答**，只有 9（拒答）设为缺失（作业要求）。
3. **教育**：按 ISCED 2011 统一为三级：低 = 0–2，中 = 3–4，高 = 5–8。核对官方对照后，印度 "High School" 和英国 GCSE 归为"中"。有争议的项在 `clean.py` 里标了 `[争议]`：澳大利亚、以色列、肯尼亚、南非、英国。
4. **世代**：所有国家 **18–29 岁 = Gen Z**，30 岁及以上 = Older。这比 Pew 定义（18–28）多含 29 岁，原因是澳大利亚只有 18–29 年龄段。
5. **拒报确切年龄的 294 人**：不进入代际分析（`generation` 为空），但保留在数据里，用于复现 topline。
6. **政治倾向**：两种量表方向一致，合并为 `ideology`。Part I 暂不使用。
7. **性别**：Part I 不使用。
8. **没有删除任何行**：进 28,333 行，出 28,333 行。

## 还没定（分析阶段）

- 是否使用调查权重、怎么用
- 分析代际差距时，Q30/Q31 的 DK 删掉还是保留

## `clean.csv` 各列

| 列名 | 含义 |
|---|---|
| `id`, `country`, `cname` | 受访者编号、国家编码、国家名 |
| `weight` | Pew 提供的调查权重，原样保留，每国平均值为 1 |
| `ai_heard` | Q30：1 = 听说很多，2 = 一点，3 = 完全没有；DK/拒答为空 |
| `ai_heard_raw` | Q30 原始值，含 8/9，复现 topline 用 |
| `ai_cncexc` | Q31：1 = 更兴奋，2 = 更担忧，3 = 两者都有；DK/拒答为空 |
| `ai_cncexc_raw` | Q31 原始值，含 8/9 |
| `aireg_us`, `aireg_china`, `aireg_eu`, `aireg_country` | Q32a–d：1 = 很信任 … 4 = 完全不信任，**8 = 不确定（有效回答）**；拒答为空 |
| `age` | 确切年龄；97 = "97 岁及以上"；澳大利亚和拒答者为空 |
| `age_band_australia` | 澳大利亚年龄段：1 = 18–29，2 = 30–49，3 = 50–64，4 = 65+ |
| `age_band_refusers` | 拒报确切年龄者的年龄段编码（各国编码不同，未使用） |
| `age_assigned` | 用于分组的年龄：确切年龄，澳大利亚取年龄段最大值 |
| `age_from_band` | 年龄是否来自年龄段（True = 澳大利亚） |
| `generation` | **Gen Z / Older**；拒报年龄者为空 |
| `educ_country_code`, `educ_key` | 各国原始教育编码，以及它属于哪国的编码体系 |
| `educ_harmonised` | **统一后的教育**：1 = 低，2 = 中，3 = 高 |
| `gender` | 1 = 男，2 = 女，3 = 其他（仅澳大利亚） |
| `internetfreq` | 上网频率；澳大利亚未问 |
| `political_scale2`, `political_scale3`, `ideology` | 政治倾向原始两列，以及合并后的 `ideology`（0 = 最左/最进步，6 = 最右/最保守） |
