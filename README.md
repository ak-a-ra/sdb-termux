# sdb-termux

SDB (Smart Development Bridge) host client for Tizen, built from source on **Termux** (Android aarch64).

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)

## About

Samsung ships no Android or aarch64 build of SDB — it only ships with Tizen Studio for x86_64 Linux, Windows, and macOS. `sdb-termux` builds the Tizen `sdb` host client from source so it runs natively on Termux, letting you push, pull, shell, logcat, and forward ports to Tizen TVs and watches straight from your Android device.

## Features

- `sdb devices` — list connected Tizen devices over USB or TCP
- `sdb connect <ip>` — pair and connect to a Tizen device over network
- `sdb shell` / `sdb shell <cmd>` — run commands on the device
- `sdb push <local> <remote>` — copy files to the device
- `sdb pull <remote> [<local>]` — copy files from the device
- `sdb dlog` — view device log output
- `sdb forward <local> <remote>` — forward TCP ports to the device

## Installation

### Requirements

- Termux on Android (aarch64)
- `clang`, `make`, `openssl` packages

```bash
pkg install clang make openssl
```

### Build

Clone and build the host `sdb` client:

```bash
git clone https://github.com/ak-a-ra/sdb-termux.git
cd sdb-termux
make clean
make sdb TARGET_HOST=true TARGET_ARCH=x86
```

This produces `bin/sdb`.

Add it to your PATH:

```bash
export PATH="$PATH:$HOME/projects/sdb-termux/bin"
```

To make it permanent, add that `export` line to `~/.zshrc`.

## Usage

```bash
sdb devices
sdb connect 192.168.1.105
sdb shell
sdb push myapp.tpk /opt/usr/home/owner/
sdb pull /opt/usr/home/owner/log.txt ./log.txt
sdb dlog
sdb forward 5000 5000
```

Enable developer mode on your Tizen device and accept the host pairing prompt before connecting.

## More Examples

See the SDB command reference in `sdb --help` for the full option list.

## Documentation

- Upstream Tizen SDB source: `platform/core/system/sdb`
- Built from: https://github.com/TizenTeam/sdb

## Contributing

Contributions are welcome. The Termux-specific patches are:

- `src/sdb.h` — `int sdb_trace_mask;` → `extern int sdb_trace_mask;` (fixes duplicate-symbol link error)
- `src/transport_local.c` — stubbed `get_devicename_from_shdmem` to return -1 (Termux renames `shmget`/`shmat` to unresolved `libandroid_*` aliases; this Android shared-memory lookup isn't needed on a Termux host)

### Development Setup

```bash
git clone https://github.com/ak-a-ra/sdb-termux.git
cd sdb-termux
make clean
make sdb TARGET_HOST=true TARGET_ARCH=x86
./bin/sdb devices
```

### Running Tests

No automated test suite is included. Verify manually with `sdb devices` against a paired Tizen device.

## Roadmap

- [ ] Prebuilt `bin/sdb` releases for Termux aarch64
- [ ] `sdbd` device-daemon build variant
- [ ] USB device support verification on Termux

## Acknowledgments

- Tizen open source project — upstream `platform/core/system/sdb` source
- Built on the TizenTeam GitHub mirror: https://github.com/TizenTeam/sdb

## License

This project incorporates upstream Tizen SDB source licensed under the [Apache License 2.0](LICENSE). See [`src/NOTICE`](src/NOTICE) for additional notices. This Termux build adds no extra license terms; the original Apache 2.0 terms apply to the source. The `bin/sdb` binary is a build artifact of that source.

Do not claim this is an official Samsung release or rebrand it as Tizen Studio tooling.
