---
name: laravel-uploads
description: Private-disk uploads, file validation, signed URLs, and ownership-checked serving. Use for any file upload/download work.
---

# Laravel Uploads (Blade)

## Role

File agent. Validated in, private at rest, ownership-checked out.

## What to do

- Store on a PRIVATE disk (`Storage::disk('private')` / s3-private): `$request->file('doc')->store('orders/{id}', 'private')` with server-generated hashed names — never the client filename on disk (keep original name in DB for display/download header only).
- Validate in FormRequest AND re-assert server-side: `file|max:<kb>|mimes:<list>|mimetypes:<list>`, plus dimension rules for images. Cap size per project (default ≤10 MB) and count per request. Blade form needs `enctype="multipart/form-data"`.
- Serve via ownership-checked controller (`Storage::download`/`response()->file` after Policy check) or `Storage::temporaryUrl()` (signed, short expiry) for S3. Private files NEVER under `public/` or direct symlinked URLs. Blade links point at the route, never the disk path.
- Images: re-encode/resize server-side (strip EXIF), store derivatives; AV-scan where the project provides a scanner hook. Delete files when the owning record is deleted (observer/job) — no orphans.
- Test with `Storage::fake('private')` + `UploadedFile::fake()` — invalid MIME, oversize, and non-owner download denial.

## What NOT to do

- No trusting client extension/MIME alone. No user-controlled paths (`../`, absolute, symlinks) — generated names only. No public-disk storage for non-public files. No permanent signed URLs.

## Important commands

```sh
composer test -- --filter=Upload
php artisan storage:link   # public disk only; private files bypass this
```

## Security

- Review: size cap? ext + MIME enforced? generated name? private disk? ownership check or signed URL on serve? cleanup on delete? Log upload/serve of sensitive files.
