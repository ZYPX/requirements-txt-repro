# FastAPI Requirements Parser Repro

This repository is a minimal repro for a Vercel Python dependency parsing issue involving complex PEP 508 environment markers.

The failing dependency is in [requirements.txt](/Users/zach/testing/fastapi/requirements.txt):

```txt
greenlet==3.3.0 ; python_version == "3.12" and (platform_machine == "aarch64" or platform_machine == "ppc64le" or platform_machine == "x86_64" or platform_machine == "amd64" or platform_machine == "AMD64" or platform_machine == "win32" or platform_machine == "WIN32")
```

This marker shape reproduces an error like:

```txt
could not parse requirements.txt: Failed to parse requirements file. Line 22, col 112: expected ")" or "and"
```

The repo also includes [poetry.lock](/Users/zach/testing/fastapi/poetry.lock) because the original report involved generating `requirements.txt` from a Poetry lockfile.

The app itself is intentionally minimal and lives in [main.py](/Users/zach/testing/fastapi/main.py).
