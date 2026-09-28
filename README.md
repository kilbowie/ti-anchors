# ti-anchors
Weekly Merkle roots over the Truly Imagined evidence chain. Each file covers chain positions 0 to `head_pos` and lists every record hash it covers, so the root can be rebuilt independently. The scheme is RFC 6962 and is documented at https://trulyimagined.com/trust

Files are never modified or removed; a file that changed would itself be the finding.

## What is here

| Path | What it is |
|---|---|
| `anchors/<ISO week>.json` | One anchor per week (format `ti-anchor-v1`), written once by an automated publisher |
| `keys/` | The public key that signs the chain, and its fingerprint ([keys/README.md](keys/README.md)) |
| `CHANGELOG.md` | Format versions, and every change to this repository other than a new anchor |

## Checking an anchor yourself

You need `openssl`, `xxd` and a POSIX shell. Nothing here requires trusting Truly Imagined.

1. **Pull out the record hashes** the anchor covers:

   ```sh
   sed -n '/"entry_hashes": \[/,/^  \]/p' anchors/2026-W40.json | grep -oE '[0-9a-f]{64}' > leaves.txt
   ```

2. **Rebuild the root.** RFC 6962: a leaf is `sha256(0x00 || hash)`, a node is
   `sha256(0x01 || left || right)`, and a trailing odd node moves up unchanged.

   ```sh
   level=$(while read -r h; do
     { printf '\x00'; printf '%s' "$h" | xxd -r -p; } | openssl dgst -sha256 -r | cut -d' ' -f1
   done < leaves.txt)
   while [ "$(printf '%s\n' "$level" | wc -l)" -gt 1 ]; do
     level=$(printf '%s\n' "$level" | paste - - | while IFS="$(printf '\t')" read -r a b; do
       if [ -z "$b" ]; then printf '%s\n' "$a"; else
         { printf '\x01'; printf '%s' "$a$b" | xxd -r -p; } | openssl dgst -sha256 -r | cut -d' ' -f1
       fi
     done)
   done
   printf '%s\n' "$level"
   ```

   The result must equal the file's `merkle_root`.

3. **Check the anchor's own record.** Each file embeds the signed record that sealed it (`seal`). Its
   signature verifies against the key in [`keys/`](keys/README.md); every record's page at
   https://trulyimagined.com/verify lists the exact commands, including the two independent timestamps.

4. **Check a record is covered.** A record is inside an anchor if its `entry_hash` appears in that
   anchor's `entry_hashes`. Anchors are cumulative, so a record is covered by the first anchor after it
   and by every anchor since.

## Licence

The anchor data and the documentation in this repository are dedicated to the public domain under
[CC0 1.0](LICENSE).
