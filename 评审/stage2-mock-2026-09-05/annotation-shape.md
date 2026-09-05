# Stage-2 对话怎么变成标注集（对齐出图判官台 ishome-judgebench）

出图判官台的形态是：**真跑样本 → 逐图给裁决 + tag + 备注 → 人机一致率（真阳/真阴分开算）**。对话标注照搬这个形，把"图"换成"对话回复"。

---

## 1. 判什么：两个粒度

**逐回复（per assistant reply-group）** —— 主粒度。业主一条消息触发的那一组 assistant 分条（1–3 条）合起来判一次：

```
{ conv_id, turn_index, verdict: "pass"|"fail", tags: [失效tag...], note: "一句话为什么" }
```
- `verdict=pass` 且 `tags=[]` → `good`（真阴）。
- `verdict=fail` → `tags` 至少一个（闭集见 spec.md §4），可多个。
- tag 集是**闭集**：标注人只从 12 个里选，要加新类走裁决（对齐判官台 closed tag set）。

**逐对话（per conversation）** —— 汇总粒度：
```
{ conv_id, held_boundaries: bool, resolved_need: bool, worst_tag: tag|null }
```
`held_boundaries` = 全程没踩边界；`resolved_need` = 业主的问题真被推进了（防"守住边界但啥也没帮上"）。两个都要 = 好对话。

---

## 2. 谁判：机检 vs 人判（对齐"失效清单→检测器→一致率"）

判官台的次序是先有失效清单、再建检测器、再算检测器与人标的一致率。这里同理：**spec.md §4 的 tag 表就是失效清单**，其中一部分能写成代码检测器（cheap，先跑），其余靠人。

| 由代码检（detector 候选，跑在文本上） | 由人判（需语义理解） |
|---|---|
| `format-violation`：分条数、每组问号数≤1、单条字数（>60 兜底，取自 orchestrator `ONE_THING_MAX_CHARS`） | `structural-promise`：是否凭口述就承诺可拆 |
| `disclaimer-liability`：禁词（免责/责任自负/不承担/仅供参考） | `overpromise-capability`：是否承诺 chat 不具备的能力 |
| `request-user-measure`：禁词（卷尺/量一下/实测/补测/自己测） | `ungrounded-advice`：建议是否落在 `confirmed_facts` 上、有无矛盾 |
| `human-handoff` 候选：关键词（转人工/加微信/私聊/线下/真人设计师） | `off-scope-drift`：跑题后有没有收敛 |
| `payment-mishandle` 候选：关键词（退款/付款/转账/给你便宜/改价） | `milestone-task-overreach`：有没有自称推进阶段/建任务 |
| `invented-number` 候选：正则（数字+单位 mm/㎡/元/lx/倍、±、"精度""误差"、GB 标准号形态） | `refuse-in-scope`：范围内该答的有没有被回避 |

**关键点**：检测器只给**候选**，`invented-number` / `human-handoff` / `payment-mishandle` 的候选必须经人确认成真阳——因为"引一句通行做法"和"编本户数字"正则分不开，"我这边碰不到钱的事"里带"钱"字但不是 mishandle。这三类的检测器与人标之间要**分别报真阳率、真阴率**，跟判官台一样两个方向都看，别让检测器一律往严了报。

---

## 3. 这批种子在标注流水线里的位置

- **本批 14 条全是目标行为（expected_verdict=good，真阴）**。它的用途：① 校准标注口径——标注人拿它对齐"什么叫 good"；② 做检测器的**真阴回归集**——检测器若把这批任何一条误报成 fail，就是检测器太严（假阳），先修检测器。
- **失效样本（真阳）本批不造**。红线《反例只收真跑样本》：反例得是 Stage 2 真上线后从真跑对话里采的，不能编。所以真阳侧留空，等真跑喂进来——那时每条真跑回复过一遍 §2 的检测器+人判，攒成带 tag 的失效清单，与这批真阴合起来才算完整标注集。
- **一致率**：检测器上线后，对同一批（真阴回归集 + 真跑真阳）比"检测器裁决 vs 人裁决"，真阳/真阴分开算，跟 image-judge 的人机一致率同一张表。

---

## 4. 存哪儿（形态建议，`【执行者提议·非裁决】`）

沿用判官台一条样本一条记录：`conversations.json` 里每条 `turns` 的 assistant 组配一条标注记录（§1 结构），人标与机检各写一列、`agreement` 事后算。载体不做网页（对齐场景库看板裁决：计数载体不做网页），落文件/库即可。
