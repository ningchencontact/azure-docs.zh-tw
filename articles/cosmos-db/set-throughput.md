---
title: "Azure Cosmos DB 的佈建輸送量 | Microsoft Docs"
description: "了解如何設定 Azure Cosmos DB 容器、集合、圖表和資料表的佈建輸送量。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: mimig
ms.openlocfilehash: afbb3392a4726ea067bf19c117792b866d9e79f3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2018
---
# <a name="set-throughput-for-azure-cosmos-db-containers"></a>設定 Azure Cosmos DB 容器的輸送量

您可以在 Azure 入口網站，或是使用用戶端 SDK，設定 Azure Cosmos DB 容器的輸送量。 

下表列出容器可使用的輸送量：

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>單一資料分割容器</strong></p></td>
            <td valign="top"><p><strong>已資料分割的容器</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>輸送量下限</p></td>
            <td valign="top"><p>每秒 400 個要求單位</p></td>
            <td valign="top"><p>每秒 1000 個要求單位</p></td>
        </tr>
        <tr>
            <td valign="top"><p>輸送量上限</p></td>
            <td valign="top"><p>每秒 10,000 個要求單位</p></td>
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

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>使用 SQL API for Java 設定輸送量

這個程式碼片段取自 [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) 存放庫中的 OfferCrudSamples.java 檔案。 

```Java
// find offer associated with this collection
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 10300;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a name="throughput-faq"></a>輸送量常見問題集

**我可以將輸送量設定為小於 400 RU/s 嗎？**

400 RU/s 是 Cosmos DB 單一資料分割容器上可用的最小輸送量 (1000 RU/s 是資料分割容器的最小值)。 要求單位是設定為 100 RU/s 時間間隔，但不能將輸送量設定為 100 RU/s 或任何小於 400 RU/s 的值。 如果您正在尋找符合成本效益的方法來開發和測試 Cosmos DB，您可以使用免費的 [Cosmos DB 模擬器](local-emulator.md)，於本機免費部署此模擬器。 

**如何使用 MongoDB API 設定輸送量？**

MongoDB API 沒有可以設定輸送量的擴充功能。 建議使用 SQL API，如[使用 SQL API for .NET 設定輸送量](#set-throughput-sdk)中所示。

## <a name="next-steps"></a>後續步驟

若要深入了解 Cosmos DB 的佈建和全球規模化，請參閱 [Cosmos DB 的資料分割和規模調整](partition-data.md)。
