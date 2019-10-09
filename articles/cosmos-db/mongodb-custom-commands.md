---
title: 管理適用於 MongoDB 的 Azure Cosmos DB 的 API 中所儲存資料的 MongoDB 延伸模組命令
description: 本文說明如何使用 MongoDB 的延伸模組命令來管理適用於 MongoDB 的 Azure Cosmos DB 的 API 中所儲存資料。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: 94b1048befc8716caf5f7f51adb1f95d047d4077
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64925662"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>使用 MongoDB 的延伸模組命令來管理適用於 MongoDB 的 Azure Cosmos DB 的 API 中所儲存資料 

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以與 Azure Cosmos DB API 通訊 mongodb 使用任何開放原始碼[MongoDB 用戶端驅動程式](https://docs.mongodb.org/ecosystem/drivers)。 Azure Cosmos DB 的 MongoDB API 藉由遵循使用現有的用戶端驅動程式[MongoDB 有線通訊協定](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)。

您可以使用 Azure Cosmos DB 的 API for MongoDB，您即可享有 Cosmos DB 全域散發、 自動分區、 高可用性、 延遲保證、 自動、 待用加密，備份，例如，和許多，同時保留您的投資在您的 MongoDB 應用程式。

## <a name="mongodb-protocol-support"></a>MongoDB 的通訊協定支援

根據預設，Azure Cosmos DB API for MongoDB 適用於 MongoDB 伺服器 3.2 版，如需詳細資訊，請參閱[支援的功能和語法](mongodb-feature-support.md)。 MongoDB 3.4 版中新增的查詢運算子的功能是目前可供 Azure Cosmos DB API for MongoDB 中預覽。 下列延伸模組命令執行 CRUD 作業，儲存在 Azure Cosmos DB 的 API，適用於 MongoDB 的資料時，支援特定功能，Azure Cosmos DB:

* [建立資料庫](#create-database)
* [更新資料庫](#update-database)
* [取得資料庫](#get-database)
* [建立集合](#create-collection)
* [更新集合](#update-collection)
* [取得集合](#get-collection)

## <a id="create-database"></a> 建立資料庫

建立資料庫的擴充功能命令會建立新的 MongoDB 資料庫。 從命令執行所針對的資料庫內容時使用的資料庫名稱。 CreateDatabase 命令的格式如下所示：

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

下表描述在命令中的參數：

|**欄位**|**型別** |**描述** |
|---------|---------|---------|
| customAction   |  string  |   名稱的自訂命令，它必須是"CreateDatabase 」。      |
| offerThroughput | int  | 您在資料庫設定的佈建的輸送量。 這個參數是選擇性的。 |

### <a name="output"></a>Output

傳回預設的自訂命令回應。 請參閱[預設輸出](#default-output)的自訂命令輸出中的參數。

### <a name="examples"></a>範例

**建立資料庫**

若要建立名為"test"的資料庫，請使用下列命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**建立與輸送量的資料庫**

若要建立的資料庫名為"test"和 1000 Ru 的佈建的輸送量，請使用下列命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a> 更新資料庫

更新資料庫擴充功能命令會更新具有指定之資料庫相關聯的屬性。 目前，您只能更新 「 offerThroughput"屬性。

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

下表描述在命令中的參數：

|**欄位**|**型別** |**描述** |
|---------|---------|---------|
| customAction    |    string     |   自訂命令名稱。 必須是"UpdateDatabase 」。      |
|  offerThroughput   |  int       |     您想要在資料庫上設定的新佈建的輸送量。    |

### <a name="output"></a>Output

傳回預設的自訂命令回應。 請參閱[預設輸出](#default-output)的自訂命令輸出中的參數。

### <a name="examples"></a>範例

**更新與資料庫相關聯的已佈建的輸送量**

若要更新資料庫的佈建的輸送量，以名稱"test"1200 ru，使用下列命令：

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a> 取得資料庫

取得資料庫的擴充功能命令會傳回資料庫物件。 從命令執行所針對的資料庫內容時使用的資料庫名稱。

```
{
  customAction: "GetDatabase"
}
```

下表描述在命令中的參數：


|**欄位**|**型別** |**描述** |
|---------|---------|---------|
|  customAction   |   string      |   自訂命令名稱。 必須是 「 GetDatabase"|
        
### <a name="output"></a>Output

如果命令成功，回應會包含具有下列欄位的文件：

|**欄位**|**型別** |**說明** |
|---------|---------|---------|
|  `ok`   |   `int`     |   回應的狀態。 1 = = 成功。 0 = = 失敗。      |
| `database`    |    `string`        |   資料庫名稱。      |
|   `provisionedThroughput`  |    `int`      |    設定資料庫的佈建的輸送量。 這是選擇性的回應參數。     |

如果命令失敗，則會傳回預設的自訂命令回應。 請參閱[預設輸出](#default-output)的自訂命令輸出中的參數。

### <a name="examples"></a>範例

**取得資料庫**

若要取得名為"test"的資料庫的資料庫物件，使用下列命令：

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a> 建立集合

建立集合的擴充功能命令會建立新的 MongoDB 集合。 從命令執行所針對的資料庫內容時使用的資料庫名稱。 CreateCollection 命令的格式如下所示：

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

下表描述在命令中的參數：

|**欄位**|**型別** |**描述** |
|---------|---------|---------|
| customAction    | string | 自訂命令名稱。 必須是 「 CreateCollection"     |
| collection      | string | 集合的名稱                                   |
| offerThroughput | int    | 若要在資料庫上設定的佈建的輸送量。 這是選擇性參數 |
| shardKey        | string | 分區金鑰路徑，以建立分區化集合。 這是選擇性參數 |

### <a name="output"></a>Output

傳回預設的自訂命令回應。 請參閱[預設輸出](#default-output)的自訂命令輸出中的參數。

### <a name="examples"></a>範例

**建立 unsharded 集合**

若要建立具有名稱"testCollection 」 和 1000 Ru 的佈建的輸送量 unsharded 集合，使用下列命令： 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**建立分區化集合**

若要建立分區化集合名稱"testCollection 」 與 1000 Ru 的佈建的輸送量，請使用下列命令：

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a> 更新集合

更新集合擴充功能命令會更新指定之集合相關聯的屬性。

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

下表描述在命令中的參數：

|**欄位**|**型別** |**描述** |
|---------|---------|---------|
|  customAction   |   string      |   自訂命令名稱。 必須是"UpdateCollection 」。      |
|  collection   |   string      |   集合的名稱。       |
| offerThroughput   |int|   若要設定的集合上佈建的輸送量。|

## <a name="output"></a>Output

傳回預設的自訂命令回應。 請參閱[預設輸出](#default-output)的自訂命令輸出中的參數。

### <a name="examples"></a>範例

**更新與集合相關聯的已佈建的輸送量**

若要更新具有名稱"testCollection 」 集合的佈建的輸送量，1200 ru，使用下列命令：

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a> 取得集合

取得集合的自訂命令傳回的集合物件。

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

下表描述在命令中的參數：


|**欄位**|**型別** |**描述** |
|---------|---------|---------|
| customAction    |   string      |   自訂命令名稱。 必須是"GetCollection 」。      |
| collection    |    string     |    集合的名稱。     |

### <a name="output"></a>Output

如果命令成功，回應會包含具有下列欄位的文件


|**欄位**|**型別** |**說明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   回應的狀態。 1 = = 成功。 0 = = 失敗。      |
| `database`    |    `string`     |   資料庫名稱。      |
| `collection`    |    `string`     |    集合的名稱。     |
|  `shardKeyDefinition`   |   `document`      |  做為分區化索引鍵 > 規格文件編製索引。 這是選擇性的回應參數。       |
|  `provisionedThroughput`   |   `int`      |    若要設定的集合上佈建的輸送量。 這是選擇性的回應參數。     |

如果命令失敗，則會傳回預設的自訂命令回應。 請參閱[預設輸出](#default-output)的自訂命令輸出中的參數。

### <a name="examples"></a>範例

**取得集合**

若要取得名為"testCollection 」 的集合的集合物件，使用下列命令：

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a> 預設輸出的自訂命令

如果未指定，自訂的回應會包含具有下列欄位的文件：

|**欄位**|**型別** |**說明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   回應的狀態。 1 = = 成功。 0 = = 失敗。      |
| `code`    |   `int`      |   命令失敗時，才傳回 （也就是 [確定] = = 0）。 包含 MongoDB 錯誤程式碼。 這是選擇性的回應參數。      |
|  `errMsg`   |  `string`      |    命令失敗時，才傳回 （也就是 [確定] = = 0）。 包含的使用者易記錯誤訊息。 這是選擇性的回應參數。      |

## <a name="next-steps"></a>後續步驟

接下來，您可以繼續了解下列的 Azure Cosmos DB 概念： 

* [Azure Cosmos DB 中的編製索引](../cosmos-db/index-policy.md)
* [利用存留時間讓 Azure Cosmos DB 中的資料自動過期](../cosmos-db/time-to-live.md)
