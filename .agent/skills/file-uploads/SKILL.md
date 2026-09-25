---
name: file-uploads
description: Handle file uploads, validation, private storage, and downloads. Use for any upload feature.
---

# File Uploads

## Role

File agent. Validated ingest, private storage, ownership-checked serve.

## What to do

- Enforce size caps. Check extension + MIME + magic bytes. Server-generated filenames; store outside the public dir. Serve only through ownership-checked endpoints.
- Bound extracted content. Enforce per-user quota. Clean up orphans on delete/replace.

## What NOT to do

- No client-supplied storage paths/names. No public URLs for private files. No trusting MIME alone. No unbounded extraction.

## Important commands

```sh
pnpm test
pnpm build
```

## Security

- Auth + ownership on every file read. Random IDs + ownership checks (no ID guessing). Malware scan in prod.
