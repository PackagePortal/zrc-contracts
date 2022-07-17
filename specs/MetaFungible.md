## MetaFungible Contract (extensions)

Currently, the [ZRC3 standard](https://github.com/Zilliqa/ZRC/blob/main/zrcs/zrc-3.md) Metafungible primarily contains "Cheque cashing" as a means of transfering tokens (primarily abstracting `TransferFrom`), I propose the extension "Meta claims", enabling the means of meta-transfering tokens via `Transfer`, implicitely gated & authorized by the `_sender` (as `Transfer` typically operates), ultimately enabling a hot-wallet or relayer token distribution.

#### 10. Claim

```ocaml
(* @dev: Enables a cheque signer to recieve/claim tokens from the `_sender` or relayer, while paying for the relayer's gas fee. *)
(* @dev: Balance of recipient will increase. Balance of `sender_ will decrease.                                                 *)
(* @param pubkey:      Public Key of the token_owner whose balance is decreased.                                                *)
(* @param to:          Address of the recipient whose balance is increased.                                                     *)
(* @param amount:      Amount of tokens to be sent.                                                                             *)
(* @param fee:         Reward taken from the cheque senders balance for the relayer.                                            *)
(* @param nonce:       A random value included in the cheque to make each unique.                                               *)
(* @param signature:   The signature of the cheque by the token owner to authorize spend.                                       *)
transition Claim(pubkey: ByStr20, to: ByStr20, amount: Uint128, fee: Uint128, nonce:Uint218, signature: ByStr64)
```

**Arguments:**

|        | Name        | Type      | Description                                                        |
| ------ | ----------- | --------- | ------------------------------------------------------------------ |
| @param | `pubkey`    | `ByStr33` | Public Key of the token_owner whose balance is to decrease.        |
| @param | `to`        | `ByStr20` | Address of the recipient whose balance is to increase.             |
| @param | `amount`    | `Uint128` | Amount of tokens to be sent.                                       |
| @param | `fee`       | `Uint128` | Reward taken from the cheque senders balance for the relayer.      |
| @param | `nonce`     | `Uint128` | A random value included in the cheque to make each unique.         |
| @param | `signature` | `ByStr64` | The signature of the cheque by the token owner to authorize spend. |


**Messages sent:**

|        | Name                        | Description                                           | Callback Parameters                                                                                                                                                                                                                                                                                  |
| ------ | --------------------------- | ----------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `_tag` | `RecipientAcceptChequeSend` | Dummy callback to prevent invalid recipient contract. | `initiator`: `ByStr20`, `sender` : `ByStr20`, `recipient`: `ByStr20`, `amount`: `Uint128`, where `initiator` is the address of an operator,`sender` is the address of the token_owner, `recipient` is the address of the recipient, and `amount` is the amount of fungible tokens to be transferred. |
| `_tag` | `ChequeSendSuccessCallBack` | Provide the relayer the status of the transfer.       | `initiator`: `ByStr20`, `sender` : `ByStr20`, `recipient`: `ByStr20`, `amount`: `Uint128`, where `initiator` is the address of an relayer,`sender` is the address of the token_owner, `recipient` is the address of the recipient, and `amount` is the amount of fungible tokens to be transferred.  |

**Events/Errors:**

|              | Name                | Description                | Event Parameters                                                                                                                                                                                                                                                                                                                                                                                                                         |
| ------------ | ------------------- | -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `_eventname` | `ClaimSuccess` | claim is successful.     | `initiator`: `ByStr20` which is the _sender's address, `sender`: `ByStr20` which is the token_owner's (and `_sender`'s) address, `recipient`: `ByStr20` which is the recipient's address, and `amount`: `Uint128` which is the amount of fungible tokens to be transferred.                                                                                                                                                                               |
| `_eventname` | `Error`             | Sending is not successful. | - emit `CodeChequeVoid` if the cheque submitted has already been transferred.<br>- emit `CodeInsufficientFunds` if the balance of the token_owner is lesser than the specified amount that is to be transferred.<br> - emit `CodeSignatureInvalid` if the signature of the cheque does not match the cheque parameters. <br> - emit `CodeInvalidSigner` if the signer of the metatransaction is not the owner of the tokens to be moved. |

Additionally, via batching:
```
transition BatchClaim(cheques: List (Pair (Pair (Pair ByStr33 ByStr20) (Pair Uint128 Uint128)) (Pair Uint128 ByStr64)))
  (* construct cheque list *)
  c =
    let map = @list_map (Pair (Pair (Pair ByStr33 ByStr20) (Pair Uint128 Uint128)) (Pair Uint128 ByStr64)) Cheque in
    map map_fn cheques;
  
  (* validate & distribute credits batched *)
  forall c ValidateAndClaim
end
```
