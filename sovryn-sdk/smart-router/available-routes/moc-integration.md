# MoC integration

Utilizes [MoC contracts](https://moneyonchain.com/) for swaps.

### Available assets

You can swap [DLLR](https://explorer.rsk.co/address/0xc1411567d2670e24d9c4daaa7cda95686e1250aa) to RBTC via `getDocFromDllrAndRedeemRBTC` function on the [MoC integration proxy contract](https://explorer.rsk.co/address/0x9363323d9c653a2b89c758f62f5043f0b2c67c71).

### Other notes

MoC swap route implements `permit` function that needs to be used to approve DLLR before swapping.

