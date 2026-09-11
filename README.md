# Model Hardware Standard (MHS)

> 本仓是 Topsun 对 **Anthropic Model Hardware Standard** 的说明与跟踪笔记，**不是** Anthropic 官方规范仓库。规格原文与预览申请以 Anthropic 为准。

## 一句话

**MHS = AI Agent 操作物理硬件的统一接口（研究预览）**——把显微镜、移液工作站、机械臂等「可编程设备」用同一套驱动原语发现、读写、编排；相对数字侧的 [MCP](https://modelcontextprotocol.io/)（Model Context Protocol），官方定位接近「**物理世界的 MCP**」。

官方公告（一手来源）：

- [Previewing the Model Hardware Standard](https://www.anthropic.com/news/model-hardware-standard-research-preview) — Anthropic，2026-08-27  
- 起步合作方：[HHMI Janelia Research Campus](https://www.janelia.org/)  
- 预览申请：见上述公告内 waitlist / apply 链接（本仓不转发过期表单）

## 官方说它解决什么

摘自 Anthropic 公告（意译 + 边界标注）：

1. **集成税**：实验室 / 产线把多台设备串起来通常要 **数周到数月** 写定制 glue；设备彼此不互通。MHS 目标是把这类集成压到 **数小时到数分钟**。  
2. **统一驱动**：标准驱动在 OS 与设备之间翻译；原语是简单的 **read / write**（如 get/set temperature），并让设备以 **统一格式可发现**，跨网络通信无需每台一台翻译器。  
3. **设备语义 / 安全边界**：驱动带 **tags**，可用自然语言写入「说明书里才有」的物理特性（臂重、行程、激光功率上限等），再生成 **reference file**（可测什么、可调什么、强制安全限）。  
4. **Agent 控制面三条路径**：**MCP**、**CLI**、**代码/API**——可多设备编排；长任务可把驱动命令打成确定性脚本，不必每步都在线推理。  
5. **模型无关**：任意 agent harness 可通过 MCP 等标准协议接入；设备需具备 **可编程接口**。无编程接口的硬件不在当前范围内（官方在与厂商补驱动）。  
6. **尚未开源**：当前是 **research preview**（科研实验室与先进制造首批伙伴）；官方明确要先做 **安全评测与物理世界最佳实践**，再开源。LLM **缺乏物理直觉**，仍需专家监督（Genentech 气泡/泡沫案例等）。

## 官方给出的早期用途（伙伴案例）

| 伙伴 | 用途摘要 |
|------|----------|
| **Genentech** | BCA 蛋白测定：移液工作站 + 机械臂 + 读板器，Claude 经 MHS 编排与闭环调流速 |
| **UW Baker / Pinglay** | 远程仪表盘、qPCR 曲线监控停机、LeRobot 臂与移液机无无碰撞交接 |
| **CMU** | 串行稀释 / 剂量响应：异质控制面（目录监视 / COM / 纯 GUI）统一成 states+procedures |
| **HHMI Janelia** | 多厂商显微镜机架共享内存字典；agentic 成像与光束对准 |
| **QuEra** | 量子机激光锁定恢复与 PID 调参（脚本 + 隔夜 agent 循环） |
| **Tetsuwan ResearchOS** | qPCR / 公民科学流水线编排与编译器启发式优化 |

厂商侧早期意向（公告列举，非本仓验证）：AWS Strands Robots、Automata LINQ、Danaher、Doosan、MBF ScanImage、QIAGEN、Tecan Fluent、Universal Robots、Hugging Face **LeRobot**、Raspberry Pi Camera MHS Driver 等。

## 和 MCP 的关系（官方口径）

- **MCP**：模型 ↔ 数字工具 / 数据源的标准。  
- **MHS**：模型 / agent ↔ **物理设备驱动层** 的标准。  
- 接入方式上，MHS **可通过 MCP 暴露给 agent**（另有 CLI 与代码 API）。两者互补，不是互相替代。

## Topsun / DimOS 视角（本仓判断，非官方）

我们现有栈（`topsun-bot/topsun_dimos`）已有：

- Agent / **MCP** 技能与蓝图（含 HoloAgent HTTP shim 等）  
- Unitree **Go2 / G1** 运控、仿真（MuJoCo / Genesis / Isaac / DimSim）  
- 多传输（LCM / Zenoh 等）——合入上游时需单独审 **显式 LCM pins vs 默认 Zenoh** 的硬件风险  

MHS **可能有用的方向**（待预览资质与开源后验证）：

1. 把 Go2/G1、外设（相机、臂、工站）收成 **可发现的 MHS 设备清单 + 安全 tags**，让 Claude / 自研 agent 走统一 read/write，而不是每台一份 glue。  
2. 与现有 **MCP skill** 对齐：DimOS 继续做机器人语义技能；MHS 做设备发现与安全限。  
3. 仿真侧：MHS 偏真机可编程接口；仿真资产仍走 EmbodiedGen 等产线，不混为一谈。

**现阶段不要做的**：

- 把未开源的 MHS 运行时 vendor 进 DimOS  
- 在未加入 research preview 前宣称「已对接 MHS」  
- 用 MHS 口号绕过真机安全联锁（#115–#117 fail-closed 门仍是我们自己的硬需求）

## 状态跟踪

| 项 | 状态 |
|----|------|
| 官方开源 | 未开源；research preview |
| 本仓角色 | 说明 / 链接 / Topsun 适用性笔记 |
| 预览申请 | 见 Anthropic 公告 |
| 与 DimOS 代码集成 | **未开始**（等公开规范或获预览权限后另开 PR） |

## 参考链接

1. Anthropic — [Previewing the Model Hardware Standard](https://www.anthropic.com/news/model-hardware-standard-research-preview)（**一手**）  
2. The Register 报道（二手）：[Anthropic proposes plumbing spec…](https://www.theregister.com/ai-and-ml/2026/08/28/anthropic-proposes-plumbing-spec-to-link-ai-agents-to-lab-kit-and-robots/5293135)  
3. heise 报道（二手）：[Anthropic introduces communication standard for hardware](https://www.heise.de/en/news/Anthropic-introduces-communication-standard-for-hardware-11435794.html)  

更新本 README 时：优先改写官方公告变更；二手媒体仅作索引。

## License

本仓文档默认 CC BY 4.0 意图（说明类笔记）。Anthropic / 伙伴商标与原文版权归各自所有；引用请回链一手来源。
