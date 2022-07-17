## MetaFungible Contract (extensions)

#### meta-claims

| Name | Params | Description
|--|--|--|
|`Claim`|`pubkey: ByStr33, to: ByStr20, amount: Uint128, fee: Uint128, nonce:Uint128, signature: ByStr64`| Enables a cheque signer to recieve/claim tokens from the `_sender` or relayer, while paying for the relayer's gas fee. |

Contracy to the [ZRC3 standard](https://github.com/Zilliqa/ZRC/blob/main/zrcs/zrc-3.md), where Metafungible primarily contains "Cheque cashing" as a means of transfering tokens (primarily abstracting `TransferFrom`), I propose the extension "Meta claims", enabling the means of transfering tokens via `Transfer`, implicitely gated by the `_sender` (as `Transfer` typically operates).
