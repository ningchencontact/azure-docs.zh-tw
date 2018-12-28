---
title: Azure Cosmos DB 資料表 API .NET Standard SDK 和資源
description: 全面了解 Azure Cosmos DB 資料表 API 和 .NET Standard SDK，包括發行日期、停用日期及每個版本之間所做的變更。
services: cosmos-db
author: donghexu
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/18/2018
ms.author: dox
ms.openlocfilehash: dd095328f8ba4cc22eea21133e7c8600a2cd64e1
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438654"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB 資料表 .NET Standard API：下載和版本資訊
> [!div class="op_single_selector"]

> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK 下載**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**目前支援的架構**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>版本資訊

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-預覽
* 新支援對 Azure 儲存體資料表端點的核心 CRUD、批次及查詢作業。 [!NOTE] 尚未支援之前 Azure 儲存體資料表 SDK 中的某些功能，例如用戶端加密。

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-預覽
* Azure Cosmos DB 資料表 .NET Standard SDK 是一個跨平台 .NET 程式庫，可提供有效率地存取 Cosmos DB 上資料表資料模型的權限。 這個初始版本支援一組完整的資料表和實體 CRUD + 查詢功能，其中具有類似[適用於 .NET Framework 的 Cosmos DB 資料表 SDK](table-sdk-dotnet.md) 的 API。 [!NOTE] 0.9.1-預覽版本中尚未支援 Azure 儲存體資料表端點。

## <a name="release-and-retirement-dates"></a>發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [0.10.0-預覽](#0.10.0-preview) |2018 年 12 月 18 日 |--- |
| [0.9.1-預覽](#0.9.1-preview) |2018 年 10 月 18 日 |--- |


## <a name="faq"></a>常見問題集

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱
若要深入了解 Azure Cosmos DB 資料表 API，請參閱 [Azure Cosmos DB 資料表 API 的簡介](table-introduction.md)。 
