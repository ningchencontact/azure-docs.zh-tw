---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b9d87fd7929607da8407ae5bbfb2f6dd6d69dab
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "67174384"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>金鑰交易（每個區域的每個保存庫在10秒內允許的最大交易數<sup>1</sup>）：

|金鑰類型|HSM 金鑰<br>建立金鑰|HSM 金鑰<br>所有其他交易|軟體金鑰<br>建立金鑰|軟體金鑰<br>所有其他交易|
|:---|---:|---:|---:|---:|
|RSA 2048 位|5|1,000|10|2,000|
|RSA 3072 位|5|250|10|500|
|RSA 4096 位|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> 在上表中，我們看到 RSA 2048 位軟體金鑰的2000取得每10秒的交易數。 針對 RSA 2048 位 HSM 金鑰1000，允許每10秒取得筆交易。
>
> 節流臨界值為加權，強制執行于其總和。 例如，如上表所示，當您在 RSA HSM 金鑰上執行 GET 作業時，使用4096位金鑰比2048位金鑰更昂貴。 這是因為 1000/125 = 8。
>
> 在指定的10秒間隔內，Azure Key Vault 用戶端只能在遇到`429`節流 HTTP 狀態碼之前，執行下列*其中一項*作業：
> - 2000 RSA 2048 位軟體-金鑰取得交易
> - 1000 RSA 2048-位 HSM-金鑰取得交易
> - 125 RSA 4096-位 HSM-金鑰取得交易
> - 124 RSA 4096 位 HSM-金鑰取得交易和 8 RSA 2048-位 HSM-金鑰取得交易

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>秘密、受控儲存體帳戶金鑰和保存庫交易：
| 交易類型 | 每個區域的每個保存庫10秒內允許的最大交易數<sup>1</sup> |
| --- | --- |
| 所有交易 |2,000 |

如需如何在超過這些限制時處理節流的資訊，請參閱[Azure Key Vault 節流指導](../articles/key-vault/key-vault-ovw-throttling.md)方針。

<sup>1</sup>所有交易類型的訂用帳戶範圍限制為每個金鑰保存庫限制的五次。 例如，每個訂用帳戶的 HSM-其他交易限制為每個訂用帳戶10秒內的5000筆交易。
