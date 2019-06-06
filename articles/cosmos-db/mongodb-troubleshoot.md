---
title: Mongo DB 為 Azure Cosmos DB 的 API 中的常見錯誤進行疑難排解
description: 此文件會討論如何對 Azure Cosmos DB 的 API 中發現適用於 MongoDB 的常見問題進行疑難排解。
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 5b3d3993a497240c1ea18f0fcf852c0e834f6e79
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735702"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>疑難排解常見的問題，在 Azure Cosmos DB 的 API for MongoDB

Azure Cosmos DB 實作常見的 NoSQL 資料庫，包括 MongoDB 有線通訊協定。 因為網路通訊協定實作中，您可以無障礙地互動使用 Azure Cosmos DB 使用現有的用戶端 Sdk、 驅動程式和工具，使用 NoSQL 資料庫。 Azure Cosmos DB 不會使用資料庫的任何原始碼 NoSQL 資料庫的任何提供網路相容的 Api。 了解網路通訊協定版本任何 MongoDB 用戶端驅動程式可以連線到 Azure Cosmos DB。

適用於 MongoDB 的 Azure Cosmos DB 的 API 相容 3.2 版的 MongoDB 有線通訊協定 （查詢運算子和 3.4 版中新增的功能是目前以預覽形式提供） 時，有一些自訂的錯誤代碼，Azure Cosmos DB特定的錯誤。 這篇文章會說明不同的錯誤、 錯誤代碼，以及解決這些錯誤的步驟。

## <a name="common-errors-and-solutions"></a>常見的錯誤和解決方案

| Error               | 代碼  | 描述  | 解決方法  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 取用的要求單位總數已超過針對集合佈建的要求單位率並已進行節流。 | 請考慮調整指派給容器或一組的容器，從 Azure 入口網站的輸送量，或您可以重試此作業。 |
| ExceededMemoryLimit | 16501 | 做為多租用戶服務，作業已超出用戶端的記憶體配額。 | 透過更嚴格的查詢準則來縮小作業的範圍，或經由 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)連絡支援人員。 範例： `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| MongoDB 連線版本的問題 | - | 舊版的 MongoDB 驅動程式將無法偵測在連接字串中的 Azure Cosmos 帳戶的名稱。 | 附加*appName = @**accountName** @* 結尾的您的 Cosmos DB API for MongoDB 連接字串，其中***accountName***是您的 Cosmos DB 帳戶名稱. |


## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Studio 3T](mongodb-mongochef.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 了解如何[使用 Robo 3T](mongodb-robomongo.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 使用 Azure Cosmos DB 適用於 MongoDB 的 API 瀏覽 Cosmos DB [範例](mongodb-samples.md)。

