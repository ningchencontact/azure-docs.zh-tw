---
title: 如何搭配 Azure Functions 使用 Azure Cosmos DB 變更摘要
description: 搭配 Azure Functions 使用 Azure Cosmos DB 變更摘要
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 18780deba1910b3ea77f7313bfb6d204dacabd82
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112016"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>使用 Azure Cosmos DB 與 Azure Functions 的無伺服器事件型架構

Azure Functions 提供最簡單的方式來連接到[變更摘要](change-feed.md)。 您可以建立小型反應式 Azure 函式，將會自動觸發在您的 Azure Cosmos 容器的變更摘要中的每個新事件。

![使用 Azure Cosmos DB 觸發程序無伺服器與事件為基礎的函數](./media/change-feed-functions/functions.png)

具有[Azure Cosmos DB 觸發程序](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)，您可以利用[變更摘要處理器](./change-feed-processor.md)的調整和可靠的事件偵測功能，而不需要維護任何[背景工作角色基礎結構](./change-feed-processor.md#implementing-the-change-feed-processor-library)。 把重點放在您的 Azure 函式邏輯而不需擔心事件來源的管線的其餘部分。 您甚至可以混合使用任何其他觸發程序[Azure Functions 繫結](../azure-functions/functions-triggers-bindings.md#supported-bindings)。

> [!NOTE]
> 目前，用於與核心 (SQL) API 僅支援 Azure Cosmos DB 觸發程序。

## <a name="requirements"></a>需求

若要實作無伺服器事件為基礎的流量，您需要：

* **受監視的容器**:受監視的容器是受監視的 Azure Cosmos 容器，並會據以產生變更摘要的資料。 任何插入和受監視的容器 (例如 CRUD) 變更會反映在容器的變更摘要中。
* **租用容器**:租用容器會跨多個維護狀態，並動態無伺服器的 Azure 函式執行個體，並可讓您動態調整。 此租用容器可以手動或自動建立 Azure Cosmos DB Trigger.To 自動建立租用容器，請設定*CreateLeaseCollectionIfNotExists*中的旗標[組態](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). 資料分割的租用容器都必須有`/id`資料分割索引鍵定義。

## <a name="create-your-azure-cosmos-db-trigger"></a>建立 Azure Cosmos DB 觸發程序

在所有 Azure Functions IDE 和 CLI 的整合現在支援使用 Azure Cosmos DB 觸發程序中建立您的 Azure 函式：

* [Visual Studio 擴充功能](../azure-functions/functions-develop-vs.md)適用於 Visual Studio 使用者。
* [Visual Studio 核心延伸模組](https://code.visualstudio.com/tutorials/functions-extension/create-function)Visual Studio Code 使用者。
* 最後[Core CLI 工具](../azure-functions/functions-run-local.md#create-func)跨平台 IDE 無從驗證體驗。

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>在本機執行 Azure Cosmos DB 觸發程序

您可以執行您[在本機 Azure Function](../azure-functions/functions-develop-local.md)具有[Azure Cosmos DB 模擬器](./local-emulator.md)建立及開發您無伺服器事件為基礎的流程而不需要有 Azure 訂用帳戶，或不會產生任何費用。

如果您想要測試雲端中的即時案例，您可以[免費試用 Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)沒有任何信用卡或所需的 Azure 訂用帳戶。

## <a name="next-steps"></a>後續步驟

您現在可以繼續深入了解變更摘要在下列文章：

* [變更摘要的概觀](change-feed.md)
* [讀取變更摘要的方式](read-change-feed.md)
* [使用變更摘要處理器程式庫](change-feed-processor.md)
* [如何使用變更摘要處理器程式庫](change-feed-processor.md)
* [使用 Azure Cosmos DB 和 Azure Functions 的無伺服器資料庫計算](serverless-computing-database.md)
