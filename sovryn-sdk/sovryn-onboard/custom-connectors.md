# Custom Connectors

You can easily implement new custom connectors. A connector is a function that returns a `WalletInit` function.

In this example we will create a custom connector that will connect to a wallet that is injected into the page via `window.ethereum`. A simpler version of 'injected' connector.

```ts
// custom.ts
const customConnector = (): WalletInit => {
  return () => {
    label: 'Custom Wallet',
    getIcon: async () => 'https://example.com/icon.png',
    getInterface: async () => {
      return {
        provider: window.ethereum,
      };
    };
  };
};

export default customConnector;
```

`getInterface` must expose a `provider` property that is a EIP-1193 compatible provider. If wallet you are creating connector for does not support EIP-1193, you can use `createEIP1193Provider` helper from `@sovryn/onboard-common` to create a compatible provider using methods that your wallet exposes.

Helper will create a provider that will expose `request` method, which is required by EIP-1193. You will also need to implement other methods like:

`eth_ethChainId`, `eth_signTransaction`, `eth_sendTransaction`, `eth_sign`, `eth_accounts`, `eth_requestAccounts` etc.

You may consult implementation of `ledger` or `walletconnect` connectors for reference.

#### Hardware Wallets

Most of the hardware wallets requires user to make additional actions. For example to choose to which account they want to connect to.

We have additional helpers in the `@sovryn/onboard-hw-common` package to deal with this.

```ts
import { accountSelect, closeAccountSelect, selectAccounts$ } from '@sovryn/onboard-hw-common';

// somewhere in your code you need to call `accountSelect` function to inform flow about a need to show account selection UI
const account = await accountSelect({
  // array of derivation paths that UI should show for user to choose from
  basePaths: ['m/44\'/60\'/0\'/0'],
  // optional, if true - UI must allow user to enter custom path to the input
  supportsCustomPath: false,
  // array of assets that can be used to derive accounts
  assets: [{ label: 'ETH', address: '0x000...' }],
  // array of networks that can be used to derive accounts
  chains: [{ id: '0x1', ... }],
  scanAccounts: async (options: { derivationPath: string, chainId: string, asset: { label: string; address?: string }}): Promise<Account[]> => {
    // here you need to implement logic to scan accounts for a given derivation path, chain and asset
    // then, return an array of accounts
    return [{
      address: '0x123....';
      derivationPath: 'm/44\'/60\'/0\'/0';
      balance: {
        asset: 'ETH';
        value: '0';
      };
    }]
  }
});

// account will resolve to the selected account, but there is still some work to do.
// after "accountSelect" is called, you have a global variable "selectAccountOptions" that contains options that were passed to the function available for you, you can use it in your UI.
// also, we have a state in "selectAccounts$" stream that you can subscribe to. It will notify you about a need to open or close account selection UI.
selectAccounts$.asObservable().subscribe((value) => {
  if (value.inProgress) {
    // show account list UI
  } else {
    // hide account list UI
  }
});

// then, to retrieve list of accounts you need to call scanAccounts with options you want to use:
const list = await selectAccountOptions.scanAccounts({
  derivationPath: selectAccountOptions.basePaths[0],
  chainId: selectAccountOptions.chains[0].id,
  asset: selectAccountOptions.assets[0],
})


// render list of accounts in your UI
// and when user selects an account, you need to call "selectAccount" function
selectAccount(list[0]);

// if you want to close HW flow without user action, you can call "closeAccountSelect" function
closeAccountSelect();

// after user triggers "selectAccount" or "closeAccount", "accounts" promise of "accountSelect" will resolve to the selected account or empty array.
```
