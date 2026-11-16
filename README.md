# Decypharr, patched

Upstream [sirrobot01/decypharr](https://github.com/sirrobot01/decypharr) plus fixes that aren't merged yet:

| Patch | Fixes |
|---|---|
| `patches/0001-sab-history-delete-missing-ok.patch` | [#474](https://github.com/sirrobot01/decypharr/issues/474): SABnzbd history delete of an already-gone item returns HTTP 500, so Arrs retry every minute |
| `patches/0002-sanitize-entry-errors.patch` | [#475](https://github.com/sirrobot01/decypharr/issues/475): raw RAR header bytes (NUL) in `fail_message` break the Arr blocklist on PostgreSQL |
| `patches/0003-skip-internal-entries-and-invalid-dirent-names.patch` | Not filed yet: the internal `__migration_status__` record shows up as an entry with an empty name, so listing `__all__` fails with `Input/output error`. Also skips any other name the kernel rejects. |

## How it works

`.github/workflows/build-patched.yml` runs daily:

1. Finds upstream's latest stable tag (no beta/rc).
2. Stops if `ghcr.io/beisem/decypharr:<tag>-patched` already exists.
3. Clones that tag and applies each patch. A patch upstream already merged is skipped. A patch that no longer applies **fails the run**, and GitHub emails you.
4. Runs `go build ./...` and the tests for the patched packages.
5. Builds the image with upstream's Dockerfile and pushes `:<tag>-patched` and `:latest`.

Run it by hand from **Actions → Build patched Decypharr → Run workflow**. Fill in `tag` to build a specific upstream tag, and tick `force` to rebuild one that already exists.

## When upstream fixes something

When the log says a patch "is already in upstream", delete that file from `patches/`. When both are gone, switch back to the upstream image (`cy01/blackhole`).
