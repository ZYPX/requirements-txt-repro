# FastAPI Requirements Parser Repro

This repository is a minimal repro for a Vercel Python dependency parsing issue involving complex PEP 508 environment markers.

The problematic marker shape is:

```txt
python_version == "3.12" and (a or b or c ...)
```

In Vercel's bundled parser, two `or` terms parse successfully, but three or more terms fail with an error like:

```txt
could not parse requirements.txt: Failed to parse requirements file. Line 22, col 112: expected ")" or "and"
```

## Repro Target

The failing dependency is in [requirements.txt](/Users/zach/testing/fastapi/requirements.txt):

```txt
greenlet==3.3.0 ; python_version == "3.12" and (platform_machine == "aarch64" or platform_machine == "ppc64le" or platform_machine == "x86_64" or platform_machine == "amd64" or platform_machine == "AMD64" or platform_machine == "win32" or platform_machine == "WIN32")
```

That line is intentionally present to trigger the parser failure.

## Why The Repo Looks Like This

Vercel prioritizes a root `pyproject.toml` over `requirements.txt` during Python manifest detection. Because the parser bug is easiest to hit through `requirements.txt`, the root `pyproject.toml` has been intentionally removed.

This repo still includes Poetry-shaped context files:

- [pyproject.poetry.toml](/Users/zach/testing/fastapi/pyproject.poetry.toml)
- [poetry.lock](/Users/zach/testing/fastapi/poetry.lock)

Those files exist because the original bug report involved generating `requirements.txt` from `poetry.lock`. They are included here to preserve that context, but they are not the active root manifest in this repo.

## Vercel Dashboard Caveat

If the Vercel project has a custom Install Command or Build Command that directly runs:

```bash
uv pip install -r requirements.txt
```

then `uv` may parse the file instead of Vercel's JS-based requirements parser, which can prevent the repro.

For the cleanest repro:

1. Remove any custom Install Command temporarily.
2. Remove any custom Build Command that installs Python dependencies directly.
3. Let Vercel detect the Python project and install dependencies using its default flow.

This repo includes [src/trim_deps.sh](/Users/zach/testing/fastapi/src/trim_deps.sh) as a no-op helper only so an existing dashboard Build Command does not fail for an unrelated missing file.

## Local App

The FastAPI app itself is intentionally minimal and lives in [main.py](/Users/zach/testing/fastapi/main.py).

To run it locally:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python main.py
```
