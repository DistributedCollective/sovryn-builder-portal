---
description: >-
  Fast rBTC to BTC transfers
---

# FastBTC (rBTC to BTC)

The rBTC-to-BTC side of FastBTC, also called Bidirectional FastBTC is a service that consists of a number of smart
contracts on the RSK blockchain.

The builder portal focuses on programmatic integration against the service. More details about the inner workings
of the service can be found [on the Github repository](https://github.com/DistributedCollective/bidirectional-fastbtc),
in particular from the following `README.md` files:

- [Overview](https://github.com/DistributedCollective/bidirectional-fastbtc/blob/master/README.md)
- [Smart contracts](https://github.com/DistributedCollective/bidirectional-fastbtc/blob/master/packages/fastbtc-contracts/README.md)
- [Federation p2p network](https://github.com/DistributedCollective/bidirectional-fastbtc/blob/master/packages/fastbtc-node/README.md)

## Smart contract addresses

The FastBTCBridge contract addresses are:
- RSK testnet: `0xb3E92Db36eeac0627385Fa4F39F615a85eA5E911`
- RSK mainnet: `0x1A8E78B41bc5Ab9Ebb6996136622B9b41A601b5C`

## Transferring rBTC to BTC

To transfer RBTC to BTC, you call the `transferToBtc` function. It takes one argument which is the BTC address you are
transferring to. The amount transferred is taken from `msg.value`.

For privacy reasons, users are encouraged to create a new receiving Bitcoin address for each transfer. However,
one Bitcoin address can be used up to 255 times. Should a user want to transfer more than 255 times, they will need to
use multiple Bitcoin addresses.

All common Bitcoin address formats are supported, except for Taproot (P2TR, Segwit v1). Segwit v0 addresses are encouraged.

There is also the function `receiveEthFromBridge` which can be used in some specific scenarios -- the arguments it takes
are a bit different (ABI-encoded), and it also takes the user's RSK address as an argument. This address is used in
events and if the transfer gets refunded for some reason (e.g. invalid BTC address).

### Validation

Bitcoin addresses are validated on the smart contract, but because of gas cost considerations, the validation is not
100% accurate. Because of this, all user addresses should go through the following validation process before sending a
transaction that calls `transferToBtc`:

- Validate using a library (for example on the frontend)
- In addition, call `isValidBtcAddress` on the smart contract

In addition to the address validation, it's recommended to validate that

- the rBTC amount transferred in wei is divisible by `SATOSHI_DIVISOR` (`10 000 000 000`)
- **satoshi** amount transferred is within the limits using the `minTransferSatoshi`
  and `maxTransferSatoshi` functions
- the user's Bitcoin address is not already used 255 times by checking that `getNextNonce` returns `< 255`


## Tracking the state of a transfer

The transfer status can be tracked by reading the `NewBitcoinTransfer` and `BitcoinTransferStatusUpdated` events.

- The argument `transferId` is the unique id that identifies a transfer
- The argument `newStatus` is the new status of the transfer
  - `NEW` (`1`) means that the transfer was initiated on RSK by the user but is not yet being processed
  - `SENDING` (`2`) means that the transfer is included in the current batch of transfers that will be sent
    (and might or might not be already sent) to Bitcoin. This also means that the Bitcoin transaction is already
    generated (though not necessarily signed or broadcast), and that there exists a predetermined Bitcoin transaction
    hash that can be used to track the transfer on the Bitcoin side (see below).
  - `MINED` (`3`) means that the transfer is included in a Bitcoin block that is verified as mined by the federation.
  - `REFUNDED` (`4`) means that the transfer was refunded to the user by the federation network (only transfers in
    state `NEW` can be refunded).
  - `RECLAIMED` (`5`) means that the transfer was reclaimed by the user (only transfers in state `NEW` can be
    reclaimed).
  - `NOT_APPLICABLE` (`0`) should not occur for any valid transfer, and is used internally to indicate that the
    transfer is not valid (i.e. it was never sent or accepted on the smart contract side)

The Bitcoin transaction hash that corresponds to the transfer (and potentially other transfers, since transfers are
batched in Bitcoin side) can be read from the `BitcoinTransferBatchSending` using (for example) the following logic:

- Read all new `BitcoinTransferBatchSending` events
- For each event, read the next `transferBatchSize` `BitcoinTransferStatusUpdated` events (these should all have
  `newStatus = SENDING`)
- If the `transferId` of any of these events corresponds to your transfer, the `bitcoinTxHash` of the
  `BitcoinTransferBatchSending` event is the Bitcoin transaction hash that transfers the BTC related to the transfer.

There are also utility functions `getTransfer`, `getTransfers`, `getTransferByTransferId` and `getTransfersByTransferId`
that can be used for querying current transfer state (but not for reading the Bitcoin transaction hash).

## Other utility functions

The functions `calculateCurrentFeeSatoshi`, `calculateCurrentFeeWei`, `minTransferSatoshi` and `maxTransferSatoshi` can
be used to determine fees and limits (min/max amounts) for new transfers.

The function `getNextNonce` returns the number of transfers already sent to a Bitcoin address. If this function returns
255, no new transfers can be sent to the address.

The function `isValidBtcAddress` can and should be used to check the validity of a Bitcoin address, but as said above,
it's not 100% accurate so it should be used as an additional check in addition to frontend validation.
