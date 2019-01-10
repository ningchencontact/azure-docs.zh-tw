---
title: 使用 mongoimport 和 mongorestore 將 MongoDB 資料遷移至 Azure Cosmos DB
description: 您會了解如何使用 mongoimport 和 mongorestore 將資料匯入到 Cosmos DB。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
Customer intent: As a developer, I want to migrate the data from my existing MongoDB to Cosmos DB.
ms.openlocfilehash: 9226a49af67659ebd7bebd9beca397830ee808bb
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039040"
---
# <a name="migrate-your-mongodb-data-to-azure-cosmos-db"></a>將 MongoDB 資料遷移至 Azure Cosmos DB

 本教學課程會提供相關指示，讓您了解如何將 MongoDB 中儲存的資料，遷移至 Azure Cosmos DB (設定為使用 Cosmos DB 的 MongoDB API)。 如果您從 MongoDB 匯入資料，而且想要將這些資料用於 Azure Cosmos DB SQL API，請使用[資料移轉工具](import-data.md)匯入資料。

在本教學課程中，您將：

> [!div class="checklist"]
> * 準備移轉計劃。
> * 使用 mongoimport 遷移資料。
> * 使用 mongorestore 遷移資料。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

在開始移轉之前，請先檢閱並完成下列先決條件。

### <a name="plan-for-the-migration"></a>規劃移轉

本節說明如何規劃資料移轉作業。 我們會預估 RU 費用、判斷機器到雲端服務的延遲，並計算批次大小和插入背景工作角色的數目。


#### <a name="pre-create-and-scale-your-collections"></a>預先建立並調整您的集合

使用 mongoimport 或 mongorestore 開始遷移之前，請先從 [Azure 入口網站](https://portal.azure.com)或 MongoDB 驅動程式與工具預先建立您的所有集合。 

從 [Azure 入口網站](https://portal.azure.com)增加您的集合用於移轉的輸送量。 藉由較高的輸送量，您可以避免速率受限，並花費較少的時間進行移轉。 您可以在移轉之後立即減少輸送量，以節省成本。

除了在集合層級佈建輸送量，您也可以在資料庫層級佈建輸送量，以供一組集合共用所佈建的輸送量。 您需要預先建立資料庫和集合，並為所共用輸送量資料庫中的每個集合定義分區索引鍵。

您可以使用慣用的工具、驅動程式或 SDK 來建立分區化集合。 在此範例中，我們使用 Mongo 殼層來建立分區化集合：

```bash
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
此命令會傳回下列結果：

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

#### <a name="calculate-the-approximate-ru-charge-for-a-single-document-write"></a>計算單一文件寫入大約需要的 RU 費用

從 MongoDB 殼層連線至 Cosmos 帳戶，該帳戶已設定為使用 Cosmos DB 的 MongoDB API。 您可以在[將 MongoDB 應用程式連線至 Cosmos DB](connect-mongodb-account.md) 中找到指示。

接下來，使用其中一個範例文件來執行範例插入命令：
   
```bash
db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
```
        
執行命令 `db.runCommand({getLastRequestStatistics: 1})`。

您會收到類似下列輸出的回應：
     
```bash
globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "insert",
    "RequestCharge": 10,
    "RequestDurationInMilliSeconds": NumberLong(50)
}
```
        
記下要求費用。
    
#### <a name="determine-the-latency-from-your-machine-to-cosmos-db"></a>判斷從機器到 Cosmos DB 的延遲
    
從 MongoDB 殼層使用 `setVerboseShell(true)` 命令啟用詳細資訊記錄。
    
使用 `db.coll.find().limit(1)` 命令對資料庫執行基本查詢。

您會收到類似下列輸出的回應：

```bash
Fetched 1 record(s) in 100(ms)
```
        
執行移轉之前，請先移除插入的文件，以確保沒有重複的文件。 您可以使用 `db.coll.remove({})` 命令來移除文件。

#### <a name="calculate-the-approximate-values-for-the-batchsize-and-numinsertionworkers-properties"></a>計算 batchSize 和 numInsertionWorkers 屬性的約略值

針對 **batchSize** 屬性，請將所佈建的總輸送量 (RU/秒) 除以單一文件寫入所取用的 RU，如＜判斷從機器到 Cosmos DB 的延遲＞一節所完成的作業。 如果計算所得的值小於或等於 24，請使用該數字作為屬性值。 如果計算所得的值大於 24，則將屬性值設定為 24。
    
針對 **numInsertionWorkers** 屬性的值，請使用此方程式：

`numInsertionWorkers = (Provisioned RUs throughput * Latency in seconds) / (batchSize * Consumed RUs for a single write)`

我們可以使用下列值來計算 **numInsertionWorkers** 屬性的值：

| 屬性 | 值 |
|--------|-----|
| **batchSize** | 24 |
| 所佈建的 RU | 10,000 |
| Latency | 0.100 秒 |
| 所取用的 RU | 10 RU |
| **numInsertionWorkers** | (10,000 RUs x 0.100 s) / (24 x 10 RUs) = **4.1666** |

執行 **monogoimport** 移轉命令。 本文稍後會說明這些命令參數。

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
```

您也可以使用 **monogorestore** 命令。 請確定所有集合的輸送量都設定為等於或大於先前計算中使用的 RU 數目。
   
```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
```

### <a name="complete-the-prerequisites"></a>完成先決條件

在規劃移轉之後，請完成下列步驟： 

* **取得範例資料**：在開始移轉之前，請先確定您有一些範例資料。 

* **增加輸送量**：資料移轉的持續時間長短取決於您為個別集合或資料庫所佈建的輸送量。 針對較大資料移轉，請務必增加輸送量。 完成移轉之後，再降低輸送量以節省成本。 

* **啟用 SSL**：Cosmos DB 有嚴格的安全性需求和標準。 與您的 Cosmos 帳戶互動時，請務必啟用 SSL。 本文中的程序包括如何針對 mongoimport 和 mongorestore 命令啟用 SSL。

* **建立 Cosmos DB 資源**：在開始移轉之前，請先從 Azure 入口網站預先建立所有集合。 如果您遷移至具有資料庫層級佈建輸送量的 Cosmos 帳戶，請在建立集合時提供分割區索引鍵。

* **取得連接字串**：在 [Azure 入口網站](https://portal.azure.com)中，選取左邊的 [Azure Cosmos DB] 項目。 在 [訂用帳戶] 底下，選取您的帳戶名稱。 在 [連接字串] 底下，選取 [連接字串]。 入口網站右側會顯示要連線至帳戶所需的資訊：

    ![連接字串資訊](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="use-mongoimport"></a>使用 mongoimport

若要將資料匯入 Cosmos 帳戶，請使用下列範本。

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

將 \<your_hostname>、\<your_username> 和 \<your_password> 參數替換為您帳戶特有的值。 在下列範例中，我們會使用 **sampleDB** 作為 \<your_database> 的值，並使用 **sampleColl** 作為 \<your_collection> 的值：

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="use-mongorestore"></a>使用 mongorestore

若要將資料還原至 Cosmos 帳戶 (設定了 Cosmos DB 的 MongoDB API)，請使用下列範本來執行匯入。

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

將 \<your_hostname>、\<your_username> 和 \<your_password> 參數替換為您帳戶特有的值。 在下列範例中，我們會使用 **./dumps/dump-2016-12-07** 作為 \<path_to_backup> 的值：

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --db mydatabase --collection mycollection --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="clean-up-resources"></a>清除資源

若不再需要這些資源，您可以刪除資源群組、Cosmos 帳戶和所有相關資源。 使用下列步驟來刪除資源群組：

1. 移至您在其中建立 Cosmos 帳戶的資源群組。
1. 選取 [刪除資源群組]。
1. 確認要刪除的資源群組名稱，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

繼續進行下一個教學課程，以了解如何使用 Azure Cosmos DB 的 MongoDB API 來查詢資料。 

> [!div class="nextstepaction"]
> [如何查詢 MongoDB 資料](../cosmos-db/tutorial-query-mongodb.md)
