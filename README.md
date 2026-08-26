# commanding-codex-projects

`commanding-codex-projects` 是面向 Codex 桌面端的项目总指挥 Skill。它把当前任务变成用户唯一需要沟通的指挥入口，由总指挥拆解需求、创建或继承可见的命名子任务、持续监听、纠偏、验收，并用项目根目录下的 `clog.md` 保存精简状态锚点。

它适合由多个 Codex 任务共同完成、阶段依赖明显、需要长期监听和统一验收的工程。总指挥不接管具体执行或数据初步处理。

## 启动方式

任何明确要求当前 Codex 任务担任“总指挥”“项目总指挥”“统筹者”或统一调度者的表达都可以触发，不要求固定句式。例如：

```text
你是蛋白模拟项目的总指挥
```

公开版在首次触发后只回复下面这一句，然后等待配置：

```text
是否调整子对话强度（默认 Luna/中），以及创建子对话是否需要逐个确认（默认不确认）？
```

回复“默认”即可采用：

- 子任务模型：`gpt-5.6-luna`
- 推理强度：`medium`
- 创建子任务：不逐个确认

当前总指挥任务推荐由用户在界面中选择 Sol；Skill 不会自行切换当前任务模型。

## 任务层级

```text
项目名 总指挥
├─ 项目名 数据获取部
├─ 项目名 数据分析部
└─ 项目名 执行部
   └─ 该命名子任务自行管理的内部 subagent
```

- `{项目名} 总指挥` 是当前唯一总指挥，不是子任务名称。
- 执行性子任务统一命名为 `{项目名} {职责部}`，并按真实依赖拆分，而不是套固定部门模板。
- 总指挥禁止创建、调用或管理内部 subagent，包括小型、只读、紧急或适合并行的工作。
- 大工程的命名子任务可以在自身阶段、路径和权限边界内使用内部 subagent；父子任务是唯一责任节点，负责整合与验证。
- 子任务不能自行创建新的侧栏任务。内部工作一旦变成独立、跨阶段或跨部门节点，必须上报，由总指挥决定是否提升为新的命名子任务。

## 调度与交接

总指挥先核对项目、目录、输入／产物谱系和当前阶段，再决定继承已有任务还是创建新任务；标题相似本身不足以安全继承。

新子任务收到“最小充分、自包含”的首条委托：只注入会影响输入选择、方法、权限、顺序、解释或验收的背景，同时保留核心项目基线、阶段边界、绝对路径、交付物、验收证据、停止条件和阶段回报格式。

## 监听、纠偏与停止

- 常规阶段主要使用有界等待，关注阶段交付而不是追逐过程消息。
- 重大决策、高风险写入或越界风险出现时，提高监听频率，并读取有限的可见摘要和必要输出。
- 偏航时发送包含事实、违反边界、停止动作、正确状态和下一安全动作的纠偏提示。
- 没有真正的中断能力和进程／产物证据时，只能报告“已下达停止指令”，不能声称任务已经停止。
- Skill 不声称读取隐藏思维链，只依据可见消息、工具输出、阶段摘要和产物证据判断。

## 上下文压缩恢复

发现可信的上下文压缩信号后，总指挥会补发一次较短恢复提示，只重申当前阶段、用户关键要求和必须规避的要害，不重新灌入整段项目历史。

## `clog.md`

`clog.md` 只能由总指挥维护。每当一个有意义的子任务阶段成果验收为“接受”，总指挥必须立即更新，不等待所谓“一整轮”结束。

文件始终是当前状态快照，只保留：

1. 当前对话核心需求。
2. 各命名子任务的工作内容、状态和规划。
3. 用户明确表达且会影响决策的偏好或禁区。
4. 关键文件绝对路径。

第 2 节只索引仍需总指挥管理的命名子任务。子任务最终使命及最终交付验收为“接受”后，先把仍有效的结论、约束和权威路径迁移到其他对应章节，再立即删除该任务索引；不保留“已完成”“已归档”或历史任务墓碑。纠偏岔路、失败重试、普通进度和微调不触发更新；内部 subagent 不单独写入 `clog.md`。

## 安装

在 Windows PowerShell 中，可使用 Codex 自带的 Skill 安装脚本：

```powershell
python "$env:USERPROFILE\.codex\skills\.system\skill-installer\scripts\install-skill-from-github.py" `
  --repo iC-weiyu/commanding-codex-projects `
  --path . `
  --name commanding-codex-projects `
  --dest "$env:USERPROFILE\.codex\skills" `
  --method download
```

安装器不会覆盖已存在的同名目录。安装后，在新的 Codex 任务中用“总指挥”等明确意图触发。

也可以直接克隆：

```powershell
git clone https://github.com/iC-weiyu/commanding-codex-projects.git "$env:USERPROFILE\.codex\skills\commanding-codex-projects"
```

## 运行要求

完整工作流依赖 Codex 桌面端提供的项目与任务管理能力，例如发现项目、列出／读取任务、创建任务、发送消息、设置标题和等待任务。若关键能力不可用，Skill 会报告阻塞，不会用总指挥内部 subagent 冒充可见子任务体系。

### 免确认调度（可选）

新版 Codex 通过内置的 `codex-app-tools@openai-bundled` MCP 插件提供创建任务和向任务发送消息的能力。该插件默认会为 `create_thread` 与 `send_message_to_thread` 请求逐次批准；这会中断需要长期监听和连续调度的总指挥工作流。

如果你信任总指挥在当前任务授权范围内自主创建和调度可见任务，可在用户级 `~/.codex/config.toml`（Windows 通常为 `%USERPROFILE%\.codex\config.toml`）加入：

```toml
[plugins."codex-app-tools@openai-bundled".mcp_servers.codex_app.tools.create_thread]
approval_mode = "approve"

[plugins."codex-app-tools@openai-bundled".mcp_servers.codex_app.tools.send_message_to_thread]
approval_mode = "approve"
```

这只预先批准创建任务和发送消息；`fork_thread`、`handoff_thread`、自动化管理等其他写操作仍沿用 Codex 内置审批策略。修改后请完全重启 Codex，并从新任务启动总指挥。Skill 本身不会修改用户权限配置。

## 仓库结构

```text
SKILL.md
manifest.yaml
agents/openai.yaml
references/operating-contract.md
static/core/contract.md
static/fragments/dispatch/default.md
static/fragments/dispatch/custom.md
```

- `SKILL.md`：触发、路由和不可变边界。
- `manifest.yaml`：静态／动态配置映射。
- `references/operating-contract.md`：任务拓扑、交接、监听、压缩恢复、验收和 `clog.md` 契约。
- `static/`：启动门禁、总指挥核心职责与默认／自定义调度配置。
