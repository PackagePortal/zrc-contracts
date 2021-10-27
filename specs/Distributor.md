## PORT Distributor Contract

### Initialization Parameters

| Name | Type | Description |
|--|--|--|
|`init_owner`| `ByStr20` | The initial owner of the token contract. |
|`token_contract` | `ByStr20` | The initial fungible smart-contract for distribution. |

### Mutables

| Name | Type | Initial Value | Description |
|--|--|--|--|
|`contract_owner`| `ByStr20` | `init_owner` | Current `owner` of the contract. |
|`pending_owner`| `Option ByStr20` | `None` | Current _pending_ new owner. |
|`merkle_roots`| `Map Uint32 ByStr32` | `Empty` | epoch num -> merkle root mapping |
|`claimed_leafs`| `Map Uint32 (Map ByStr32 Bool)` | `Empty` | epoch num -> leaf hash -> True mapping |
|`next_epoch_number`| `Uint32` | `0` | next epoch number |

### Transitions

#### Administrative

| Name | Params | Description | Requires admin privilege
|--|--|--|--|
|`RequestOwnershipTransfer`|`newOwner : ByStr20`| Allows the current `owner` to transfer control of the contract to a `newOwner`. | :heavy_check_mark: |
|`ConfirmOwnershipTransfer`| | Finalizes ownership transfer to `pendingOwner`. note `_sender` must be `pendingOwner`. |  |
|`RevokeOwnership`| | revoke contract ownership | :heavy_check_mark: |
|`WithdrawDistribution`|`amount: Uint128`| allows `owner` to withdraw from distribution funds stored on the contract. | :heavy_check_mark: |
|`SetMerkleRoot`|`epoch_number: Uint32, merkle_root: ByStr32`| Sets the merkle root for (the next) epoch | :heavy_check_mark: |
|`Claim`|`claim: Claim`| Claims from a distribution for an epoch by providing proof of inclusion. claim: The claim data, which contains the epoch number, leaf and proof for the claim. | |
|`ClaimMulti`| `account: ByStr20, claims: List (Pair (Pair Uint32 Uint128) (List ByStr32))`| Handles multiple claims at once | |

#### Migrating

| Name | Params | Description | Requires admin privilege
|--|--|--|--|
|`MigrateData`| `legacy_contract: ByStr20 with contract field merkle_roots: Map Uint32 ByStr32, field claimed_leafs: Map Uint32 (Map ByStr32 Bool) end` | Migrate mutable data from previous contract impl.  | :heavy_check_mark: |

