# sbcl-builds

Linux builds of [SBCL](https://www.sbcl.org/) for the machines that work on
the afluentlife-loom projects. Claude Code cloud sessions run on x86-64. The
OrbStack machines on Apple-silicon Macs run on arm64.

SBCL publishes a Linux binary for x86-64 only. These are built from SBCL's
source release, with one recipe on both architectures, so every machine runs
the same SBCL. They are not official SBCL releases.

## Releases

Each release is tagged `sbcl-<version>` and carries:

- `sbcl-<version>-x86-64-linux.tar.gz` and `sbcl-<version>-arm64-linux.tar.gz`,
  each holding SBCL's install tree for `/usr/local`: `bin/`, `lib/sbcl/` and
  `share/`;
- a `.sha256` file for each tarball.

Install by unpacking into `/usr/local`:

```sh
sudo tar -xzf sbcl-2.6.9-arm64-linux.tar.gz -C /usr/local
```

## How they are built

On Ubuntu 24.04, from SBCL's source release:

1. Ubuntu's own `sbcl` package (2.2.9) is installed to bootstrap the build.
2. Build dependencies: `build-essential`, `zlib1g-dev` and `libzstd-dev`.
3. Build: `sh make.sh --fancy`. This builds SBCL with threads, Unicode, core
   compression and all contribs, `sb-simd` included.
4. Install: `INSTALL_ROOT=<stage> sh install.sh`, then the stage is made into
   a tarball with root ownership.

Each tarball's architecture is built natively, or under Rosetta 2 in OrbStack
for x86-64 on an Apple-silicon Mac. The recipe is `build_sbcl` in
afluentlife-loom/ai-interact-mcp's `bin/cloud-setup.sh`, run with
`--sbcl-tarball DIR`. That script also installs these tarballs, and it checks
each against a pinned SHA-256 before installing.

### 2.6.9

- Source: `sbcl-2.6.9-source.tar.bz2` from
  https://sourceforge.net/projects/sbcl/files/sbcl/2.6.9/, with SHA-256
  `c6fd1d735570eb4ff34caf9609988ca77ed0bd12b55d09a4fed075be890da513`.
- Built 2026-10-18 in `ubuntu:24.04` containers.

## Licence

SBCL is in the public domain, with BSD-style and MIT parts whose notices must
be kept. [COPYING](COPYING) is SBCL's own statement, and each tarball carries
it in `share/doc/sbcl/COPYING`. This repository adds nothing of its own to the
builds.
