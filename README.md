# Agent Engineering Lab

> 双人 AI Agent 工程学习仓库：以 [deepagents-in-action](https://github.com/datawhalechina/deepagents-in-action) 为学习主线，通过独立实验、技术讨论、Code Review 和工程实践，逐步积累 Agent 系统开发能力。

## 1. 项目目标

本仓库主要用于：

1. **动手实验**：运行、修改 AgentSeek 模板，理解 Agent Loop、工具调用、上下文管理、任务规划、MCP 等机制。
2. **知识沉淀**：整理原理、源码调用链、问题记录和双人讨论结论。
3. **双人协作**：用 Git 分支、Pull Request（PR）和 Code Review 形成可回顾的学习记录。
4. **独立实践**：在理解课程机制后，逐步开发自己的轻量级 Agent Runtime，而不是直接复制课程框架。

**原则：能运行 ≠ 已理解。每次实验尽量做到能解释、能修改、能测试。**

## 2. 仓库目录

```text
agent-lab/
├── docs/                       # 按章节整理知识笔记与讨论结论
│   ├── ch02-agent-loop.md      # 示例：第 2 章笔记（学习后创建）
│   └── discussions/            # 可选：集中记录专题讨论
├── labs/                       # 基于课程模板的独立实验代码
│   ├── person-a/               # 成员 A 的实验
│   │   ├── default/            # 示例：默认模板（实际名称以生成结果为准）
│   │   └── research/           # 示例：研究类模板
│   └── person-b/               # 成员 B 的实验，组织方式相同
├── shared/
│   └── agent_runtime/          # 自研 Agent 执行框架，和课程模板分开
├── tests/                      # 公共测试、跨模块集成测试
├── .github/
│   └── PULL_REQUEST_TEMPLATE.md # 可选：PR 填写模板
├── .gitignore                  # 忽略环境文件、密钥、缓存等
├── .python-version             # Python 版本约定（若已用 uv 创建）
├── pyproject.toml              # 仓库根项目配置（若已初始化）
├── uv.lock                     # 根项目依赖锁文件（若已生成）
└── README.md                   # 本文档
```

**各目录的职责：**

| 目录                               | 放什么                                      | 不建议放什么               |
| ---------------------------------- | ------------------------------------------- | -------------------------- |
| `labs/person-a/`、`labs/person-b/` | 自己的模板、实验源码、章节测试              | API Key、虚拟环境          |
| `docs/`                            | 原理笔记、流程图、问题与讨论结论            | 大量重复的教程原文         |
| `shared/agent_runtime/`            | 独立实现的 Agent Loop、工具系统、状态管理等 | 直接复制且未理解的框架源码 |
| `tests/`                           | 公共单元测试、故障注入和集成测试            | 与测试无关的临时文件       |
| `.github/`                         | PR 模板、后续 CI 配置                       | 项目业务代码               |

> `person-a` / `person-b` 可以替换为各自的 GitHub 用户名。空目录中的 `.gitkeep` 仅用作占位，后续有真实文件时可删除。

## 3. 开发环境

- **操作系统**：Windows + WSL2 Ubuntu。
- **编辑器**：Windows 版 VS Code，通过 WSL 扩展连接 Ubuntu。
- **仓库位置**：建议放在 Ubuntu 文件系统 `~/projects/agent-lab`。
- **Python**：双方约定同一版本（当前建议 Python 3.12）。
- **依赖管理**：uv；各 AgentSeek 模板项目可拥有独立的 `pyproject.toml`、`uv.lock`、`.venv/`。
- **GitHub**：远程仓库 `https://github.com/wuhaoyang-w/agent-lab`。

### 新成员首次 Clone

在 **Ubuntu 终端**中执行：

```bash
mkdir -p ~/projects
cd ~/projects
git clone https://github.com/wuhaoyang-w/agent-lab.git
cd agent-lab
code .
```

- `mkdir`：创建目录；`-p`：父目录不存在时一并创建，目录已存在也不会因此报错。
- `cd`：切换目录；`~`：当前 Linux 用户的主目录。
- `git clone`：复制 GitHub 远程仓库到本地。
- `code .`：在 VS Code 中打开当前目录；`.` 表示当前目录。

### 实验环境约定

AgentSeek 是命令行脚手架工具，可用 `uv tool install agentseek` 安装到 **Ubuntu 用户的独立工具环境**；模板生成的**代码**存放在 `labs/`。

首次进入某个实验模板时，先阅读该模板的 `README` 和环境说明；如果模板有自己的 `pyproject.toml`，就在**该模板目录**中按要求运行 `uv sync`、`uv run ...`，不要默认所有实验共用根目录 `.venv`。同一模板可以服务多个章节，不必每章重复生成。

**注意**：模板若自带 `.git/`，提交前应处理嵌套 Git 仓库的问题；遵守原项目许可证与署名要求。

## 4. Git 协作约定

### 4.1 分支命名

- `main`：团队正式分支，日常学习代码不直接推送到这里。
- `feat/a-ch02-agent-loop`：成员 A 完成第 2 章实验。
- `feat/b-ch02-agent-loop`：成员 B 完成第 2 章实验。
- `fix/a-tool-timeout`：修复某个功能问题。
- `docs/b-context-notes`：仅修改知识文档。

**一个分支对应一个可独立验收的任务**，不需要每天新建分支。完成 PR 并合并后，通常删除该功能分支。

### 4.2 一次完整的协作流程

#### ① 确认任务

较大的学习任务可以先创建 GitHub Issue，写明目标、验收标准和负责人。示例：`第 2 章：实现工具调用并添加最大迭代次数限制`。

#### ② 从最新 main 创建分支

```bash
cd ~/projects/agent-lab
git status
git switch main
git pull --ff-only origin main
git switch -c feat/a-ch02-agent-loop
```

开始之前，先用 `git status` 检查是否有尚未提交的修改。如有修改，先妥善提交或保存，再切换分支。

#### ③ 独立实验 + 编写文档

- 在 `labs/person-a/` 或 `labs/person-b/` 完成实验和必要测试。
- 在 `docs/` 记录原理、关键设计、遇到的问题、讨论结论。
- 对核心机制尽量做一次**不照抄教程的改造或测试**。

#### ④ 提交本地代码并推送

以下路径仅为示例，替换为本次实际修改的文件：

```bash
git status
git add labs/person-a/default/ docs/ch02-agent-loop.md
git diff --cached
git commit -m "feat: complete chapter 02 experiment"
git push -u origin feat/a-ch02-agent-loop
```

`git diff --cached` 可以在提交前检查已暂存的内容，避免误提交密钥和无关文件。

#### ⑤ 在 GitHub 创建 PR

进入仓库的 **Pull requests → New pull request**（也可能直接看到 **Compare & pull request**）。

- **base** 选择 `main`。
- **compare** 选择本次的 `feat/...` 分支。
- 标题写清楚完成的学习任务；正文说明实现内容、测试结果和需要讨论的问题。
- 选择另一位同学为 **Reviewer**。

#### ⑥ 交叉 Code Review

审查时至少关注：

1. **正确性**：功能和测试是否符合预期？
2. **原理**：作者能否解释调用链、状态变化和技术取舍？
3. **边界情况**：无限循环、工具异常、超时、重复执行时会怎样？
4. **可维护性**：命名、模块划分、类型注解、错误处理是否清晰？

收到意见后，在**同一个功能分支**继续修改、Commit、Push，PR 会自动更新。双方确认并通过测试后再合并。

#### ⑦ 合并、删除分支、同步主分支

初学阶段建议使用 GitHub 上的 **Merge pull request**；合并后点击 **Delete branch** 删除远程功能分支。

然后在 Ubuntu 本地执行：

```bash
git switch main
git pull --ff-only origin main
git branch -d feat/a-ch02-agent-loop
```

`git branch -d` 是安全删除本地分支：若 Git 判断尚未合并，通常会拒绝删除。不要为了省事直接使用强制删除 `-D`。如果使用了 Squash Merge，需先确认 PR 已合并、代码与提交都已保留，再处理本地分支。

下一个任务重新从最新 `main` 创建新分支。

## 5. 常用 Git 命令与参数

| 命令                             | 含义                                        |
| -------------------------------- | ------------------------------------------- |
| `git status`                     | 查看当前分支、未暂存与未提交的修改          |
| `git switch main`                | 切换到 `main`                               |
| `git pull --ff-only origin main` | 从远程 `origin` 更新 `main`，只允许快进合并 |
| `git switch -c feat/xxx`         | 创建并切换到新分支；`-c` = create           |
| `git add 文件路径`               | 将指定文件或目录加入暂存区                  |
| `git diff --cached`              | 查看已暂存的改动                            |
| `git commit -m "说明"`           | 保存本地提交；`-m` 指定提交说明             |
| `git push -u origin feat/xxx`    | 首次推送分支；`-u` 设置上游追踪关系         |
| `git push`                       | 推送当前已配置上游分支的提交                |
| `git branch -d feat/xxx`         | 安全删除本地分支；`-d` = delete             |
| `git log --oneline`              | 用一行显示一条提交历史                      |

> 不要在有未提交改动时盲目切换分支或拉取更新。如果发生合并冲突，先确认双方修改意图，再手动解决并测试；不要无条件选“全部接受当前版本”。

## 6. 章节验收与知识沉淀

每完成一个学习任务，尽量满足以下检查项：

- [ ] 实验代码能运行，说明所需依赖和启动步骤。
- [ ] 能独立解释本章核心概念与调用流程。
- [ ] 至少完成一个主动修改、异常测试或独立复现挑战。
- [ ] 在 `docs/` 留下结论、疑问及其解决过程。
- [ ] 创建 PR，由另一位同学 Review 并完成必要修改。
- [ ] 合并到 `main` 后双方能拉取、运行。

**技术讨论建议**：一人用 10～15 分钟讲解本章，另一人针对边界条件和设计取舍提问；讨论结束后只记录最终有价值的结论，不强制制作 PPT。

## 7. Git Tag（可选）

Git Tag 用于标记**整个仓库某次提交的状态**，不是复制某个文件夹。无需每章打 Tag，只在重要里程碑使用。

例如，确认当前提交是需要标记的版本后：

```bash
git tag -a learn-a-ch04 -m "Chapter 04 completed"
git push origin learn-a-ch04
```

- `-a`：创建附注标签（annotated tag）。
- `-m`：给标签增加说明。
- `git push origin 标签名`：将标签上传到 GitHub。

## 8. 安全与提交规范

请勿提交：

- `.env`、API Key、Token、账号密码等敏感信息。
- `.venv/`、缓存、构建产物、大体积临时文件。
- 未经确认许可的第三方源码或数据。

在根目录 `.gitignore` 中建议至少包含：

```gitignore
.venv/
__pycache__/
.pytest_cache/
.env
.env.*
!.env.example
*.pyc
```

`.env.example` 只放配置项的**占位示例**，不放真实凭据。注意：`.gitignore` 对已经被 Git 跟踪的文件不会自动生效。

## 9. 第一次双人协作练习

1. 两个人分别 Clone 同一个仓库，并确认 `git status` 正常。
2. A 创建 `feat/a-first-lab`，提交自己的一个 Python 小实验。
3. B 创建 `feat/b-first-lab`，提交自己的一个 Python 小实验。
4. 双方分别发起 PR，交叉 Review，合并到 `main`。
5. 双方执行 `git switch main`、`git pull --ff-only origin main`，确认能看到彼此提交的文件。

成功完成一次后，再用同样的流程学习 Deep Agents 正式章节。

---

**仓库约定一句话：`labs` 做实验，`docs` 沉淀知识，`shared` 独立造框架；用 PR 互相审查，用 Git 历史记录成长。**