# Vercel Python runtime bootstrap reproduction (`vc_handler` ImportError)

Minimal, sanitized reproduction prepared for a Vercel Support case. It mirrors the
build inputs of a project whose deployments fail at runtime with:

```
File "/var/task/vc__handler__python.py", line 43, in <module>
  from vercel_runtime.vc_init import vc_handler
ImportError: cannot import name 'vc_handler' from 'vercel_runtime.vc_init' (/var/task/_vendor/vercel_runtime/vc_init.py)
```

The root cause is **not known**. This repository does not claim to reproduce the error; it exists
so Vercel can deploy it and inspect the generated Python handler and the vendored `vercel_runtime`.

## Layout
- `vercel.json` – legacy `builds` config (`@vercel/python` for `api/index.py`, `@vercel/static-build` for the frontend), routes and crons. Unmodified.
- `api/index.py` – entry shim (adds `../botTronix-backend` to `sys.path`, then `from main import app`). Unmodified.
- `requirements.txt` – root Python dependency list, unmodified. No Python version pin, no lockfile.
- `.vercelignore` – unmodified.
- `botTronix-backend/main.py` – **replaced**: a minimal FastAPI app with `GET /api/health`.
- `botTronix-frontend/package.json`, `package-lock.json`, `vite.config.ts` – unmodified.
- `botTronix-frontend/index.html`, `src/main.ts` – **replaced** by trivial placeholders so `vite build` succeeds.

Not included: application source, `postcss.config.js`/Tailwind config, `public/` assets, tests, environment files, Git history.

## Notes
- Deploy with no environment variables. The crons in `vercel.json` call routes that do not exist here (404).
- Python version is not pinned anywhere; the builder default applies.
