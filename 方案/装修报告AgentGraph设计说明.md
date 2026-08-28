# 装修报告生成 · Agent Graph 设计说明

版本 v0.2 · 2026-08-27
承接：v0.1（方法论 v4.0 侧成稿，未入仓）；改造依据：《Agent Graph 评审-2026-08-27.md》四改两补、《装修报告生成规则规范》v2.2。
v0.1 → v0.2 变更一句话：**数字由求值决定、状态真相在表、生成侧匿名、编号全语义化；新增冷启动条款与手册编译归宿。**

---

## 0. 结论与硬性条件

- 拆分单位仍是**领域单元**（生成轴），不是报告章节（呈现轴）。两轴分离的论证 v0.1 已说清，不重复。
- **数字不由 LLM 决定**（规则 8.2 可重放 + 版本锁定）：一切落点在任务派发前由 project-svc 规则引擎求值完毕，随 input_snapshot 下发。图内没有查表节点。
- **状态真相在 `svc_project` 表**（规则 8.1 禁第二台状态机）：一次生成 = 一次短 workflow run；"改一条决策局部重跑" = 里程碑引擎判定本次派哪些单元的任务，不是图内 checkpoint 恢复。
- **生成侧不知用户是谁**：单元输入是 slots 派生的匿名结构，无任何用户标识。
- **无人工节点**；人以产品/开发角色改源（规则 4.19），运行时不读任何人写的文本。
- 编号全按规则 1.7：`dom-` / `art-` / `pt-` / `cr-` / `lkp-` / `gap-`，禁纯序号。

## 1. 领域注册表（dom-，单一命名空间）

一套 `dom-` 同时承载两种身份：**生成单元**（图节点）与**知识/release 域**（规则 4.12 发布粒度）。两列打勾即两种身份都有——规则 1.8 第四条（禁两套域枚举）由单一注册表满足。

| dom- | 原 v0.1 | 生成单元 | 知识域(release) | 主产物（art-） |
|---|---|---|---|---|
| dom-site | D-1 户型条件 | ✓ | — | floorplan-current / daylight-analysis / wall-structure |
| dom-household | D-2 居住者 | ✓ | — | （时刻表+变量轴，供全图，匿名） |
| dom-storage | D-3 收纳 | ✓ | ✓ | storage-chapter |
| dom-layout | D-4 格局 | ✓ | — | plan-compare / plan-final / flow-analysis |
| dom-utility-points | D-5 点位 | ✓ | — | hydro-layout / hydro-checklist |
| dom-lighting | D-6 照明 | ✓ | ✓ | ceiling-lighting-plan / lighting-chapter |
| dom-material | D-7 材料 | ✓ | ✓ | material-chapter / material-mood |
| dom-kitchen-bath | D-8 厨卫 | ✓ | — | （产物并入 layout/points/purchase 各表） |
| dom-softdeco | D-9 软装 | ✓ | ✓ | color-soft-chapter |
| dom-budget | D-10 预算 | ✓ | ✓ | budget-chapter / quotation-checklist |
| dom-construction-seq | D-11 施工时序 | ✓ | — | acceptance-checklist（施工期节奏） |
| dom-vision | D-12 愿景与机会 | ✓ | — | space-render 叙事 / 开篇承诺 |
| dom-ergonomics | （v0.1 缺） | — | ✓ | ergonomics-chapter（求值层产出，dom-layout 消费其校核） |

注：dom-ergonomics 是 v0.1 的缺口——12 项通道校核与定制尺寸族在 v0.1 隐含在"格局"里，现独立为知识域（release 已有种子 23 条参数）。

## 2. 两条流水线（v0.1 单图拆两层）

```
用户确认决策 → 里程碑引擎 checkCompletion → 判定应出产物集
   │
   ├─ 求值线（project-svc 规则引擎，Java，同步，gen-locked/evaluated/assembled）
   │    读 slots + 户型特征 + 各域 release 快照
   │    → 全部 lkp- 落点、清单条目、校核结论、交叉冲突检出（确定性）
   │    → 查不到 → gap- 记录（随产物回流，不阻塞）
   │    → 产出【报告数据包】：落点对象(值/区间/依据 release 引用/管的时刻/生活翻译)
   │                        + 锁定清单 + 动作表 + 匿名画像 + persona release 引用
   │
   └─ 成文线（reportgen，Temporal 短 workflow，gen-generated/polished）
        input_snapshot = 报告数据包（自包含，不回查任何库）
        → 各 dom- 单元并行成文（依赖只存在于求值线；成文线单元间零通信）
        → 页面装配（唯一知道"页"的节点，pt- 页型库，确定性）
        → 册级校验 → 渲染
```

要点：
- **v0.1 的"共享状态"退化为报告数据包**——一个可序列化、版本锁定的值，不需要 reducer/checkpoint/累加语义。
- **仲裁不再是热点 LLM 节点**：尺寸/规范/净距类冲突在求值线确定性检出（plan-rule-check 同构）；只有叙事语域冲突留一个小仲裁节点在成文线装配前。v0.1 §8 待定项 1 就此关闭。
- v0.1 的第一层假并行（dom-vision 依赖 dom-site、同刻扫描依赖 dom-household）在求值线内按真实依赖排序，成文线不存在此问题。

## 3. 单元子图（成文线，十二单元共用）

```
输入：本域落点对象组 + 时刻表(匿名) + persona release（★不是手册文本）
  → 叙事推导（内部语域；只组织"怎么讲"，不产生任何数字）
  → 卡片写作（客户语域；结构化输出，数字字段只能引用落点对象）
  → 出口过检·规则层：schema 必填 + cr- 机检（本域 release 物化的禁词/量纲/计数/删减不算通过）
  → 出口过检·判官层：分域反例库（release 数据），只报 cr- 编号不改写
       └→ 不合格 → 重写 ≤2 轮 → 仍不合格 → failed verdict 上抛（绝不静默假成功，v0.1 缺此出口）
  → 契约产出：卡片组 + 图描述（画什么推导）+ 本单元 gap- 回流
```

- 推导/写作语域分离保留（v0.1 正确设计）；差别：写作节点的数字来源从"上游节点产出"改为"落点对象引用"，机检可逐字段比对零漂移。
- 判据下沉次序不变：schema > 规则 > prompt > 判官；四者全部是 release 数据（规则 4.17 自迭代回路产出），人不写其中任何一条。

## 4. 校验三作用域（不变，编号更新）

| 作用域 | 位置 | 判据（cr-） |
|---|---|---|
| 单元级 | 子图出口 | moment-scan / param-debt / causal-link / unit-translation / exclusion-visible |
| 页级 | 装配后 | takeaway / first-visual / one-thesis-per-page |
| 册级 | 渲染前 | branch-cap(≤2) / set-closure / promise-fulfilled / concept-through |

## 5. 评测与回流（v0.1 §6 收编为规则 4.17 的实例）

- **读者代理**：评测回路组件，不在生成链路。人设由**匿名合成家庭**生成（golden set），不从用户档案取。四问不变（带走了什么/哪句读不懂/哪句让我做事/读完想问什么）；机检合格 × 读者代理"没带走"的交集 = 判据缺口。
- **运行时信号**：追问经 chat-svc 埋点事件（报告页无聊天框，规则 6.4）；换款/修订/跳读/勾选覆盖按既有埋点。
- **入册门禁**：冻结种子集回归 + 观察态 N 份 + 每轮上限——v0.1 §6.3 原样保留，前移为判官反例库的准入条件（判官与写手同源，这三道门是唯一防漂移机制）。

## 6. 冷启动（v0.2 新增，规则 4.18）

信号为零期：只发 calibrated 条目支撑的内容；经验条目（含全部 persona 语域样例）降档或缺席；**主动收窄产物范围**，宁薄勿撑。退出判据：某域行为信号量达阈值，该域经验条目方可走转正路径。当前事实（获取回路首跑 run-2026-08-27）：**零条 calibrated**——冷启动期的真实起点比 v0.1 设想更靠后，第一批可发内容取决于核验跑批何时落地。

## 7. 手册的归宿（v0.2 新增，规则 4.19）

| 手册 | 编译为 | 状态 |
|---|---|---|
| 手册 3 领域段 | persona 资产包（personas 表，随域 release） | 六域已编译（rulebook-seeds） |
| 手册 2 判据 | schema 字段 + cr- 机检 + 判官反例库 | 机检 27 条已编译；反例库待 13 轮附录 A 转化 |
| 手册 1 装配规则 | pt- 页型声明 + 页/册级 cr- | 待编译 |
| 手册 0 父图 | workflow 定义本身 | 即本文 §2 |

**手册文本不进 prompt。** 人改手册 → AI 重编译 → 核验 + 种子集回归 → release。方法论 v4.0 与四分册标注为冷启动种子输入，随信号积累被逐条替换。

## 8. 推进节奏

1. 夹具避开 floorplan-parse（咽喉未验证）：用 estate 已录入标注户型作输入。
2. 首批跑 dom-lighting + dom-ergonomics（种子密度最高、国标锚已接）+ dom-budget（机检纪律最全）。
3. 同一输入跑 N 次看两件事：求值线可重放是否成立（字节级同输出）；成文线机检拦截率与判官触发分布。
4. 落点：求值线=project-svc 规则引擎模块；成文线=新仓 ishome-reportgen（独立 Temporal worker，队列 reportgen-activities）；编排=genpipe workflow。

## 9. 遗留

- reportgen 建仓与队列注册（contracts task_queues 只增）。
- 判官反例库：13 轮附录 A 的问题句/改后句转化为 release 数据（人驱动 AI 编译，同种子路径）。
- pt- 页型库首版声明。
- dom-kitchen-bath / dom-site 等非知识域单元的求值规则落哪张表（当前 rule.domain 枚举含空间域，够用；出现独立发布节奏再拆）。
