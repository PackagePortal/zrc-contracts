## PORT Staking Contract

### Initialization Parameters

| Name | Type | Description |
|--|--|--|
|`init_owner`| `ByStr20` | The initial owner of the token contract. |
|`init_fungible` | `ByStr20` | The initial fungible smart-contract for stakes. |

### Mutables

| Name | Type | Initial Value |Description |
|--|--|--|--|
|`admin`| `ByStr20` | `init_owner` | Current `owner` of the contract. |
|`pending_owner`| `Option ByStr20` | `None` | Current _pending_ new owner. |
|`fungibleContract`| `ByStr20` | `init_fungble` | Current fungible contract for zrc transactions. |
|`stakingEnabled`| `Bool` | `True` | Staking on/off switch toggle |
|`whitelistEnabled`| `Bool` | `True` | Whitelist honored for contract usage on/off switch toggle |
|`futureContract`| `ByStr20` | `_this_address` | potential future impl. for migrations |
|`implementationContract`| `ByStr20` | `_this_address` | current contract’s address, or previous (for migrations) |
|`operators`| `Map ByStr20 Bool` | `init_owner: True` | whitelist |
|`pendingAddStake`| `Map ByStr20 (Pair (ByStr20) (Uint128))`| `Empty`| pending stake requests|
|`stakers`| `Map ByStr20 Uint128`| `Empty`| current list of stakes |
|`removeStaker`| `Map ByStr20 Uint128`| `Empty`| pending unstake requests |
|`removeBlock`| `Map ByStr20 BNum`| `Empty`| pending unstake requests by blocks remaining|
|`totalStaked`| `UInt128`| `0`| total staked amount |
|`totalStakers`| `UInt128`| `0`| total stakers amount |
|`culmulativeStakes`| `Map ByStr20 Bool`| `Empty`| list of stakers with automatic staking toggled |
|`pendingRewards`| `Map ByStr20 Uint128`| `Empty`| pending rewards for stakers withdrawable|
|`rewardBlocks`| `BNum`| `0`| block height for next reward distribution |
|`lastReward`| `BNum`| `0`| block height of last reward distribution |
|`rewardCadence`| `Uint128`| `2200`| staking reward distribution cadence, roughly a day |
|`minimumStake`| `Uint128`| `100`| minimum stake allowed |
|`maximumStake`| `Uint128`| `50,000`| maximum stake allowed |
|`tokensAvailable`| `Uint128`| `0`| rewards available to be distributed to stakers |
|`tokensLastAmount`| `Uint128`| `0`| last amount of `tokensAvailable` |
|`unstakePercent`| `Uint128`| `7`| pending unstake rewards |
|`rewardPercent`| `Uint128`| `15`| apy of stake rewards |
|`unbondingPeriod`| `Uint64`| `11000`| block duration for unbonding period |
|`penalty`| `Uint128`| `10`| penalty percent for instant withdrawals |
|`daily`| `Bool`| `True`| toggle for daily reward cadence (if false, assumes weekly) |
|`unstakeDays`| `Uint128`| `10`| unbondingPeriod in days to calculate fees |
|`penaltyRewardPerStaker`| `Uint128`| `0`| potential reward to stakers from the penalty of an instant withdraw |
|`totalUnstaked`| `Uint128`| `0`| total unstaked tokens |
|`cycleCount`| `Map ByStr20 Int32`| `Empty`| cycle (epoch) counts for each staker |
|`cycleAmount`| `Map ByStr20 Uint128`| `Empty`| cycle (epoch) rewards for each staker |
|`rewardCount`| `Int32`| `10`| cycle (epoch) amount for cycle bonus rewards |
|`rewardRate`| `Uint128`| `1`| bonus reward percentage for stakers once they hit `rewardCount`  |

### Transitions

#### Administrative

| Name | Params | Description | Requires admin privilege
|--|--|--|--|
|`RequestOwnershipTransfer`|`newOwner : ByStr20`| Allows the current `admin` to transfer control of the contract to a `newOwner`. | :heavy_check_mark: |
|`ConfirmOwnershipTransfer`| | Finalizes ownership transfer to `pendingOwner`. note `_sender` must be `pendingOwner`. |  |
|`OwnerWithdrawAdded`|`amount: Uint128`| owner can withdraw from tokens available | :heavy_check_mark: |
|`UpdateCycleEpochCount`|`cycle: Int32`| Update cycle rewards cadence | :heavy_check_mark: |
|`UpdateRewardBlocks`|`block: Uint128`| Update stake rewards cadence | :heavy_check_mark: |
|`UpdateUnstakeBlocks`|`blocks: Uint64`| Update unstake unbonding period | :heavy_check_mark: |
|`UpdateUnstakeRate`| `rate: Uint128` | Update unstake rate percentage. | :heavy_check_mark: |
|`UpdatePenalty`| `rate: Uint128, days: Uint128` | Update penalty rate & unstake days (`unbondingPeriod` in terms of days). | :heavy_check_mark: |
|`UpdateRewardPercent`| `rate: Uint128` | Update stake rewards apy percentage| :heavy_check_mark: |
|`ToggleDailyToWeekly`| | Toggle `daily` if reward cadence moves to weekly| :heavy_check_mark: |
|`UpdateMinimumStake`| `amount: Uint128` | update minimum stake required | :heavy_check_mark: |
|`UpdateMaximumStake`| `amount: Uint128` | update maximum stake allowed | :heavy_check_mark: |
|`UpgradeFungibleImplementationTo`| `newImplementation: ByStr20` | Update fungible impl. contract | :heavy_check_mark: |
|`UpgradeImplementationTo`| `newImplementation: ByStr20` | Update current impl. contract | :heavy_check_mark: |
|`UpgradeFutureImplementationTo`| `newImplementation: ByStr20` | Update future impl. contract | :heavy_check_mark: |
|`UpdateOperator`| `recipient: ByStr20, allowed: Bool` | Update an operator’s (whitelist) access | :heavy_check_mark: |
|`ToggleWhitelist`| `enabled: Bool` | toggle whitelist honoring | :heavy_check_mark: |
|`ToggleStaking`| `enabled: Bool` | toggle staking on/off switch | :heavy_check_mark: |

#### Staking

| Name | Params | Description |
|--|--|--|
|`RewardAll`| | Facilitates staking rewards distribution | 
|`RewardCycleRewards`| | Facilitates cycle bonus rewards distribution | 
|`ToggleCulmulative`| | Toggles cumulative staking for the `_sender` (need to fix misspelling) | 
|`RecipientAcceptTransfer`| `sender: ByStr20, recipient: ByStr20, amount: Uint128` | Handles inbound staking requests | 
|`TransferSuccessCallBack`| | | 
|`WithdrawRewards`| | Withdraws the pending rewards for `_sender` | 
|`RemoveStake`| `amount: Uint128` | Creates a pending unstake for `_sender` | 
|`WithdrawStake`| | Withdraws a pending stake once it’s unbonding is complete | 
|`InstantWithdrawal`| | Withdraws a pending stake instantly for a fee | 
|`AddPendingStakeAddForOther`| `address: ByStr20, amount: Uint128` | _Proxily_ adds a pending stake request on behalf of a staker | 
|`RemovePendingStakeAddForOther`| | `_sender` removes their _proxily_ added pending stake request | 



#### Migrating

| Name | Params | Description |
|--|--|--|
|`TransferStakeToNewContract`| `address : ByStr20` | Transfers a stake (of `address`) to a new address  |
|`TransferUnstakeToNewContract`| `address : ByStr20` | Transfers an unstake (of `address`) to a new address |
|`TransferStakeToNew`| `staker_address : ByStr20, staked_amount : Uint128, cycleAmount : Uint128, cycleCount : Int32` | Handles an incoming stake transfer  |
|`TransferUnstakeToNew`| `staker_address : ByStr20, unstaked_amount : Uint128, unstaked_blocks : BNum` | Handles an incoming unstake transfer  |
|`TransferStakeSuccessfulCallBack`| `address : ByStr20` | Handles finalizing a transferred stake on new contract  |
|`TransferUnstakeSuccessfulCallBack`| `address : ByStr20` | Handles finalizing a transferred unstake on new contract |

