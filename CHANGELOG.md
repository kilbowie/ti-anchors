# Changelog

Changes to the formats and the housekeeping of this repository. The anchor files themselves are never
changed or removed; this log records everything else.

## 2026-09-28

- Added `LICENSE` (CC0 1.0): the anchor data is dedicated to the public domain.
- Added `keys/` with the public key `h2a-issuer-v1` and its fingerprint.
- README: how to check an anchor yourself.
- `main` is protected against force-pushes and deletion.

## Formats in use

| Format | Since | What it is |
|---|---|---|
| `ti-anchor-v1` | 2026-W38 | One weekly anchor file, `anchors/<ISO week>.json` |
| `rfc6962-sha256` | 2026-W38 | The Merkle tree scheme each anchor's root is built with |
| `ti-seal-v1` | Chain position 0 | The record scheme of the seal embedded in each anchor |
