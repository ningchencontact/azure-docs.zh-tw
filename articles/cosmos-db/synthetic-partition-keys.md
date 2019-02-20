---
title: 在 Azure Cosmos DB 中建立綜合分割區索引鍵，以平均分散您的資料和工作負載。
description: 了解如何在 Azure Cosmos 容器中使用綜合分割區索引鍵
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: mjbrown
ms.openlocfilehash: 8becfe375f2e887348729cf1d76820fc41156d2a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997095"
---
# <a name="create-a-synthetic-partition-key"></a>建立綜合分割區索引鍵

最佳做法是讓分割區索引鍵具有許多相異值，例如數百個或數千個。 目標是在與這些分割區索引鍵值相關聯的項目上平均散發您的資料和工作負載。 如果您的資料中沒有這類屬性，則可以建構綜合分割區索引鍵。 下列各節將說明數個用來為容器產生綜合分割區索引鍵的基本技術。

## <a name="concatenate-multiple-properties-of-an-item"></a>串連項目的多個屬性

您可以藉由將多個屬性值串連到單一人工 `partitionKey` 屬性來形成分割區索引鍵。 這些索引鍵稱為綜合索引鍵。 例如，請考慮下列範例文件：

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

針對先前的文章，其中一個選項是將 /deviceId 或 /date 設為分割區索引鍵。 如果您想要依裝置識別碼或日期分割您的容器，請使用此選項。 另一個選項是將這兩個值串連到一個綜合 `partitionKey` 屬性以用來作為分割區索引鍵。

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

在即時案例中，您的資料庫中可能會有數以千計的文件。 與其手動新增綜合索引鍵，您可以定義用戶端邏輯來串連值，並將綜合索引鍵插入文件中。

## <a name="use-a-partition-key-with-a-random-suffix"></a>搭配隨機尾碼使用分割區索引鍵

另一個更平均散發工作負載的可行策略是在分割區索引鍵值結尾附加一個隨機數字。 當您以此方式散發項目時，您可以跨分割區執行平行的寫入作業。

其中一個範例是分割區索引鍵代表日期的情況。 您可以選擇介於 1 到 400 的隨機數字，並將它當作尾碼串連至該日期。 此方法會產生分割區索引鍵值，像是  2018-08-09.1、 2018-08-09.2，依此類推，一直到  2018-08-09.400。 由於您是將分割區索引鍵隨機化，因此每天在容器上的寫入作業都會平均分散在多個分割區中。 此方法會產生更好的平行處理原則及整體更高的輸送量。

## <a name="use-a-partition-key-with-precalculated-suffixes"></a>搭配預先計算的尾碼使用分割區索引鍵 

隨機化策略可以大幅改善寫入輸送量，但會很難讀取特定的項目。 您不知道寫入該項目時所使用的尾碼值。 為了能更輕鬆地讀取個別項目，請使用預先計算的尾碼策略。 與其在分割區之間使用隨機數字來散發項目，請改用根據您想要查詢的項目來計算的數字。

請考慮上一個範例，其中的容器會在分割區索引鍵中使用日期。 現在，假設每個項目都具有可存取的車輛識別號碼 (VIN) 屬性。 進一步假設您經常會依 VIN (以及日期) 來執行查詢以尋找項目。 在您的應用程式將項目寫入到容器之前，它可以根據 VIN 來計算雜湊尾碼，並將它附加至分割區索引鍵日期。 計算可能會產生平均分佈且介於 1 和 400 之間的值。 此結果與由隨機化策略方法所產生的結果類似。 分割區索引鍵值先前是搭配計算結果串連的日期。

使用這個策略，寫入會在分割區索引鍵值之間以及在分割區之間平均分散。 您可以輕鬆地讀取特定項目和日期，因為您可以針對特定的車輛識別號碼來計算分割區索引鍵值。 這個方法的優點是您可以避免建立單一熱點分割區索引鍵。 熱點分割區索引鍵是佔用所有工作負載的分割區索引鍵。 

## <a name="next-steps"></a>後續步驟

您可以在下列文章中深入了解分割概念：

* 深入了解[邏輯分割區](partition-data.md)。
* 深入了解如何[在 Azure Cosmos 容器和資料庫上佈建輸送量](set-throughput.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
