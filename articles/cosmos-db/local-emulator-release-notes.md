---
title: Azure Cosmos 模擬器下載和版本資訊
description: 閱讀 Azure Cosmos 模擬器版本資訊並下載。
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 5985d0d82341c76993ee91b8dff6927edd1ed8b4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332149"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>使用 Azure Cosmos 模擬器進行本機開發和測試

本文顯示 Azure Cosmos 模擬器版本資訊，以及每個版本中所做的功能更新清單。 它也會列出最新版本的模擬器以供下載和使用。

## <a name="download"></a>下載

| | |
|---------|---------|
|**MSI 下載**|[Microsoft 下載中心](https://aka.ms/cosmosdb-emulator)|
|**開始使用**|[使用 Azure Cosmos 模擬器在本機開發](local-emulator.md)|

## <a name="release-notes"></a>版本資訊

### <a name="243"></a>2.4.3

- 停用依預設啟動 MongoDB 服務。 只有 SQL 端點預設為啟用。 使用者必須使用模擬器的 "/EnableMongoDbEndpoint" 命令列選項，以手動啟動端點。 如同所有其他服務端點，如 Gremlin、Cassandra 和資料表。
- 已修正模擬器中的錯誤 (bug)，當從 “/AllowNetworkAccess” 開頭，其中 Gremlin、Cassandra 和資料表端點未正確處理來自外部用戶端的要求。
- 將直接連線連接埠新增到防火牆規則設定中。

### <a name="240"></a>2.4.0

- 已修正當主機電腦上有網路監視應用程式 (如 Pulse Client) 時，模擬器無法啟動的問題。
