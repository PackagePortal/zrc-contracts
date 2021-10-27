## PORT TokenSwap Contract

### Initialization Parameters

| Name | Type | Description |
|--|--|--|
|`init_owner`| `ByStr20` | The initial owner of the contract. |
|`init_wallet` | `ByStr20` | The initial wallet to transfer the swapped funds from. |
|`init_fung_implementation`| `ByStr20` | The destination token contract. |
|`init_xport_implementation` | `ByStr20` | The source (xport in our case) token contract. |

### Mutables

| Name | Type | Initial Value | Description |
|--|--|--|--|
|`admin`| `ByStr20` | `init_owner` | Current `owner` of the contract. |
|`pending_owner`| `Option ByStr20` | `None` | Current _pending_ new owner. |
|`wallet`| `ByStr20` | `init_wallet` | Source wallet to transfer swapped funds from. |
|`fung_implementation`| `ByStr20` | `init_fung_implementation` | Destination token contract. |
|`xport_implementation`| `ByStr20` | `init_xport_implementation` | Source token contract |
|`min_token_amount`| `Uint32` | `50000` | Minimum allowable swappable amount |
|`denom`| `Uint32` | `100` | Denominator of the swap ratio (e.g in our case 100; 100 xport = 1 port) |
|`whitelistEnabled`| `Bool` | `True` | Whitelist honored for contract usage on/off switch toggle |
|`operators`| `Map ByStr20 Bool` | `init_owner: True` | Whitelist mapping Address -> Bool |

### Transitions

#### Administrative

| Name | Params | Description | Requires admin privilege
|--|--|--|--|
|`RequestOwnershipTransfer`|`newOwner : ByStr20`| Allows the current `owner` to transfer control of the contract to a `newOwner`. | :heavy_check_mark: |
|`ConfirmOwnershipTransfer`| | Finalizes ownership transfer to `pendingOwner`. note `_sender` must be `pendingOwner`. |  |
|`UpgradeFutureImplementationTo`| `newImplementation: ByStr20` | Update future impl. contract | :heavy_check_mark: |
|`UpdateOperator`| `recipient: ByStr20, allowed: Bool` | Update an operator’s (whitelist) access | :heavy_check_mark: |
|`UpgradeWalletOwner`| `newOwner: ByStr20` | Update source of swapped funds | :heavy_check_mark: |
|`ToggleWhitelist`| `enabled: Bool` | Toggle whitelist honoring | :heavy_check_mark: |
|`SetMin`| `min: Uint128` | Adjust minimum allowable amount to be swapped | :heavy_check_mark: |
|`SetDenom`| `denom: Uint128` | Adjust denomator ratio for swapping | :heavy_check_mark: |

#### Swapping

| Name | Params | Description
|--|--|--|
|`RecipientAcceptTransfer`| `sender: ByStr20, recipient: ByStr20, amount: Uint128` | Handles inbound swap requests | 

