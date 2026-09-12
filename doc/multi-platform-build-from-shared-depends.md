# Building all platforms from one shared `depends/` tree

This is the process for producing Linux, Windows, and ARM builds from a
single, already-built `depends/` directory, reused across separate clones
rather than rebuilt per platform. Building `depends/` once (all four
supported host triples: `x86_64-pc-linux-gnu`, `x86_64-w64-mingw32`,
`arm-linux-gnueabihf`, `x86_64-apple-darwin16`) takes a long time; this
lets every platform build reuse that same output.

## One-time setup: build `depends/` for every target

From the main working checkout:

```bash
cd depends
make HOST=x86_64-pc-linux-gnu -j$(nproc)
make HOST=x86_64-w64-mingw32 -j$(nproc)
make HOST=arm-linux-gnueabihf -j$(nproc)
# macOS also needs an extracted SDK in depends/SDKs/ first - see
# contrib/macdeploy/README.md
make HOST=x86_64-apple-darwin16 -j$(nproc)
```

## Per-platform build: clone + symlink + configure + make

For each additional platform, clone the source fresh and symlink the
already-built `depends/` into it, rather than copying (saves disk and
time; the dependency build itself doesn't change per source clone):

```bash
git clone <repo> freedomcoin-<platform>
ln -s /path/to/depends freedomcoin-<platform>/depends
cd freedomcoin-<platform>
./autogen.sh
```

Then configure per target. **`--disable-online-rust` is required for
every cross-compile target** (Windows, ARM, macOS) — without it, cargo
builds for the native host instead of the target, and the Rust library
never lands where the C++ linker expects it.

### Linux (native)

```bash
./configure --prefix=/
make -j$(nproc)
```

### Windows

```bash
CONFIG_SITE=$PWD/depends/x86_64-w64-mingw32/share/config.site \
RUST_TARGET=x86_64-pc-windows-gnu \
  ./configure --prefix=/ --disable-online-rust
make -j$(nproc)
```

### ARM (headless — no GUI on this target)

```bash
CONFIG_SITE=$PWD/depends/arm-linux-gnueabihf/share/config.site \
RUST_TARGET=arm-unknown-linux-gnueabihf \
  ./configure --prefix=/ --disable-online-rust --without-gui
make -j$(nproc)
```

### macOS

```bash
CONFIG_SITE=$PWD/depends/x86_64-apple-darwin16/share/config.site \
RUST_TARGET=x86_64-apple-darwin \
  ./configure --prefix=/ --disable-online-rust
make -j$(nproc)
```

## One thing to watch for: GUI silently disabled

If `depends/` was originally built under a different absolute path than
where it's symlinked from now, `./configure`'s link-check for the static
Qt plugins can fail silently even though the files exist, because paths
baked into the dependency build still point at the original location.
Symptom:

    checking whether to build FreedomCoin Core GUI... no (Qt5)

If this happens, symlink the *original* path to the new one (so both
resolve) and re-run `./configure` — the decision is baked into the
generated Makefile and won't fix itself with `make` alone.
