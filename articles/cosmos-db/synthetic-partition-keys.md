---
title: Azure Cosmos DB 中的綜合分割區索引鍵
description: 了解如何在 Azure Cosmos DB 容器中使用綜合分割區索引鍵
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: f6323549c0ecf8f0196d327779f7f7cb67c6e03f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263343"
---
# <a name="create-a-synthetic-partition-key"></a>建立綜合分割區索引鍵

最佳做法是讓分割區索引鍵具有許多相異值，例如數百個或數千個。 目標是在與這些分割區索引鍵值相關聯的項目上平均散發您的資料和工作負載。 如果您的資料中沒有這類屬性，就可以建構綜合分割區索引鍵。 下列各節將說明數個用來為容器產生綜合分割區索引鍵的基本技術。

## <a name="concatenating-multiple-properties-of-an-item"></a>串連項目的多個屬性

您可以藉由將多個屬性值串連到單一人工 `partitionKey` 屬性來形成分割區索引鍵。 這些索引鍵稱為綜合索引鍵。 例如，請考慮下列範例文件：

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

針對前一份文件，如果您想要根據裝置識別碼或日期來分割容器，則選項之一是將 /deviceId 或 /date 設定為分割區索引鍵。 另一個選項是將這兩個值串連到一個綜合 `partitionKey` 屬性以用來作為分割區索引鍵。

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

在即時案例中，您可以在資料庫中擁有數千份文件，因此，您不需手動新增綜合索引鍵，而是應該定義用戶端邏輯來串連值，並將該綜合索引鍵插入至文件。

## <a name="using-a-partition-key-with-random-suffix"></a>將分割區索引鍵與隨機尾碼搭配使用

另一個更平均散發工作負載的可行策略是在分割區索引鍵值結尾附加一個隨機數字。 以此方式散發項目可讓您跨分割區執行平行的寫入作業。

例如，如果分割區索引鍵代表日期，您就可以選擇介於 1 到 400 的隨機數字，並將它當作尾碼串連至該日期。 此方法會產生分割區索引鍵值，像是  2018-08-09.1、 2018-08-09.2，依此類推，一直到  2018-08-09.400。 由於您正在將分割區索引鍵隨機化，因此，每天在容器上的寫入作業都會平均分散在多個分割區中。 此方法會產生更好的平行處理原則及整體更高的輸送量。

## <a name="using-a-partition-key-with-pre-calculated-suffixes"></a>將分割區索引鍵與預先計算的尾碼搭配使用 

透過隨機化策略可大幅改善寫入輸送量，但很難讀取特定項目，因為您不知道在寫入該項目時所使用的尾碼值。 為了能更輕鬆地讀取個別項目，您可以使用預先計算的尾碼策略。 不需在分割區之間使用隨機數字來散發項目，而是改用根據您想要查詢的項目來計算的數字。

請考慮上一個範例，其中的容器會在分割區索引鍵中使用日期。 現在假設每個項目都具有可存取的 VIN (車輛識別號碼) 屬性，而且，假設除了日期，您通常還會依 VIN 執行查詢來尋找項目。 在您的應用程式將項目寫入到容器之前，它可以根據 VIN 來計算雜湊尾碼，並將它附加至分割區索引鍵日期。 此計算可能會產生介於 1 到 400 之間平均散發的數字，類似透過隨機策略方法所產生的結果。 分割區索引鍵值接著將是與計算結果串連的日期。

使用這個策略，寫入會在分割區索引鍵值之間以及在分割區之間平均分散。 您可以輕鬆地讀取特定項目和日期，因為您可以針對特定的車輛識別號碼來計算分割區索引鍵值。 這個方法的優點是，您可以避免建立單一熱點分割區索引鍵 (佔用所有工作負載的分割區索引鍵)。 

## <a name="next-steps"></a>後續步驟

您可以在下列文章中深入了解分割概念：

* 深入了解[邏輯分割區](partition-data.md)
* 深入了解如何[在 Cosmos DB 容器和資料庫上佈建輸送量](set-throughput.md)
* 了解[如何在 Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)
* 了解[如何在 Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)
