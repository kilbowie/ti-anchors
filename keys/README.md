# Signing keys

The public keys that sign records on the Truly Imagined evidence chain. The private keys never leave
AWS KMS, in an account of their own.

## h2a-issuer-v1

| | |
|---|---|
| File | [`h2a-issuer-v1.pub.pem`](h2a-issuer-v1.pub.pem) |
| Algorithm | ECDSA on P-256 with SHA-256 (ES256) |
| Fingerprint | SHA-256 of the DER public key: `ba3f5cac86c94436a9b8430881b6e6305d2bd02e90d8208ff8f6f622355abc02` |
| In use from | Chain position 0, the genesis record `37865752-8a9f-4ba3-a2c6-756a3cca43f6` |
| Status | Current |

Check the fingerprint yourself:

```sh
openssl pkey -pubin -in h2a-issuer-v1.pub.pem -outform DER | openssl dgst -sha256
```

**Identify the key by its fingerprint, not its name.** Each record carries a key alias
(`h2a-issuer-v1`), but the alias is not part of what is signed: it tells you which key to try, not that
the key is ours. A key is ours if its fingerprint matches the one published here, on
https://trulyimagined.com/trust, and in the verifier built into that site.

## What is signed

Each record's signature is ES256 over the 32 bytes of its `entry_hash`, where `entry_hash` is the
SHA-256 of the previous record's entry hash and this record's payload hash, joined as hex text. The
signature is stored in the raw `r‖s` form (64 bytes, base64url), not DER. Step-by-step commands are on
every record's page at https://trulyimagined.com/verify.

## If a key changes

A key is replaced only for a reason, such as suspected compromise, not on a timetable. If that
happens, a notice is added here and on /trust giving the chain position from which the old key is no
longer relied on, and the new key is published here before any record it signs. Records already made
are never re-signed.
