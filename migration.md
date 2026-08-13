# Migrating to the release model

VitaSDK now ships as versioned, signed releases through package channels. Nothing forces you to migrate: existing installs keep working and no download links die. What the new model gives you is knowing exactly which SDK you have, signature verification on everything you download, and one-command updates. Find your case below.

## "I already have vitasdk installed"

Your install keeps working as-is. To move to the release model, bootstrap fresh into a new directory and keep the old one around until you're satisfied:

```bash
git clone https://github.com/vitasdk/vdpm
cd vdpm
VITASDK=/usr/local/vitasdk-2026.08 ./bootstrap-vitasdk.sh
```

Point `$VITASDK` (and your `PATH`) at the new directory and rebuild your project. Whenever you're confident, delete the old directory — vitasdk has always been "one directory", there is nothing else to uninstall.

## "I use the vdpm scripts to add libraries"

The old vdpm fetched prebuilt package tarballs, with no versioning and no dependency handling. The new `vdpm` is a real package client backed by signed channels:

```bash
vdpm install libpng     # zlib comes along automatically
```

Same library names you're used to. Dependencies resolve on their own.

## "How do I update now?"

Two commands, two meanings:

```bash
vdpm upgrade            # package fixes within YOUR release — safe, no surprises
vdpm refresh 2026.08    # explicit move to a release, when one is announced
```

`upgrade` will never move you to a new toolchain behind your back. That only happens when you `refresh` to a newer release yourself.

## "My CI installs vitasdk"

Pin the exact bootstrap archive and its checksum — your builds become reproducible and the SDK can't drift under a green pipeline:

```bash
./bootstrap-vitasdk.sh \
  --url https://github.com/vitasdk/autobuilds/releases/download/sdk-core-2026.08.0/vitasdk-bootstrap-x86_64-linux-gnu.tar.bz2 \
  --sha256 <value from the matching .sha256 asset>
```

Bootstrap archives are published for every release and all four platforms (`x86_64-linux-gnu`, `aarch64-linux-gnu`, `arm64-apple-darwin`, `x86_64-w64-mingw32`) on the [autobuilds releases page](https://github.com/vitasdk/autobuilds/releases).

## "I use the Docker image"

This is the one change that reaches you without you doing anything.
`vitasdk/vitasdk:latest` used to be a nightly, rebuilt every night from
whatever master held. It now follows the newest supported release, so it is
2026.08 from now on — same triplet, same headers, a toolchain that stops moving
under you.

Nothing was removed and no tag disappeared. If you want a build that cannot
change at all, pin a dated tag:

```bash
docker pull vitasdk/vitasdk:2026.08-20260813
```

The [front page](/#docker) lists the rest of the tags, including the `-minimal`
images that leave out the target packages.

## FAQ

**Why signed channels?** In 2023 our build automation failed silently, and for a long time nobody noticed. Signed, sequenced channels make that class of failure impossible: the client either verifies fresh signed data, or it tells you loudly — never stale data pretending to be fresh.

**What does "supported" mean for 2026.08?** It receives package updates and fixes via `vdpm upgrade` until the next release supersedes it. The toolchain within a release never changes.

**Where did the nightlies go?** The `nightly` development channel replaces the old master builds. It keeps rolling, it is where the next release comes from, and it is the right place to check whether something is already fixed. Expect it to move under you.

**Do my projects need changes?** No. Same `arm-vita-eabi` triplet, same headers, same CMake toolchain file. If it built against a recent master build, it builds against 2026.08.

**What about Windows?** Native Windows is supported: every release publishes a `x86_64-w64-mingw32` bootstrap archive, and vdpm ships a PowerShell installer (`bootstrap-vitasdk.ps1`). That said, we recommend WSL2 — follow the Linux instructions on the [front page](/).
