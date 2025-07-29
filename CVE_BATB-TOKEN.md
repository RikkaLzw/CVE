# CVE

## Vendor
Binance Smart Chain / Ethereum blockchain

## Vulnerability Type
Incorrect Access Control / Privilege Escalation

## Abstract
We found a vulnerability in the `BATBToken` Ethereum smart contract that can lead to unauthorized access and potential financial losses. This vulnerability is due to incorrect access control implementation in whitelist management functions, allowing unauthorized users to bypass transfer restrictions and fee mechanisms.

## Details
The `BATBToken` contract contains several functions with inadequate access control that can be exploited by unauthorized users:

1. **Public Whitelist Functions**: The `setColdWhiteList` and `setSpecialAddress` functions in the base `ERC20` contract are declared as `public virtual` without any access control modifiers, allowing anyone to call these functions and modify critical whitelist settings.

2. **Missing Access Control**: These functions should be restricted to contract owners or authorized addresses but currently lack proper `onlyOwner` or similar modifiers.

3. **Bypass Transfer Restrictions**: By manipulating the `coldWhiteList` mapping, attackers can bypass the cold time restrictions that prevent immediate transfers after purchases.

4. **Special Address Manipulation**: The `setSpecialAddress` function allows modification of special address status without authorization, potentially disrupting the dividend distribution mechanism.

## Attack Process

**Step 1** An attacker identifies the public `setColdWhiteList` function that lacks access control.

**Step 2** The attacker calls `setColdWhiteList(attackerAddress, true)` to add themselves to the cold whitelist.

**Step 3** The attacker can now bypass the cold time transfer restrictions in the `_beforeTokenTransfer` function, allowing immediate transfers without waiting for the `coldTime` period.

**Step 4** The attacker can also call `setSpecialAddress` to manipulate special address settings, potentially affecting dividend distributions or other special mechanisms.

**Step 5** This allows the attacker to perform rapid trading without restrictions, potentially engaging in front-running or other malicious activities.

## Other
**Inheritance Issue**: The vulnerability exists in the base `ERC20` contract where these functions are defined as `public virtual`, but the derived `BATBToken` contract doesn't override them with proper access control.

**Design Flaw**: The contract architecture allows critical administrative functions to be called by any user, which violates the principle of least privilege.

**Impact on Tokenomics**: Unauthorized whitelist manipulation can disrupt the intended tokenomics, including transfer restrictions, fee mechanisms, and dividend distributions.

## Conclusion
This vulnerability is due to incorrect access control implementation in the `BATBToken` contract's whitelist management functions. The impact of this vulnerability is significant as it allows unauthorized users to bypass critical transfer restrictions and manipulate special address settings. It's recommended that proper access control modifiers (such as `onlyOwner`) be implemented for all administrative functions to prevent unauthorized access and maintain the integrity of the token's economic model.
