# Usage

As a first step you will need to initialise the core package and at least one connector. The core package is initialized with a list of connectors and a list of chains. The connectors are used to connect to a wallet, and the chains are used to determine which chains are supported by the wallet.

```ts
import Onboard from '@sovryn/onboard-core';
import injectedModule from '@sovryn/onboard-injected';
import { OnboardProvider } from '@sovryn/onboard-react';
import walletConnectModule from '@sovryn/onboard-walletconnect';

const injected = injectedModule();
const walletConnect = walletConnectModule();

const chains = [
  {
    id: '0x1e',
    rpcUrl: 'https://public-node.rsk.co',
    label: 'RSK',
    token: 'RBTC',
    blockExplorerUrl: 'https://explorer.rsk.co',
  },
];

export const onboard = Onboard({
  wallets: [injected, walletConnect],
  chains,
});
```

If you are using the React UI component, you will to add the `OnboardProvider` to your app. The `OnboardProvider` will provide the core package to the React UI component.

```tsx
import { OnboardProvider } from '@sovryn/onboard-react';

const App = () => {
  return (
    <>
      <MyApp />
      <OnboardProvider />
    </>
  )
}
```

Once the core package is initialised, you can use it to connect to a wallet. The `connectWallet` function will open a modal that allows the user to select a wallet and connect to it. The `disconnectWallet` function will disconnect the user from the wallet.

```ts
import { onboard } from './onboard';

const connectWallet = async () => {
  await onboard.connectWallet();
};

const disconnectWallet = async () => {
  await onboard.disconnectWallet();
};
```

If you are not using the React UI component, `connectWallet` will not show a model. For this reason you should pass a connectors name to `connectWallet` - this will allow you to connect to a specific wallet without showing a modal. Because of this, some wallets, like Hardware wallets, will not work without the React UI component.

```ts
import { onboard } from './onboard';

const connectWallet = async () => {
  await onboard.connectWallet('injected');
};
```

`connectWallet` returns a Promise with array of `Wallet` objects. Each `Wallet` object contains information about the wallet that was connected to. The `Wallet` object also contains a `provider` property which is a Web3 provider that can be used to interact with the wallet.

```ts
import { onboard } from './onboard';

const connectWallet = async () => {
  const wallets = await onboard.connectWallet();
  const wallet = wallets[0];
  const provider = wallet.provider;
};
```

You can also retrieve onboard state by calling `onboard.state.get()`. It allows you to get the current state of the wallet, including the `wallet` object, the `address` of the connected wallet, and the `provider` that the wallet is connected to. Subscribing to state changes is also possible by calling `onboard.state.select('wallets').subscribe`.

```ts
import { onboard } from './onboard';

const wallets = onboard.state.get().wallets;
console.log('initial wallets', wallets);

const wallets = onboard.state.select('wallets').subscribe((wallets) => {
  console.log('wallets changed', wallets);
});
```

#### Changing chain

To change the chain, you can call `onboard.setChain`. If wallet does not support changing chains, it will do nothing.

setChain will only allow you to change to a chain that was passed to the core package when it was initialised.

```ts
import { onboard } from './onboard';

const changeChain = async () => {
  await onboard.setChain('0x1e');
};
```

If user is connected to multiple wallets, `setChain` will change the chain for the first of them. If you want to change the chain for a specific wallet, you can pass the wallet name to `setChain`. This is useful for cross-chain flows, like bridges.

```ts
import { onboard } from './onboard';

const changeChain = async () => {
  await onboard.setChain('0x1e', undefined, 'ledger');
};
```
