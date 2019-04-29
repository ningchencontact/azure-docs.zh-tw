---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0e55c372c6f5dc3484bd64cf4f328479d2d0b245
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461311"
---
## <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>金鑰交易 (每個區域的每個在 10 秒，允許的最大交易保存庫<sup>1</sup>):

|金鑰類型|HSM 金鑰<br>建立金鑰|HSM 金鑰<br>所有其他交易|軟體金鑰<br>建立金鑰|軟體金鑰<br>所有其他交易|
|:---|---:|---:|---:|---:|
|RSA 2,048 位元|5|1,000|10|2,000|
|RSA 3072 位元|5|250|10|500|
|RSA 4096 位元|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> 在上表中，我們可以看到，RSA 2,048 位元軟體金鑰，每 10 秒 2,000 GET 筆交易都允許的。 RSA 2,048 位元 HSM 金鑰，都被允許每 10 秒的 1,000 取得交易。
>
> 節流臨界值進行加權，並強制執行位於其總和。 比方說，如下表所示，您執行 GET 作業 RSA HSM 金鑰時，八倍昂貴使用 4,096 位元的金鑰，相較於 2,048 位元的金鑰。 這是因為 1,000/125 = 8。
>
> 在給定的 10 秒間隔內，可以執行的 Azure 金鑰保存庫用戶端*只能有一個*的下列作業遇到之前，先`429`節流 HTTP 狀態碼：
> - 2,000 RSA 2,048 位元軟體金鑰的 GET 筆交易
> - 1,000 筆 RSA 2,048 位元 HSM 金鑰取得交易
> - 125 RSA 4096 位元 HSM 金鑰取得交易
> - 124 的 RSA 4096 位元的 HSM 金鑰取得交易和 8 的 RSA 2,048 位元 HSM 金鑰取得交易

## <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>祕密、 受管理的儲存體帳戶金鑰及保存庫交易：
| 交易類型 | 每個區域的每個在 10 秒，允許的最大交易保存庫<sup>1</sup> |
| --- | --- |
| 所有交易 |2,000 |

如需如何處理節流時超過這些限制的資訊，請參閱[Azure Key Vault 節流指導方針](../articles/key-vault/key-vault-ovw-throttling.md)。

<sup>1</sup>全訂用帳戶限制的所有交易類型是五次，每個金鑰保存庫的限制。 比方說，每個訂用帳戶的 HSM-其他交易在 10 秒後，每個訂用帳戶僅限於 5,000 筆交易筆。
