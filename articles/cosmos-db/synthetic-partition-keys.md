---
title: 在 Azure Cosmos DB 中建立綜合分割區索引鍵，以平均分散您的資料和工作負載。
description: 了解如何在 Azure Cosmos 容器中使用綜合分割區索引鍵
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 6b3499c36ae7abd03c4a1f1ca3a3a46e2c315120
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762730"
---
# <a name="create-a-synthetic-partition-key"></a>建立綜合分割區索引鍵

它是最佳作法有許多相異值的詳細資訊，例如數百或數千個資料分割索引鍵。 目標是在與這些分割區索引鍵值相關聯的項目上平均散發您的資料和工作負載。 如果這類屬性不存在於您的資料，您可以建構*綜合資料分割索引鍵*。 本文件說明數個產生的綜合資料分割索引鍵，為您的 Cosmos 容器的基本技巧。

## <a name="concatenate-multiple-properties-of-an-item"></a>串連項目的多個屬性

您可以藉由將多個屬性值串連到單一人工 `partitionKey` 屬性來形成分割區索引鍵。 這些索引鍵稱為綜合索引鍵。 例如，請考慮下列範例文件：

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

針對先前的文章，其中一個選項是將 /deviceId 或 /date 設為分割區索引鍵。 如果您想要分割您的裝置識別碼或日期為基礎的容器，請使用此選項。 另一個選項是將這兩個值串連到一個綜合 `partitionKey` 屬性以用來作為分割區索引鍵。

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

在即時的情況下，您可以有數千個資料庫中的項目。 而不是以手動方式新增的綜合的索引鍵，定義用戶端邏輯，串連的值，並插入您的 Cosmos 容器中的項目中的綜合的索引鍵。

## <a name="use-a-partition-key-with-a-random-suffix"></a>搭配隨機尾碼使用分割區索引鍵

另一個更平均散發工作負載的可行策略是在分割區索引鍵值結尾附加一個隨機數字。 當您以此方式散發項目時，您可以跨分割區執行平行的寫入作業。

其中一個範例是分割區索引鍵代表日期的情況。 您可以選擇隨機的數字，介於 1 到 400，並將該值串連做為尾碼的日期。 這個方法會導致類似的資料分割索引鍵值 `2018-08-09.1`，`2018-08-09.2`，依此類推，透過 `2018-08-09.400`。 由於您是將分割區索引鍵隨機化，因此每天在容器上的寫入作業都會平均分散在多個分割區中。 此方法會產生更好的平行處理原則及整體更高的輸送量。

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>使用預先計算的後置詞的資料分割索引鍵 

隨機的尾碼策略可大幅改善寫入輸送量，但很難讀取特定的項目。 您不知道您所撰寫的項目時所使用的尾碼值。 若要讓您更輕鬆地讀取個別項目，使用預先計算的尾碼策略。 而不是使用一個隨機數字来散發的分割區之間的項目，請使用計算的數字，根據您想要查詢的項目。

請考慮上述範例中，其中一個容器會使用日期做為資料分割索引鍵。 現在，假設每個項目具有 `Vehicle-Identification-Number`(`VIN`) 我們想要存取的屬性。 此外，假設您經常執行查詢，以尋找的項目`VIN`，除了日期。 在您的應用程式將項目寫入到容器之前，它可以根據 VIN 來計算雜湊尾碼，並將它附加至分割區索引鍵日期。 計算可能會產生平均分佈的數字介於 1 到 400。 此結果是類似於隨機的尾碼策略方法所產生的結果。 分割區索引鍵值先前是搭配計算結果串連的日期。

使用這個策略，寫入會在分割區索引鍵值之間以及在分割區之間平均分散。 您可以輕鬆地讀取特定項目和日期，因為您可以計算資料分割索引鍵值特定`Vehicle-Identification-Number`。 這個方法的優點是，您可以避免建立單一的熱點資料分割索引鍵，亦即，接受所有的工作負載資料分割索引鍵。 

## <a name="next-steps"></a>後續步驟

您可以在下列文章中深入了解分割概念：

* 深入了解[邏輯分割區](partition-data.md)。
* 深入了解如何[在 Azure Cosmos 容器和資料庫上佈建輸送量](set-throughput.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
