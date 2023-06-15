# Creating new Smart Route

Smart route is a function which accepts a single argument of ethers provider and returns object of `SwapRoute` type.

```typescript
// smart route type
type SwapRouteFunction = (provider: providers.Provider) => SwapRoute;
```

## SwapRoute API

### name: string

Define a name of your smart route.

### pairs(): Promise\<SwapPairs>

Map of all supported tokens. Give an entry token as a key and all possible destination tokens as it's value in array. Tokens must be defined as lowercased contract addresses. It's expected to define RBTC token as zero address.

```typescript
async pairs() {
    return new Map([
       // RBTC -> SOV, XUSD
       '0x0000000000000000000000000000000000000000': [
         '0xefc78fc7d48b64958315949279ba181c2114abbd', // SOV
         '0xb5999795be0ebb5bab23144aa5fd6a02d080299f', // XUSD
       ],
       // SOV -> RBTC
       '0xefc78fc7d48b64958315949279ba181c2114abbd': [
         '0x0000000000000000000000000000000000000000' // BTC
       ],
    ]);
} 
```

In the example above, smart route expected to support swaps from RBTC to SOV and XUSD, but SOV can be swapped only to the RBTC and XUSD has no destinations at all.

### quote(entry: string, destination: string, amount: BigNumberish, options?: Partial\<Options>, overrides?: Partial\<TransactionRequest>): Promise\<BigNumber>

Use entry, destination and amount to calculate quote amount and return it as a BigNumber.

### swap(entry: string, destination: string, amount: BigNumberish, options?: Partial\<Options>, overrides?: Partial\<TransactionRequest>): Promise\<TransactionRequest>

Build an ethers transaction request which can be used to sign and broadcast transaction later. `overrides` must be merged to the end result.

```typescript
swap(entry, destination, amount, options, overrides) {
    return {
        to: '0xefc78fc7d48b64958315949279ba181c2114abbd',
        data: '0x00000',
        value: '0',
        ...overrides,
    };
}
```

`to`, `data` and `value` must be defined in the response.

### approve(entry: string, destination: string, amount: BigNumberish, from: string, overrides?: Partial\<TransactionRequest>): Promise\<TransactionRequest | undefined>

If swap must have tokens approved to be used, you need to prepare transaction request here, if it's not required or required amount is already approved - just return `undefined`

`entry` is likely to be a token you will need to approve.

`amount` is amount we want to swap later, so also an amount we need to have approved

`from` wallet address of an user who owns tokens.

### permit(entry: string, destination: string, amount: BigNumberish, from: string, overrides?: Partial\<PermitTransactionRequest>): Promise\<PermitTransactionRequest | undefined>

&#x20; If token supports `permit`s, then you can use this instead of `approve` to make swap cheaper.

