# Custom UI

To implement custom onboarding UI you will need to watch events from the core package. Simple example of custom UI implementation:

```ts
import { helpers, state } from '@sovryn/onboard-core';
import { connectWallet$ } from '@sovryn/onboard-core/dist/streams';


// get all connector modules that was passed to the initialization
// you can use it to get connector icon, name, etc
const modules = state.get().walletModules;

const connect = (label: string) => {
  const connector = modules.find((m) => m.label === label);
  helpers.connectWallet(connector);
};

// watch for connectWallet$ event
// it notifies about `connectWallet` function call
$connectWallet$.asObservable().subscribe((value) => {
  // if module is passed, it means that `connectWallet` was called with a specific wallet name and we should connect to it.
  // we can skip showing ui and connect to wallet right away
  if (value.module) {
    connect(value.module);
  }

  // inProgress means that onboard waits for user to select wallet and finish connection
  if (value.inProgress) {
    // show list of possible wallets
  } else {
    // hide list of possible wallets
  }

});
```
