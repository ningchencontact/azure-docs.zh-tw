---
title: Azure Cosmos DB 的佈建輸送量 | Microsoft Docs
description: 了解如何設定 Azure Cosmos DB 容器、集合、圖表和資料表的佈建輸送量。
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: sngun
ms.openlocfilehash: bede91ed3ffc456740a0eb63ed7a15278e99ebe2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers"></a>設定及取得 Azure Cosmos DB 容器的輸送量

您可以在 Azure 入口網站，或是使用用戶端 SDK，設定 Azure Cosmos DB 容器或一組容器的輸送量。 

下表列出容器可使用的輸送量：

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>單一資料分割容器</strong></p></td>
            <td valign="top"><p><strong>已資料分割的容器</strong></p></td>
            <td valign="top"><p><strong>一組容器</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>輸送量下限</p></td>
            <td valign="top"><p>每秒 400 個要求單位</p></td>
            <td valign="top"><p>每秒 1,000 個要求單位</p></td>
            <td valign="top"><p>每秒 50,000 個要求單位</p></td>
        </tr>
        <tr>
            <td valign="top"><p>輸送量上限</p></td>
            <td valign="top"><p>每秒 10,000 個要求單位</p></td>
            <td valign="top"><p>無限</p></td>
            <td valign="top"><p>無限</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>使用 Azure 入口網站設定輸送量

1. 在新的視窗中，開啟 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左側的 [Azure Cosmos DB]，或按一下底部的 [所有服務]，捲動至 [資料庫]，然後按一下 [Azure Cosmos DB]。
3. 選取您的 Cosmos DB 帳戶。
4. 在新視窗中，按一下導覽功能表中的 [資料總管]。
5. 在新視窗中，展開您的資料庫和容器，然後按一下 [規模與設定]。
6. 在新視窗中，在 [輸送量] 方塊中輸入新的輸送量，然後按一下 [儲存]。

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>使用 SQL API for .NET 設定輸送量

下列程式碼片段會取出目前的輸送量，並將其更改為 500 RU/秒。 如需完整的程式碼範例，請參閱 GitHub 上的 [CollectionManagement](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L188-L216) 專案。

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
// To change the throughput for a set of containers, use the database's selflink instead of the collection's selflink
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 500 request units per second
offer = new OfferV2(offer, 500);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>使用 SQL API for Java 設定輸送量

下列程式碼片段會取出目前的輸送量，並將其更改為 500 RU/秒。 如需完整的程式碼範例，請參閱 GitHub 上的 [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) 檔案。 

```Java
// find offer associated with this collection
// To change the throughput for a set of containers, use the database's resource id instead of the collection's resource id
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a id="GetLastRequestStatistics"></a>使用 MongoDB API 的 GetLastRequestStatistics 命令取得輸送量

MongoDB API 支援自訂命令 getLastRequestStatistics，可擷取指定作業的要求費用。

例如，在 Mongo 殼層中，執行您想要驗證要求費用的作業。
```
> db.sample.find()
```

接著，執行命令 *getLastRequestStatistics*。
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

有鑑於此，若要估計您的應用程式所需的保留輸送量，其中一個方法為對照應用程式使用的代表性項目，記錄與執行一般作業相關聯的要求單位費用，然後估計您預期每秒會執行的作業數目。

> [!NOTE]
> 如果您的項目類型與已編製索引之屬性的大小與數目截然不同，則請記錄與每個一般項目「類型」相關聯的適用作業要求單位費用。
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>使用 MongoDB API 入口網站計量取得輸送量

若要準確估計 MongoDB API 資料庫的要求單位費用，最簡單的方法就是使用 [Azure 入口網站](https://portal.azure.com)計量。 利用 [要求數目] 和 [要求費用] 圖表，您可以估計每個作業耗用多少要求單位，以及它們彼此之間相對耗用多少要求單位。

![MongoDB API 入口網站計量][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> 超過 MongoDB API 中保留的輸送量限制
超過為容器或一組容器佈建之輸送量的應用程式會受到速率限制，直取用速率降到佈建的輸送量速率以下為止。 發生速率限制時，後端會提前結束要求，並傳回 `16500` 錯誤碼 - `Too Many Requests`。 根據預設，在傳回 `Too Many Requests` 錯誤碼之前，MongoDB API 會自動重試最多 10 次。 如果您收到很多 `Too Many Requests` 錯誤碼，您可以考慮在應用程式的錯誤處理常式中新增重試行為，或[提高容器的佈建輸送量](set-throughput.md)。

## <a name="throughput-faq"></a>輸送量常見問題集

**我可以將輸送量設定為小於 400 RU/s 嗎？**

400 RU/s 是 Cosmos DB 單一資料分割容器上可用的最小輸送量 (1000 RU/s 是資料分割容器的最小值)。 要求單位是設定為 100 RU/s 時間間隔，但不能將輸送量設定為 100 RU/s 或任何小於 400 RU/s 的值。 如果您正在尋找符合成本效益的方法來開發和測試 Cosmos DB，您可以使用免費的 [Cosmos DB 模擬器](local-emulator.md)，於本機免費部署此模擬器。 

**如何使用 MongoDB API 設定輸送量？**

MongoDB API 沒有可以設定輸送量的擴充功能。 建議使用 SQL API，如[使用 SQL API for .NET 設定輸送量](#set-throughput-sdk)中所示。

## <a name="next-steps"></a>後續步驟

若要深入了解 Cosmos DB 的佈建和全球規模化，請參閱 [Cosmos DB 的資料分割和規模調整](partition-data.md)。

[1]: ./media/set-throughput/api-for-mongodb-metrics.png