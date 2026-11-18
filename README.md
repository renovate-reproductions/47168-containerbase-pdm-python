# Reproduction for renovatebot/renovate#47168

A pdm project on Python 3.9 (`requires-python = "==3.9.*"`), with an outdated `six` pin, so Renovate runs `pdm update` with `binarySource=install`.

pdm 2.27.0 and later need Python 3.10 or newer, the newest release for Python 3.9 is 2.26.9.

## Current behavior

util/exec looks up `pdm` with the `github-releases` datasource, which has no Python constraints, and installs the newest pdm next to Python 3.9:

```text
install-tool python 3.9.25
install-tool pdm 2.29.7
ERROR: Ignored the following versions that require a different python version: 2.27.0 Requires-Python >=3.10; ...
ERROR: No matching distribution found for pdm==2.29.7
```

The PR gets an artifact error instead of the `pdm.lock` update.

## Expected behavior (#47168)

util/exec looks up `pdm` with the `containerbase` datasource, which passes the Python constraint of each pdm version.
The resolved Python 3.9 filters them, so it installs pdm 2.26.9 and updates `pdm.lock`.

## Run

```sh
LOG_LEVEL=debug renovate --platform=github --binary-source=install renovate-reproductions/47168-containerbase-pdm-python
```

In the debug log, look for `install-tool pdm`.
The `renovate-main/` and `renovate-pr/` PRs come from runs of the released image and of the #47168 branch.
