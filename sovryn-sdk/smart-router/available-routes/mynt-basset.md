# MYNT bAsset

Uses [Mynt contracts](https://explorer.rsk.co/address/0x5f777270259e32f79589fe82269db6209f7b7582) for swaps.

### Available assets

* [DLLR](https://explorer.rsk.co/address/0xc1411567d2670e24d9c4daaa7cda95686e1250aa) (`redeemTo` function)
  * [ZUSD](https://explorer.rsk.co/address/0xdb107fa69e33f05180a4c2ce9c2e7cb481645c2d)
  * [DOC](https://explorer.rsk.co/address/0xe700691da7b9851f2f35f8b8182c69c53ccad9db)
* [ZUSD](https://explorer.rsk.co/address/0xdb107fa69e33f05180a4c2ce9c2e7cb481645c2d) -> [DLLR](https://explorer.rsk.co/address/0xc1411567d2670e24d9c4daaa7cda95686e1250aa) (`mintTo` function)
* [DOC](https://explorer.rsk.co/address/0xe700691da7b9851f2f35f8b8182c69c53ccad9db) -> [DLLR](https://explorer.rsk.co/address/0xc1411567d2670e24d9c4daaa7cda95686e1250aa) (`mintTo` function)

### Other notes

The route implements `approve` function that needs to be used before swapping.

