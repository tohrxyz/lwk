# BEWallet
BEWallet is a collection of Rust crates for the keystone project.

The keystone project aims to provide an easy solution to use
multisig and HWW on the Elements/Liquid network, including the
ability to create and sign issuance, reissuance and burn
transactions.

## History
BEWallet was [originally](https://github.com/LeoComandini/BEWallet/)
a Elements/Liquid wallet library written in Rust to develop
prototypes and experiments.

BEWallet was based on [Blockstream's GDK](https://github.com/Blockstream/gdk).
Essentially some GDK Rust pieces were moved to this project.

This was used as the starting point for the keystone project,
initially the parts that were not necessary have been dropped,
things have been polished and new features have been addded.

The code base has been entirely re-written, and now it has
almost no similarity with the original code.

## Structure
Instead of going for a monolithic approach, we opted to split
the projects in different components that might be useful
independently.

### Wollet
A library for Elements/Liquid watch only wallets.

The caller specifies a [CT descriptor](https://github.com/ElementsProject/ELIPs/blob/main/elip-0150.mediawiki),
and the library will use a Elements/Liquid Electrum server to fetch
blockchain data.

The wallet can generate new addresses, get transactions/utxos/balance,
create PSETs and other actions.

Used by:
* `app`

This module might be used by:
* Exchanges who need a watch-only wallet to process incoming payments.

### Signer
Library to interact with Elements/Liquid signers.

Signer are capable of inspecting and signing PSETs.

Currently supported signers:
* Software
* Jade

Used by:
* `app`
* `wollet` (tests)

This module might be used by:
* AMP2

### Jade
Library to interact with Jade.

Unlock Jade, register multisig wallets, sign PSETs.

Used by:
* `signer`
* `wollet` (tests)

This module might be used by:
* (Mobile) apps that needs to interact with Jade

### Hwi
Placeholder crate, currently unused.

Once we will have support for multiple HWW vendors,
we can make `jade` a dependency of this crate.

### Pset details
Utils to inspect a PSET.

Get the net effect of a PSET on a given wallet,
or get how many signatures are missing,
and which signers should provide them.

Used by:
* `wollet`
* `signer`

### Bs cointainers
Collections of docker containers wrappers to setup test cases
using Blockstream projects:
* Jade emulator
* Pin server

This module might be used by:
* Projects using the above Blockstream projects

Used by:
* `wollet` (tests)
* `jade` (tests)

### Tiny jrpc
Tiny json rpc server.

Used by:
* `app`

### App
Handle the jrpc server and serves requests coming from clients
such as `cli`.

Used by:
* `cli`

### Cli
CLI to interact with the json rpc server.

## Tests

Run tests:

Run unit tests:
```
cargo test --lib
```

End to end tests needs local servers:

```
./context/download_bins.sh # needed once unless server binaries changes
. .envrc  # not needed if you use direnv and you executed `direnv allow`
```

To run end to end tests:

```
cargo test
```

### Test with physical Jade
Tests using the serial need an additional dependency:
```
apt install -y libudev-dev
```

Test cannot be executed in parallel:
```
cargo test -p jade --features serial auth -- --include-ignored
cargo test -p jade --features serial ping -- --include-ignored
cargo test -p jade --features serial logout -- --include-ignored
cargo test -p wollet --features serial serial -- --include-ignored
```
