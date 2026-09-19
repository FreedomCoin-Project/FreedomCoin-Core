# FreedomCoin Core

FreedomCoin (FREED) is a Proof-of-Stake cryptocurrency with a masternode
tier (called *patriotnodes*), derived from PIVX. It was formerly known as
TrumpCoin.

- **Consensus:** Proof of Stake
- **Block time:** ~60 seconds
- **Patriotnode collateral:** 10,000 FREED
- **Shielded transactions:** Sapling

## Downloads

Pre-built wallets for Linux, Windows, macOS and ARM are published on the
[Releases page](https://github.com/FreedomCoin-Project/FreedomCoin-Core/releases).

Verify the SHA256 checksum of any download against the value published in
the release notes before running it.

## Building from source

Dependencies are built via the `depends` system, which produces a
self-contained toolchain for the target platform:

```bash
cd depends
make HOST=x86_64-pc-linux-gnu -j$(nproc)
cd ..
./autogen.sh
CONFIG_SITE=$PWD/depends/x86_64-pc-linux-gnu/share/config.site ./configure --prefix=/
make -j$(nproc)
```

Cross-compiling for Windows, macOS or ARM additionally requires
`--disable-online-rust`. See
[doc/multi-platform-build-from-shared-depends.md](doc/multi-platform-build-from-shared-depends.md)
for the full process, and [doc/build-unix.md](doc/build-unix.md) for
platform prerequisites.

## Running a node

```bash
./src/freedomcoind -daemon
./src/freedomcoin-cli getblockchaininfo
```

Configuration goes in `~/.freedomcoin/freedomcoin.conf`. An RPC username
and password are required for `freedomcoin-cli` to connect.

## Community

- Discord: https://discord.gg/KkK5Pdg
- Telegram: https://t.me/FreedomcoinWorldWide
- Twitter: https://twitter.com/FreedomcoinWW
- BitcoinTalk: https://bitcointalk.org/index.php?topic=1858645
- Website: https://freedomcoin.global/

## Block explorers

- https://chain.freedomcoin.global
- https://explorer.freedomcoin.global
- https://chainz.cryptoid.info/freed/

## License

FreedomCoin Core is released under the terms of the MIT license. See
[COPYING](COPYING) for more information.
