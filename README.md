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

## 7. [M] `RewardsClaimer` Uses Transfer Functions that Do Not Check The Return Value
https://solodit.cyfrin.io/issues/m-05-rewardsclaimer-uses-transfer-functions-that-do-not-check-the-return-value-shieldify-none-pear-v2-markdown

The `RewardsClaimer.sol` contract here has a bug that can cause reward tokens to not be transferred successfully. This can happen because the contract does not check if the transfer was successful or not. This can affect the distribution and claiming of rewards. The bug is located in the `RewardsClaimer.sol` file and can be fixed by using the `safeTransfer()` and `safeTransferFrom()` functions from OpenZeppelin.

## 8. [M] Missing check for supported asset
https://solodit.cyfrin.io/issues/m-01-missing-check-for-supported-asset-pashov-audit-group-none-ionprotocol-july-markdown

This bug report discusses a vulnerability in the `depositAndBridge` function which could potentially allow an attacker to use a custom token as the deposit asset, leading to the creation of shares that are not backed by approved assets. This bug is currently not exploitable, but could become a concern in certain situations where the protocol has deprecated a supported asset but has not removed the corresponding price feeds. To fix this issue, it is recommended to add a check in the `depositAndBridge` function to ensure that the deposit asset is supported by the Vault.

## 9. [M] Protocol will not be compatible with commonly used ERC20 tokens
https://solodit.cyfrin.io/issues/m-01-protocol-will-not-be-compatible-with-commonly-used-erc20-tokens-pashov-none-pump-markdown

This bug report discusses an issue with the code in the `PumpV1` contract that could potentially cause problems with certain tokens. The code calls the `transferFrom` method of the `ERC20` implementation, which requires a `bool` return value. However, some tokens like `USDT` and `BNB` do not have this return value, which could break the integration with the application. Additionally, the code relies on the `baseToken` having exactly 18 decimals, which may not be the case for commonly used tokens like `USDC` and `USDT` (which have 6 decimals) or `YAM-V2` (which has 24 decimals). To address this issue, the report recommends using OpenZeppelin's `SafeERC20` library and its `safeTransferFrom` method, as well as scaling all token amounts to 18 decimals or explicitly checking for non-18 decimals tokens in the code.

## 10. [M] Lack of support for ERC20 token that is not 18 decimals
https://solodit.cyfrin.io/issues/m-28-lack-of-support-for-erc20-token-that-is-not-18-decimals-code4rena-astaria-astaria-git

This bug report is about the lack of support for ERC20 tokens with decimals other than 18 in the PublicVault.sol implementation. The code in the PublicVault.sol is hardcoded to 18 decimals, which means that tokens with a different decimal precision will not function correctly. This is an issue because it can cause confusion and complicate integration across front-ends and other off-chain users. To illustrate this, the example of USDC is used, which is a token with 6 decimals. If this token is used, the convertToAssets() function will be broken. This is because the totalSupply is in 18 deimals, but the totalAssets is in 6 decimals, but the totalSupply should be 6 decimals as well to match the underlying token precision. In addition, the logic for liquidation ratio calculation is also broken because the hardcoded 1e18 is used. The recommended mitigation steps for this bug are for the protocol to make the PublicVault.sol decimal match the underlying token decimals

## 11. [M] Protocol does not work with ERC20 tokens that have a mechanism for balance modifications outside of transfers
https://solodit.cyfrin.io/issues/m-07-protocol-does-not-work-with-erc20-tokens-that-have-a-mechanism-for-balance-modifications-outside-of-transfers-pashov-none-zerem-markdown

This bug report is about how tokens with arbitrary balance modifications, such as Ampleforth-style rebasing tokens, can cause funds to become stuck in the Zerem system. This happens when a protocol integrated with Zerem calls the `Zerem::transferTo` method and the amount sent is greater than the `lockThreshold`, which causes the funds to be locked. If a rebasing of the token balances happens before the `unlockPeriodSec` passes, the cached amount may be more than the actual amount held in the Zerem protocol, resulting in the funds getting stuck. Additionally, if the rebasing of the tokens increases the protocol balance, then the excess tokens will be stuck in it. The impact of this bug is considered to be of Medium severity since it requires a special type of ERC20 token. The recommendation is to either allow partial unlock of funds or document that the protocol does not support such tokens, as well as adding functionality to rescue excess funds out of the Zerem protocol. The client has acknowledged this bug report.

## 12. [M]Re-entrancy risk in UniERC20
https://solodit.cyfrin.io/issues/re-entrancy-risk-in-unierc20-consensys-1inch-exchange-aggregationrouter-v5-markdown

A bug was identified in the UniERC20 library, a general library for facilitating transfers of any ERC20 or native coin assets. The library utilizes the `.call(){value:X}` method of transferring chain native assets, such as ETH, which introduces a large risk in the form of re-entrancy attacks. To guard against this, 1inch team remediated the issue by forwarding a limited amount of gas to guard against complex execution at the target. However, it is recommended that instead of using the `.call()` method, projects utilizing the library should implement `transfer()` or `send()` methods for transferring chain native assets, such as ETH.

## 13. [H] Reentrancy risks in interactions with bidAsset
https://solodit.cyfrin.io/issues/reentrancy-risks-in-interactions-with-bidasset-trailofbits-advanced-blockchain-pdf

This bug report is about a data validation issue in the AavePool.sol contract. It is classified as a medium difficulty issue. The bug is related to reentrancy patterns that may be exploitable if the bidAsset ERC20 token is set to an asset with a callback mechanism. The \_bid function's use of the reentrant safeTransferFrom function allows the caller to execute a double transfer. This could be used by an attacker to exploit the reentrancy and double their profits. To solve the issue in the short term, the developers should either ensure that none of the tokens have callback features, or follow the checks-effects-interactions pattern.

## 14. [H] Using underlying as the input `amount` instead of normalized balances
https://solodit.cyfrin.io/issues/c-01-using-underlying-as-the-input-amount-instead-of-normalized-balances-pashov-audit-group-none-ionprotocol-markdown

The report states that there is a bug in the `IonPool` contract which inherits from `RewardToken`. This bug can have a high impact and is likely to occur. The bug occurs during token transfers, where the contract accepts the underlying amount as input and converts it to a normalized amount using a factor. However, this conversion does not take into account the current supply factor, which can result in incorrect amounts being transferred. Additionally, since `RewardToken` is now a non-rebasing token, the `balanceOf` function will return the normalized balances instead of the underlying balances, which can cause issues for users and third-party integrators. To fix this bug, it is recommended to directly transfer the normalized balances instead of converting them from the underlying amount.

## 15. [H] Malicious users can steal reward tokens via re-entrancy attack
https://solodit.cyfrin.io/issues/h-8-malicious-users-can-steal-reward-tokens-via-re-entrancy-attack-sherlock-notional-leveraged-vaults-pendle-pt-and-vault-incentives-git

This bug report discusses a vulnerability in which malicious users can steal reward tokens through a re-entrancy attack. The vulnerability is caused by a function that updates account rewards during the redemption of vault shares. The function does not properly check for re-entrancy, allowing an attacker to repeatedly claim reward tokens and receive more than they are entitled to. This can result in the theft of reward tokens from the vault and other shareholders. 
 
