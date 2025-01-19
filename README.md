## 1. [M] Issues with unsafe ERC20 operations
https://solodit.cyfrin.io/issues/m-02-issues-with-unsafe-erc20-operations-pashov-audit-group-none-overload-markdown

This bug report discusses a medium-severity issue in a farm contract that could potentially lead to the loss of funds for honest users. The issue is caused by the use of non-safe versions of ERC20 interactions, which could be exploited by malicious users. This issue could be exploited by malicious users who, despite not having sufficient funds, could initiate a deposit that falsely appears successful, leading to incorrectly minted shares. Subsequently, these users could burn these shares to withdraw actual funds from the contract, effectively draining assets from honest users. The report recommends implementing additional verification steps to confirm that the token address is a contract.

## 2. [L] Contracts are incompatible with ERC20 tokens with fee
https://solodit.cyfrin.io/issues/contracts-are-incompatible-with-erc20-tokens-with-fee-trailofbits-none-wonderland-prophet-pdf

The contracts are meant to interface with ERC20 tokens. However, several ERC20 tokens do not fully comply with the ERC20 standard, meaning the contract will not work
with such tokens. For example, some ERC20 tokens take a fee on transfer, which means the actual amount received by a contract will not be equal to the amount of tokens sent.

## 3. [M] ERC20.approve Used Instead of Safe Approvals, Causing Pool Failures with Some ERC20s
https://solodit.cyfrin.io/issues/m-1-erc20approve-used-instead-of-safe-approvals-causing-pool-failures-with-some-erc20s-sherlock-teller-lender-groups-update-audit-git

The function acceptFundsForAcceptBid, which handles accepting a loan bid, transferring funds to the borrower, and securing collateral, uses the ERC20.approve function. However, this causes issues with some ERC20 tokens, such as USDT and USDC, which do not return a value. This contradicts the protocol's intentions to support all mainstream tokens and breaks the functionality of the protocol. The root cause is the use of approve instead of safeApprove, which does not work with tokens like USDT on the main net. This bug makes the protocol unusable with these tokens and can cause the pool to fail. To mitigate this, Use `safeApprove` instead of `approve`

## 4. [M] ERC20 tokens without return value will DoS reward claiming
https://solodit.cyfrin.io/issues/m-03-erc20-tokens-without-return-value-will-dos-reward-claiming-pashov-audit-group-none-interpol_2024-12-24-markdown

The application has a bug where some tokens do not return a `bool` value when using the `transfer` function, which is causing errors in several functions. This is due to the ERC20 interface expecting a `bool` return value, causing the functions to revert. The affected functions are `withdrawERC20` in `HoneyLocker`, `unstake` in `BeradromeAdapter`, `unstake` in `BGTStationAdapter`, `unstake` in `InfraredAdapter`, and `unstake` in `KodiakAdapter`. Even functions that have a `try-catch` block are still experiencing issues. This can be resolved by upgrading the contract, but users may experience a temporary freeze of their assets until the fix is implemented. It is likely that this bug will occur due to the widespread use of tokens that do not strictly follow the ERC20 standard. To fix this, the `SafeTransfer` library can be used, or the adapters can be modified to handle the safe transfer functionality and not revert on failure.

## 5. [L] `SafeERC20` is not used
https://solodit.cyfrin.io/issues/safeerc20-is-not-used-mixbytes-none-dia-markdown

The protocol uses a SafeERC20 Library, which is a part of OpenZeppelin's contracts and provides "safe" wrappers around the standard ERC-20 functions such as transfer, transferFrom, etc. These wrappers ensure additional safety by handling potential failures from ERC-20 tokens that do not strictly follow the standard (e.g., tokens that return false instead of reverting on failure).
The Prestaking contract imports the SafeERC20 library. While the SafeERC20 library is imported, its functions are never used in the contract. Instead, the contract uses the standard ERC-20 functions, such as transfer and transferFrom, directly.

## 6. [L] Use safeTransfer for ERC20 transfers
https://solodit.cyfrin.io/issues/l-21-use-safetransfer-for-erc20-transfers-pashov-audit-group-none-stationx-markdown

Some tokens may not be [compatible](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#missing-return-values) with the `IERC20::transfer()` / `IERC20::transferFrom()` interfaces and the transaction would revert.
Example: The `airDropToken()` function in `zairdrop` uses `transferFrom()`:
```solidity
IERC20(tokenAddr).transfer(_owner, balance);
```
Consider using `safeTransferFrom` in these cases.

## 7. [L] Use safeTransfer for ERC20 transfers

