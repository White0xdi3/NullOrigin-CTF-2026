# NullOrigin CTF 2026

Categories:
- Steganography
- Reverse Engineering
- Boot2Root
- Cryptography
- Forensics

Challenge payloads are distributed using **GitHub Releases**, not this git repository. This repository holds only lightweight metadata and documentation — release checksums, the release manifest, and this README. The challenge files themselves (binaries, VM images, disk/memory images, capture files, etc.) are attached to the releases listed below.

## Releases

| Category | Tag |
|---|---|
| Steganography | `stego-v1` |
| Reverse Engineering | `rev-v1` |
| Boot2Root | `boot2root-v1` |
| Cryptography | `crypto-v1` |
| Forensics | `forensics-v1` |

## Downloading

Each release page lists the archive(s) for that category as attached assets.

### Single-file archives

Most categories ship as a single `.7z` archive. Download it and extract with 7-Zip:

```
7z x NullOrigin_<Chain>_Chain.7z
```

### Multipart archives (Cryptography chain)

The Cryptography chain exceeds GitHub's per-asset size limit and is split into multiple `.7z` volumes (`NullOrigin_Crypto_Chain.7z.001`, `.002`, `.003`, ...).

1. Download **all** parts of the release.
2. Put all parts in the **same directory**.
3. Extract by opening the **`.001`** file with 7-Zip — 7-Zip automatically reads the remaining volumes from the same folder.

```
7z x NullOrigin_Crypto_Chain.7z.001
```

Do not try to extract any part other than `.001`.

## Verifying your download

Every asset's SHA-256 hash is published in [`SHA256SUMS.txt`](SHA256SUMS.txt) and [`MANIFEST.json`](MANIFEST.json) in this repository.

**Windows PowerShell:**

```powershell
Get-FileHash .\NullOrigin_Stego_Chain.7z -Algorithm SHA256
```

Compare the output against the matching line in `SHA256SUMS.txt`. For multipart archives, verify every individual part (`.001`, `.002`, ...) before extracting.

**Linux / WSL:**

```
sha256sum -c SHA256SUMS.txt
```

## Notes

- Archives use 7-Zip STORE mode (no compression) for faster packaging — most payloads (VM images, disk images, binaries) don't compress meaningfully anyway.
- No solutions, writeups, flags, or credentials are included in this repository or in any release asset.
