---
title: 在 Azure Cosmos DB 中建立綜合分割區索引鍵，以平均分散您的資料和工作負載。
description: 了解如何在 Azure Cosmos 容器中使用綜合分割區索引鍵
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 6b3499c36ae7abd03c4a1f1ca3a3a46e2c315120
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60937095"
---
# <a name="create-a-synthetic-partition-key"></a>建立綜合分割區索引鍵

采用具有大量（例如，几百甚至几千个）非重复性值的分区键是最佳做法。 目標是在與這些分割區索引鍵值相關聯的項目上平均散發您的資料和工作負載。 如果這類屬性不存在於您的資料，您可以建構*綜合資料分割索引鍵*。 本文档介绍为 Cosmos 容器生成合成分区键的几种基本方法。

## <a name="concatenate-multiple-properties-of-an-item"></a>串連項目的多個屬性

您可以藉由將多個屬性值串連到單一人工 `partitionKey` 屬性來形成分割區索引鍵。 這些索引鍵稱為綜合索引鍵。 例如，請考慮下列範例文件：

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

針對先前的文章，其中一個選項是將 /deviceId 或 /date 設為分割區索引鍵。 若要根据设备 ID 或日期将容器分区，可使用这种做法。 另一個選項是將這兩個值串連到一個綜合 `partitionKey` 屬性以用來作為分割區索引鍵。

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

在实时方案中，数据库可能包含数千个项。 请不要手动添加合成键，而应该定义客户端逻辑来连接值，并将合成键插入 Cosmos 容器的项中。

## <a name="use-a-partition-key-with-a-random-suffix"></a>搭配隨機尾碼使用分割區索引鍵

另一個更平均散發工作負載的可行策略是在分割區索引鍵值結尾附加一個隨機數字。 當您以此方式散發項目時，您可以跨分割區執行平行的寫入作業。

其中一個範例是分割區索引鍵代表日期的情況。 则你可以选择介于 1 和 400 之间的随机数，并将其作为后缀连接到该日期。 此方法会生成如下所示的分区键值： `2018-08-09.1`、`2018-08-09.2` ...  `2018-08-09.400`。 由於您是將分割區索引鍵隨機化，因此每天在容器上的寫入作業都會平均分散在多個分割區中。 此方法會產生更好的平行處理原則及整體更高的輸送量。

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>使用具有预先计算的后缀的分区键 

尽管随机后缀策略能够大幅提高写入吞吐量，但却难以读取特定的项。 你不知道写入该项时使用的后缀值。 若要更轻松地读取单个项，请使用预先计算的后缀策略。 此策略不是使用随机数在分区之间分配项，而是使用根据想要查询的内容计算的某个数字。

沿用前面的示例，其中的某个容器使用日期作为分区键。 现在假设每个项有一个我们需要访问的  `Vehicle-Identification-Number` (`VIN`) 属性。 此外，假设你经常运行查询来按 `VIN` 和日期查找项。 在您的應用程式將項目寫入到容器之前，它可以根據 VIN 來計算雜湊尾碼，並將它附加至分割區索引鍵日期。 这种计算可以生成介于 1 和 400 之间的均匀分布数字。 此结果类似于随机后缀策略方法生成的结果。 分割區索引鍵值先前是搭配計算結果串連的日期。

使用這個策略，寫入會在分割區索引鍵值之間以及在分割區之間平均分散。 由于可以计算特定 `Vehicle-Identification-Number` 的分区键值，因此可以轻松读取特定的项和日期。 此方法的好处是可以避免创建单个热分区键（即，取所有工作负荷值的分区键）。 

## <a name="next-steps"></a>後續步驟

您可以在下列文章中深入了解分割概念：

* 深入了解[邏輯分割區](partition-data.md)。
* 深入了解如何[在 Azure Cosmos 容器和資料庫上佈建輸送量](set-throughput.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
