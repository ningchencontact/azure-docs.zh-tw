---
title: 利用存留時間讓 Azure Cosmos DB 中的資料過期
description: Microsoft Azure Cosmos DB 可讓您利用 TTL 在一段時間後自動從系統清除文件。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 27540c3dfce73788e01f0f8ab0892c733f153fdf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271266"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>存留時間 (TTL 在 Azure Cosmos DB) 

具有**存留時間**或 TTL，Azure Cosmos DB 讓您能夠在一段時間後刪除自動從容器項目。 根據預設，您可以在容器層級設定存留時間，且覆寫每個項目的值。 在容器或項目層級設定 TTL 之後，Azure Cosmos DB 會在自從上次修改以來的時間週期後自動移除這些項目。 存留時間值會以秒設定。 當您設定 TTL 時，系統將自動刪除過期的項目，根據的 TTL 值，而不需要明確發出的用戶端應用程式的刪除作業。

## <a name="time-to-live-for-containers-and-items"></a>容器和項目的存留時間

設定存留時間值秒數，並將它解譯為項目上次修改時間的差異。 您可以設定容器或容器內項目的存留時間：

1. **容器的存留時間** (使用 `DefaultTimeToLive` 設定)：

   - 如果遺失 (或設為 null)，項目便不會自動過期。

   - 如果存在且值設定為"-1"，則為無限，和依預設不會過期的項目。

   - 如果存在且值設定為某個數字 *"n 名"* – 項目將過期 *"n"* 之後其上次修改時間的秒數。

2. **項目的存留時間** (使用 `ttl` 設定)：

   - 僅在父容器有 `DefaultTimeToLive` 且未設定為 DefaultTTL 時，才適用此屬性。

   - 如果有的話，它會覆寫父容器的 `DefaultTimeToLive` 值。

## <a name="time-to-live-configurations"></a>存留時間組態

* 如果 TTL 設定為 *"n 名"* 容器，然後在該容器中的項目後到期*n*秒。  如果有相同的容器中有自己的時間，設定為-1 （表示它們不會過期） 的項目，或如果某些項目已覆寫以不同的數字設定存留時間，這些項目過期根據他們自己設定的 TTL 值。 

* 如果未設定容器的 TTL，則此容器中項目的存留時間沒有任何作用。 

* 如果容器的 TTL 設定為 -1，則此容器中存留時間設為 n 的項目會在 n 秒後過期，而其餘的項目則不會過期。 

刪除以 TTL 為基礎的項目是免費的。 因為 TTL 過期而刪除項目時，沒有任何額外的成本 (也就是不會使用任何額外的 RU)。

## <a name="next-steps"></a>後續步驟

通过以下文章了解如何配置生存时间：

* [如何設定存留時間](how-to-time-to-live.md)
