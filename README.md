# skill-siyuan-api

中文 | [English](#english)

`skill-siyuan-api` 是一个用于安全调用本地 SiYuan HTTP API 的 Agent Skill 仓库。

## 致谢

感谢 [SiYuan](https://github.com/siyuan-note/siyuan) 团队和贡献者。SiYuan 让本地优先的知识管理变得既实用又有可塑性，也给这个 skill 提供了很好的基础。

这个仓库发布的是 `siyuan-api` skill。它是一个 instruction-only skill：只包含给 agent 使用的说明、约束、示例和经过筛选的 API 参考，不包含安装脚本、代理逻辑或第三方可执行依赖。

## 目录结构

```text
skill-siyuan-api/
  skills/
    siyuan-api/
      SKILL.md
      references/
        safe-api.md
```

这个层级是有意保留的：

- `skill-siyuan-api` 是 GitHub 仓库名，用来说明这是一个 skill 项目。
- `skills/siyuan-api` 是实际的 skill 目录，保持和 CloudHub / Agent 识别名一致。
- `references/safe-api.md` 是随 skill 一起发布的安全 API 子集参考。

## 功能范围

这个 skill 面向本地运行的 SiYuan，支持 agent 在安全边界内进行：

- 笔记本和文档的列出、创建、重命名、移动和删除
- Markdown 文档创建
- 块的插入、追加、更新、移动和删除
- 笔记资源上传
- Markdown 内容导出
- 有限制的只读 SQL 查询，用于笔记发现和元数据检索
- 明确限定在 SiYuan 工作空间内的文件读写

## 安全边界

这个 skill 默认只允许访问本地 SiYuan 服务：

- `127.0.0.1`
- `localhost`
- 用户明确确认过的本地局域网地址

它明确排除会扩大攻击面的能力，例如：

- `/api/network/forwardProxy`
- `/api/convert/pandoc`
- 任何主要用于外部网络访问、命令式转换或间接执行的接口

更多细节见：

- `skills/siyuan-api/SKILL.md`
- `skills/siyuan-api/references/safe-api.md`

## 安装

### 从 GitHub 克隆

```bash
git clone https://github.com/xybio/skill-siyuan-api.git
cd skill-siyuan-api
```

这个仓库的实际 skill 目录是：

```text
skills/siyuan-api
```

### OpenClaw

如果你的 OpenClaw 版本支持 GitHub skill 安装，可以直接使用：

```bash
openclaw skills install github:xybio/skill-siyuan-api
openclaw skills list
```

如果安装器没有自动识别仓库里的 `skills/siyuan-api` 子目录，请使用下面的手动部署方式。

### Hermes

为了保留 `references/safe-api.md`，推荐安装完整目录：

```bash
git clone https://github.com/xybio/skill-siyuan-api.git
mkdir -p ~/.hermes/skills
cp -R skill-siyuan-api/skills/siyuan-api ~/.hermes/skills/siyuan-api
hermes skills list
```

如果只想快速安装单文件版，也可以使用 Hermes 的 URL 安装方式；注意这种方式通常不会带上 `references/` 目录：

```bash
hermes skills install https://raw.githubusercontent.com/xybio/skill-siyuan-api/main/skills/siyuan-api/SKILL.md --name siyuan-api
```

### Claude Code

Claude Code 的个人 skill 目录是 `~/.claude/skills/<skill-name>/SKILL.md`：

```bash
git clone https://github.com/xybio/skill-siyuan-api.git
mkdir -p ~/.claude/skills
cp -R skill-siyuan-api/skills/siyuan-api ~/.claude/skills/siyuan-api
```

也可以安装到某个项目内，只在该项目使用：

```bash
mkdir -p .claude/skills
cp -R /path/to/skill-siyuan-api/skills/siyuan-api .claude/skills/siyuan-api
```

### Codex

Codex 支持 Agent Skills 格式。常见的本地安装方式是把完整 skill 目录放入 Codex 的用户级 skills 目录：

```bash
git clone https://github.com/xybio/skill-siyuan-api.git
mkdir -p ~/.codex/skills
cp -R skill-siyuan-api/skills/siyuan-api ~/.codex/skills/siyuan-api
```

在使用 `~/.agents/skills` 作为通用 Agent Skills 目录的环境中，也可以使用下一节的手动部署方式。

### 手动部署到通用 Agent Skills 目录

如果你的 agent 框架扫描 `~/.agents/skills`，可以把实际 skill 目录软链接到用户级 agent skills 目录：

```bash
mkdir -p ~/.agents/skills
ln -s /path/to/skill-siyuan-api/skills/siyuan-api ~/.agents/skills/siyuan-api
```

如果你的 agent 框架不支持软链接，可以直接复制目录：

```bash
mkdir -p ~/.agents/skills
cp -R /path/to/skill-siyuan-api/skills/siyuan-api ~/.agents/skills/siyuan-api
```

## 配置

使用前需要设置环境变量：

```bash
export SIYUAN_API_TOKEN=your_token_here
export SIYUAN_API_URL=http://127.0.0.1:6806
```

`SIYUAN_API_TOKEN` 是必需的，可以在 SiYuan 的 `Settings > About` 中获取。

`SIYUAN_API_URL` 是可选的，默认使用：

```text
http://127.0.0.1:6806
```

## 版本管理

仓库名和 skill 名保持稳定，版本通过 Git tag 管理：

```bash
git tag v1.4.4
git push origin v1.4.4
```

## 兼容性

这个 skill 不绑定 OpenClaw 专有运行时。只要目标 agent 框架支持 `SKILL.md` 风格的 skill 目录，通常都可以复用其主要内容。

不同框架可能在安装目录、元数据字段和触发规则上略有差异。如果某个框架不识别 frontmatter 里的 `metadata` 字段，核心说明仍然在 `SKILL.md` 中。

## English

[中文](#skill-siyuan-api) | English

`skill-siyuan-api` is an Agent Skill repository for safe local SiYuan HTTP API automation.

## Acknowledgements

Thanks to the [SiYuan](https://github.com/siyuan-note/siyuan) team and contributors. SiYuan makes local-first knowledge management practical and extensible, and gives this skill a strong foundation to build on.

This repository publishes the `siyuan-api` skill. It is an instruction-only skill: it contains agent-facing instructions, guardrails, examples, and a curated API reference. It does not include installation scripts, proxy logic, or third-party executable dependencies.

## Structure

```text
skill-siyuan-api/
  skills/
    siyuan-api/
      SKILL.md
      references/
        safe-api.md
```

This layout is intentional:

- `skill-siyuan-api` is the GitHub repository name, making it clear that this is a skill project.
- `skills/siyuan-api` is the actual skill folder, kept stable for CloudHub and agent discovery.
- `references/safe-api.md` is the curated safe API subset shipped with the skill.

## Scope

This skill is designed for locally running SiYuan instances. Within its safety boundaries, it supports:

- listing, creating, renaming, moving, and removing notebooks and documents
- creating documents from Markdown
- inserting, appending, updating, moving, and deleting blocks
- uploading note assets
- exporting Markdown content
- limited read-oriented SQL queries for note discovery and metadata lookup
- file reads and writes clearly scoped to the SiYuan workspace

## Safety Boundary

By default, this skill only allows access to local SiYuan endpoints:

- `127.0.0.1`
- `localhost`
- a user-confirmed local LAN address

It explicitly excludes capabilities that would expand the attack surface, such as:

- `/api/network/forwardProxy`
- `/api/convert/pandoc`
- any endpoint primarily used for external network access, command-style conversion, or indirect execution

For details, see:

- `skills/siyuan-api/SKILL.md`
- `skills/siyuan-api/references/safe-api.md`

## Installation

### Clone from GitHub

```bash
git clone https://github.com/xybio/skill-siyuan-api.git
cd skill-siyuan-api
```

The actual skill folder in this repository is:

```text
skills/siyuan-api
```

### OpenClaw

If your OpenClaw version supports installing skills from GitHub, use:

```bash
openclaw skills install github:xybio/skill-siyuan-api
openclaw skills list
```

If the installer does not automatically detect the nested `skills/siyuan-api` folder, use the manual deployment method below.

### Hermes

To preserve `references/safe-api.md`, install the full directory:

```bash
git clone https://github.com/xybio/skill-siyuan-api.git
mkdir -p ~/.hermes/skills
cp -R skill-siyuan-api/skills/siyuan-api ~/.hermes/skills/siyuan-api
hermes skills list
```

Hermes can also install a single `SKILL.md` from a URL. This is convenient, but it usually will not include the `references/` directory:

```bash
hermes skills install https://raw.githubusercontent.com/xybio/skill-siyuan-api/main/skills/siyuan-api/SKILL.md --name siyuan-api
```

### Claude Code

Claude Code personal skills live at `~/.claude/skills/<skill-name>/SKILL.md`:

```bash
git clone https://github.com/xybio/skill-siyuan-api.git
mkdir -p ~/.claude/skills
cp -R skill-siyuan-api/skills/siyuan-api ~/.claude/skills/siyuan-api
```

You can also install it into a single project:

```bash
mkdir -p .claude/skills
cp -R /path/to/skill-siyuan-api/skills/siyuan-api .claude/skills/siyuan-api
```

### Codex

Codex supports the Agent Skills format. A common local installation method is to place the full skill directory under the Codex user skills directory:

```bash
git clone https://github.com/xybio/skill-siyuan-api.git
mkdir -p ~/.codex/skills
cp -R skill-siyuan-api/skills/siyuan-api ~/.codex/skills/siyuan-api
```

In environments that use `~/.agents/skills` as a shared Agent Skills directory, use the manual deployment method below.

### Manual Deployment to a Shared Agent Skills Directory

If your agent framework scans `~/.agents/skills`, link the actual skill folder into your user-level agent skills directory:

```bash
mkdir -p ~/.agents/skills
ln -s /path/to/skill-siyuan-api/skills/siyuan-api ~/.agents/skills/siyuan-api
```

If your agent framework does not support symlinks, copy the directory instead:

```bash
mkdir -p ~/.agents/skills
cp -R /path/to/skill-siyuan-api/skills/siyuan-api ~/.agents/skills/siyuan-api
```

## Configuration

Set these environment variables before using the skill:

```bash
export SIYUAN_API_TOKEN=your_token_here
export SIYUAN_API_URL=http://127.0.0.1:6806
```

`SIYUAN_API_TOKEN` is required and can be found in SiYuan under `Settings > About`.

`SIYUAN_API_URL` is optional and defaults to:

```text
http://127.0.0.1:6806
```

## Versioning

Keep the repository name and skill name stable. Use Git tags for releases:

```bash
git tag v1.4.4
git push origin v1.4.4
```

## Compatibility

This skill is not tied to an OpenClaw-specific runtime. Its core content should be reusable by agent frameworks that support `SKILL.md`-style skill folders.

Different frameworks may vary in installation paths, metadata fields, and trigger rules. If a framework does not recognize the frontmatter `metadata` field, the core instructions remain available in `SKILL.md`.
