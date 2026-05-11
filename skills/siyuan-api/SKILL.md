---
name: siyuan-api
description: Local SiYuan API integration for notebook, document, block, asset, and limited SQL read operations. Restricted to local HTTP endpoints and environment-based token auth.
homepage: https://github.com/siyuan-note/siyuan
metadata: {"openclaw":{"primaryEnv":"SIYUAN_API_TOKEN","requires":{"env":["SIYUAN_API_TOKEN","SIYUAN_API_URL"]}}}
---

# SiYuan Skill

本技能用于调用本地运行的 SiYuan HTTP API，面向笔记本、文档、块、资源和受限查询场景。
它是一个 instruction-only skill：只描述允许调用的接口范围、约束和示例，不包含安装脚本、代理逻辑或可执行第三方依赖。

## Security Scope

- Only call a local SiYuan endpoint: `127.0.0.1`, `localhost`, or a user-provided local LAN address explicitly confirmed by the user.
- Never call third-party internet endpoints through SiYuan.
- Never hardcode, echo, or log `SIYUAN_API_TOKEN`.
- Treat this skill as high-trust local automation over the user's SiYuan workspace.
- Prefer note/block/document APIs over raw file APIs whenever both can solve the task.

## Explicitly Disallowed Endpoints

Do not use the following APIs through this skill:

- `/api/network/forwardProxy`
- `/api/convert/pandoc`
- Any endpoint whose main purpose is external network access, command-style conversion, or indirect execution

Reason: these endpoints materially expand the attack surface beyond ordinary note management and are intentionally out of scope for this published skill.

## Configuration

Configuration is provided via environment variables:

```bash
export SIYUAN_API_TOKEN=your_token_here
export SIYUAN_API_URL=http://127.0.0.1:6806
```

- `SIYUAN_API_TOKEN` required: from SiYuan `Settings > About`
- `SIYUAN_API_URL` optional: defaults to `http://127.0.0.1:6806`

Before using the skill:

- confirm the endpoint is local
- confirm SiYuan is already running
- stop if the task requires internet proxying or document conversion via external tools

## Allowed Operations

This skill is intended for the following operations:

- list, create, rename, move, and remove notebooks/documents
- create documents from Markdown
- insert, append, update, move, and delete blocks
- upload assets used by notes
- export Markdown content from documents
- run limited read-oriented SQL queries for note discovery or metadata inspection
- read or write workspace files only when the task is clearly about SiYuan-managed content or assets

## SQL Guardrails

SQL access is powerful and should be treated conservatively.

- Prefer `SELECT` queries
- Keep result sets small with `LIMIT`
- Do not use SQL for broad destructive modification
- Prefer higher-level APIs over SQL when both can solve the task

Example safe pattern:

```sql
SELECT id, content
FROM blocks
WHERE content LIKE '%keyword%'
LIMIT 10;
```

## File API Guardrails

File APIs are in scope only for SiYuan workspace content, not for general system administration.

- Only access paths inside the SiYuan workspace
- Prefer `/data/assets/`, note-related paths, and explicit user-requested files
- Do not use file APIs to browse unrelated directories
- Do not remove or overwrite files unless the user clearly requested it

## Safe Reference

Use the curated safe reference by default:

- [references/safe-api.md](references/safe-api.md)

Only the curated safe reference is intended to be part of the published skill bundle.
Full upstream API reference material should be maintained outside the published skill directory to avoid expanding the reviewed attack surface.

## Common Examples

### List Notebooks

```javascript
const SIYUAN_API_TOKEN = process.env.SIYUAN_API_TOKEN;
const SIYUAN_API_URL = process.env.SIYUAN_API_URL || 'http://127.0.0.1:6806';

fetch(`${SIYUAN_API_URL}/api/notebook/lsNotebooks`, {
  method: 'POST',
  headers: {
    'Authorization': 'token ' + SIYUAN_API_TOKEN,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({})
});
```

### Create Document with Markdown

```javascript
const SIYUAN_API_TOKEN = process.env.SIYUAN_API_TOKEN;
const SIYUAN_API_URL = process.env.SIYUAN_API_URL || 'http://127.0.0.1:6806';

fetch(`${SIYUAN_API_URL}/api/filetree/createDocWithMd`, {
  method: 'POST',
  headers: {
    'Authorization': 'token ' + SIYUAN_API_TOKEN,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    notebook: 'notebook-id',
    path: '/doc-path',
    markdown: '# Title\n\nContent...'
  })
});
```

### Append Block

```javascript
const SIYUAN_API_TOKEN = process.env.SIYUAN_API_TOKEN;
const SIYUAN_API_URL = process.env.SIYUAN_API_URL || 'http://127.0.0.1:6806';

fetch(`${SIYUAN_API_URL}/api/block/appendBlock`, {
  method: 'POST',
  headers: {
    'Authorization': 'token ' + SIYUAN_API_TOKEN,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    parentID: 'parent-block-id',
    dataType: 'markdown',
    data: 'appended content'
  })
});
```

### Read-Only SQL Query

```javascript
const SIYUAN_API_TOKEN = process.env.SIYUAN_API_TOKEN;
const SIYUAN_API_URL = process.env.SIYUAN_API_URL || 'http://127.0.0.1:6806';

fetch(`${SIYUAN_API_URL}/api/query/sql`, {
  method: 'POST',
  headers: {
    'Authorization': 'token ' + SIYUAN_API_TOKEN,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    stmt: 'SELECT id, content FROM blocks WHERE content LIKE \"%keyword%\" LIMIT 10'
  })
});
```

## Notes

- Repeated `createDocWithMd` on the same path does not overwrite an existing document.
- Custom block attributes must be prefixed with `custom-`.
- All endpoints use `POST` and return `{ code, msg, data }`.
- Header format is `Authorization: token <your-token>`.
- If a task would require proxying, conversion tooling, or broad filesystem manipulation, do not use this skill as currently published.
