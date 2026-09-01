# AI 装修效果图产品｜第一阶段视觉提案与 Prompt 说明

> **活文档** · v1.1 · 2026-09-01（用户交入 8-21~22 与 Codex 的完整原始对话，
> 对照后发现首次整理漏了四条原则，补为 §13；其余章节未改）。
> v1.0＝2026-08-22 首次整理。

整理时间：2026-08-22

项目样例：张江金茂府 138㎡

当前方案分支：三口之家＋改善型书房

## 1. 本轮讨论沉淀

这轮讨论的核心不是单纯"生成一张好看的户型图"，而是明确第一阶段产品应该完成什么。

第一阶段的目标：

- 用基础户型图生成一张有吸引力、有获得感、愿意继续使用和分享的视觉生活提案。
- 保证基本规划合理，没有明显空间错误。
- 不做深度施工分析，不输出未经验证的精确尺寸。
- 用画风、生活场景和轻量解释，让用户觉得"这是我的家，可以这样住"。

第二阶段才进入：

- 户型深度分析。
- 采光、动线、家具尺度、材质、预算和施工可行性。
- 需要 CAD、实测尺寸或至少一个可靠标尺。

因此，第一阶段不是"设计报告"，而是"视觉吸引＋轻量专业感＋生活想象"。

## 2. 第一阶段的产品原则

第一阶段图像的用户心理路径可以概括为：

`认出自己的家 → 想象自己住进去 → 感觉是为我设计的 → 得到一个可复述的新发现 → 愿意保存或分享`

对应到图片设计，需要四个层次：

1. 真实感：户型轮廓、房间关系、厨卫、门窗、阳台不能漂移。
2. 个性化：明确家庭结构、生活偏好和本轮分支。
3. 生活感：用人物、小物、动作、场景让用户想象入住后的日常。
4. 获得感：提供少量专业解释或小贴士，让用户觉得"学到一点东西"。

一个可用的判断公式：

`分享意愿 ≈ 个性化 × 新发现 × 审美完成度 × 身份表达 ÷ 阅读负担`

这不是数学公式，而是产品取舍原则。信息越多不一定越好，关键是让用户获得"我懂了""我想存""我想给别人看"的感受。

## 3. 母版与不可变规则

当前母版图：

- `stage1-master-138sqm-no-text.png`

当前母版规格：

- `stage1-master-138sqm-spec.md`

已知信息：

- 建筑面积：138㎡
- 得房率：84%
- 估算套内面积：约116㎡
- 家庭结构：三口之家
- 当前分支：三口之家＋改善型书房
- 装修氛围：温润现代自然

固定空间关系：

| 位置 | 功能 | 规则 |
|---|---|---|
| 左上 | 客房＋储物 | 建议使用 1.2m 日间床＋通顶柜，不再默认普通三人位沙发 |
| 上中 | 独立厨房 | 保持封闭 U 型厨房，不生成中岛或开放厨房 |
| 右上 | 双人书房 | 双人长桌、两把椅子、整墙书柜 |
| 中部 | 餐厅 | 靠近厨房，表达上菜动线短 |
| 右侧 | 玄关 | 鞋柜、换鞋凳、随手置物，入户门开启范围不被遮挡 |
| 左侧 | 公卫、主卫 | 湿区位置不移动 |
| 左下 | 主卧 | 双人床、整墙衣柜、安静睡眠 |
| 中下 | 客厅 | 沙发贴近左侧实体隔墙，面向右侧电视墙 |
| 右下 | 儿童房 | 单人床、书桌、书架、低矮收纳 |
| 下中 | 生活阳台 | 洗烘、绿植、休闲椅 |

必须避免：

- 改变外轮廓、门窗、阳台和厨卫位置。
- 新增中岛、浴缸、独立衣帽间、庭院、猫区、电竞房、健身房等未确认空间。
- 把客房画成第二客厅。
- 家具遮挡门洞、柜门或主要通道。
- 输出精确房间尺寸或施工承诺。
- 用上一张生成图继续生成下一张，导致户型逐步漂移。每张图都应重新读取母版。

## 4. 客房家具规则

本轮发现：把左上"弹性客房"直接画成大沙发，容易造成误导。

沙发床的初衷是：

- 平时不空着，可以坐、可以收纳。
- 偶尔有客人时可以睡。
- 比固定双人床更弹性。

但如果图里只是普通三人位沙发，会产生几个问题：

- 用户看不出它如何睡人。
- 房间像第二客厅，功能不清楚。
- 未验证展开尺寸，可能挡门或挡柜。

因此第一阶段默认规则改为：

- 使用 1.2m 单人日间床。
- 靠墙布置，带真实床垫和靠垫。
- 床下画抽屉，旁边保留通顶柜。
- 不生成大茶几、不生成普通三人位沙发、不生成第二客厅。

只有在用户明确有双人过夜需求，并提供或确认展开尺度时，才考虑沙发床、抽拉床或壁床方案。

## 5. 信息架构：从"漂亮图"到"有获得感"

本轮对比后，第一阶段图像更适合采用四层信息：

| 层级 | 作用 | 示例 |
|---|---|---|
| 标题 | 建立记忆点和生活身份 | 温暖奶油之家 |
| 情绪总结 | 命中用户痛点 | 全屋收纳充足，娃的玩具也有家，生活才能不慌乱～ |
| 房间批注 | 让用户想象具体生活 | 菜一出锅就能上桌，少绕好几步～ |
| 专业小贴士 | 提供保存价值 | 收纳小贴士、动线小贴士、亲子时光小贴士 |

上一版曾缺少顶部总结和底部小贴士，原因是 Prompt 明确要求"不要底部总结卡片、避免密集分析"。这会降低报告感，但也会损失专业获得感。

更合理的边界是：

- 不做深度分析表。
- 不做尺寸表。
- 不做施工建议。
- 但保留轻量、生活化、可保存的小贴士。

## 6. 文案风格规则

原始示意图的措辞更像屋主手账，不像设计师说明。

应使用：

- 具体动作：做饭、上菜、收玩具、赶稿、洗漱、晒衣服、搭积木。
- 日常感受：少绕几步、终于不用排队、顺手放、清清爽爽。
- 口语表达：有人来再睡、也不用抢桌子、生活才能不慌乱～。

避免：

- 兼顾
- 赋能
- 承担
- 提升效率
- 完整活动区
- 更从容
- 核心动线
- 功能优化

这些词会让图片从"小红书屋主分享"变成"设计汇报"。

## 7. 风格模板一：温暖奶油之家

定位：

- 更适合小红书传播。
- 强调治愈、收纳焦虑、亲子生活、保存价值。
- 画面有标题、情绪总结、房间批注和底部小贴士。

视觉关键词：

- 奶油粉
- 少女手账
- 暖白纸张
- 水彩、彩铅、马克笔
- 可爱贴纸
- 生活痛点总结
- 轻量专业小贴士

已生成样图：

- `stage1-style-01-cream-journal-v4-summary-panels.png`

完整 Prompt 归档：

- `stage1-style-01-cream-journal-v4-summary-panels.prompt.md`

### 可复用 Prompt

```text
Use case: style-transfer
Asset type: 138㎡三口之家奶油粉生活手账户型分享图，信息完整版。

Input image roles:
- Image 1 is the ONLY architectural geometry source. Preserve its exact floor-plan outer silhouette, walls, room relationships, entrance, doors and door swings, windows, kitchen and bathroom wet areas, balcony, circulation and major furniture positions.
- Image 2 is the primary STYLE AND INFORMATION-ARCHITECTURE reference. Match its polished cream-pink scrapbook mood, hand-drawn watercolor/colored-pencil texture, warm paper grain, title treatment, top emotional summary bubble, room-attached handwritten notes, cute original stickers, and three hand-drawn summary panels along the bottom. Do not copy its floor plan, rooms or furniture.
- Image 3 is a supporting household/furniture reference. Preserve its three-person-family allocation and use its compact single daybed idea for the upper-left guest/storage room. Do not copy its page layout or title.

Primary goal:
Create one new independent image for the real 138㎡ plan in Image 1. The page should combine emotional attraction, recognizable personal life, and lightweight professional value. It must look like a young homeowner's beautifully drawn Xiaohongshu home journal, not a sales-office poster and not a technical report.

Composition:
Vertical portrait, ideally 4:5 but allow the tall scrapbook composition needed for readable text. Warm off-white textured paper. Top 12–14%: title on a soft blush crayon swash and a small emotional summary bubble. Middle 62–66%: full top-down 2D floor plan with room names and six concise surrounding notes. Bottom 18–20%: three side-by-side hand-drawn summary panels.
Strict orthographic top-down 2D, no perspective, no isometric, no 3D.

Exact title:
"温暖奶油之家"

Exact subtitle below title:
"一家三口的治愈小窝"

Top emotional insight bubble, exact:
"全屋收纳充足
娃的玩具也有家
生活才能不慌乱～"

Architectural and furniture invariants:
- upper-left: guest + storage room. Replace the large normal sofa from Image 1 with one compact 1.2m single daybed against the same left wall, real mattress and small back cushions, visible under-bed drawer fronts, plus the existing full-height cabinets. No ordinary three-seat sofa, no coffee table, no second living room.
- upper-center: enclosed independent U-shaped kitchen, no island, no open kitchen.
- upper-right: double-person study, long shared desk, two proper work seats, full-wall bookcase.
- center: dining table and chairs.
- right-middle: entrance foyer storage, clear door swing.
- left-side bathrooms remain exactly where Image 1 shows them.
- lower-left master bedroom.
- lower-center living room: sofa close to the LEFT solid partition, facing RIGHT TV wall, no large empty zone behind sofa.
- lower-right child room with low storage, single bed, desk and bookcase.
- bottom-center laundry + leisure balcony.
Do not move walls, doors, windows, kitchen, bathrooms or balcony. Do not add rooms.

Room labels:
"客房＋储物"
"独立厨房"
"双人书房"
"餐厅"
"玄关"
"公卫"
"主卫"
"主卧"
"客厅"
"儿童房"
"生活阳台"

Six room-attached homeowner notes:
Point to guest room/daybed:
"平时能坐、能收
有人来再睡"

Point to double study:
"两个人一起赶稿
也不用抢桌子"

Point to kitchen and dining:
"菜一出锅就能上桌
少绕好几步～"

Point to entry:
"钥匙、快递、娃的小外套
进门顺手放"

Point to child room:
"书和玩具放低一点
孩子自己拿、自己收"

Point to balcony:
"洗完直接晾
旁边还能坐着晒太阳"

Bottom summary panels:
Three equal-width but hand-drawn, slightly irregular cream panels with light peach borders. Each has a pale-yellow handwritten heading, tiny heart bullets, and one small original decorative doodle.

Left panel heading:
"收纳小贴士"
Bullets:
"玄关柜＋换鞋凳"
"客房通顶柜＋床下抽屉"
"儿童房低矮收纳"
"生活阳台吊柜收洗护"

Middle panel heading:
"动线小贴士"
Bullets:
"玄关→餐厅→客厅→阳台"
"厨房→餐厅，上菜少绕路"
"卧室动静分开"
"家务更顺手～"

Right panel heading:
"亲子时光小贴士"
Bullets:
"一起吃饭"
"一起阅读"
"一起搭积木"
"一起成长"

Footer:
"概念方案 · 尺寸以实测为准"

Lifestyle scenes:
- both parents quietly work at the shared study desk.
- one child reads or draws in the child room.
- living-room rug contains a small picture book, a few blocks and a soft toy; do not cover furniture.
- guest room has one folded blanket or small overnight bag near the daybed.
- balcony has laundry, plants and one lounge chair.

Writing voice and hierarchy:
Room labels are factual and concise. Surrounding notes are casual homeowner speech. The top bubble is an emotional pain-point resolution. Bottom panels are helpful professional takeaways.
No numbering. Avoid corporate/design-report words such as "赋能""提升效率""完整活动区""承担".

Visual style:
Thick slightly wobbly dark warm-brown pencil/marker exterior walls, medium inner walls, thin furniture lines; transparent watercolor and colored-pencil fill; low-saturation cream pink, blush, peach, apricot, warm beige, pale lavender with tiny mint and butter-yellow accents; visible paper fiber, uneven hand coloring and mild scanned-page shadow. Cute, healing, youthful, information-rich but breathable.

Avoid:
wrong floor plan; copied spatial content from style reference; normal large sofa in guest room; extra plus signs; missing room labels; garbled Chinese; English; numbered notes; corporate infographic styling; dense technical analysis; dimension table; false measured room sizes; open kitchen; island; bathtub; walk-in closet; gym; cat room; extra balcony; shifted wet areas; furniture blocking doors; 3D; perspective; CAD blueprint; photoreal render; watermark.
```

## 8. 风格模板二：彩铅建筑生活草图

定位：

- 更适合作为生活方式提案图或兴趣生活规划图。
- 强调"这个家能装下很多兴趣和日常"。
- 不靠总结卡片，而靠空间内部的生活密度、短标签和小物件表达获得感。

视觉关键词：

- 黑色粗马克笔墙线
- 彩铅排线
- 扫描稿质感
- 丰富生活物件
- 短标签
- 空间内部信息密度
- 建筑师或屋主随手画的生活草图

完整反推 Prompt 归档：

- `stage1-style-02-colored-pencil-life-sketch-reverse-prompt.md`

### 可复用 Prompt

```text
Use case: style-transfer
Asset type: 138㎡三口之家彩铅建筑生活草图。

Input image roles:
- Image 1 is the ONLY architectural geometry source. Preserve its exact outer silhouette, wall relationships, wet-area positions, entrance, door swings, windows, balcony, circulation and room count.
- Image 2 is STYLE AND INFORMATION-EXPRESSION reference only. Borrow its rough black marker wall lines, colored-pencil texture, hand-drawn furniture, scanned-paper feeling, dense lived-in details, direct room labels, equipment labels and casual arrows. Do not copy its floor plan, garden, cat area, esports room, gym, kitchen shape, bedroom shape or any specific room allocation.

Primary goal:
Create a top-down 2D hand-drawn residential plan that feels like an architect-owner's colorful lifestyle sketch. The image should show that this real 138㎡ home can hold work, parenting, storage, cooking, resting and small daily pleasures. It should be useful, vivid and shareable, but not a sales poster and not a CAD drawing.

Composition:
Near-square or vertical 4:5 canvas, warm white scanned sketch paper. The floor plan occupies about 82–88% of the canvas, with only small natural margins. No large title banner, no bottom summary cards, no legend. Keep the home itself as the main object. Strict top-down orthographic 2D, no 3D, no perspective, no isometric.

Visual style:
Rough natural black marker linework. Exterior walls are thick, dark, uneven and slightly repeated. Interior walls are medium thickness. Furniture, plants and equipment use thinner hand-drawn ink lines. Use colored pencil, watercolor pencil and light marker filling with visible hatching, uneven pressure, small gaps, paper grain and a mild scanned shadow.

Color palette:
Warm white paper base, pale wood floors, light gray tile grid, mint green wet areas, clay orange, terracotta, grass green, sky blue, butter yellow, soft pink and beige. Colors should be fresh and warm but not highly saturated. Avoid a purely cream-pink palette; this style needs richer everyday color.

Architectural invariants for the 138㎡ plan:
- upper-left room: "客房＋储物", compact 1.2m daybed against the wall, under-bed drawers and full-height storage cabinets. No ordinary three-seat sofa, no coffee table, no second living room.
- upper-center: enclosed independent U-shaped kitchen, no island and no open kitchen.
- upper-right: "双人书房", one long shared desk, two work seats and full-wall bookcase.
- center: dining area.
- right-middle: entry foyer with storage and clear door swing.
- left-side bathrooms stay in original positions.
- lower-left: master bedroom.
- lower-center: living room, sofa close to the left solid partition and facing the right TV wall.
- lower-right: child room with low storage, bed, desk and bookcase.
- bottom-center: laundry and leisure balcony.
Do not move walls, doors, windows, kitchen, bathrooms or balcony. Do not add extra rooms.

Room labels:
"客房＋储物"
"独立厨房"
"双人书房"
"餐厅"
"玄关"
"公卫"
"主卫"
"主卧"
"客厅"
"儿童房"
"生活阳台"

Object and lifestyle labels:
Use short handwritten Chinese labels next to objects, not long explanatory notes. Add loose arrows only where needed. Select 8–10 labels from below; do not use all if the image becomes crowded:
"通顶柜"
"日间床"
"大冰箱"
"直饮机"
"长餐桌"
"换鞋凳"
"共享书桌"
"低矮书架"
"玩具收纳"
"移动电视"
"洗烘区"
"晒太阳角"
"绿植角"

Lifestyle details:
- books, laptop and mugs on the double study desk.
- picture books, blocks and a soft toy on the living-room rug.
- a school bag or drawing book in the child room.
- folded blanket or overnight bag near the daybed.
- laundry, plants and a lounge chair on the balcony.
- cooking tools and produce in the kitchen.

Text style:
All text is black handwritten Chinese marker, casual and direct. Use mostly nouns and short labels, like a real owner marking objects on a sketch. Do not use numbered tips, professional analysis cards, long value sentences or corporate wording.

Optional small title:
"住进去以后，家会长成这样"

Footer:
"概念方案 · 尺寸以实测为准"

Avoid:
copying the reference image's garden, cat room, esports room, gym, terrace or room layout; changing the real 138㎡ floor plan; extra rooms; open kitchen; kitchen island; bathtub; walk-in closet; furniture blocking doors; oversized people; dense text cards; bottom summary panels; numbered tips; sales-copy tone; CAD blueprint; clean vector icons; 3D render; isometric view; photorealistic render; watermark; English text; garbled Chinese.
```

## 9. 生成流程建议

推荐固定为四步：

1. 确定图片主题
   例如：布置图、规划图、生活提案、生活方式草图。

2. 确认图片目标
   例如：氛围感、获得感、专业感、分享感、保存价值。

3. 生成详细 Prompt
   明确母版、风格参考、信息层级、文案、构图、家具、人机尺度、动线和负面约束。

4. 生成并 QA
   检查户型一致性、文字可读性、空间功能、家具尺度、生活感和传播感。

关键规则：

- 每张图基于同一个母版独立生成。
- 不以上一张生成图作为下一张的唯一依据。
- 参考图只提供风格或信息结构，不提供户型事实。
- 文字最好由系统先确定，再交给图像模型排版。

## 10. QA 清单

生成后需要检查：

- 是否还能一眼认出原户型。
- 厨房、公卫、主卫、阳台是否漂移。
- 客厅沙发是否贴近左侧隔墙并面向右侧电视墙。
- 左上客房是否为日间床＋收纳，而不是大沙发。
- 房间名称是否完整、位置是否正确。
- 中文是否乱码、漏字、重复。
- 生活批注是否像屋主口吻，而不是设计报告。
- 是否有轻量专业获得感。
- 是否避免了未经验证的尺寸和施工承诺。

## 11. 后续可扩展方向

可以继续沉淀为模板库：

| 模板 | 主要价值 | 适合场景 |
|---|---|---|
| 温暖奶油之家 | 情绪安抚、收纳痛点、保存分享 | 小红书首图、用户首次获得感 |
| 彩铅建筑生活草图 | 生活密度、兴趣身份、真实手绘 | 生活方式提案、兴趣家庭 |
| 功能分析图 | 基础动线、采光、分区说明 | 第二张说明图 |
| 深度分析图 | 尺度、采光、家具、材质 | 第二阶段深化设计 |

目前最稳定的第一阶段组合是：

1. 温暖奶油之家：负责吸引力和保存价值。
2. 功能说明图：负责基础专业感。
3. 彩铅生活草图：负责生活方式和传播性。

## 12. 产品化改造要点（2026-08-22 对齐架构后补充）

本文档的两个 Prompt 是**张江金茂府 138㎡ 的实例样本**，产品化时按以下方式改造（详见《架构对齐-设计Agent×技术架构》V1.2 §七）：

1. **模板/实例分离**：Prompt 中的户型专属内容改为槽位，由系统从 PreliminaryPlan 自动填充——包括房间表与家具不变式、房间标签、六条批注、标题与情绪总结、生活场景、面积信息。模板本体只保留：风格描述、构图规则、信息层级、文案口吻与禁词表、通用负面约束。
2. **文字合成方式**待对齐文档待拍板⑩结论：倾向图像模型只画图形层、文字由确定性排版层用手写风字体叠加（QA 通过率高、文案可独立修改），代价是手写真实感折损；建议同一模板两种方式各生成一批实测对比后定。
3. **术语对应**：母版 = plan-2d-render 确定性产物（绑定 PreliminaryPlan Revision，除 PNG 外输出房间遮罩/墙体图层）；功能说明图 ≈ Agent 方案原"图二"（规划层）；本文 §10 QA 清单 = consistency-check activity 的检查规范，其中"户型是否漂移"用母版遮罩比对量化。
4. **客房日间床规则（§4）归入提案默认规则**（plan-layout-solve 的生成默认值），不是图像规则；沙发床需用户确认过夜需求与展开尺度（`user_confirmed`）才启用。
5. **"每张图独立回读母版"已上升为架构约束**（Agent 方案 V1.2 §4.4），不再只是生成技巧。

## 13. 首次整理漏掉的四条（2026-09-01 从完整对话补齐）

用户 9-01 把 8-21~22 与 Codex 的原始对话全文交入，对照后这四条在首次整理时丢了，补上：

1. **三张图分工＝三个问题，且同一份底稿派生。**三张分别回答"看懂怎么改（功能布置）／
   相信为什么（方案分析）／想要（生活方式插画）"，产品逻辑＝"这是方案／这是依据／这是向往"。
   **第一张是事实底座，后两张都从它派生**——五个一致缺一不可：户型边界、房间功能、
   家具位置与数量、色彩分区、名称。违反的症状原话记着："分析图说是儿童房，卡通图又变成客房"。

2. **三层分离：固定户型层＋固定信息层＋可变画风层。**当时的事故：两版信息量差异巨大，
   根因是**只锁了户型、没把信息模板列为必须保留**，模型把"户型正确优先、其余信息可简化"
   当成了指令；且所谓"硬约束"对图片模型只是权重更高的文字，户型越复杂、分给文字与批注的
   注意力越少。所以信息层（哪些房间名、几条批注、逐字文案、每条对应哪个空间）必须与户型层
   一样是**逐字固定的输入**，画风层只许改线条、色彩、人物画法、纸张材质、贴纸与情绪。
   （§12.1 的模板/实例分离是它的产品化表述；本条补的是"信息层跨画风一致"这半。）

3. **心理所有权：用户要参与 1~2 个选择，方案才变成"我的方案"。**产品要在生成前后给出
   少量真选择（客房平时怎么用：来客＋收纳／父母常住／多功能房），图片模型只画已确定的家具、
   **不负责猜生活需求**。标题也属这条：写家庭决策（"我们把一间卧室留给两个人一起办公"），
   不写面积参数（"138㎡生活方式提案"）——身份表达比参数更可传播。

4. **透视图是第四阶段，先锁空间模型再生成。**当时那张客厅透视把 138㎡ 画成两百平大平层，
   结论：不能让模型拿户型图直接"想象"透视——先锁定墙体、门窗、家具坐标与大致尺寸、
   镜头（28–35mm 等效、机位 1.5m 高），图像模型只负责材质、灯光与软装。
   在本项目对应：**透视图必须等三维那条线的场景包与底渲**，不做"户型图直出透视"。
