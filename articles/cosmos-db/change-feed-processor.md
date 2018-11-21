---
title: 在 Azure Cosmos DB 中使用變更摘要處理器程式庫
description: 使用 Azure Cosmos DB 變更摘要處理器程式庫。
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rafats
ms.openlocfilehash: 9d427a8001112e4994597b86579d85156f94a870
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628472"
---
# <a name="using-the-azure-cosmos-db-change-feed-processor-library"></a>使用 Azure Cosmos DB 變更摘要處理器程式庫

[Azure Cosmos DB 變更摘要處理器程式庫](sql-api-sdk-dotnet-changefeed.md)可協助您將事件處理分散給多個取用者。 此程式庫會簡化跨分割區和多個平行運作執行緒上的變更讀取。

變更摘要處理器程式庫的主要優點是您不需要管理每個分割區和接續權杖，也不需要手動輪詢每個容器。

變更摘要處理器程式庫可以簡化跨分割區和多個平行運作執行緒上的變更讀取。 它會使用租用機制自動管理所有分割區上的變更讀取。 如您在下圖中所見，如果您啟動了使用變更摘要處理器程式庫的兩個用戶端，它們會在彼此之中分割工作。 當您繼續增加用戶端數目時，它們會繼續在彼此之中分割工作。

![使用 Azure Cosmos DB 變更摘要處理器程式庫](./media/change-feed-processor/change-feed-output.png)

先啟動左邊的用戶端，它開始並啟監視所有分割區，接著啟動第二個用戶端，然後第一個將部份放租用釋出給第二個用戶端。 這是在不同電腦與用戶端之間分散工作的有效方式。

如果您有兩個無伺服器的 Azure 函式使用相同的租用在監視相同的容器，則這兩個函式可能會得到不同的文件，取決於處理器程式庫決定如何處理分割區。

## <a name="implementing-the-change-feed-processor-library"></a>實作變更摘要處理器程式庫

實作變更摘要處理器程式庫時會用到四個主要元件： 

1. **受監視的容器：** 受監視的容器含有會產生變更摘要的資料。 對於受監視的容器所進行的任何插入和變更，都會反映在容器的變更摘要中。

1. **租用容器：** 租用容器會協調如何處理多個背景工作角色的變更摘要。 另外會有一個容器用來儲存租用 (每個分割區一個租用)。 在不同帳戶儲存此租用容器，並讓其寫入區域更靠近變更摘要處理器的執行所在位置會有好處。 租用物件包含下列屬性：

   * 擁有者：指定擁有租用的主機。

   * 接續：指定特定分割區在變更摘要中的位置 (接續權杖)。

   * 時間戳記：上次更新租用的時間；時間戳記可用來檢查是否將租用視為過期。

1. **處理器主機：** 每一部主機都會根據主機的其他執行個體中有多少個執行個體擁有作用中租用，來決定要處理的分割區數量。

   * 主機在啟動時會取得租用，以平衡分配所有主機的工作負載。 主機會定期更新租用，以便讓租用保持作用中狀態。

   * 主機會對其每個讀取的租用設置最後一個接續權杖的檢查點。 為確保能夠安全地進行並行存取，主機會檢查每個租用更新的 ETag。 主機也支援其他檢查點策略。

   * 關機後，主機會將所有租用釋出，但會保留接續資訊，以便之後能夠繼續讀取預存的檢查點。

   目前，主機數目不能大於分割區 (租用) 數目。

1. **取用者：** 取用者 (或背景工作角色) 是負責執行每個主機所起始之變更摘要處理活動的執行緒。 每個處理器主機都可以有多個取用者。 每個取用者會從其獲派到的分割區讀取變更摘要，並向其主機通知所發生的變更和已過期的租用。

為了進一步了解變更摘要處理器的這四個元素是如何一起運作的，我們來看看下圖中的範例。 受監視的集合會儲存文件，並使用 'City' 來作為分割區索引鍵。 我們可以看到，藍色的分割區包含 'City' 欄位為 "A 到 E" 的文件，依此類推。 主機有兩部，每一部都有兩個取用者從四個分割區進行平行讀取。 箭號顯示從變更摘要中的特定地點進行讀取的取用者。 在第一個分割區中，較深的藍色代表未讀取的變更，較淺的藍色則代表變更摘要上已讀取的變更。 主機會使用租用集合來儲存「接續」值，以追蹤每個取用者目前的讀取位置。

![變更摘要處理器範例](./media/change-feed-processor/changefeedprocessor.png)

### <a name="change-feed-and-provisioned-throughput"></a>變更摘要和佈建的輸送量

您需針對耗用的 RU 支付費用，因為資料移入或移出 Cosmos 容器一律會耗用 RU。 您必須針對租用容器耗用的 RU 支付費用。

## <a name="additional-resources"></a>其他資源

* [Azure Cosmos DB 變更摘要處理器程式庫](sql-api-sdk-dotnet-changefeed.md)
* [NugGet 套件](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [GitHub 上的其他範例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要：

* [變更摘要的概觀](change-feed.md)
* [讀取變更摘要的方式](read-change-feed.md)
* [搭配使用變更摘要與 Azure Functions](change-feed-functions.md)