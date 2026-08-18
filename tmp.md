# 自媒体工作流项目评估与规划

> 日期：2026-08-18
> 目标：基于 selfmedia（小贝/xiaobei）项目，开发并维护一套定时发布高质量内容的自媒体工作流。短期优先图文形式，初期跑通小红书和微信公众号。

---

## 一、selfmedia（小贝/xiaobei）项目能力分析

### 项目概览

小贝（xiaobei）是为 OPC/中小微企业量身打造的自媒体获客智能体，底层架构基于 [openclaw](https://github.com/openclaw/openclaw)。通过微信即可实现全部功能操作（同时支持飞书、企业微信）。

### 核心能力

| 能力域 | 具体功能 |
|--------|----------|
| **内容创作** | 公众号文章写作+排版、小红书图文、短视频生成（脚本→素材→TTS→渲染→合成）、海报制作 |
| **多平台发布** | 小红书、微信公众号、视频号、抖音、微博、知乎、Twitter/X、YouTube 等 |
| **数据闭环** | content-calibrator 打分预测 → 发布 → published-track 数据采集 → 复盘 → rubric 进化 |
| **智能搜索** | Smart Search 覆盖 18 类信源（小红书、抖音、微博、知乎、B站、Twitter、YouTube 等），零 key、免费 |
| **互动运营** | 小红书互动（xhs-interact）、公众号 engagement 抓取、视频号数据监控 |
| **AI 团队协作** | main agent + it-engineer + content-producer + sales-cs 四角色自主协作 |
| **竞品分析** | xhs-content-ops 图文对标分析、viral-chaser 追爆分析 |
| **信息搜集** | lead-hunting 潜客挖掘、intel-gathering 商业情报、market-research 市场调研 |

### AI 团队架构

| Crew | 职责 | 关键技能 |
|------|------|---------|
| **小贝（main agent）** | 统筹全局、对接用户、内容选题与发布策略 | 多平台发布、打分、复盘、搜索、情报搜集等 |
| **IT 工程师（it-engineer）** | 幕后支撑，被其他 crew spawn 协助 | 系统运维、配置管理、登录管理、排障 |
| **创作者（content-producer）** | 专业内容制作者 | 视频生产（脚本→素材→TTS→渲染→合成）、视觉设计 |
| **销售型客服（sales-cs）** | AI 客服 | 售前咨询、销售推进、客户画像维护 |

### 技能清单（与图文内容创作发布相关）

**公共技能：**
- `smart-search` — 智能搜索（18 类信源，免费）
- `browser-guide` — 浏览器操作最佳实践
- `siliconflow-img-gen` — AI 图片生成
- `pexels-footage` / `pixabay-footage` — 免版权素材
- `web-form-fill` — 网络表单填报

**main agent 专属技能：**
- `xhs-publish` — 小红书发布（图文/视频）
- `xhs-content-ops` — 小红书图文调研与对标
- `xhs-interact` — 小红书互动（评论/点赞/收藏）
- `wx-mp-publisher` — 微信公众号排版与推送
- `wx-mp-hunter` — 公众号素材获取
- `wx-mp-engagement` — 公众号数据抓取
- `content-calibrator` — 内容校准预测循环（打分+预测+复盘）
- `published-track` — 发布记录追踪（SQLite 多平台分表）
- `login-manager` — 平台登录态管理
- `generate-wenyan-theme` — 公众号排版主题生成
- `viral-chaser` — 爆款视频追爆分析

---

## 二、部署难度评估：中高（7/10）

| 维度 | 评估 |
|------|------|
| 硬件要求 | 4核/16G/120G，需 7×24 常开机器 |
| 软件依赖 | Node.js + Python + pnpm + camoufox（反指纹浏览器 ~557MB）+ bash |
| 网络要求 | 住宅 IP，部分功能需固定 IP（官方 VIP 提供中转服务） |
| 安装体验 | 一键脚本安装，tarball ~140MB，幂等可重跑 |
| API 配置 | 至少需要阿里云百炼 key（Lite 39元/月，Standard 139元/月） |
| 平台登录 | 需手动扫码绑定各平台账号 |
| Windows 支持 | 需要 Git Bash/WSL，开发者模式，有折腾成本 |

**系统要求：**
- 推荐 Ubuntu 22.04
- 支持 WSL2 / macOS（arm64 + x64）/ Windows 10 1803+
- 推荐用 7×24 常开的电脑部署

---

## 三、维护难度评估：中（5/10）

- **升级**：重跑 install 脚本即可，用户数据不动
- **登录态**：各平台 session 有自管探活+自动重登机制
- **AI 自修复**：it-engineer 可自主排障（token 过期、API 异常等）
- **成本**：主力模型 39-139 元/月（阿里云百炼 Token Plan）
- **风险点**：平台 API 变动（如微信公众号素材接口曾经历变更）

---

## 四、二次开发可行性：高（8/10）

- **技能系统（skill）设计清晰**：每个功能是独立的 SKILL.md + 脚本，增删功能非常模块化
- **Crew 模板机制**：可自定义 agent 角色、工作流程
- **开发规范完善**：有 CLAUDE.md、AGENTS.md、workspace-bootstrap-files.md 等规范文档
- **Python/Node/Bash 混合栈**：灵活但也增加了复杂度
- **skill wrapper 模式**：路径管理安全，规避 agent 自行拼接路径的错误风险
- **开源协议注意**：自 4.2 版本起更新了许可协议，需确认商业使用限制

---

## 五、xiaohongshu-mcp 项目分析

### 项目概览

xiaohongshu-mcp 是一个基于 MCP（Model Context Protocol）的小红书接入层，Go 语言编写，让 AI 助手直接访问小红书数据和操作。

### 功能清单

| 功能 | 说明 |
|------|------|
| 登录管理 | 二维码扫码登录，cookie 持久化 |
| 发布图文 | 支持本地图片+URL图片，标签，定时发布（schedule_at），商品绑定，原创声明 |
| 发布视频 | 本地视频上传发布 |
| 搜索内容 | 关键词搜索+多维度筛选（排序/类型/时间/范围/位置） |
| 获取推荐 | 首页推荐 Feed |
| 帖子详情 | 含互动数据+全部评论加载 |
| 互动操作 | 点赞/取消点赞、收藏/取消收藏、评论/回复评论 |
| 用户主页 | 获取用户信息+笔记列表+收藏/点赞 tab |
| 通知管理 | 获取点赞/回复通知 |

### 技术特点

- **Go 语言**：编译型，性能好，部署简单（单二进制）
- **标准 MCP 协议**：可接入 Claude Code、Cursor、VS Code 等多种 AI 工具
- **同时提供 HTTP API + MCP 工具**两种接口
- **humanize 模块**：模拟人类输入行为（打字延迟、鼠标移动）
- **Docker 部署支持**：docker-compose 一键启动
- **浏览器自动化**：基于 Chromium，自动处理页面操作
- **社区活跃**：29 contributors，有 n8n/AnythingLLM/CherryStudio 等多种集成示例

---

## 六、selfmedia 自带 xhs-publish vs xiaohongshu-mcp 对比评估

| 维度 | selfmedia 自带 xhs-publish | xiaohongshu-mcp |
|------|---------------------------|-----------------|
| **发布方式** | COS 上传 + web_api v2（API 级别，更轻量） | 浏览器自动化（模拟人类操作） |
| **反检测能力** | camoufox 反指纹浏览器 + session 持久化 + relay sign 签名服务 | humanize 模块模拟人类行为，但浏览器指纹方面较弱 |
| **登录管理** | 与 login-manager 深度集成，共享 session profile，消费者+创作者双域 SSO | 独立登录，cookie 持久化 |
| **协议接口** | CLI wrapper（给 openclaw agent 调用） | 标准 MCP 协议 + HTTP REST API |
| **功能完整度** | 发布（图文/视频）+ 互动 + 数据采集 + 复盘闭环 | 发布 + 搜索 + 互动 + 用户信息获取（无数据闭环） |
| **与 selfmedia 系统集成** | 原生无缝（shared session + published-track + content-calibrator） | 需额外桥接（MCP 协议对接） |
| **定时发布** | 通过系统 cron + 工作流自动化 | 原生支持 schedule_at 参数（1小时至14天内） |
| **部署复杂度** | 随系统一起部署，无额外成本 | 需要独立 Docker 实例 + 浏览器环境 |
| **社区活跃度** | 作为大系统的一部分 | 独立项目，社区活跃，迭代快 |
| **稳定性风险** | 依赖 relay sign 服务（团队维护） | 浏览器自动化方式相对抗平台变更更强 |
| **数据获取能力** | 需要配合 xhs-content-ops 技能 | 自带搜索、详情、用户主页等数据读取能力，接口丰富 |
| **多平台支持** | 同一系统覆盖所有平台 | 仅小红书单平台 |
| **商品绑定** | 不支持 | 支持（products 参数） |
| **可见范围控制** | 支持（--private） | 支持（公开/仅自己/仅互关） |

### 结论与建议

**不建议将 xiaohongshu-mcp 作为主力发布组件，但建议保留作为数据获取补充。**

理由：

1. **发布场景用 selfmedia 自带的 xhs-publish 更优**：
   - 已与整套系统深度集成（content-calibrator 打分 → 发布 → published-track 记录 → 复盘），形成完整闭环
   - camoufox 反指纹浏览器 + relay sign 的反检测能力更强
   - 无需额外维护一个独立服务

2. **xiaohongshu-mcp 作为数据补充有价值**：
   - 搜索功能强大且接口规范，可用于选题调研、竞品分析
   - 标准 MCP 协议，可以直接在 Claude Code 中调用做内容研究
   - 与 selfmedia 系统不冲突，各管各的
   - 适合在开发/调研阶段快速获取小红书数据

---

## 七、公众号内容创作与发布完整流程

### 总体流程框架

```
选题/素材积累 → 内容创作 → [质量打分+预测] → 排版 → 发布到草稿箱 → 定时/立即群发 → 记录入库 → [T+3d 数据复盘] → 进化 rubric
```

### Step 1：选题与素材准备

给小贝一个主题或写作思路，可附参考资料。小贝会：

1. 在 `output_articles/<article-english-title>/` 创建工作区
2. 写出 `article.md`（含 frontmatter：title, cover, author, need_open_comment 等）
3. 准备配图：用户素材 > campaign_assets > AI 生成 > 免版权图库

### Step 2：质量校准（可选）

启用 content-calibrator 后：

1. **盲打分**：隔离 sub-agent 只看稿件 + rubric，输出 7 维评分（情感共鸣/钩子强度/社会议题/金句密度/叙事性/受众广度/实用价值，各 0-5 分）
2. **阈值门**：综合分达标 → 放行；不达标 → 自动改稿重打（最多 2 轮），仍不过 → 暂停问用户
3. **盲预测**：预测该内容在平台的表现

### Step 3：排版与发布

```bash
wx-mp-publisher article.md pie --account default
```

- 自动根据内容选择排版主题（7 种内置 + 自定义）
- 通过 relay 服务渲染 HTML 推送到**公众号草稿箱**
- 在公众号后台预览确认后定时/立即群发

**排版主题决策树（未指定时自动选择）：**

| 主题 ID | 风格 | 适用场景 |
|---------|------|---------|
| `pie` | 现代锐利（仿少数派） | 深度长文、评测、观点（默认） |
| `lapis` | 极简冷蓝 | 技术教程、代码分析 |
| `purple` | 简约紫调 | 品牌、商务、精品内容 |
| `orangeheart` | 暖橙优雅 | 情感、故事、节日 |
| `maize` | 淡雅玉米黄 | 健康生活、美食、户外 |
| `rainbow` | 多彩活泼 | 亲子、宠物、娱乐 |
| `phycat` | 薄荷清爽 | 科普、知识型内容 |
| `default` | 简洁经典 | 资讯、通知、简讯 |

### Step 4：记录入库

`published-track/scripts/record.sh` 写入 `pub_wx_mp` 表（reads, shares, favorites, likes, comments）

### Step 5：数据复盘（T+3d 后）

`wx-mp-engagement` 通过 camoufox 登录创作者中心后台，抓取阅读/点赞/评论/分享/收藏数据，与盲预测对比，推动 rubric 进化。

---

## 八、小红书图文发布完整流程

### Step 1：选题与素材准备

同公众号，但需注意小红书的硬限制：
- 标题 ≤ 20 字
- 正文 ≤ 1000 字
- 图片 ≤ 18 张，建议 3:4 竖版
- hashtag ≤ 10 个

### Step 2：质量校准（同上）

### Step 3：发布

```bash
xhs-publish --mode image --title "笔记标题" --body "正文内容 #话题1 #话题2" --images img1.jpg img2.jpg img3.jpg
```

底层流程：
1. 探活登录态：`xhs-publish check`
2. COS 上传图片：取许可证 → PUT 到 COS
3. 创建笔记：调用 `edith.xiaohongshu.com/web_api/sns/v2/note`
4. 返回笔记 URL

**登录态管理（两步登录）：**
- 消费者域 `web_session`（login-manager 管，共享 xhs-browse session）
- 创作者域 `galaxy_creator_session_id`（本技能 SSO 自管）
- 两套 cookie 合并发布，同一指纹 UA 避免风控

### Step 4：记录入库

写入 `pub_xhs` 表（views, likes, favorites, comments, shares）

### Step 5：数据复盘

`xhs-content-ops` 或定时任务抓取互动数据，与盲预测对比。

---

## 九、如何介入流程——规划把控选题与产出质量

### 1. 选题规划层面

| 介入点 | 具体操作 |
|--------|----------|
| **业务知识沉淀** | 维护 `business_knowledge.md`——核心业务信息、产品背景、红线，所有内容从这里出发 |
| **素材积累** | 随时发资料给小贝（文章链接、灵感、图片），存入 `campaign_assets/` 并维护 index.md |
| **竞品对标（小红书）** | 用 `xhs-content-ops` 分析对标账号的图文策略（提供 xhslink 链接或关键词） |
| **竞品对标（公众号）** | 用 `generate-wenyan-theme` 参考竞品公众号创建排版模板 |
| **起号方法论** | 系统内置知识库：`knowledge/channels-account-launch-expert/xhs.md` 和 `wx_mp.md` |
| **运营讨论** | 直接和小贝讨论运营思路、账号定位、内容方向 |

### 2. 产出质量把控

| 控制手段 | 说明 |
|----------|------|
| **人工审核（默认）** | 每篇文章写完后，小贝主动问你是否打分、是否发布，你有完全决定权 |
| **content-calibrator 自动门** | 启用后，7 维评分未过阈值的文章自动被拦截修改。你设定阈值高低 |
| **盲预测+复盘闭环** | 系统记住预测和实际表现的偏差，逐步进化评分公式（rubric） |
| **定时任务模式** | 成熟后可设 cron 自动生产+发布，主 agent inline 打分（不再每篇问你） |
| **阈值控制命令** | `cal-toggle.sh --set-threshold N`（每维须 > N 分才放行，0=不拦截） |

### 3. 建议的渐进式介入策略

#### 第一阶段：手动把控（前 1-2 周）

```
你给选题 → 小贝出稿 → 你审阅修改 → 你说"发布" → 小贝执行
```

- 不启用 content-calibrator
- 每篇都亲自过目，建立质量标准
- 通过修改意见让小贝理解你的偏好（存入 MEMORY.md）
- 积累素材库和排版模板

#### 第二阶段：半自动（第 3-4 周）

```
你给选题 → 小贝出稿+打分 → 过阈值自动通知你 → 你快速确认 → 发布
```

- 启用 content-calibrator，初始阈值设低（如 2.5/5）
- 积累 5+ 篇后启动复盘闭环
- 根据复盘结果调整阈值和 rubric
- 开始设定发布频率规律

#### 第三阶段：定时自动化（稳定后）

```
cron 触发 → 小贝自主选题+出稿+打分+发布 → 你收到每日复盘报告
```

- 用 heartbeat/cron 设置定时任务
- 阈值调高（如 3.5/5），低于标准的自动拦截
- 你只介入异常情况和策略调整
- 定期做综合复盘（3b），推动 rubric 进化

### 4. 关键控制点速查

| 你想要的控制 | 对应操作 |
|-------------|----------|
| 控制选题方向 | 维护 `business_knowledge.md` + `campaign_assets/` |
| 控制内容质量底线 | `cal-toggle.sh --set-threshold N` |
| 控制发布频率 | cron 配置（如每天发一篇小红书，每周两篇公众号） |
| 控制平台内容风格 | `calibration/<platform>/audience.md` + `benchmark.md` |
| 控制排版风格 | `generate-wenyan-theme` 定制 CSS |
| 查看所有历史表现 | `published-track query.sh` |
| 暂停某平台发布 | `cal-toggle.sh --platform xhs --disable` |
| 启用/停用打分 | `cal-toggle.sh --platform <p> --enable/--disable` |

---

## 十、小红书 vs 公众号关键差异

| 维度 | 小红书 | 微信公众号 |
|------|--------|-----------|
| 标题 | ≤20 字（硬限制） | 无硬限制（建议 ≤30 字） |
| 正文 | ≤1000 字 | 无限制 |
| 图片 | ≤18 张，建议 3:4 竖版 | 无数量限制 |
| 发布方式 | 直接发布到平台 | 先推到草稿箱，可定时/立即群发 |
| 数据抓取 | camoufox 爬消费者页面 | 需创作者中心后台登录 |
| 适合内容 | 短平快、视觉驱动、干货清单 | 深度长文、教程、观点输出 |
| 内容形态 | 图文笔记 / 视频笔记 | 长文 article / 小绿书（图片轮播） |
| 发布技能 | `xhs-publish` | `wx-mp-publisher` |
| 数据抓取技能 | `xhs-content-ops` | `wx-mp-engagement` |

---

## 十一、部署实施建议路线

### 第一阶段：部署 selfmedia

1. 准备常开机器（推荐 Linux/WSL2，4核16G）
2. 运行一键安装脚本
3. 配置阿里云百炼 API key
4. 微信扫码绑定

### 第二阶段：跑通小红书图文流程

1. 配置 xhs-publish 的 login-manager（扫码登录小红书）
2. 测试手动流程：选题 → 出稿 → 审阅 → 发布
3. 启用 content-calibrator（初始化 xhs 平台校准）
4. 测试完整闭环：打分 → 发布 → 记录 → 复盘
5. 设置 cron 定时任务

### 第三阶段：跑通微信公众号流程

1. 配置 wx-mp-publisher（appId/appSecret）
2. 配置 relay（OFB_KEY — VIP Club 凭证）
3. 选择或定制排版主题
4. 测试流程：出稿 → 排版 → 推送草稿箱 → 群发
5. 配置 wx-mp-engagement 数据抓取

### 可选：部署 xiaohongshu-mcp 作为调研工具

1. Docker 一键启动（`docker-compose up`）
2. 配置到 Claude Code 的 MCP server
3. 用于竞品搜索、热点趋势分析、辅助选题

---

## 十二、成本估算

| 项目 | 费用 | 说明 |
|------|------|------|
| 阿里云百炼 Token Plan | 39-139 元/月 | 主力模型（DeepSeek-V4-Flash 等） |
| VIP Club（可选） | 168 元/年 | 含中转服务、知识库、技术支持 |
| 服务器/电脑 | 视情况 | 需 7×24 常开，4核16G |
| 视频生成（可选） | 含在百炼套餐 | happyhorse-1.1 免费额度 |

---

## 十三、风险与注意事项

1. **平台风控**：小红书和公众号都有反自动化检测，camoufox 反指纹浏览器可缓解但非万能
2. **固定 IP 需求**：部分发布功能需固定 IP（VIP Club 提供中转服务）
3. **登录态维护**：各平台 session 有有效期，需定期扫码维护（系统有探活机制）
4. **API 变更风险**：微信公众号素材接口已历过一次变更，需跟进项目更新
5. **内容合规**：AI 生成内容需声明；禁止引流、导流、放二维码/联系方式
6. **许可协议**：selfmedia 自 4.2 版本起更新了许可协议，商业使用需确认
