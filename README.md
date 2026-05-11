# skill-siyuan-api

Agent skill for safe local SiYuan HTTP API automation.

This repository contains an instruction-only skill for agents that support
`SKILL.md`-style skill folders. It focuses on local SiYuan notebook, document,
block, asset, and limited read-oriented SQL operations.

## Structure

```text
skills/
  siyuan-api/
    SKILL.md
    references/
      safe-api.md
```

## Install Locally

Clone this repository, then link the skill folder into your user-level agent
skills directory:

```bash
ln -s /path/to/skill-siyuan-api/skills/siyuan-api ~/.agents/skills/siyuan-api
```

If your agent framework does not support symlinks, copy the directory instead.

## Configuration

Set these environment variables before using the skill:

```bash
export SIYUAN_API_TOKEN=your_token_here
export SIYUAN_API_URL=http://127.0.0.1:6806
```

`SIYUAN_API_TOKEN` is required. `SIYUAN_API_URL` defaults to local SiYuan at
`http://127.0.0.1:6806`.

## Safety

The skill is intentionally scoped to local SiYuan automation. It excludes
network proxying, command-style conversion, and broad filesystem operations.
See `skills/siyuan-api/SKILL.md` and
`skills/siyuan-api/references/safe-api.md` for the reviewed surface.

## Versioning

Use Git tags for releases:

```bash
git tag v1.4.4
git push origin v1.4.4
```
