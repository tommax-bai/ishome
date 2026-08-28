# AI装修效果图产品 · 技术架构方案

> 讨论时间：2026-08 ｜ 参与：TMax × Claude
> 口径：**正式产品架构，不做MVP过渡形态**（数据模型、身份体系、资金路径、事件契约按十年不动摇标准设计）
> 团队假设：~~8-15人研发，Java业务班底 + Python AI管线班底~~（**2026-08-23 拍板**：开发完全由 Claude 承担，语言栈不受班底约束，双栈架构与 11 服务维持；原假设作历史记录保留）
> 状态：待拍板项见文末，定稿后进入 contracts 骨架与模块结构落地
> 修订：2026-08-22 依《架构对齐-设计Agent×技术架构》V1.2 回改——新增 design-svc、notify-svc 升格 channel-svc、catalog 模块、IM 主通道（首发飞书）、视觉模板体系
> 修订二：2026-08-22 **移除系统中全部"人工介入"环节**（人审门、结构复核、抽检队列、转人工），并确立设计取向：**只面向把系统做好用，不面向责任划分**（产出性质由入口服务声明一次性说明，交互与交付物中无免责文案，责任话题不进入设计讨论）。质量保障：约束优先（确定性母版/模板槽位/默认不动结构）+ 设计时判断（模板上线一次性验收）+ 当事人校验（确认闭环/换一张/下单前量房）+ 机检全覆盖；结构数据的系统性正解在户型库侧沉淀。审核工作台缩编为模板验收台。对应 Agent 方案 V1.3。

---

## 〇、总原则

1. **重构灾难的真实藏身处**是数据模型、身份体系、资金路径、事件契约四处——这四样按最高标准一次做对；进程/服务拆分反而是可逆决策。
2. **服务存在性判据**：每个服务必须能回答"它的伸缩轴、故障域、团队所有权哪一个是独立的"，答不出即为拆过头。
3. **规范即工具链**：所有规范必须固化在 CI/pre-commit 强制执行，写在文档里不进流水线的规范视为不存在。
4. 语言按**团队所有权**切分，不按技术品味：业务域 Java，生成域 Python，仅此两种（+前端TS）。

---

## 一、服务拆分（11个服务）

### 1.1 服务清单

| 服务 | 职责 | 独立存在理由 | 语言 |
|---|---|---|---|
| c-bff | C端（小程序/H5）聚合层 | 端侧裁剪与聚合，隔离内部演进 | Java |
| admin-bff | 管理端聚合 + RBAC执行点 | 权限模型与C端完全异构 | Java |
| identity-svc | 多渠道身份绑定（首发飞书 open_id/union_id，微信系后续）、JWT签发、户型认领关系 | 安全域隔离；认领关系是核心指标数据源 | Java |
| estate-svc | 城市/小区/户型资产、交付日历、搜索索引 | 供给侧主数据，排产驱动源；PostGIS地理查询 | Java |
| content-svc | 方案套系、图/视频媒资、注释层、发布状态 | C端读流量主体，独立缓存与CDN策略 | Java |
| genpipe-svc | 生成工作流编排（Temporal workflow）+ 发布门禁状态机（全机检） | 与业务服务负载形态完全不同；AI团队所有权 | Python |
| genpipe-worker | 生成/打分/合规检测/母版绘制/场景编译/渲染 activities | 弹性伸缩轴独立（外部模型API配额、未来GPU） | Python |
| design-svc | 交互设计会话：Design Orchestrator、确认闭环、Patch 引擎、ProjectState 属主、设计项目 Temporal 工作流 | 会话式 LLM 负载形态独立；设计会话故障域与内容消费、资金路径隔离；AI团队所有权 | Python |
| trade-svc | 订单、微信支付、退款、对账、权益 | 资金路径故障域隔离，审计要求独立 | Java |
| shelf-svc | 选品池、联盟商品同步、带佣链接、点击/转化归因 | 归因是营收数据源，写入形态独立 | Java |
| channel-svc | IM 渠道网关（可插拔 adapter，首发飞书）+ 订阅消息/短信触达与频控 | 渠道协议、限频与重试逻辑收口；IM 是产品主通道 | Java |

### 1.2 明确不单拆清单

- **媒体处理**：OSS原生图片处理 + MPS转码，content-svc 持凭证，不成服务
- **内容安全**：genpipe 的一个 pipeline stage + 共享 adapter（阿里云内容安全 / 微信 msgSecCheck），不成服务
- **搜索**：索引由 estate-svc 所有，Meilisearch 定位为基础设施，不成服务
- **推荐**：远期数据团队议题，当前不建
- **三维资产库**：catalog 模块落 estate-svc（schema `svc_catalog`），运营 CRUD 走基座代码生成；升格独立服务的触发条件写死：出现专职资产团队或独立发布节奏需求
- **3D 资产加工**（格式转换/LOD/缩略图）：genpipe activity，不成服务（同媒体处理原则）
- **渠道适配器**：channel-svc 内插件（adapter + 能力声明 + 凭证配置），不按渠道拆服务

### 1.3 领域模块对照（逻辑边界）

estate（供给侧原子）/ catalog（设计资产，物理落 estate）/ content（消费终物）/ genpipe（生成算力管线）/ design（交互设计状态机与决策）/ user→identity / commerce→trade / shelf / channel（原 notify，双向渠道）/ distribution（对接AIDCP，按外部系统处理）/ insight（开源承接，不自研）

---

## 二、关键技术决策

### 2.1 语言与框架

- 业务域：Java 21 + Spring Boot 3
- 生成域：Python（Temporal Python SDK，编排+执行全归AI团队，避免跨语言维护编排逻辑）
- 前端：TypeScript

### 2.2 协议分层与契约

- 对外：REST + OpenAPI（BFF层）
- 对内：gRPC + protobuf
- 契约集中于独立 **contracts 仓**，CI 自动发布 Java/TS/Python SDK，**禁止手写客户端**（Java↔Python接口漂移唯一解法=契约先行）
- 渠道统一消息模型与能力声明（`channel.v1`）、design 域接口与事件同入 contracts 仓

### 2.3 工作流引擎

**Temporal（自托管）**。生成管线本质是长事务："多候选生成→打分→机检门禁→发布"，含重试、补偿、外部信号（用户确认=signal），为Temporal靶心场景。正式产品口径下自研DB状态机属重复造轮子。
部署口径：genpipe 与 design 各用独立 namespace；design 侧为跨月长周期项目工作流（用户确认=signal，continue-as-new 控历史长度），与 genpipe 的批量生成作业隔离。

### 2.4 消息

**RocketMQ 5（云托管）** 为唯一业务事件总线。
- 选型理由（vs Kafka）：延迟消息（订单超时关单）、事务消息（outbox替代）为原生能力
- 分析事件经 consumer 落 ClickHouse
- 预案：埋点吞吐爆炸时单独为分析链路加Kafka，业务总线不动

### 2.5 存储

- **Postgres（RDS）**：按服务分schema（`svc_estate`、`svc_trade`…），**禁止跨schema外键与join**——"将来拆库不流血"的全部秘密；JSONB存方案结构；PostGIS做"附近小区"
- **ClickHouse**：行为/归因分析
- **Redis 集群**：缓存
- **OSS + CDN**：媒资，防盗链+水印；图/视频流量走CDN直出，不过网关
- 备选：团队强烈偏MySQL可换，代价为失去PostGIS与JSONB表达力（待拍板项②）

### 2.6 资金与归因硬纪律（灾难高发区）

- trade/shelf 所有变更接口**强制幂等键**
- 金额一律 int 分存储
- 本地事务 + outbox 表发事件
- 每日对账 job 核微信支付账单
- 归因事实表以 PG 为准，ClickHouse 只做分析副本
- 全链路审计日志表

### 2.7 基建基线

- ACK（托管K8s）+ Argo CD（GitOps）+ Harbor + Terraform
- 服务发现：K8s原生DNS；配置：ConfigMap/Apollo
- **不上** Nacos注册中心/Dubbo（网关统一路由+K8s DNS已覆盖）；Sentinel仅在需精细流控时按库引入
- 分布式事务：编排补偿（saga）+ outbox，不用XA

---

## 三、统一网关层

### 3.1 结论

**要有，且只有一个**；职责边界为"请求级横切语义"；一切带业务语义的东西下推给BFF与服务。

### 3.2 为什么不能没有：真实入口清单

| 入口 | 特征 |
|---|---|
| c-bff | 脉冲流量（业主群转发瞬时爆发）、需反爬 |
| admin-bff | 低流量、高权限、强访问控制 |
| 微信支付回调 → trade-svc | 资金关键路径，IP白名单+最高可用性 |
| IM 事件 → channel-svc | 首发飞书（选长连接模式时此入口为 channel-svc 主动外连，不经网关）；微信系接入后微信服务器直连走此口 |
| 联盟转化回调 → shelf-svc | 归因数据源，丢失=丢钱 |
| AIDCP回调 → genpipe-svc | 内部系统跨界调用 |

关键事实：
- 后四类 **webhook 无BFF可藏身**，无统一网关则边缘策略散落六处
- 网关第一价值 = 把攻击面收敛为一个有统一日志、统一策略的口
- 本系统特有的两个边缘层刚需：**内容资产反爬**（户型方案库是护城河，bot对抗只能做在网关）、**小程序审核期服务端灰度**（客户端发版受审核节奏钳制，新老版本按版本路由）
- 反向减负事实：流量大头（图/视频）走CDN→OSS直出，网关只扛JSON API，不会成为容量瓶颈

### 3.3 网关与BFF分界线

**网关处理连接和请求级横切语义，BFF处理会话和产品级业务语义。**

- 进网关：TLS终结、路由、WAF/DDoS、粗粒度与按身份限流、灰度分流、访问日志、IP白名单、CORS
- **禁止进网关**：聚合裁剪（BFF存在理由；网关插件写聚合=不可测试的业务逻辑埋进配置）、业务鉴权决策（RBAC在admin-bff、登录态交换在identity）、微信签名验证（碰密钥、语义属接收方，网关只做IP白名单外壳）
- 强网关不能替代BFF（聚合是代码不是配置）；BFF不能替代网关（webhook无处安放、边缘策略双份漂移）
- admin的"统一"方式：**同一网关集群，不同域名+不同策略组**（IP白名单/办公网准入），策略分离靠配置不靠双套部署

### 3.4 实现选型（三档）

1. **首选：云托管网关（阿里云MSE云原生网关 / Higress）**——Ingress与API网关合一（少一跳）、无etcd运维、与云WAF/证书/SLS原生联动；支付回调这类"网关挂=钱断"路径的可用性外包给云厂商更划算
2. **备选：自托管APISIX**——需写自定义插件（如反爬做深）或明确保留换云自由时成立
3. **下限：Ingress-nginx + 云WAF**——正式产品口径不推荐（按身份限流与灰度能力糙）

一↔二的唯一决策变量：**对云厂商锁定的立场**（供应商策略判断，非技术判断，待拍板项④）

---

## 四、前端与管理后台

### 4.1 C端

- **IM 会话为主线程**（channel-svc 可插拔渠道，首发飞书）：信息收集、确认、图片交付、召回都发生在聊天里；"指着图说话"的富交互（看图点错、三图对比、材质选择、3D 查看、支付）跳 H5
- **uni-app（Vue3）一码双端**保留：H5 承接指图时刻与小红书外链；小程序降级为分享卡片与支付容器（微信系渠道接入后启用），按场景选容器
- 每次 H5 交互结果回写一条摘要消息到聊天线程，聊天线程 = 用户可见的完整项目时间线
- 不做SSR：H5落地页静态化即可

### 4.2 管理后台

- 基座：**RuoYi-Vue-Plus**（RBAC、审计日志、字典、代码生成开箱；Java班底零学习成本）
- 运营CRUD（小区库、交付日历、选品池）全走基座代码生成
- **自研只砸一处：模板验收台**（修订二：原"审核工作台三队列"取消——系统无运行时人审）——模板上线前的一次性验收界面：样张网格对比、打分器分数透出、验收/打回、模板配置发布。人的判断只发生在这里（设计时、对资产），运行时逐张实例全走机检（consistency-check / compliance-check / scorer 门禁）
- 运行时质量信号不设人工队列，改为产品内嵌采集：用户"换一张"点击（免费负样本标注）、确认闭环修改率、投诉回流——全部落 PostHog/ClickHouse，作为模型与检测器迭代的地面真值

---

## 五、仓库拆分

按语言域切monorepo，不按服务切多仓（20人以下多仓只收获PR编排地狱）：

```
gitlab.{code}.com/
  contracts/     # proto + OpenAPI，唯一真源，CI发布 Java/TS/Python SDK
  backend/       # Gradle多模块：services/{identity,estate,content,trade,shelf,channel,c-bff,admin-bff}
                 #              shared/{kernel,starter}；每模块独立出镜像
  aipipe/        # uv workspace：services/{genpipe-svc,genpipe-worker,design-svc}
                 #              packages/{scoring,adapters,patch-engine}
  frontend/      # pnpm：apps/{c-app,admin-web}、packages/{ui, api-client(生成)}
  infra/         # Terraform、Helm charts、Argo CD apps、网关声明式配置
  aidcp          # 既存系统独立仓，按"外部系统+API契约"对接，不并入
```

拆多仓触发条件（写死）：出现独立团队边界（>20人或外包隔离）。

---

## 六、编码与命名规范（全部CI强制）

> 本节只定基础设施级命名与工具链；服务内部分层、类/函数/变量命名、领域术语表、变化轴抽象规则（含多渠道命名专项）见《开发规范与代码分层》。

### 6.1 命名

- 服务：`{code}-{domain}-svc`；K8s namespace按环境
- proto包：`{code}.{domain}.v1`
- 事件：CloudEvents格式 `com.{code}.{domain}.{entity}.{past-verb}`（如 `genpipe.candidate.scored`）
- 表：`{schema}.{复数名词}`
- REST：`/api/v1/estates/{id}/floorplans` 复数资源；JSON字段 snake_case 端到端统一（OpenAPI生成TS客户端，消灭手写驼峰转换）
- 错误码：`{DOMAIN}_{3位}` + request_id 统一错误信封，注册表放 contracts 仓
- API版本策略：URL大版本；字段只增不删；废弃字段带 sunset 头

### 6.2 Java

- 阿里巴巴Java手册为底；Spotless + Checkstyle + PMD 强制
- **ArchUnit架构测试**（分层依赖、禁止repo跨域引用）——规范不进CI等于不存在
- MyBatis-Plus + Flyway；Testcontainers集成测试 + 覆盖率门禁

### 6.3 Python

- ruff（lint+format）+ mypy(strict) + **import-linter**（锁模块依赖方向，违规挂流水线）
- 目录按领域纵切（`src/{domain}/router|service|repo|models`），禁止 `utils/`、`common/` 垃圾场目录

### 6.4 数据

- 主键 ULID；时间戳 UTC；`created_at/updated_at` 全表必备；软删统一 `deleted_at`
- 枚举存字符串不存数字；金额 int 分

### 6.5 Git

- trunk-based + Conventional Commits，scope=服务名（commitlint校验）
- 语义化版本；镜像 tag = git sha

---

## 七、开源系统清单

### 7.1 引入

| 系统 | 用途 |
|---|---|
| 云托管网关 / APISIX | 南北向统一网关（见三·3.4） |
| Temporal | 生成工作流编排 |
| RocketMQ 5 | 业务事件总线（延迟/事务消息） |
| LiteLLM + Langfuse | 多模型API网关 + 逐任务成本追踪（单图成本、单项目会话 token 成本是经济账输入变量） |
| RuoYi-Vue-Plus | 管理后台基座 |
| Meilisearch | 小区/户型搜索 |
| PostHog(自托管) + ClickHouse + Metabase | 行为漏斗 + 归因分析 + 业务BI |
| OpenTelemetry + SkyWalking 或 Grafana LGTM | 全链路观测（Java班底SkyWalking更顺手，二选一） |
| Sentry / Argo CD / Harbor / Terraform / Flyway | 错误监控 / GitOps / 镜像仓库 / IaC / 迁移 |

### 7.2 明确不引入

| 系统 | 理由 |
|---|---|
| Istio（service mesh） | <50服务前为负资产；东西向用K8s NetworkPolicy+内网隔离 |
| Nacos / Dubbo / Seata | K8s原生发现 + gRPC + outbox/saga 替代；不用XA |
| Keycloak | C端=渠道OAuth（首发飞书，微信系后续），自建identity-svc做多渠道绑定更薄 |
| Strapi类CMS | 内容是管线产物非编辑产物，品类不对口 |
| 自建MinIO | 用云OSS |

---

## 八、待拍板项

| # | 决策 | 变量 | 影响 |
|---|---|---|---|
| ① | ~~团队画像确认~~ **已拍 2026-08-23**：开发完全由 Claude 承担，语言栈不受班底约束，双栈与 11 服务维持 | — | — |
| ② | ~~Postgres vs MySQL~~ **已拍 2026-08-23**：Postgres | — | — |
| ③ | ~~审核工作台第一版范围~~ → 模板验收台范围（修订二缩编） | 是否含"换三款灯"候选管理与单品意图数据回流 | genpipe↔shelf契约复杂度 |
| ④ | ~~网关：云托管 vs 自托管APISIX~~ **已拍 2026-08-23**：云托管（阿里云 MSE 云原生网关 / Higress），部署平台=阿里云 | — | — |
| ⑤-⑩ | 见《架构对齐-设计Agent×技术架构》§九：微信系渠道时机、渲染算力、三维资产来源、~~结构复核 SLA~~（修订二裁决：无该环节，作废）、~~真人接管~~（修订二裁决：v1 无转人工，作废）、风格图文字合成 | — | — |

注：对齐后总范围较本方案初版扩大（design-svc、channel 双向化、深度 3D 链路、模板库），**待拍板项①（团队画像）的权重进一步上升**——8-15 人假设若不成立，需先裁剪交付节奏而非架构。

拍板后下一步：出 contracts 仓初版 proto/OpenAPI 骨架 + backend Gradle 模块结构。
