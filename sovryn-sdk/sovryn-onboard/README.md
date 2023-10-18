# Sovryn Onboard

Sovryn-onboard is a wallet connection library that when integrated into a dapp, allows users to connect their Web3 wallets to sign messages and transactions. It's modular so each part can be easily changed, with a split between core and connector packages.

Modules are forked from and therefore compatible with [web3-onboard](https://github.com/blocknative/web3-onboard). Most changes are to add additional features required by Sovryn.

Core packages are required for library to work seamlessly and connectors are optional.

#### Core

| package                     | description                                                 |
| --------------------------- | ----------------------------------------------------------- |
| `@sovryn/onboard-core`      | core package, required to use sovryn onboard                |
| `@sovryn/onboard-common`    | common Web3 provider logic and typings                      |
| `@sovryn/onboard-hw-common` | common provider logic for Hardware wallets (Trezor, Ledger) |
| `@sovryn/onboard-react`     | React UI component                                          |

#### Connectors

At least one connector is required to use sovryn onboard

| package                         | description                          |
| ------------------------------- | ------------------------------------ |
| `@sovryn/onboard-injected`      | connector for injected Web3 wallets  |
| `@sovryn/onboard-walletconnect` | connector for WalletConnect          |
| `@sovryn/onboard-ledger`        | connector for Ledger Hardware wallet |
| `@sovryn/onboard-trezor`        | connector for Trezor Hardware wallet |

