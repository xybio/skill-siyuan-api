# SiYuan Safe API Reference

This file is the default reference for the published `siyuan-api` skill.
It intentionally lists only the safer, in-scope API families for local note management.

## In Scope

### Notebook

- `/api/notebook/lsNotebooks`
- `/api/notebook/openNotebook`
- `/api/notebook/closeNotebook`
- `/api/notebook/createNotebook`
- `/api/notebook/removeNotebook`
- `/api/notebook/renameNotebook`

### Document and File Tree

- `/api/filetree/createDocWithMd`
- `/api/filetree/renameDoc`
- `/api/filetree/renameDocByID`
- `/api/filetree/removeDoc`
- `/api/filetree/removeDocByID`
- `/api/filetree/moveDocs`
- `/api/filetree/moveDocsByID`
- `/api/filetree/getHPathByPath`
- `/api/filetree/getHPathByID`
- `/api/filetree/getPathByID`
- `/api/filetree/getIDsByHPath`

### Block

- `/api/block/insertBlock`
- `/api/block/prependBlock`
- `/api/block/appendBlock`
- `/api/block/updateBlock`
- `/api/block/deleteBlock`
- `/api/block/moveBlock`
- `/api/block/getBlockInfo`
- `/api/block/getBlockAttrs`
- `/api/block/setBlockAttrs`

### Asset

- `/api/asset/upload`

### Export

- `/api/export/exportMdContent`

### Limited SQL

- `/api/query/sql`

Allowed use:

- read-oriented discovery
- small result sets
- metadata inspection

Avoid:

- mutation-heavy SQL
- large unbounded scans
- using SQL when a notebook/document/block API already exists

### Workspace File APIs

- `/api/file/getFile`
- `/api/file/putFile`
- `/api/file/removeFile`
- `/api/file/renameFile`
- `/api/file/readDir`

Use these only when the task is clearly about SiYuan workspace content or assets.

## Out of Scope

Do not use these APIs via this published skill:

- `/api/network/forwardProxy`
- `/api/convert/pandoc`

Also avoid any endpoint intended for:

- outbound internet proxying
- external command-style conversion
- broad system administration
- behavior outside local SiYuan workspace management

## Operating Rules

- Only target a local SiYuan server
- Never expose the API token
- Prefer structured APIs over raw file access
- Ask for confirmation before destructive file or document deletion
- Stay within the user's SiYuan workspace and note-management intent
