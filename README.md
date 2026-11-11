# sdb-termux

SDB (Smart Development Bridge) host client for Tizen, built from source on **Termux** (Android aarch64).

Samsung ships no Android/aarch64 SDB binary — it only comes with Tizen Studio for x86_64 Linux, Windows, and macOS. This project builds the host `sdb` client from Tizen sources so it runs natively on Termux.

## Source

Upstream Tizen SDB source (mirror): `platform/core/system/sdb`.  
Built here from: https://github.com/TizenTeam/sdb

## Build

Requires Termux with `clang`, `make`, `openssl`.

```bash
cd ~/projects/sdb-termux
make clean
make sdb TARGET_HOST=true TARGET_ARCH=x86
```

Produces `bin/sdb` (aarch64 Android ELF).

## Usage

```bash
export PATH="$PATH:$HOME/projects/sdb-termux/bin"
sdb devices
sdb connect <tizen-device-ip>
sdb shell
sdb push <local> <remote>
sdb pull <remote> [<local>]
```

Enable developer mode on the Tizen device and pair it first. SDB talks over USB or TCP.

## Termux patches

- `src/sdb.h` — `int sdb_trace_mask;` → `extern int sdb_trace_mask;` (fixes duplicate-symbol link error)
- `src/transport_local.c` — stubbed `get_devicename_from_shdmem` to return -1 (Termux renames `shmget`/`shmat` to unresolved `libandroid_*` aliases; this Android shared-memory lookup isn't needed on a Termux host)
