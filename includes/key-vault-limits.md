---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ed0c387f9785336fbf18b3fd3c0cd9a7b09df633
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279502"
---
金鑰交易 (每個區域中的每個保存庫在 10 秒內允許的最大交易數<sup>1</sup>)：

|金鑰類型|HSM-Key<br>CREATE 金鑰|HSM-key<br>所有其他交易|Software-key<br>CREATE 金鑰|Software-key<br>所有其他交易|
|:---|---:|---:|---:|---:|
|RSA 2048 位元|5|1000|10|2000|
|RSA 3072 位元|5|250|10|500|
|RSA 4096 位元|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

祕密、受控儲存體帳戶金鑰和保存庫交易：
| 交易類型 | 每個區域中的每個保存庫在 10 秒內允許的最大交易數<sup>1</sup> |
| --- | --- |
| 所有交易 |2000 |
|

如需有關如何在超過這些限制時處理節流的相關資訊，請參閱 [Azure Key Vault 節流指導方針](../articles/key-vault/key-vault-ovw-throttling.md)。

<sup>1</sup> 所有交易類型都有全訂用帳戶限制，也就是每個金鑰保存庫 5x 的限制。 例如，每個訂用帳戶的 HSM - 其他交易受限於每個訂閱 10 秒內 5000 筆交易。
