# 智题 · 历年真题库与 AI 题型分析平台

把散落在各省市、各年份的高考 / 中考 / 考研真题结构化沉淀下来，用 AI 从题型结构与考点分布中读出命题规律，
最后落到每个学生的具体薄弱点上——**查题 → 懂规律 → 补短板**，形成闭环。

> **已接入 4 个公开数据源，共 621 套试卷 / 39,125 道题 / 21 个省级行政区 / 14 个学科 / 2008–2025 年。**
>
> | 数据源 | 学段 | 规模 | 覆盖范围 |
> | --- | --- | --- | --- |
> | **GAOKAO-Bench**（复旦大学 OpenLMLab） | 高考·全国卷 | 286 套 / 3,101 题 | 2010–2024 年 / 9 学科 |
> | **高考英语真题数据库**（分省） | 高考·分省 | 180 套 / 7,893 题 | 2008–2025 年 / **17 省** |
> | **gaokaophysics**（分省 PDF） | 高考·分省 | 109 套 / 1,980 题 | 2010–2024 年 / **19 省** |
> | **CJEval**（腾讯优图 × 北京大学） | 中考 | 40 个题目合集 / 26,136 题 | 10 学科（题目级，无年份省份） |
>
> 其中**英语、物理为分省命题卷**，省份筛选在真实数据上真正可用（如江苏 28 套、北京 30 套）。
> 物理分省卷由 **PDF 文本层自动抽取**，公式会被打散、题图无法还原，已在试卷页显著标注（详见第七节）。
> 中考题库为**题目级**数据集，以「题目合集」而非「成套试卷」呈现。
>
> 考研模块仍为内置示例数据，数据结构完全一致，可直接替换。
>
> ⚠️ 各数据集均声明**仅供研究与学术用途、不得用于商业目的**；题目版权归原出题机构所有。
> 正式使用请以各省教育考试院发布的官方试卷为准。

---

## 一、四大模块

| 模块 | 页面 | 能力 |
| --- | --- | --- |
| **模块 1 · 真题库** | `/#/papers` | 按考试类型 → 省份 → 年份 → 学科 → 试卷逐层筛选；按年份时间线展示；题目含选项、答案、解析、考点、难度；试卷维度自动聚合题型结构与考点分值分布 |
| **模块 2 · AI 题型分析** | `/#/analysis` | 选定学科后展示历年难度趋势、题型结构演变、考点分值分布；一键调用 AI 输出命题趋势、高频考点、题型变化、难度判断、备考建议与趋势预判 |
| **模块 3 · 针对性学习** | `/#/learn` | 知识图谱（模块 → 知识点，含提分技巧与常见失分点）；自测诊断（按知识点聚合正确率，定位薄弱环节）；生成三阶段学习路径并关联真题 |
| **模块 4 · 考研专区** | `/#/kaoyan` | 硕士招生考试模块，复用同一套数据结构，已具备题库、统计与知识图谱骨架，并给出扩展路线图 |

页面支持**深链**（带筛选条件的链接可直接分享，也用于无头浏览器回归测试）：

```
/#/papers?examType=zhongkao&subjectId=math        中考 · 数学题目合集
/#/papers?examType=zhongkao&paper=zk-math-sc      直接打开某个题目合集
/#/analysis?examType=zhongkao&subjectId=math      中考数学分析（全量分布视图）
```

---

## 二、快速开始

环境要求：Node.js ≥ 18（推荐 20 / 22）。

```bash
cd exam-ai

# 1. 安装依赖
npm install

# 2. （可选）导入公开题库；已导入的产物随仓库提供，可跳过
npm run import:all             # 高考（全国卷 + 英语分省 + 物理分省）+ 中考，一次导入
# 或按数据源分开导入（`:fetch` 变体会先联网拉取源数据，再导入）
npm run import:bank:fetch         # 高考 GAOKAO-Bench（全国卷）
npm run import:gaokao-english:fetch  # 高考英语（分省，结构化 JSON）
npm run import:gaokao-physics:fetch  # 高考物理（分省，PDF → 文本 → 结构化）
npm run import:zhongkao:fetch     # 中考 CJEval

# 3. 开发模式（同时启动后端 3001 与前端 5173，前端自动代理 /api）
npm run dev
# 打开 http://127.0.0.1:5173

# 或者：构建后由后端统一托管（单端口，推荐体验方式）
npm run build
npm start
# 打开 http://127.0.0.1:3001
```

> **Windows 用户注意**：若 `npm install` 因文件锁报 `EBUSY`，重试一次即可。
> 若报 `The package "@esbuild/win32-x64" could not be found`，说明平台二进制包（optionalDependencies）未装上，补装：
> ```bash
> npm i -D @esbuild/win32-x64@0.21.5 @rollup/rollup-win32-x64-msvc@4.64.5
> ```

---

## 三、AI 接入

模块 2 的分析能力走 **OpenAI 兼容协议**（`POST {baseUrl}/chat/completions`），因此主流模型可直接接入。
在 **AI 设置** 页面填写 `Base URL` / `API Key` / `模型名` 即可，内置了以下预设：

| 服务商 | Base URL | 常用模型 |
| --- | --- | --- |
| DeepSeek | `https://api.deepseek.com/v1` | `deepseek-chat`、`deepseek-reasoner` |
| 通义千问（阿里云百炼） | `https://dashscope.aliyuncs.com/compatible-mode/v1` | `qwen-plus`、`qwen-max` |
| 智谱 GLM | `https://open.bigmodel.cn/api/paas/v4` | `glm-4-plus`、`glm-4-flash` |
| 月之暗面 Kimi | `https://api.moonshot.cn/v1` | `moonshot-v1-8k` |
| OpenAI | `https://api.openai.com/v1` | `gpt-4o-mini` |
| 本地 / 自建网关 | `http://127.0.0.1:11434/v1`（Ollama、vLLM、One-API） | 任意 |

### 核心设计：AI 是增强项，不是必需项

- **未配置 Key** → 模块 2 自动降级为**内置规则引擎**，基于历年统计数据做确定性推断，输出同样结构化的分析结果。
- **配置了 Key** → 调用大模型做深度分析；若模型返回无法解析，或调用失败，**自动降级回规则分析**，页面不会白屏。
- 模块 1、3、4 **完全不依赖 AI**，离线可用。

### 生产环境建议

不要把 Key 放在浏览器端。启动后端前注入环境变量即可（服务端优先读取）：

```bash
# Windows PowerShell
$env:AI_BASE_URL="https://api.deepseek.com/v1"
$env:AI_API_KEY="sk-xxxxxxxx"
$env:AI_MODEL="deepseek-chat"
npm start

# macOS / Linux
AI_BASE_URL=https://api.deepseek.com/v1 \
AI_API_KEY=sk-xxxxxxxx \
AI_MODEL=deepseek-chat npm start
```

### 抑制幻觉

历年的题型分布、考点分值、难度系数等**结构化统计数据会作为事实依据注入 Prompt**，
并明确要求「不得编造数据中不存在的数字或年份」，因此模型输出不会凭空生成统计数字。

---

## 四、目录结构

```
exam-ai/
├── package.json            # 统一依赖与脚本
├── vite.config.js          # Vite 配置（root=client，代理 /api → 3001）
├── client/                 # React 前端
│   ├── index.html
│   └── src/
│       ├── main.jsx        # 入口（HashRouter）
│       ├── App.jsx         # 路由 + 全局上下文（设置 / 元数据 / 概览）
│       ├── api.js          # 接口封装 + localStorage 设置读写
│       ├── styles.css      # 设计系统（浅色主题）
│       ├── components/
│       │   ├── Layout.jsx  # 侧边栏 + 顶栏
│       │   ├── UI.jsx      # Card / Stat / Chip / Tag / Loading / Empty …
│       │   └── Charts.jsx  # 纯 SVG 图表：折线 / 分组柱 / 横向条 / 环形
│       └── pages/
│           ├── Home.jsx        # 首页 · 数据看板
│           ├── Papers.jsx      # 模块 1 · 真题库
│           ├── PaperDetail.jsx # 试卷详情（题目 + 答案 + 解析）
│           ├── Analysis.jsx    # 模块 2 · AI 题型分析
│           ├── Learn.jsx       # 模块 3 · 针对性学习
│           ├── Kaoyan.jsx      # 模块 4 · 考研专区
│           └── Settings.jsx    # AI 设置
└── server/                 # Node 后端（Express，ESM）
    ├── index.js            # 路由 + 静态托管
    ├── lib/ai.js           # OpenAI 兼容调用 + 规则分析降级
    ├── scripts/
    │   ├── import-bank.mjs            # 高考全国卷导入器（含合卷拆分算法）
    │   ├── fetch-and-import.mjs       # 拉取 GAOKAO-Bench 源仓库 + 导入（一键）
    │   ├── import-gaokao-english.mjs  # 高考英语分省导入器（仅分省卷，全国卷由 GAOKAO-Bench 覆盖）
    │   ├── fetch-gaokao-english.mjs   # 拉取英语分省结构化 JSON（走 ghproxy 反代）
    │   ├── import-gaokao-physics.mjs  # 高考物理分省导入器（解析 PDF 文本层）
    │   ├── fetch-gaokao-physics.mjs   # 下载物理分省 PDF（仅单科卷，跳过理综）
    │   ├── extract-physics-text.py    # pypdf 抽取 PDF 文本 → 中间产物（供上一步消费）
    │   ├── import-cjeval.mjs          # 中考导入器（CJEval → 题目合集）
    │   └── fetch-cjeval.mjs           # 拉取 CJEval 数据集（走 GitHub Contents API）
    └── data/
        ├── meta.js         # 考试类型 / 31 省级行政区 / 学科体系
        ├── papers.js       # 内置示例试卷（考研）
        ├── stats.js        # 内置示例统计（考研）
        ├── curriculum.js   # 知识图谱与学习路径（★ 模块 3 的数据底座）
        ├── bank/           # ★ 公开题库产物（4 个数据源）
        │   ├── papers.json / stats.json / manifest.json                              # 高考全国卷
        │   ├── gaokao-english-papers.json / -stats.json / -manifest.json             # 高考英语（分省）
        │   ├── gaokao-physics-papers.json / -stats.json / -manifest.json             # 高考物理（分省）
        │   └── zhongkao-papers.json / -stats.json / -manifest.json                   # 中考
        ├── bank-loader.js  # 多数据源加载器（按文件名前缀加载 + 统计合并；缺失自动回退示例数据）
        └── index.js        # 数据查询层（聚合、检索、排序、诊断、权重标定）
```

顶层另有 `verify-bank.mjs`（接口校验）、`verify-dom.mjs`（无头浏览器渲染校验）
与 `scripts/screenshot.mjs`（关键页面截图 → `preview/`）。

```bash
npm run verify   # 接口 + 渲染校验（17 组接口断言 / 15 个页面路由）
npm run shots    # 重新生成 preview/*.png
```

---

## 五、API 一览

统一响应格式：`{ success: boolean, data?: any, message?: string }`

### 基础

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/api/health` | 健康检查 |
| GET | `/api/meta` | 考试类型、省份、地区分组、学科体系、题型枚举 |
| GET | `/api/overview` | 平台总览（试卷数、题目数、覆盖省份、各类型明细、数据源标记） |
| GET | `/api/sources` | 数据来源、许可声明、处理说明与各项计数（供前端如实标注） |

### 模块 1 · 真题库

| 方法 | 路径 | 参数 |
| --- | --- | --- |
| GET | `/api/papers` | `examType` `province` `year` `subjectId` `paperType` `q` |
| GET | `/api/papers-by-year` | 同上，按年份分组返回 |
| GET | `/api/papers/:id` | 试卷详情（含全部题目） |
| GET | `/api/questions` | `examType` `subjectId` `province` `year` `type` `knowledge` `difficulty` `q` `limit` |
| GET | `/api/questions/:id` | 单题详情 |

### 模块 2 · AI 分析

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/api/stats/subjects` | 可分析的学科列表（`?examType=`） |
| GET | `/api/stats/:examType/:subjectId` | 历年统计序列 |
| GET | `/api/stats/:examType/:subjectId/quick` | 仅规则分析（不消耗额度，秒出） |
| POST | `/api/ai/analyze` | body：`{ examType, subjectId, options: { baseUrl, apiKey, model, temperature } }` |
| POST | `/api/ai/test` | 连通性测试 |
| POST | `/api/ai/chat` | 通用问答（为后续对话式讲解预留） |

### 模块 3 · 针对性学习

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/api/curricula` | 有知识图谱的学科列表 |
| GET | `/api/curriculum/:examType/:subjectId` | 知识图谱（树 + 展平后的知识点） |
| GET | `/api/learn/plan` | `examType` `subjectId` `weaknesses` `mastered` `limit` |
| POST | `/api/learn/diagnose` | body：`{ answers: [{ questionId, correct }] }` |

### 模块 4 · 考研

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/api/kaoyan/overview` | 试卷、科目、统计、知识图谱与扩展路线图 |

---

## 六、数据模型

### 试卷 `paper`

```js
{
  id, examType, year, province, regions: [],   // regions 为适用省份（全国卷场景）
  subject, subjectId, paperName, paperType,
  officialTotalScore, totalScore, importedScore, // 官方满分 / 收录题目分值合计
  duration, difficulty,                        // difficulty = 平均得分率，越高越易
  track,                                       // 文科 / 理科（数学等分文理的科目）
  tags: [],
  // —— 题目级数据集（如 CJEval 中考题库）的如实建模 ——
  paperKind: 'questionSet',                    // 题目合集：按「学科 × 题型」归集，不是成套试卷
  source: { name, org, repo, paper, license, note },
  // —— 数据质量标记（详见第七节）——
  isPartial,                                   // 收录题目不足官方满分 90%
  splitFromMerged, setIndex, setCount,         // 由合卷拆分还原
  isMerged, mergeNote,                         // 疑似合卷但无法可靠拆分
  scoreOverOfficial,                           // 分值合计高于官方满分（多为选做题）
  // —— PDF 抽取来源的如实降级标记（如高考物理分省卷）——
  extractionQuality: 'pdf-text',               // 内容由 PDF 文本层自动抽取
  formulaDegraded: true,                       // 公式已被打散（h=½gt² → "22 ht g="）
  figuresMissing: true,                        // 题干中的题图不在文本层内，无法还原
  hasAnswerInfo: true | false,                 // 该卷源数据是否收录答案与解析（逐卷标注）
  questions: [ /* 见下 */ ]
}
```

> **为什么会有「题目合集」**：CJEval 只提供**题目级**数据——有学科、题型、难度、考点、答案，
> 但**没有**省份、年份与试卷结构。强行按年份/省份切分等于凭空捏造，因此如实建模为
> `paperKind: 'questionSet'`、`year: null`、`province: '未标注'`、`officialTotalScore: null`，
> 前端相应地隐藏年份与省份筛选、并把标题显示为「年份未标注 / N 个题目合集」。

### 题目 `question`

```js
{
  id, no, originalNo, type, score,
  stem,                                        // 题干，支持 \n 换行
  options: [{ key: 'A', text: '…' }] | null,   // 选择题才有
  material, materialLabel,                     // 阅读/完形的原文与篇目标签（同一篇只存一次，不重复进题干）
  multiPart, partCount,                        // 材料题（一段材料 + 多个带选项的小题）
  answer,                                      // 参考答案（多选为连写，如 "AD"）
  analysis,                                    // 解析
  knowledge: [], knowledgeSource: 'rule',      // 考点标签（诊断与统计的基础）
  difficulty, difficultyLevel,                 // difficultyLevel ∈ 易 / 中 / 难
  difficultySource: 'estimated' | 'provided',  // 官方标注 or 估算
  difficultyLabel,                             // 官方原始难度标签（如「较易」「困难」）
  scoreSource: 'estimated' | 'provided'        // 分值来源（CJEval 未提供分值 → estimated）
}
```

### 历年统计 `stats`

```js
{
  examType, subjectId, subject, scope, scopeLabel,
  // 题目级数据集（无年份）时置为 true，且 years 只有一项、year 为 null
  noTimeDimension: false,
  difficultyBasis: 'estimated' | 'provided',   // 难度是否为官方标注
  scoreBasis: 'estimated' | 'provided',        // 分值是否为官方提供
  years: [{
    year, questionCount, totalScore, avgDifficulty,
    periodLabel,                                // 无年份时为「全量（数据集未标注年份）」
    typeDistribution:       [{ type, count, score }],
    knowledgeDistribution:  [{ name, score, ratio }],
    difficultyDistribution: [{ level, ratio }],
    highlights                                  // 命题特点
  }]
}
```

> 无年份数据源不会伪造时间轴：`noTimeDimension = true` 时，模块 2 改为展示**全量题目**的
> 题型结构、难度分布与考点分布，并在页面顶部说明「该题库不含年份信息，无法做逐年趋势分析」。

### 知识图谱 `curriculum`

```js
{
  examType, subjectId, subject,
  tree: [{
    id, name, weight, difficulty, examFreq, summary,
    children: [{
      id, name, weight, difficulty, examFreq,
      keyPoints: [], tips: [], mistakes: [], prerequisites: []
    }]
  }]
}
```

**学习优先级算法**：`priority = 分值权重 × 0.5 + 考频归一化 × 0.3 + 掌握难度 × 0.2`，
再叠加个人薄弱点加权（诊断命中的知识点 +0.35，已掌握 −0.5）。

**模块权重来自真题**：知识图谱的模块权重不再使用人工估值，而是由**近 3 年（2022–2024）真实真题的考点分值占比**聚合得出
（`weightBasis.source = 'derived'`，高考数学 7/7 模块全部命中），未匹配的模块保留人工估值。
例如高考数学：函数与导数 21.5%、集合/逻辑/向量与复数 15.7%、解析几何 12.9%、概率与统计 9.8%、
数列 8.9%、三角函数与解三角形 8.2%、立体几何 6.1%。

---

## 七、公开题库导入（已落地）

### 数据源总览

| id | 数据源 | 学段 | 导入器 | 试卷 | 题目 | 覆盖 |
| --- | --- | --- | --- | --- | --- | --- |
| `gaokao` | GAOKAO-Bench（复旦大学 OpenLMLab） | 高考·全国卷 | `import-bank.mjs` | 286 | 3,101 | 2010–2024 / 9 学科 |
| `gaokao-english` | 高考英语真题数据库（ZhangYixuan-in-BUPT） | 高考·分省 | `import-gaokao-english.mjs` | 180 | 7,893 | 2008–2025 / 17 省 |
| `gaokao-physics` | gaokaophysics（deekur，PDF） | 高考·分省 | `import-gaokao-physics.mjs` | 109 | 1,980 | 2010–2024 / 19 省 |
| `zhongkao` | CJEval（腾讯优图 × 北京大学） | 中考 | `import-cjeval.mjs` | 40 合集 | 26,136 | 10 学科（题目级） |

合计 **621 套 / 39,125 道题 / 21 个省级行政区 / 14 个学科 / 2008–2025 年**。

四个数据源由 `bank-loader.js` 按**文件名前缀**加载（`gaokao-english-`、`gaokao-physics-`、`zhongkao-`），
并做**统计合并**：英语在「全国卷」与「分省卷」两个来源里都有统计条目，合并后按 `examType|subjectId`
归并为**一条**（`scope: '混合'`，年份区间取并集，省份取并集），避免学科下拉里出现两个「英语」。

### 高考全国卷（GAOKAO-Bench）

| 项 | 内容 |
| --- | --- |
| 数据集 | **GAOKAO-Bench**，复旦大学 OpenLMLab |
| 原始仓库 | <https://github.com/OpenLMLab/GAOKAO-Bench> |
| 结构化衍生 | <https://github.com/rainewhk/gaokao>（465 个 jsonl，按 `学科/年份/卷别/题型` 组织） |
| 许可 | 原始数据集与衍生仓库均声明**仅供研究与学术用途，不得用于商业目的** |
| 版权 | 题目版权归原出题机构所有 |

### 使用方式（高考）

```bash
# 方式一：仓库已存在（.gk-src 在项目上一级目录），直接重新导入
npm run import:bank

# 方式二：一键拉取源仓库并导入
npm run import:bank:fetch
```

导入产物写入 `server/data/bank/`：

- `papers.json` — 试卷与题目（含答案、解析、考点、难度）
- `stats.json` — 由题目**反向聚合**出的历年统计（模块 2 的数据底座）
- `manifest.json` — 数据来源、许可、处理说明与各项计数

服务启动时由 `bank-loader.js` 加载；**若目录不存在则自动回退到内置示例数据**，服务不会启动失败。

### 导入结果

```
源记录 3126 条 → 试卷 286 套 / 题目 3101 道
含选项 1899 道 · 材料题 189 道 · 年份 2010–2024 · 卷别 7 类 · 学科 9 门
```

### 关键处理：合卷拆分

源数据把**同年同卷别的多套试卷**（如 2019 年全国 I / II / III 卷）合并存放在同一目录，
直接导入会得到「一套卷 50 道题、卷面分 334 分（官方满分 150）」的错误结果。

导入器按两个互补信号拆分，并跨题型按序号对齐：

1. **题号信号**——单套试卷内每个题号只出现一次，因此当某题号在「当前这一套」中已出现过，
   即进入新的一套。
   - 数学 2019 选择题 → `1,2,3,6,7,8,10,11 | 1,2,…,11 | 1,2,…,12`
   - 历史 2018 解答题 → `14,13,15,16,17 | 14,13,15,16,17 | 13,14,15,17`
   - ⚠️ 不能用「题号是否回退」判断：源数据在同一套内并不保证题号有序（上例先 14 后 13）。
2. **条目信号**——语文等题型的题面没有前置编号，但每种题型文件恰好按「每套一条」排列，
   于是可按顺序逐套对应（古代诗歌阅读 3 条 → 第 1/2/3 套各一条）。

只有在**多数题型都给出同一个套数 N（2 ≤ N ≤ 4）**、且拆分结果每一套都「像一套卷」
（题量 ≥ 4 且分值 ≥ 官方满分 20%）时才真正拆分；否则保留合卷并标记。

拆分效果：**58 组 → 还原 144 套**，题目数异常（> 25 题）的数学卷由 15 套降为 **0 套**。

### 数据质量标记

| 字段 | 含义 |
| --- | --- |
| `splitFromMerged` / `setIndex` / `setCount` | 由合卷拆分还原，本卷是第 N 套 / 共 M 套 |
| `isMerged` + `mergeNote` | 疑似多套合并，但题号无法可靠对齐（题量稀疏），**保留合卷并说明原因** |
| `scoreOverOfficial` | 题面分值合计高于官方满分。主因是该学科含**选做题**（学生只需选答一题，数据集却全部计入），也可能是源数据分值字段偏高；**不代表该卷由多套试卷合并而成** |
| `isPartial` | 收录题目分值不足官方满分的 90%（数据集为节选） |
| `difficulty` / `knowledge` | **估算/规则推断**，非官方标注（见 `manifest.json` 的 `estimatedFields`） |

上述标记均会在真题库列表与试卷详情页以标签或说明条如实展示。

---

### 高考英语（分省）

GAOKAO-Bench 的 `province` 字段在本地 3,126 条记录中**全为 `null`**，上游也未补充分省数据——
因此省份筛选若要落到真实数据，必须接入**按省组织**的数据源。

| 项 | 内容 |
| --- | --- |
| 数据集 | **高考英语真题数据库**（ZhangYixuan-in-BUPT） |
| 仓库 | <https://github.com/ZhangYixuan-in-BUPT/gaokao-english-exam-database> |
| 结构化文件 | `structured/database.json`（9.48 MB） |
| 许可 | **未声明明确许可证，仅作研究与学习用途** |
| 规模 | 233 套 → 导入 **180 套分省卷** / 7,893 题 |
| 覆盖 | 2008–2025 年 / **17 个省级行政区**（上海 29 套 · 浙江 28 · 北京 25 · 天津 21 · 江苏 13 …） |

```bash
npm run import:gaokao-english:fetch   # 拉取 + 导入
npm run import:gaokao-english         # 源数据已存在时只重新导入
```

产物：`gaokao-english-papers.json` / `-stats.json` / `-manifest.json`。

#### 关键处理

1. **只导入分省卷，不重复导入全国卷**。源数据里 53 套是「全国Ⅰ/Ⅱ/Ⅲ卷、新课标卷、新高考卷」——
   这些已由 GAOKAO-Bench 覆盖，重复导入会让同一份卷子出现两次。导入器按 `region` 字段识别并**跳过**，
   跳过明细写入 `manifest.stats.skippedRegions`（可审计，而不是静默丢弃）。
2. **阅读/完形原文单独存放**。源数据把整篇阅读原文 + N 道小题绑在一起。
   导入器把原文写入题目的 `material` 字段、篇目标签写入 `materialLabel`（如「阅读理解 A」），
   **不再重复塞进每道题的题干**；前端同一篇只渲染一次（`.q-material` 限高可滚动），避免长文把题目挤出视野。
3. **题号 → 短码 id**。源数据存在「浙江 1 月 / 6 月 / 7 月」这类同省同年多卷，
   若用哈希取高位（`slice(0,4)`）会撞成同一个 id（实测 2 套卷 / 90 题重复）。
   现改为 **FNV-1a 取低位**（`slice(-6)`）并附**唯一性兜底**（冲突时追加 `-2`/`-3`）。
4. **难度来源如实标记**。阅读篇目难度是数据集**标注**的（简单/中等/困难）→ `difficultySource: 'provided'`；
   其余题型按题型与题内位置估算 → `'estimated'`。
5. **分值如实留空**。数据集不提供每题分值，按题型估算并标 `scoreSource: 'estimated'`，
   `officialTotalScore: null`（不伪造满分），前端以 `≈` 前缀提示。

#### 导入结果

```
源记录 233 条 → 分省卷 180 套（跳过全国卷 53 套）/ 题目 7,893 道
含选项 6,554 道 · 难度来自数据集标注 3,191 道 · 省份 17 个 · 年份 2008–2025
id 唯一性：试卷 180 套无重复 / 题目 7,893 道无重复
```

---

### 高考物理（分省，PDF 抽取）

| 项 | 内容 |
| --- | --- |
| 数据集 | **gaokaophysics**（deekur） |
| 仓库 | <https://github.com/deekur/gaokaophysics> |
| 原始形态 | **PDF 试卷**（442 个文件，1952–2026，约 563 MB） |
| 许可 | **仓库未声明明确许可证，仅作研究与学习用途** |
| 导入规模 | 109 套单科物理卷 / 1,980 题 |
| 覆盖 | 2010–2024 年 / **19 个省级行政区**（浙江 17 套 · 海南 15 · 上海 15 · 江苏 14 …） |

```bash
npm run import:gaokao-physics:fetch   # 下载 PDF → 抽取文本 → 解析入库（一条龙）
npm run fetch:gaokao-physics          # 仅下载 PDF（默认只下 2010–2024 单科卷，约 96 MB）
npm run extract:physics               # 仅做 PDF → 文本（pypdf，产物在 .gk2-src/phys-text/）
npm run import:gaokao-physics         # 仅解析文本 → 结构化入库
```

产物：`gaokao-physics-papers.json` / `-stats.json` / `-manifest.json`。

> **PDF 是这一来源最大的诚实性挑战**，因此拆成三步、并把降级信息全部落库：
>
> 1. **`extract-physics-text.py`（pypdf）** 只做「PDF → 文本」，与解析解耦——
>    这样迭代解析规则时不必反复重跑 PDF。
> 2. **`import-gaokao-physics.mjs`** 解析文本，产出结构化试卷。
>
> 三处已知降级，均**逐卷标记、页面显著提示、绝不做猜测性修补**：
>
> - **公式被打散**：PDF 文本层按字形位置输出，`h = ½gt²` 会被抽成 `22 ht g=`。
>   `formulaDegraded: true`，界面提示「公式会被打散……一律原样保留、未做猜测性修正」。
> - **题图无法还原**：「如图所示」指向的图不在文本层内，`figuresMissing: true`。
> - **约三分之一试卷无答案**：`hasAnswerInfo` **逐卷**标注；无答案的卷如实显示「该卷源数据未收录答案与解析」，
>   而不是留空假装有、或从别处拼凑。
>
> 另：**仅收录单科物理卷，不收录理综卷**——理综是物理 + 化学 + 生物合卷，按题号切分物理部分不可靠，
> 宁可少收也不臆造边界。`INCLUDE_COMBINED=1` 可显式开启（默认关闭）。

#### 关键处理

1. **卷名 → 省份/年份**。文件名即元数据（`2024江苏.pdf` → 2024 年 / 江苏），多省合卷名（如 `2024黑吉辽.pdf`）无法拆分则跳过。
2. **题干定位**。只在出现首个章节标题（`一、选择题…` / `一、单选题…`）之后才开始找题号，
   否则卷首「1. 答题前，考生务必将自己的姓名……」这类**编号说明**会被当成第 1 题。
   另有 `INSTRUCTION_RE`（考生/答题卡/准考证/满分/考试时间…）兜底过滤。
3. **章节标题容错**。各卷写法不一（`选择题`/`单选题`/`单项题`/`多项题`/`实验题`/`计算题`…），
   且源文件存在**笔误**（2011 江苏写作「一、单项题」，漏了「选」字），
   故用统一的 `TYPE_PATTERN` 覆盖全部变体，并对 `单项题 → 单选题`、`多项题 → 多选题` 做归一。
4. **答案两种格式**。行内 `故选 C` / `【答案】A`，以及卷末「参考答案」键（`1．D  2．A  3．BCD`），
   两种都支持。
5. **选项提取**。要求选项字母严格递增（A→B→C…）；若抽出来全是空文本（纯图片选项，文本层无内容），
   则不当作有选项——避免产出一堆 `A. ` `B. ` 的空壳。

#### 导入结果

```
源 PDF 109 套 → 试卷 109 套 / 题目 1,980 道（含选项 1,315 道）
含答案的卷 74 套（其中 853 题带答案）/ 无答案的卷 35 套（如实标注 hasAnswerInfo=false）
省份 19 个 · 年份 2010–2024 · 抽取质量 pdf-text · 公式降级 / 题图缺失 全卷标记
```

> 答案覆盖率说明：全库 1,980 题中 853 题带答案（含答案卷内覆盖率约 65%）。
> 未覆盖部分是**源数据本身**没有答案（部分卷如「浙江 4 月」只有 `【解析】` 而无答案），
> 按「不伪造」原则如实留空并标注，而非从其他来源拼凑。

---

### 中考题库（CJEval）

| 项 | 内容 |
| --- | --- |
| 数据集 | **CJEval** — *A Benchmark for Assessing LLMs Using Chinese Junior High School Exam Data* |
| 发布方 | 腾讯优图实验室（Tencent YouTu Lab）× 北京大学 |
| 仓库 | <https://github.com/SmileWHC/CJEval> |
| 论文 | <https://arxiv.org/abs/2409.16202> |
| 许可 | **仅供学术与研究用途，禁止商业使用** |
| 规模 | 26,136 条样本 / 10 个学科 |
| 原生字段 | `subject`、`ques_type`、`ques_difficulty`、`ques_content`、`ques_answer`、`ques_analyze`、`ques_knowledges` |
| **不包含** | 省份、年份、试卷结构、每题分值 |

```bash
npm run import:zhongkao:fetch   # 拉取 + 导入（走 GitHub Contents API）
npm run import:zhongkao         # 源数据已存在时只重新导入
```

产物写入 `server/data/bank/`：`zhongkao-papers.json` / `zhongkao-stats.json` / `zhongkao-manifest.json`。

#### 为什么是「题目合集」而不是「试卷」

CJEval 是**题目级**数据集，没有省份、年份与试卷结构。若强行按年份或省份切分，等于凭空捏造。
因此导入器**只按「学科 × 题型」归集**，并如实标注：

```
paperKind: 'questionSet'   题目合集，不是成套试卷
year: null                 不伪造年份
province: '未标注'          不伪造省份
officialTotalScore: null   不伪造满分
```

前端据此联动：真题库隐藏年份 / 省份筛选（并显示「该题库未标注年份 / 省份」），
标题显示「年份未标注 · N 个题目合集」，每条打上「题目合集 · 非成套试卷」标签；
模块 2 走**全量分布视图**而非逐年趋势视图。

#### 导入结果

```
源记录 26136 条（跳过 0 条）→ 题目合集 40 个 / 题目 26136 道（含选项 10578 道）
学科 10 门：生物 3016 · 化学 2820 · 语文 2499 · 英语 2242 · 地理 2900
           历史 2961 · 信息技术 2417 · 数学 2717 · 物理 2828 · 科学 1736
难度分布（官方标注）：一般 11594 · 较易 8943 · 容易 4076 · 较难 1347 · 困难 176
考点覆盖：26136/26136 道题均带考点标注（缺 0）
id 唯一性：合集 40 个无重复 / 题目 26136 道无重复
```

#### 关键处理

1. **题型 → 稳定 id 前缀**：CJEval 共 14 种题型（单选/多选/判断/填空/解答/计算/简答/词汇选择/单项选择/阅读单选/阅读判断/诗歌鉴赏/现代文阅读…），
   全部映射为固定短码（`sc`/`mc`/`tf`/`fb`/`aq`/`calc`/`sa`/`vc`/`ss`/`rs`/`rt`/`po`/`mr`），
   未知题型回落到哈希后缀——否则「同科多题型」会撞出重复合集 id。
2. **选项解析**：CJEval 把选项拼在 `ques_content` 里（`… 选项：A. … B. …`）。
   解析时先定位 `选项：` 分隔符，再**只保留严格递增的 A→B→C… 连续段**，
   避免把题干正文里的「连接 A、B 两点」误判成选项标记。
3. **难度标签**：CJEval 官方给 5 档（容易 / 较易 / 一般 / 较难 / 困难），
   映射为得分率后归一到 易 / 中 / 难 三档，同时保留 `difficultyLabel` 原文，标记 `difficultySource: 'provided'`。
4. **分值**：数据集不提供每题分值，按题型估算（3 分客观题 / 8 分主观题），标记 `scoreSource: 'estimated'`，
   前端以 `≈` 前缀提示。
5. **多选答案**：`ques_answer` 中多选以连写形式给出（如 `"AD"`）。
   前端只有当答案**整体由 A–H 字母构成**时才按选项字母高亮，
   避免把「B 细胞」这类文本答案误判成选项 B。

#### 数据质量实测

| 项 | 实测结果 |
| --- | --- |
| 题干（+选项）完全重复 | 18 / 26136 = **0.1%** |
| 原始数据跨 train/valid/test 重复 | 1 / 26136 = **0.0%**（导入器未做任何去重） |
| 缺考点标注 | 0 道 |
| 缺题号 | 0 道 |
| 合集 id / 题目 id 重复 | 0 |
| 题目数 < 5 的极小合集 | 2 个（地理多选题 1 道、科学多选题 3 道）——源数据本身如此，未做合并 |

> 结论：CJEval 三个 split 之间**几乎没有重叠**，无需去重；仅有 2 个极小合集（地理多选题、科学多选题）是源数据本身的稀疏组合，按「不伪造结构」原则保留原样。

### 从零接入自有题库

若要换成自建题库，保持 `paper` / `question` / `stats` 结构（见第六节）即可，其余无需改动：

1. **试题来源**：优先使用各省教育考试院公开的真题与答案；商用需确认授权。
2. **落库**：建议 PostgreSQL + JSONB 存题目（或 MongoDB），由 `data/index.js` 从数据库读取。
3. **统计数据**：参考 `import-bank.mjs` 的做法由题库**自动聚合**（按 `knowledge` 标签与 `score` 分组求和）。
4. **向量检索**：为题干生成 embedding 存入 pgvector / Milvus，把模块 3 的「关联真题」改为语义检索。

### 校验脚本

```bash
npm start          # 另开一个终端保持服务运行
npm run verify:api # 17 组接口断言 / 104 条（数据规模、来源许可、题目合集、分省卷、PDF 降级标记、无年份统计、id 唯一性、统计合并、派生权重…）
npm run verify:dom # 无头浏览器渲染 15 个页面路由并断言关键内容（含分省筛选、英语/物理卷详情、无答案卷、中考页、无年份分析视图）
npm run verify     # 以上两者一起跑
```

### 网络受限环境的取数经验

本项目在**强防火墙环境**下完成取数，相关经验对复现/扩展有用：

| 通道 | 结果 |
| --- | --- |
| `github.com` git clone | ❌ `CONNECT tunnel failed, response 502` |
| `raw.githubusercontent.com` | ❌ 连接被拒（exit 35） |
| `cdn.jsdelivr.net` / `gcore.jsdelivr.net` / `cdn.statically.io` / `raw.githack.com` / `mirror.ghproxy.com` | ❌ 全部不可达（`000`） |
| `api.github.com` | ⚠️ 可用，但**未认证限速 60 次/小时**，且 **Contents API 无法内联 > 1 MB 的文件**（需改用 `/git/blobs/{sha}`） |
| **`ghproxy.net`** | ✅ **可用**：`https://ghproxy.net/https://raw.githubusercontent.com/<owner>/<repo>/main/<path>`，字节精确、无 API 配额限制 |

结论：批量取 GitHub 大文件时优先走 `ghproxy.net` 反代（配合并发），不要用 Contents API 逐文件拉——
本项目正是在 60 次/小时配额耗尽后（HTTP 403 → 429）才切到 ghproxy 的。

> **Windows 提示**：Node 里 `/tmp` 会被解析成 `C:\tmp`，而 bash 映射到 `AppData\Local\Temp`。
> 中间产物一律写进项目工作目录，避免路径错位。

---

## 八、后续可扩展方向

- [ ] 用户体系与错题本（登录后同步诊断记录与学习进度）
- [ ] 试卷在线作答 + 自动判分（客观题可直接比对，主观题走 AI 批改）
- [ ] 按目标院校 / 专业生成考研备考方案（模块 4）
- [ ] 对话式 AI 讲解（`/api/ai/chat` 已预留，接上多轮上下文即可）
- [ ] 组卷导出 PDF（按知识点、难度、题型配比自动组卷）
- [ ] 移动端适配与微信小程序端
- [x] **接入「分省数据源」**：已导入**高考英语分省卷**（17 省）与**高考物理分省卷**（19 省），
      真题库的省份筛选在真实数据上已真正可用（江苏 28 套、北京 30 套…）。
      剩余可扩展方向：继续接入语文 / 数学 / 化学 / 生物 / 政治 / 历史 / 地理的分省卷。
- [ ] **补全中考、考研公开题库**：中考已接入 CJEval（题目级）；**考研仍为示例数据**，待找公开数据集。
- [ ] **物理分省卷的公式与题图还原**：当前 PDF 文本层抽取会打散公式、丢失题图，
      后续可尝试版面分析（如接入 OCR / 公式识别）以提升可用性——在此之前保持原样 + 显著标注。
- [ ] 图形/表格类题目的图片渲染（当前 LaTeX 公式以源码展示）
- [ ] 题目查重（跨数据源同题识别，避免不同来源导入同一份卷子）

---

## 九、免责说明

- **高考·全国卷**题目、答案、解析来自 **GAOKAO-Bench** 公开数据集，**仅供研究与学术用途、不得商用**；
  该数据集不保证题目、答案、解析的准确性，正式使用请以各省教育考试院发布的官方试卷为准。
- **高考·英语分省卷**来自 **高考英语真题数据库**（ZhangYixuan-in-BUPT），该仓库**未声明明确许可证，仅作研究与学习用途**；
  数据由项目方从公开真题整理并结构化，**非官方发布**。
- **高考·物理分省卷**来自 **gaokaophysics**（deekur），仓库**未声明明确许可证，仅作研究与学习用途**；
  内容由 **PDF 文本层自动抽取**，**公式会被打散、题图无法还原**，约三分之一试卷不含答案与解析，
  上述降级均**逐卷标注并在试卷页显著提示**，抽取文字一律**原样保留、未做猜测性修正**。
- **中考**题目、答案、解析来自 **CJEval**（腾讯优图实验室 × 北京大学），**仅供学术与研究用途、禁止商业使用**。
  题目由真实中考试题**改写**而来，**非原卷原文**；该数据集不含省份、年份与试卷结构，
  因此以「题目合集」而非「成套试卷」呈现。
- **考研**部分为**结构演示用示例数据**。
- `difficulty`（难度系数）：GAOKAO-Bench 部分为按题型、题内位置、分值占比**估算**；
  英语阅读/完形与中考部分为数据集**官方标注**；物理分省卷为**估算**。各处均以 `difficultySource` 标明。
- `knowledge`（考点标签）：GAOKAO-Bench 部分为关键词规则**推断**；英语与中考部分取自数据集**标注**。
- 分值：GAOKAO-Bench 部分来自源数据；英语、物理分省卷与中考部分数据集未提供，按题型**估算**
  （`scoreSource: 'estimated'`，界面以 `≈` 标注）；`officialTotalScore` 缺失时置为 `null`，不伪造满分。
- 合卷拆分为按题号推断的自动化还原，拆分后的「第 N 套」**不等同于**官方全国 I/II/III 卷编号；
  无法可靠对齐时保留合卷并如实标注，不做强行拆分。
- 所有「无年份 / 无省份 / 无分值 / 无答案」的场景均**如实标注为未知**，不以估算值冒充实测值。
