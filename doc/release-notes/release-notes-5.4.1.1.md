FreedomCoin Core version v5.4.1.1 is now available from: https://github.com/FreedomCoin-Project/FreedomCoin-Core/releases

This is a mandatory release. All nodes, patriotnodes and exchanges must upgrade before block 3,800,000 (approximately mid-November 2026).

Please report bugs using the issue tracker at github: https://github.com/FreedomCoin-Project/FreedomCoin-Core/issues

How to Upgrade
==============

If you are running an older version, shut it down. Wait until it has completely shut down, then run the installer (on Windows) or just copy over /Applications/FreedomCoin-Qt (on Mac) or freedomcoind/freedomcoin-qt (on Linux).

Notable Changes
==============

### Emission Schedule

The block reward and patriotnode payment schedules had no bracket defined beyond block 3,800,000. Past that height the block reward would have dropped to 1 FREED and the patriotnode payment schedule would have fallen out of alignment with it.

Both schedules are extended to block 10,000,000 at the current, already-active rates: 9 FREED block reward and 4.5 FREED patriotnode payment. There is no change to present emission.

### Block Validation

An uninitialised variable in `ConnectBlock()` was passed to the patriotnode payment validation check, causing the required patriotnode payment to be computed from an arbitrary block height rather than the real one. A node could reject its own otherwise-valid stake blocks as a result. This is a pre-existing defect present in earlier releases.

### Base64 Encoding

The base64 encoding alphabet contained a substituted character, so any base64 output containing the affected bit pattern was malformed. This affected signed message output, patriotnode broadcast signatures and RPC client authentication.

The same substitution was corrected in a string sanitisation constant, in the BIP38 passphrase validator (where uppercase "M" was rejected as an invalid character), in a vendored cryptographic library and in the autotools build scripts.

Signed messages produced by earlier versions may not verify. Re-sign any message that needs to remain verifiable.

Addresses, private keys and wallet files are unaffected: these use Base58, a separate implementation.

### RPC Authentication

This release includes the RPC authentication fix committed after v5.4.0.0 but not previously shipped in a release. RPC credentials are now actually enforced. A node whose configured credentials were never correct will refuse client connections until they are corrected.

### Other

* Testnet and regtest genesis block assertions corrected.
* Project domain references updated.
* Copyright year updated.
* New README and multi-platform build documentation.
* Updated splash image.

Known Issues
==============

Block reward calculation consults a money supply figure that is refreshed periodically rather than per block. This only affects the code path that engages as total supply approaches the maximum, which is not expected for approximately 300 days. It is not addressed in this release and is being handled as part of the vNext work.

Cold staking is not confirmed to produce rewards. One contributing defect is fixed in this release (see Block Validation above), but a separate issue affecting stake timing remains under investigation.
