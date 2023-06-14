# Installation

To start using sovryn onboard, install the core package and at least one connector

```bash
# install core package and injected wallet connector (for Metamask, Nifty, etc)
yarn add @sovryn/onboard-core @sovryn/onboard-injected
```

Additionally, if direct integration with a hardware wallet is required, install the corresponding connector

```bash
# install ledger and trezor connectors
yarn add @sovryn/onboard-ledger @sovryn/onboard-trezor
```

We also recommend installing the React UI component which provides a simple flow for connecting to a wallet. If you are not using React or want to implement your own UI, you can skip this step, but you should check on `@sovryn/onboard-react` package for examples of how to use the core package.

```bash
# install React UI component
yarn add @sovryn/onboard-react
```
