---
name: Upload a file to a Netter workspace
description: Authenticate with a Netter API key and push a CSV/JSON/XLSX file into a workspace via the public API, then confirm it landed.
api: openapi/netter-openapi-original.json
operations: [create_api_key_api_v1_api_keys__post, upload_file_api_v1_files_upload_post, list_user_files_api_v1_files__get]
---

# Upload a file to a Netter workspace

Netter's public API lets you push data into your workspace programmatically. Today the marquee public operation is file upload, which stores the file and starts turning it into a queryable database.

## Auth

All requests use a bearer API key that starts with `ntr_`:

```
Authorization: Bearer ntr_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

Create/manage keys in **Settings -> API keys** (UI) or via `create_api_key_api_v1_api_keys__post`. A key is shown once at creation, is tied to the creating user + active company, and inherits that user's permissions. Base URL is `https://api.netter.ai` (use `https://ingest.netter.ai` only when you need Netter's static IPs for an allowlist).

## Steps

1. Obtain an API key (UI or `create_api_key_api_v1_api_keys__post`). Store it in a secrets manager — Netter only keeps a hash.
2. Upload the file with `upload_file_api_v1_files_upload_post`:
   - `POST /api/v1/files/upload`
   - `multipart/form-data` with a `file` field (CSV, JSON, or XLSX; max 50 MB)
   - optional `folder` field, e.g. `invoices/2024` (created on the fly)
   ```bash
   curl -X POST https://api.netter.ai/api/v1/files/upload \
     -H "Authorization: Bearer ntr_..." \
     -F "file=@data.csv" \
     -F "folder=invoices/2024"
   ```
3. Confirm it landed with `list_user_files_api_v1_files__get` (`GET /api/v1/files/`).

## Rules

- A revoked or missing key returns `401 Unauthorized` — rotate by creating a new key.
- Validation failures return `422` with a FastAPI `detail[]` envelope; read `detail[].loc` / `detail[].msg` to fix the request.
- No idempotency key is supported; re-uploading a file creates another file.
