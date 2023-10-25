# MaxButton

## Description

The Max Button is a user interface element that serves a specific purpose: when clicked, it selects the maximum amount. This component is particularly useful in scenarios where users need to quickly and easily choose the highest available value, such as their balance.

## Variants

### Max BTC

`(BTC balance - 0.000402832 BTC)` or `0` if the amount would go negative with this formula

```
Example 1:
User BTC balance is 1 BTC
Max is (1 - 0.000402832) = 0.999597168 BTC

Example 2:
User BTC balance is 0.0004 BTC
Max is (0.0004 - 0.000402832) = -0.000002832 -> 0 BTC
```

### Max token

Token balance

```
Example:
User token balance is 1000 DLLR
Max is 1000 DLLR
```

### Max AMM pool deposit

The lesser of `(Token balance, (BTC balance - 0.000402832) * BTC price denominated in the token according to the given AMM pool)`

```
Example:
User token balance is 1000 DLLR
User BTC balance is 0.01 BTC
BTC price according to DLLR/BTC pool is 30000 DLLR per 1 BTC
(0.01 - 0.000402832) * 30000 = 287.91504
287.91504 < 1000, max deposit is 287.91504 DLLR

Example:
User token balance is 1000 DLLR
User BTC balance is 0.05 BTC
BTC price according to DLLR/BTC pool is 30000 DLLR per 1 BTC
(0.05 - 0.000402832) * 30000 = 1487.91504
1487.91504 > 1000, max deposit is 1000 DLLR

```

### Default max borrow with BTC collateral:

The lesser of `(available liquidity in the lending pool, ((((BTC balance - 0.000402832) * (1 - Origination fee rate)) * BTC price in the loan asset) * Maximum initial loan-to-value ratio) - Prepaid interest amount)`

If the user hasn't changed the prepaid interest duration, Prepaid interest amount is calculated using the default prepaid interest duration specified in the Lend page frontend spec.

Prepaid interest amount formula:\
`(maxBorrow / (1 - interestRateForDuration)) - maxBorrow`\
\
maxBorrow is `((((BTC balance - 0.000402832) * (1 - Origination fee rate)) * BTC price denominated in the loan asset) * Maximum initial loan-to-value ratio)`\
\
interestRateForDuration is `(Borrow APR * (60 * 60 * 24 * Prepaid interest duration) / 31536000)`\
\
Putting it all together:\
`((((BTC balance - 0.000402832) * (1 - Origination fee rate)) * BTC price denominated in the loan asset) * Maximum initial loan-to-value ratio) / (1 - (Borrow APR * (60 * 60 * 24 * Prepaid interest duration) / 31536000)) - ((((BTC balance - 0.000402832) * (1 - Origination fee rate)) * BTC price in the loan asset) * Maximum initial loan-to-value ratio)`

```
Example:  
User BTC balance is 1 BTC  
Origination fee rate is 0.09%  
Borrow asset is DLLR  
BTC price is 30,000 DLLR  
Minimum initial collateral ratio is 150% = 0.6666666666666667 maximum initial loan-to-value (LTV) ratio 
APR is 5%  

Prepaid interest amount, given a default prepaid interest of 28 days: ((((1 - 0.000402832) * (1 - 0.0009)) * 30000) * 0.6666666666666667) / (1 - (0.05 * (60 * 60 * 24 * 28) / 31536000)) - ((((1 - 0.000402832) * (1 - 0.0009)) * 30000) * 0.6666666666666667) = 76.90740059231757 DLLR

Max is (((((1 - 0.000402832) * (1 - 0.0009)) * 30000) * 0.6666666666666667) - 76.90740059231757) = 19897.043210383683 DLLR

```

### Default max borrow with token collateral:

The lesser of `(available liquidity in the lending pool, (((Collateral token balance * (1 - Origination fee rate)) * Collateral asset price in the loan asset) * Maximum initial loan-to-value ratio) - Prepaid interest amount)`

If the user hasn't changed the prepaid interest duration, Prepaid interest amount is calculated using the default prepaid interest duration specified in the Lend page frontend spec.

Prepaid interest amount formula:\
`(maxBorrow / (1 - interestRateForDuration)) - maxBorrow`\
\
maxBorrow is `(((Collateral token balance * (1 - Origination fee rate)) * Collateral asset price denominated in the loan asset) * Maximum initial loan-to-value ratio)`\
\
interestRateForDuration is `(Borrow APR * (60 * 60 * 24 * Prepaid interest duration) / 31536000)`\
\
Putting it all together:\
`(((Collateral token balance * (1 - Origination fee rate)) * Collateral asset price denominated in the loan asset) * Maximum initial loan-to-value ratio) / (1 - (Borrow APR * (60 * 60 * 24 * Prepaid interest duration) / 31536000)) - (((Collateral token balance * (1 - Origination fee rate)) * Collateral asset price in the loan asset) * Maximum initial loan-to-value ratio)`

```
Example:  
User DLLR balance is 100000 DLLR  
Origination fee rate is 0.09%  
Collateral asset is DLLR
Borrow asset is BTC  
DLLR price is 0.00003333333333333 BTC per DLLR  
Minimum initial collateral ratio is 150% = 0.6666666666666667 maximum initial loan-to-value (LTV) ratio 
APR is 2%  

Prepaid interest amount, given a default prepaid interest of 28 days: (((100000 * (1 - 0.0009)) * 0.00003333333333333) * 0.6666666666666667) / (1 - (0.02 * (60 * 60 * 24 * 28) / 31536000)) - (((100000 * (1 - 0.0009)) * 0.00003333333333333) * 0.6666666666666667) = 0.003411602580519091 BTC

Max is ((((100000 * (1 - 0.0009)) * 0.00003333333333333) * 0.6666666666666667) - 0.003411602580519091) = 2.2168106196414814 BTC

```

### Max borrow:

The lesser of `(available liquidity in the lending pool, (((Collateral amount * (1 - Origination fee rate)) * Collateral asset price denominated in the loan asset) * Maximum initial loan-to-value ratio) - Prepaid interest amount)`

Prepaid interest amount formula:\
`(((Collateral amount * (1 - Origination fee rate)) * Collateral asset price denominated in the loan asset) * Maximum initial loan-to-value ratio) / (1 - (Borrow APR * (60 * 60 * 24 * Prepaid interest duration) / 31536000)) - (((Collateral amount * (1 - Origination fee rate)) * Collateral asset price in the loan asset) * Maximum initial loan-to-value ratio)`

```
Example 1:  
Collateral amount is 0.75 BTC 
Origination fee rate is 0.09%  
Borrow asset is DLLR  
BTC price is 30,000 DLLR  
Minimum initial collateral ratio is 150% = 0.6666666666666667 maximum initial loan-to-value (LTV) ratio 
APR is 5%  

Prepaid interest amount, given a prepaid interest duration of 100 days: (((0.75 * (1 - 0.0009)) * 30000) * 0.6666666666666667) / (1 - (0.05 * (60 * 60 * 24 * 100) / 31536000)) - (((0.75 * (1 - 0.0009)) * 30000) * 0.6666666666666667) = 208.14583333333394 DLLR

Max is ((((0.75 * (1 - 0.0009)) * 30000) * 0.6666666666666667) - 208.14583333333394) = 14778.354166666668 DLLR

```

### Default max borrow increase with BTC collateral:

The lesser of `(available liquidity in the lending pool, ((((Current collateral + (BTC balance - 0.000402832)) * (1 - Origination fee rate)) * Collateral asset price denominated in the loan asset) * Maximum initial loan-to-value ratio) - Prepaid interest amount - Current debt)`

Prepaid interest amount formula: &#x20;

`((((Current collateral + (BTC balance - 0.000402832)) * (1 - Origination fee rate)) * Collateral asset price denominated in the loan asset) * Maximum initial loan-to-value ratio) / (1 - (Borrow APR * (60 * 60 * 24 * Prepaid interest duration) / 31536000)) - ((((Current collateral + (BTC balance - 0.000402832)) * (1 - Origination fee rate)) * Collateral asset price in the loan asset) * Maximum initial loan-to-value ratio)`

```
Example 1:  
Current collateral balance is 1 BTC 
Current debt is 20000 DLLR
Current BTC balance is 0.1 BTC
Origination fee rate is 0.09%   
BTC price is 30,000 DLLR  
Minimum initial collateral ratio is 115% = 0.8695652173913043 maximum initial loan-to-value (LTV) ratio 
APR is 5%  

Prepaid interest amount, given a prepaid interest duration of 1 hour: ((((1 + (0.1 - 0.000402832)) * (1 - 0.0009))f * 30000) * 0.8695652173913043) / (1 - (0.05 * (60 * 60) / 31536000)) - ((((1 + (0.1 - 0.000402832)) * (1 - 0.0009)) * 30000) * 0.8695652173913043) = 0.16358156658316148 DLLR

Max is (((((1 + (0.1 - 0.000402832)) * (1 - 0.0009)) * 30000) * 0.8695652173913043) - 0.16358156658316148 - 20000) = 8659.163302315159 DLLR
```

### Default max borrow increase with token collateral:

The lesser of `(available liquidity in the lending pool, ((((Current collateral + Collateral token balance) * (1 - Origination fee rate)) * Collateral asset price denominated in the loan asset) * Maximum initial loan-to-value ratio) - Prepaid interest amount - Current debt)`

Prepaid interest amount formula: &#x20;

`((((Current collateral + Collateral token balance) * (1 - Origination fee rate)) * Collateral asset price denominated in the loan asset) * Maximum initial loan-to-value ratio) / (1 - (Borrow APR * (60 * 60 * 24 * Prepaid interest duration) / 31536000)) - ((((Current collateral + Collateral token balance) * (1 - Origination fee rate)) * Collateral asset price in the loan asset) * Maximum initial loan-to-value ratio)`

```
Example 1:  
Current collateral balance is 100000 DLLR 
Current debt is 2 BTC
Current DLLR balance is 10000 DLLR
Origination fee rate is 0.09%   
DLLR price is 0.00003333333333333 BTC per DLLR 
Minimum initial collateral ratio is 115% = 0.8695652173913043 maximum initial loan-to-value (LTV) ratio 
APR is 5%  

Prepaid interest amount, given a prepaid interest duration of 1 hour: ((((100000 + 10000) * (1 - 0.0009)) * 0.00003333333333333) * 0.8695652173913043) / (1 - (0.05 * (60 * 60) / 31536000)) - ((((100000 + 10000) * (1 - 0.0009)) * 0.00003333333333333) * 0.8695652173913043) = 0.00001818238820927 BTC

Max is ((((100000 + 10000) * (1 - 0.0009)) * 0.00003333333333333) * 0.8695652173913043) - 0.00001818238820927 - 2  = 1.18551804949553 BTC

```

### Max BTC collateral:

`(BTC balance - 0.000402832) * (1 - origination fee rate)`

```
Example:
BTC balance is 1 BTC
Origination fee rate is 0.09%
Max is (1 - 0.000402832) * (1 - 0.0009) = 0.9986975305488 BTC
```

### Max token collateral:

`Token balance * (1 - origination fee rate)`

```
Example:
Token balance is 100000 DLLR
Origination fee rate is 0.09%
Max is 100000 * (1 - 0.0009) =  99910.0 DLLR
```

### Max collateral withdrawal:

`Collateral balance - ((Total debt * Maintenance margin) / Collateral price denominated in loan asset)`

```
Example:
Collateral balance is 1 BTC
Total debt is 10000 DLLR
BTC price is 30000 DLLR
Maintenance margin is 115%
Max is 1 - ((10000 * 1.15) / 30000) = 0.6166666666666667 BTC
```

### Max BTC repayment:

The lesser of `(total debt - interest refund, BTC balance - 0.000402832 BTC, 0 if the amount would go negative with the previous formula)`

```
Example 1:
(Total debt - interest refund) is 0.5 BTC
User BTC balance is 1 BTC
Max is 0.5 BTC

Example 2:
(Total debt - interest refund) is 1 BTC
User BTC balance is 1 BTC
Max is (1 - 0.000402832) = 0.999597168 BTC

Example 3:
(Total debt - interest refund) is 1 BTC
User BTC balance is 0.0004 BTC
Max is (0.0004 - 0.000402832) = -0.000002832 -> 0 BTC
```

### Max token repayment:

The lesser of `(total debt - interest refund, token balance)`

```
Example 1:
(Total debt - interest refund) is 800 DLLR
User token balance is 1000 DLLR
Max is 800 DLLR

Example 2:
(Total debt - interest refund) is 1500 DLLR
User token balance is 1000 DLLR
Max is 1000 DLLR
```

### Max contract withdrawal:

The lesser of `(balance in the contract owned by or owed to the user, contract balance available liquidity)`

```
Example 1:
Balance in the contract owned by or owed to the user is 1 BTC
Contract balance available liquidity is 2 BTC
Max is 1 BTC

Example 2:
Balance in the contract owned by or owed to the user is 1 BTC
Contract balance available liquidity is 0.1 BTC
Max is 0.1 BTC
```

### Max aggregator withdrawal:

The lesser of `(token balance, aggregator balance + conversion fee)`

```
Example 1:
User token balance is 100000 DLLR
Aggregator balance is 200000 ZUSD
Max is 100000 DLLR

Example 2:
User token balance is 100000 DLLR
Aggregator balance is 20000 DOC and conversion fee is 5 DLLR
Max is 20005 DLLR
```

### Max bridge:

The lesser of `(token balance, aggregator balance, destination bridge max limit, destination chain bridge balance)`

```
Example 1:
User token balance is 100000 XUSD
Aggregator balance is 150000 USDT
Destination bridge max limit is 250000 USDT
Destination chain bridge balance is 150000
Max is 100000 USDT

Example 2:
User token balance is 100000 XUSD
Aggregator balance is 90000 USDT
Destination bridge max limit is 250000 USDT
Destination chain bridge balance is 150000
Max is 90000 USDT

Example 3:
User token balance is 300000 XUSD
Aggregator balance is 500000 USDT
Destination bridge max limit is 250000 USDT
Destination chain bridge balance is 300000
Max is 250000 USDT

Example 4:
User token balance is 225000 XUSD
Aggregator balance is 300000 USDT
Destination bridge max limit is 250000 USDT
Destination chain bridge balance is 150000
Max is 150000 USDT
```

### Max FastBTC:

The lesser of `(FastBTC max limit, BTC balance - 0.000402832 BTC, 0 if the amount would go negative with the previous formula)`

```
Example 1:
FastBTC max limit is 3 BTC
User BTC balance is 4 BTC
Max is (4 - 0.000402832) = 3.999597168 -> 3 BTC

Example 2:
FastBTC max limit is 3 BTC
User BTC balance is 1 BTC
Max is (1 - 0.000402832) = 0.999597168 BTC

FastBTC max limit is 3 BTC
User BTC balance is 0.0004 BTC
Max is (0.0004 - 0.000402832) = -0.000002832 -> 0 BTC
```
