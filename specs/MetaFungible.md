## MetaFungible Contract (extensions)

#### meta-claims

| Name | Params | Description
|--|--|--|--|
|`Claim`|`pubkey: ByStr33, to: ByStr20, amount: Uint128, fee: Uint128, nonce:Uint128, signature: ByStr64`| Enables a cheque signer to recieve/claim tokens from the `_sender` or relayer, while paying for the relayer's gas fee. |