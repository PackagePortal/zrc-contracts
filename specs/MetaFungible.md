## MetaFungible Contract (extensions)

#### meta-claims

| Name | Params | Description
|--|--|--|
|`Claim`|`pubkey: ByStr33, to: ByStr20, amount: Uint128, fee: Uint128, nonce:Uint128, signature: ByStr64`| Enables a cheque signer to recieve/claim tokens from the `_sender` or relayer, while paying for the relayer's gas fee. |

Currently, the [ZRC3 standard](https://github.com/Zilliqa/ZRC/blob/main/zrcs/zrc-3.md) Metafungible primarily contains "Cheque cashing" as a means of transfering tokens (primarily abstracting `TransferFrom`), I propose the extension "Meta claims", enabling the means of meta-transfering tokens via `Transfer`, implicitely gated & authorized by the `_sender` (as `Transfer` typically operates), ultimately enabling a hot-wallet or relayer token distribution.
