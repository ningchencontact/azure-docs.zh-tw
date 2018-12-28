---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: efa367157a8fd896cdc9680bf2ab6ba6a9e3dbb0
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429932"
---
金鑰交易 (每個區域中的每個保存庫在 10 秒內允許的最大交易數<sup>1</sup>)：

|金鑰類型|HSM-key<br>CREATE 金鑰|HSM-key<br>所有其他交易|Software-key<br>CREATE 金鑰|Software-key<br>所有其他交易|
|:---|---:|---:|---:|---:|
|RSA 2048 位元|5|1000|10|2000|
|RSA 3072 位元|5|250|10|500|
|RSA 4096 位元|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

> [!NOTE]
> 您查看下表時，會發現我們允許軟體支援的金鑰每 10 秒鐘有 2000 筆交易，至於 HSM 支援的金鑰每 10 秒鐘則允許 1000 筆交易。 3072 金鑰與 2048 金鑰的軟體支援交易比例為 500/2000，即 0.4。 這表示如果客戶在 10 秒鐘內進行了 500 筆 3072 金鑰交易，就會達到上限，無法再進行任何其他的金鑰作業。 
   
|金鑰類型  | 軟體金鑰 |HSM-key  |
|---------|---------|---------|
|RSA 2048 位元     |    2000     |   1000    |
|RSA 3072 位元     |     500    |    250     |
|RSA 4096 位元     |    125     |    250     |
|ECC P-256     |    2000     |  1000     |
|ECC P-384     |    2000     |  1000     |
|ECC P-521     |    2000     |  1000     |
|ECC SECP256K1     |    2000     |  1000     |


祕密、受控儲存體帳戶金鑰和保存庫交易：
| 交易類型 | 每個區域中的每個保存庫在 10 秒內允許的最大交易數<sup>1</sup> |
| --- | --- |
| 所有交易 |2000 |
|

如需有關如何在超過這些限制時處理節流的相關資訊，請參閱 [Azure Key Vault 節流指導方針](../articles/key-vault/key-vault-ovw-throttling.md)。

<sup>1</sup> 所有交易類型都有全訂用帳戶限制，也就是每個金鑰保存庫 5x 的限制。 例如，每個訂用帳戶的 HSM - 其他交易受限於每個訂閱 10 秒內 5000 筆交易。
