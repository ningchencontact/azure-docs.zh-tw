---
title: 利用存留時間讓 Azure Cosmos DB 中的資料過期
description: Microsoft Azure Cosmos DB 可讓您利用 TTL 在一段時間後自動從系統清除文件。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: fbbaefc62adedc2374c47fd0736368d3dec3e6a4
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043425"
---
# <a name="time-to-live-for-azure-cosmos-db-data"></a>Azure Cosmos DB 資料的存留時間

Azure Cosmos DB 可讓您利用「存留時間」或 TTL，在一段時間後自動從容器中刪除項目。 根據預設，您可以在容器層級設定存留時間，且覆寫每個項目的值。 在容器或項目層級設定 TTL 之後，Azure Cosmos DB 會在自從上次修改以來的時間週期後自動移除這些項目。 存留時間值會以秒設定。 當您設定 TTL 時，系統會根據 TTL 值自動刪除過期的項目，這不同於用戶端應用程式所明確發出的刪除作業。

## <a name="time-to-live-for-containers-and-items"></a>容器和項目的存留時間

存留時間值會以秒設定，而且會解譯為自從項目上次修改以來的時間差異。 您可以設定容器或容器內項目的存留時間：

1. **容器的存留時間** (使用 `DefaultTimeToLive` 設定)：

   - 如果遺失 (或設為 null)，項目便不會自動過期。

   - 如果存在且值設為 "-1" (等於無限)，項目預設不會過期。

   - 如果存在且值設為某個數字 ("n")，則項目會在其上次修改時間後的 "n" 秒過期。

2. **項目的存留時間** (使用 `TimeToLive` 設定)：

   - 僅在父容器有 `DefaultTimeToLive` 且未設定為 DefaultTTL 時，才適用此屬性。

   - 如果有的話，它會覆寫父容器的 `DefaultTimeToLive` 值。

## <a name="time-to-live-configurations"></a>存留時間組態

* 如果容器的 TTL 設定為 'n'，則該容器中的項目將會在 n 秒後過期。  如果相同容器中有些項目將自己的存留時間設為 -1 (表示它們不會過期)，或如果有些項目已使用不同的數字覆寫存留時間設定，這些項目會根據所設定的 TTL 值過期。 

* 如果未設定容器的 TTL，則此容器中項目的存留時間沒有任何作用。 

* 如果容器的 TTL 設定為 -1，則此容器中存留時間設為 n 的項目會在 n 秒後過期，而其餘的項目則不會過期。 

刪除以 TTL 為基礎的項目是免費的。 因為 TTL 過期而刪除項目時，沒有任何額外的成本 (也就是不會使用任何額外的 RU)。

## <a name="next-steps"></a>後續步驟

在下列文章中了解如何設定存留時間：

* [如何設定存留時間](how-to-time-to-live.md)
