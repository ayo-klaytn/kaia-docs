# Intrinsic Gas

Gas is a sum of `IntrinsicGas` and `ExecutionGas`. In here, we would focus on how `IntrinsicGas` is organized.

:::note

Intrinsic gas related hardfork changes can be found at the bottom of this page. Go to [Hardfork Changes](#hardfork-changes).

:::

## Overview

A transaction's `intrinsicGas` can be calculated by adding up the next four factors.

```
IntrinsicGasCost = KeyCreationGas + KeyValidationGas + PayloadGas + TxTypedGas
```

- `PayloadGas` is calculated based on the size of the data field in the tx.
- `KeyCreationGas` is calculated when the transaction registers new keys. Only applicable in `accountUpdate` transaction.
- `KeyValidationGas` is calculated based on the number of signatures.
- `TxTypedGas` is defined based on the transaction types.

Before we get into the detail, keep in mind that not all key types apply the keyGases (`KeyCreationGas` and `KeyValidationGas`).

| Key Type  | Are those keyGases applicable?     |
| :-------- | :--------------------------------- |
| Nil       | No                                 |
| Legacy    | No                                 |
| Fail      | No                                 |
| Public    | Yes                                |
| MultiSig  | Yes                                |
| RoleBased | Depending on key types in the role |

## KeyCreationGas <a id="keycreationgas"></a>

The KeyCreationGas is calculated as `(number of registering keys) x TxAccountCreationGasPerKey (20000)`.\
The KeyCreationGas is calculated as `(number of registering keys) x TxAccountCreationGasPerKey (20000)`.\
Please keep in mind that Public key type always has only one registering key, so the gas would be always 20000.

## 키 검증 가스 <a id="keyvalidationgas"></a>

`KeyValidationGas` is calculated as `(number of signatures - 1) x TxValidationGasPerKey(15000)`.\
`KeyValidationGas` is calculated as `(number of signatures - 1) x TxValidationGasPerKey(15000)`.\
Please keep in mind that Public key type always has only one signature key, so the gas would be always zero.

A Kaia transaction can also have a feePayer, so the total KeyValidationGas is like this.

```
KeyValidationGas =  (KeyValidationGas for a sender) + (KeyValidationGas for a feePayer)
```

## 페이로드 가스 <a id="payloadgas"></a>

Basically, `PayloadGas` is charged with `number_of_bytes_of_tx_input x TxDataGas (100)`.

In the case of a transaction creating contract, an additional charge of `number_of_words_of_initcode x InitCodeWordGas (2)` is applied. It is effective since Shanghai hardfork.

## TxTypedGas <a id="txtypedgas"></a>

There are three types of transactions in klaytn; `base`, `feeDelegated`, and `feeDelegatedWithFeeRatio`.

For example,

- TxTypeValueTransfer is the `base` type of the valueTransaction transaction.
- TxTypeFeeDelegatedValueTransfer is a `feeDelegated` type of the valueTransfer transaction.
- TxTypeFeeDelegatedValueTransferWithRatio is a `feeDelegatedWithRatio` type of the valueTransfer transaction.

This is important when calculating TxTypedGas:

- First, check the TxType is `feeDelegated` or `feeDelegatedWithFeeRatio`.
    - If the TxType is `feeDelegated`, add `TxGasFeeDelegated(10000)` to TxTypedGas
    - If the TxType is `feeDelegatedWithFeeRatio`, add `TxGasFeeDelegatedWithRatio (15000)` to TxTypedGas
- Second, check the transaction creates contract or not.
    - If the transaction creates contract, add `TxGasContractCreation (53000)` to TxTypedGas.
    - Otherwise, add `TxGas (21000)` to TxTypedGas.

For example,

- If it's legacyTransaction and creates contract, the TxTypedGas would be `0 + TxGasContractCreation(53000)`.
- If it's TxTypeFeeDelegatedValueTransfer, the TxTypedGas would be `TxGasFeeDelegated(10000) + TxGas (21000)`
- If it's TxTypeFeeDelegatedSmartContractDeployWithRatio, the TxTypedGas would be `TxGasFeeDelegatedWithRatio (15000) + TxGasContractCreation (53000)`

## Hardfork changes

| Hardfork     | Change                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Shanghai EVM | limit and meter initcode when calculating intrinsicGas<br/>- started to add 2 gas per word of the initcode                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Istanbul EVM | legacyTxType의 [PayloadGas](#payloadgas)를 다른 TxType과 일관되게 만듭니다.<br/>- Before: PayloadGas=number_of_zero_bytes_of_tx_input x 4 + number_of_nonzero_bytes_of_tx_input x 68 <br/> - After: PayloadGas=number_of_bytes_of_tx_input x 100<br/><br/>change [keyValidationGas](#keyvalidationgas) 계산 로직<br/>- Before: KeyValidationGas=(키 수 - 1) x 15,000<br/>- 이후: KeyValidationGas=(서명 수 - 1) x 15,000 |

