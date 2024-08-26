---
description: >-
  When a user borrows from their Line of Credit, ZUSD tokens are minted to their own address, and a debt is recorded on the Line of Credit.
---

# [Borrower (Trove) Operations](https://github.com/DistributedCollective/zero/blob/main/README.md#borrower-trove-operations---borroweroperationssol) - `BorrowerOperations.sol`  

When a user borrows from their Line of Credit, ZUSD tokens are minted to their own address, and a debt is recorded on the Line of Credit. Conversely, when they repay their Line of Credit’s ZUSD debt, ZUSD is burned from their address, and the debt on their Line of Credit is reduced.

```solidity
openTrove(uint _maxFeePercentage, uint _ZUSDAmount, address _upperHint, address _lowerHint)
```
Payable function that creates a Line of Credit for the caller with the requested debt, and the RBTC received as collateral. Successful execution is conditional mainly on the resulting collateralization ratio which must exceed the minimum (110% in Normal Mode, 150% in Recovery Mode). In addition to the requested debt, extra debt is issued to pay the issuance fee, and cover the gas compensation. The borrower has to provide a `_maxFeePercentage` that he/she is willing to accept in case of a fee slippage, i.e. when a redemption transaction is processed first, driving up the issuance fee. 

```solidity
addColl(address _upperHint, address _lowerHint)
``` 
Payable function that adds the received RBTC to the caller's active Line of Credit.

```solidity
withdrawColl(uint _amount, address _upperHint, address _lowerHint)
``` 
Withdraws `_amount` of collateral from the caller’s Line of Credit. Executes only if the user has an active Line of Credit, the withdrawal would not pull the user’s Line of Credit below the minimum collateralization ratio, and the resulting total collateralization ratio of the system is above 150%. 

```solidity
function withdrawZUSD(uint _maxFeePercentage, uint _ZUSDAmount, address _upperHint, address _lowerHint)
``` 
Issues `_amount` of ZUSD from the caller’s Line of Credit to the caller. Executes only if the Line of Credit's collateralization ratio would remain above the minimum, and the resulting total collateralization ratio is above 150%. The borrower has to provide a `_maxFeePercentage` that they are willing to accept in case of a fee slippage i.e. when a redemption transaction is processed first, driving up the borrowing fee.

```solidity
repayZUSD(uint _amount, address _upperHint, address _lowerHint)
``` 
Repay `_amount` of ZUSD to the caller’s Line of Credit, subject to leaving 20 ZUSD debt in the Line of Credit (which corresponds to the 20 ZUSD gas compensation).

```solidity
_adjustTrove(address _borrower, uint _collWithdrawal, uint _debtChange, bool _isDebtIncrease, address _upperHint, address _lowerHint, uint _maxFeePercentage)
``` 
Enables a borrower to simultaneously change both their collateral and debt, subject to all the restrictions that apply to individual increases/decreases of each quantity with the following particularity: if the adjustment reduces the collateralization ratio of the Line of Credit, the function only executes if the resulting total collateralization ratio is above 150%. The borrower has to provide a `_maxFeePercentage` that they are willing to accept in case of a fee slippage i.e. when a redemption transaction is processed first, driving up the borrowing fee. The parameter is ignored if the debt is not increased with the transaction.

```solidity
closeTrove()
```
: allows a borrower to repay all debt, withdraw all their collateral, and close their Line of Credit. Requires the borrower to have a ZUSD balance sufficient to repay their Line of Credit's debt, excluding gas compensation - i.e. `(debt - 20)` ZUSD.

```solidity
claimCollateral(address _user)
```
: when a borrower’s Line of Credit has been fully redeemed from and closed, or liquidated in Recovery Mode with a collateralization ratio above 110%, this function allows the borrower to claim their RBTC collateral surplus that remains in the system (collateral - debt upon redemption; collateral - 110% of the debt upon liquidation).

## Hint Helper Functions - `HintHelpers.sol`

```solidity
function getApproxHint(uint _CR, uint _numTrials, uint _inputRandomSeed)
```
: helper function, returns a positional hint for the sorted list. Used for transactions that must efficiently re-insert a Line of Credit  to the sorted list.

```solidity
getRedemptionHints(uint _ZUSDamount, uint _price, uint _maxIterations)
```
: helper function specifically for redemptions. Returns three hints:

- `firstRedemptionHint` is a positional hint for the first redeemable Line of Credit (i.e. Line of Credit with the lowest ICR >= MCR).
- `partialRedemptionHintNICR` is the final nominal ICR of the last Line of Credit after being hit by partial redemption, or zero in case of no partial redemption.
- `truncatedZUSDamount` is the maximum amount that can be redeemed out of the the provided `_ZUSDamount`. This can be lower than `_ZUSDamount` when redeeming the full amount would leave the last Line of Credit of the redemption sequence with less debt than the minimum allowed value.

The number of Lines of Credit to consider for redemption can be capped by passing a non-zero value as `_maxIterations`, while passing zero will leave it uncapped.

## Supplying Hints to Line of Credit operations

Troves in Zero are recorded in a sorted doubly linked list, sorted by their NICR, from high to low. NICR stands for the nominal collateral ratio that is simply the amount of collateral (in RBTC) multiplied by 100e18 and divided by the amount of debt (in ZUSD), without taking the RBTC:USD price into account. Given that all Lines of Credit are equally affected by RBTC price changes, they do not need to be sorted by their real ICR.

All Line of Credit operations that change the collateralization ratio need to either insert or reinsert the Line of Credit to the `SortedTroves` list. To reduce the computational complexity (and gas cost) of the insertion to the linked list, two ‘hints’ may be provided.

A hint is the address of a Line of Credit with a position in the sorted list close to the correct insert position.

All Line of Credit operations take two ‘hint’ arguments: a `_lowerHint` referring to the `nextId` and an `_upperHint` referring to the `prevId` of the two adjacent nodes in the linked list that are (or would become) the neighbors of the given Line of Credit. Taking both direct neighbors as hints has the advantage of being much more resilient to situations where a neighbor gets moved or removed before the caller's transaction is processed: the transaction would only fail if both neighboring lines of credit are affected during the pendency of the transaction.

The better the ‘hint’ is, the shorter the list traversal, and the cheaper the gas cost of the function call. `SortedList::findInsertPosition(uint256 _NICR, address _prevId, address _nextId)` that is called by the Line of Credit operation firsts check if `prevId` is still existant and valid (larger NICR than the provided `_NICR`) and then descends the list starting from `prevId`. If the check fails, the function further checks if `nextId` is still existant and valid (smaller NICR than the provided `_NICR`) and then ascends list starting from `nextId`. 

The `HintHelpers::getApproxHint(...)` function can be used to generate a useful hint pointing to a Line of Credit relatively close to the target position, which can then be passed as an argument to the desired Line of Credit operation or to `SortedTroves::findInsertPosition(...)` to get its two direct neighbors as ‘exact‘ hints (based on the current state of the system).

`getApproxHint(uint _CR, uint _numTrials, uint _inputRandomSeed)` randomly selects `numTrials` amount of Lines of Credit, and returns the one with the closest position in the list to where a Line of Credit with a nominal collateralization ratio of `_CR` should be inserted. It can be shown mathematically that for `numTrials = k * sqrt(n)`, the function's gas cost is with very high probability worst case `O(sqrt(n)) if k >= 10`. For scalability reasons (Infura is able to serve up to ~4900 trials), the function also takes a random seed `_inputRandomSeed` to make sure that calls with different seeds may lead to different results, allowing for better approximations through multiple consecutive runs.

**Line of Credit operation without a hint**

1. User performs Line of Credit operation in their browser.
2. Call the Line of Credit operation with `_lowerHint = _upperHint = userAddress`.

Gas cost will be worst case `O(n)`, where n is the size of the `SortedTroves` list.

**Line of Credit operation with hints**

1. User performs Line of Credit operation in their browser.
2. The frontend computes a new collateralization ratio locally, based on the change in collateral and/or debt.
3. Call `HintHelpers::getApproxHint(...)`, passing it the computed nominal collateralization ratio. Returns an address close to the correct insert position.
4. Call `SortedTroves::findInsertPosition(uint256 _NICR, address _prevId, address _nextId)`, passing it the same approximate hint via both `_prevId` and `_nextId` and the new nominal collateralization ratio via `_NICR`. 
5. Pass the ‘exact‘ hint in the form of the two direct neighbors, i.e. `_nextId` as `_lowerHint` and `_prevId` as `_upperHint`, to the Line of Credit operation function call. (Note that the hint may become slightly inexact due to pending transactions that are processed first, though this is gracefully handled by the system that can ascend or descend the list as needed to find the right position.)

Gas cost of steps 2-4 will be free, and step 5 will be `O(1)`.

Hints allow cheaper Line of Credit operations for the user, at the expense of a slightly longer time to completion, due to the need to await the result of the two read calls in steps 1 and 2 - which may be sent as JSON-RPC requests to Infura, unless the Frontend Operator is running a full RSK node.

### Example Borrower Operations with Hints

#### Opening a Line of Credit 
```javascript
  const toWei = web3.utils.toWei
  const toBN = web3.utils.toBN

  const ZUSDAmount = toBN(toWei('2500')) // borrower wants to withdraw 2500 ZUSD
  const RBTCColl = toBN(toWei('5')) // borrower wants to lock 5 RBTC collateral

  // Call deployed Line of Credit Manager contract to read the liquidation reserve and latest borrowing fee
  const liquidationReserve = await Line of Credit Manager.ZUSD_GAS_COMPENSATION()
  const expectedFee = await Line of Credit Manager.getBorrowingFeeWithDecay(ZUSDAmount)
  
  // Total debt of the new Line of Credit = ZUSD amount drawn, plus fee, plus the liquidation reserve
  const expectedDebt = ZUSDAmount.add(expectedFee).add(liquidationReserve)

  // Get the nominal NICR of the new Line of Credit 
  const _1e20 = toBN(toWei('100'))
  let NICR = RBTCColl.mul(_1e20).div(expectedDebt)

  // Get an approximate address hint from the deployed HintHelper contract. Use (15 * number of Lines of Credit) trials 
  // to get an approx. hint that is close to the right position.
  let numTroves = await sortedTroves.getSize()
  let numTrials = numTroves.mul(toBN('15'))
  let { 0: approxHint } = await hintHelpers.getApproxHint(NICR, numTrials, 42)  // random seed of 42

  // Use the approximate hint to get the exact upper and lower hints from the deployed SortedTroves contract
  let { 0: upperHint, 1: lowerHint } = await sortedTroves.findInsertPosition(NICR, approxHint, approxHint)

  // Finally, call openTrove with the exact upperHint and lowerHint
  const maxFee = '5'.concat('0'.repeat(16)) // Slippage protection: 5%
  await borrowerOperations.openTrove(maxFee, ZUSDAmount, upperHint, lowerHint, { value: RBTCColl })
```

#### Adjusting a Line of Credit 
```javascript
  const collIncrease = toBN(toWei('1'))  // borrower wants to add 1 RBTC
  const ZUSDRepayment = toBN(toWei('230')) // borrower wants to repay 230 ZUSD

  // Get Line of Credit's current debt and collateral
  const {0: debt, 1: coll} = await Line of Credit Manager.getEntireDebtAndColl(borrower)
  
  const newDebt = debt.sub(ZUSDRepayment)
  const newColl = coll.add(collIncrease)

  NICR = newColl.mul(_1e20).div(newDebt)

  // Get an approximate address hint from the deployed HintHelper contract. Use (15 * number of Lines of Credit) trials 
  // to get an approx. hint that is close to the right position.
  numTroves = await sortedTroves.getSize()
  numTrials = numTroves.mul(toBN('15'))
  ({0: approxHint} = await hintHelpers.getApproxHint(NICR, numTrials, 42))

  // Use the approximate hint to get the exact upper and lower hints from the deployed SortedTroves contract
  ({ 0: upperHint, 1: lowerHint } = await sortedTroves.findInsertPosition(NICR, approxHint, approxHint))

  // Call adjustTrove with the exact upperHint and lowerHint
  await borrowerOperations.adjustTrove(maxFee, 0, ZUSDRepayment, false, upperHint, lowerHint, {value: collIncrease})
```

