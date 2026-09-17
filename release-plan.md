# NullOrigin CTF — Release Inspection Report

**Status: INSPECTION ONLY. Nothing has been uploaded, moved, renamed, compressed, or modified.**
Report generated: 2026-09-18.

---

## 0. Preflight checks

| Check | Result |
|---|---|
| Working directory | `C:\Users\vatsa\Downloads\Chall\CTF` |
| Folders present | Confirmed: `NullOrigin_Stego_Chain`, `NullOrigin_Rev_Chain`, `NullOrigin_Boot2Root_Chain`, `NullOrigin_Crypto_Chain`, `NullOrigin_Forensics_Chain` — all present |
| `gh --version` | `gh version 2.101.0 (2026-09-15)` |
| `gh auth status` | Logged in to github.com as **White0xdi3** (keyring), active account, protocol `https`, token scopes: `gist`, `read:org`, `repo`, `workflow` |

No nested `.git` repos, no editor/OS junk files (`.bak`, `.swp`, `Thumbs.db`, `.DS_Store`, etc.) found anywhere in the tree.

---

## 1–2. Size & file count per chain

| Chain | Total size | File count |
|---|---|---|
| NullOrigin_Stego_Chain | 15 MB | 28 |
| NullOrigin_Rev_Chain | 799 MB | 33 |
| NullOrigin_Boot2Root_Chain | 773 MB | 13 |
| NullOrigin_Crypto_Chain | 8.2 GB | 52 |
| NullOrigin_Forensics_Chain | 228 MB | 43 |
| **Total** | **~10.0 GB** | **169** |

---

## 3. Existing ZIP/RAR/7Z archives

**None found.** A recursive case-insensitive search for `*.zip`, `*.rar`, `*.7z` across all five chains returned zero results.

Incidentally, the **Boot2Root** chain ships 4 `.ova` VM images (not classic archives, but bundled multi-file disk images — the unit players import into a hypervisor):
- `00-smoke/00-smoke.ova` (194.18 MB)
- `28-blackdamp/28-blackdamp.ova` (193.69 MB)
- `29-stillwater/29-stillwater.ova` (193.45 MB)
- `30-deadlight/30-deadlight.ova` (191.59 MB)

Each ships with a `SHA256SUMS.txt` integrity manifest (safe to publish, contains only hashes).

---

## 4. Files larger than 1.8 GB

Only 2 files exceed the threshold, both in **NullOrigin_Crypto_Chain**:

| File | Size |
|---|---|
| `NullOrigin_Crypto_Chain/11-cartouche/nand.bin` | 2.1 GB (2112 MB) |
| `NullOrigin_Crypto_Chain/09-catalogue/shelf.idx` | 1.9 GB (1920 MB) |

Note: both are close to or above typical single-asset limits for release hosting. `nand.bin` at 2.1 GB in particular may need splitting, Git LFS, or external hosting depending on your release target's per-file cap — worth checking before upload.

---

## 5. Largest 20 files overall (across all chains)

| # | Size | Path |
|---|---|---|
| 1 | 2112.00 MB | `NullOrigin_Crypto_Chain/11-cartouche/nand.bin` |
| 2 | 1920.00 MB | `NullOrigin_Crypto_Chain/09-catalogue/shelf.idx` |
| 3 | 1363.64 MB | `NullOrigin_Crypto_Chain/08-errata/bus.cap` |
| 4 | 1220.70 MB | `NullOrigin_Crypto_Chain/10-carillon/ledger-R.dat` |
| 5 | 762.94 MB | `NullOrigin_Crypto_Chain/10-carillon/ledger-I.dat` |
| 6 | 514.50 MB | `NullOrigin_Crypto_Chain/12-lastpage/records.bin` |
| 7 | 400.42 MB | `NullOrigin_Crypto_Chain/07-daybook/core.5711` |
| 8 | 280.00 MB | `NullOrigin_Rev_Chain/23-quicklime/reduction_tables.bin` |
| 9 | 240.00 MB | `NullOrigin_Rev_Chain/22-dovetail/dovetail.png` |
| 10 | 230.73 MB | `NullOrigin_Rev_Chain/21-cinder/cinder` |
| 11 | 194.18 MB | `NullOrigin_Boot2Root_Chain/00-smoke/00-smoke.ova` |
| 12 | 193.69 MB | `NullOrigin_Boot2Root_Chain/28-blackdamp/28-blackdamp.ova` |
| 13 | 193.45 MB | `NullOrigin_Boot2Root_Chain/29-stillwater/29-stillwater.ova` |
| 14 | 191.59 MB | `NullOrigin_Boot2Root_Chain/30-deadlight/30-deadlight.ova` |
| 15 | 111.91 MB | `NullOrigin_Forensics_Chain/14-slackwater/card.img` |
| 16 | 96.00 MB | `NullOrigin_Forensics_Chain/18-carrier/carrier.img` |
| 17 | 36.24 MB | `NullOrigin_Crypto_Chain/06-bindery/index.sealed` |
| 18 | 20.72 MB | `NullOrigin_Rev_Chain/25-ouroboros/ouroboros` |
| 19 | 12.00 MB | `NullOrigin_Forensics_Chain/17-greylist/physmem.img` |
| 20 | 12.00 MB | `NullOrigin_Rev_Chain/27-hourglass/machine-image.bin` |

All of these are consistent with intended challenge payloads (disk/memory/NAND/capture images, VM exports, keygen binaries and their data tables) per each chain's own README.

---

## 6. Suspicious files review

### 6a. Direct name-pattern search

Searched for: `flag.txt`, `root.txt`, `user.txt`, `*solution*`, `*solve*`, `*solver*`, `*writeup*`, `*write-up*`, `*answer*`, `.env`/`*.env*`, `*password*`, `*pwd*`, `*apikey*`, `*api_key*`, `*token*`, `*secret*`, `id_rsa*`, `id_dsa*`, `id_ed25519*`, `*.pem`, `*.ppk`, `*private*key*`, `*flag*`.

**Zero matches anywhere in the tree.** No `flag.txt`, `root.txt`, `user.txt`, `.env`, credential, key, or writeup-named file exists in any of the five chains.

This lines up with the chains' own documented design:
- **Boot2Root README** states `user.txt`/`root.txt` are flags players find *inside the VM guest filesystem* after booting the `.ova` — never shipped as loose host files.
- **Crypto/Forensics READMEs** state flags are never stored in full; `*.sealed` files are encrypted and `verify` only checks a submitted candidate.
- **Rev_Chain README** states these are "keygen-me" binaries — the flag is never resident in the binary or memory, only checked as a property.
- **Stego_Chain README** states each flag is the decryption key for the next stage, held by the player, not stored on disk.

### 6b. Content-based scan for plaintext flags

Grepped all text-like files (`.txt .md .py .c .cpp .php .json .yml .yaml .conf .cfg .ini .sh .js .log`, the `verify` files, `.export` files, and all `*.shadow` files) under 25 MB for flag-format patterns (`flag{`, `FLAG{`, `ctf{`, `CTF{`, `Null0rigin{`, `NullOrigin{`).

**Zero matches.** No plaintext flag text was found in any inspected file. (File contents were not printed to this report — only filenames were checked via `grep -l`, list-only mode.)

### 6c. Items flagged for your manual review (not confirmed secrets, but non-obvious / undocumented)

| Item | Location | Why it's flagged |
|---|---|---|
| `.strikes` (hidden, 36 bytes each) | 8x in `NullOrigin_Crypto_Chain/*/`, 7x in `NullOrigin_Forensics_Chain/*/` (15 total) | Implements the "THREE wrong answers locks the challenge" counter mentioned in both READMEs. Not listed as a shipped file in either README's file inventory. Looks like local runtime state left over from your own testing/solving, not something the READMEs describe as distributed. Recommend deleting or resetting these before release so every downloader starts with a fresh (0-strike) lock counter — otherwise a stage you tested may already be partially or fully locked out for the first player who downloads it. |
| `*.shadow` (7 files, one per stage: cinder, dovetail, quicklime, palimpsest, ouroboros, deadman, hourglass) | `NullOrigin_Rev_Chain/*/` | Single-line ASCII text files, named after their challenge binary, in the style of a Unix shadow-file hash entry. The Rev_Chain README's file inventory says each folder ships only "the challenge binary, an answer checker called verify, and a spoiler-free description.txt. Nothing else is provided and nothing else is needed" — yet these files are present and not mentioned there. No flag-pattern text was found inside them (see 6b), but given the README's note that some stages require a PASSWORD "built to one site-wide credential standard," this could plausibly be an intended crackable hash tied to that mechanic. Please manually confirm whether each `.shadow` file is meant to ship (a crackable artifact players need) or is leftover build/debug output that should be removed. |

### 6d. Confirmed-by-design, no action needed

- **`*.sealed` files** (Crypto/Forensics chains) — documented in both READMEs as "the flag, encrypted. No binary here ever prints it" / "confirmation only, does not hand you the flag." Intended to ship.
- **`verify` binaries** (stripped ELF, one per Crypto/Forensics/Rev stage) — documented as the player-facing answer-checker. Intended to ship.
- **`SHA256SUMS.txt`** files — plain hash manifests for integrity verification. Safe to publish.
- **`.dll` / `.jar` / `.elf` challenge binaries** (`shelfmark.dll`, `Ledgerd.dll`, `Lastpage.jar`, `recover.elf`, etc.) and themed "notes" documents (`bench-notes.txt`, `ledger-notes.txt`, `slips.txt`, `notebooks.txt`, `incident-notes.txt`, etc.) — these are the actual per-stage challenge payloads / in-universe flavor documents referenced by each README ("ships its carrier(s) plus one document. Read it."). No flag text found inside any of them.

**No `.env` files, no plaintext passwords, no API keys, no tokens, and no private key files (`id_rsa`, `.pem`, `.ppk`) were found anywhere in the five chains.**

---

## Summary / recommendation before APPROVE UPLOAD

1. Directory structure and `gh` auth confirmed — ready for release tooling.
2. No archives to worry about re-compressing; no accidental ZIP/RAR/7Z bundles to review.
3. `nand.bin` (2.1 GB) exceeds the 1.8 GB flag threshold and may need special handling (Git LFS / split / external host) depending on your release target's per-file limit.
4. Recommend deleting or resetting the 15 `.strikes` files before release — they're local lock-counter state from testing, not documented as shippable.
5. Recommend manually confirming the 7 `.shadow` files in Rev_Chain are intended to ship as-is (likely yes, given the password-cracking mechanic described in that README) rather than debug leftovers.
6. No flags, credentials, keys, or writeups were found in plaintext anywhere in the tree.

Waiting for explicit **APPROVE UPLOAD** before any release/upload action is taken.
