# Overview

Sovryn-onboard is a wallet connection library. It's modular so each part could be easily changed, but to make usage faster it's split to core and connector packages.&#x20;

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
