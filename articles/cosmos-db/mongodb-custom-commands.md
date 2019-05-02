---
title: 管理適用於 MongoDB 的 Azure Cosmos DB 的 API 中所儲存資料的 MongoDB 延伸模組命令
description: 本文說明如何使用 MongoDB 的延伸模組命令來管理適用於 MongoDB 的 Azure Cosmos DB 的 API 中所儲存資料。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: 94b1048befc8716caf5f7f51adb1f95d047d4077
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925662"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>使用 MongoDB 的延伸模組命令來管理適用於 MongoDB 的 Azure Cosmos DB 的 API 中所儲存資料 

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以與 Azure Cosmos DB API 通訊 mongodb 使用任何開放原始碼[MongoDB 用戶端驅動程式](https://docs.mongodb.org/ecosystem/drivers)。 Azure Cosmos DB 的 MongoDB API 藉由遵循使用現有的用戶端驅動程式[MongoDB 有線通訊協定](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)。

您可以使用 Azure Cosmos DB 的 API for MongoDB，您即可享有 Cosmos DB 全域散發、 自動分區、 高可用性、 延遲保證、 自動、 待用加密，備份，例如，和許多，同時保留您的投資在您的 MongoDB 應用程式。

## <a name="mongodb-protocol-support"></a>MongoDB 协议支持

根據預設，Azure Cosmos DB API for MongoDB 適用於 MongoDB 伺服器 3.2 版，如需詳細資訊，請參閱[支援的功能和語法](mongodb-feature-support.md)。 MongoDB 3.4 版中新增的查詢運算子的功能是目前可供 Azure Cosmos DB API for MongoDB 中預覽。 下列延伸模組命令執行 CRUD 作業，儲存在 Azure Cosmos DB 的 API，適用於 MongoDB 的資料時，支援特定功能，Azure Cosmos DB:

* [创建数据库](#create-database)
* [更新数据库](#update-database)
* [获取数据库](#get-database)
* [创建集合](#create-collection)
* [更新集合](#update-collection)
* [获取集合](#get-collection)

## <a id="create-database"></a> 建立資料庫

“创建数据库”扩展命令可创建新的 MongoDB 数据库。 数据库名称取自该命令所针对的数据库上下文。 CreateDatabase 命令的格式如下：

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

下表描述了该命令中的参数：

|**欄位**|**類型** |**說明** |
|---------|---------|---------|
| customAction   |  string  |   自定义命令的名称，必须是“CreateDatabase”。      |
| offerThroughput | int  | 对数据库设置的预配吞吐量。 這是選擇性參數。 |

### <a name="output"></a>輸出

返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>範例

**建立資料庫**

若要创建名为“test”的数据库，请使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**创建具有吞吐量的数据库**

若要创建名为“test”、预配吞吐量为 1000 RU 的数据库，请使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a> 更新資料庫

“更新数据库”扩展命令可更新与指定的数据库相关联的属性。 目前只能更新“offerThroughput”属性。

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

下表描述了该命令中的参数：

|**欄位**|**類型** |**說明** |
|---------|---------|---------|
| customAction    |    string     |   自定义命令的名称。 必须是“UpdateDatabase”。      |
|  offerThroughput   |  int       |     要对数据库设置的新预配吞吐量。    |

### <a name="output"></a>輸出

返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>範例

**更新与数据库关联的预配吞吐量**

若要将名为“test”的数据库的预配吞吐量更新为 1200 RU，请使用以下命令：

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a> 取得資料庫

“获取数据库”扩展命令返回数据库对象。 数据库名称取自该命令所针对的数据库上下文。

```
{
  customAction: "GetDatabase"
}
```

下表描述了该命令中的参数：


|**欄位**|**類型** |**說明** |
|---------|---------|---------|
|  customAction   |   string      |   自定义命令的名称。 必须是“GetDatabase”|
        
### <a name="output"></a>輸出

如果该命令成功，则响应包含带有以下字段的文档：

|**欄位**|**類型** |**說明** |
|---------|---------|---------|
|  `ok`   |   `int`     |   响应的状态。 1 == 成功。 0 == 失败。      |
| `database`    |    `string`        |   資料庫名稱。      |
|   `provisionedThroughput`  |    `int`      |    对数据库设置的预配吞吐量。 这是一个可选的响应参数。     |

如果该命令失败，则返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>範例

**获取数据库**

若要获取名为“test”的数据库的数据库对象，请使用以下命令：

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a> 建立集合

“创建集合”扩展命令可创建新的 MongoDB 集合。 数据库名称取自该命令所针对的数据库上下文。 CreateCollection 命令的格式如下：

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

下表描述了该命令中的参数：

|**欄位**|**類型** |**說明** |
|---------|---------|---------|
| customAction    | string | 自定义命令的名称。 必須是 「 CreateCollection"     |
| collection      | string | 集合的名称                                   |
| offerThroughput | int    | 要对数据库设置的预配吞吐量。 它是一个可选参数 |
| shardKey        | string | 要在其中创建分片集合的分片键路径。 它是一个可选参数 |

### <a name="output"></a>輸出

返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>範例

**创建未分片集合**

若要创建名为“testCollection”、预配吞吐量为 1000 RU 的未分片集合，请使用以下命令： 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**创建分片集合**

若要创建名为“testCollection”、预配吞吐量为 1000 RU 的分片集合，请使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a> 更新集合

“更新集合”扩展命令可更新与指定的集合相关联的属性。

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

下表描述了该命令中的参数：

|**欄位**|**類型** |**說明** |
|---------|---------|---------|
|  customAction   |   string      |   自定义命令的名称。 必须是“UpdateCollection”。      |
|  collection   |   string      |   集合的名称。       |
| offerThroughput   |int|   要对集合设置的预配吞吐量。|

## <a name="output"></a>輸出

返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>範例

**更新与集合关联的预配吞吐量**

若要将名为“testCollection”的集合的预配吞吐量更新为 1200 RU，请使用以下命令：

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a> 取得集合

“获取集合”自定义命令返回集合对象。

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

下表描述了该命令中的参数：


|**欄位**|**類型** |**說明** |
|---------|---------|---------|
| customAction    |   string      |   自定义命令的名称。 必须是“GetCollection”。      |
| collection    |    string     |    集合的名称。     |

### <a name="output"></a>輸出

如果该命令成功，则响应包含带有以下字段的文档


|**欄位**|**類型** |**說明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   响应的状态。 1 == 成功。 0 == 失败。      |
| `database`    |    `string`     |   資料庫名稱。      |
| `collection`    |    `string`     |    集合的名称。     |
|  `shardKeyDefinition`   |   `document`      |  用作分片键的索引规范文档。 这是一个可选的响应参数。       |
|  `provisionedThroughput`   |   `int`      |    要对集合设置的预配吞吐量。 这是一个可选的响应参数。     |

如果该命令失败，则返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>範例

**获取集合**

若要获取名为“testCollection”的集合的集合对象，请使用以下命令：

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a> 預設輸出的自訂命令

如果未指定，则自定义响应包含带有以下字段的文档：

|**欄位**|**類型** |**說明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   响应的状态。 1 == 成功。 0 == 失败。      |
| `code`    |   `int`      |   仅当命令失败时才返回（即，正常 == 0）。 包含 MongoDB 错误代码。 这是一个可选的响应参数。      |
|  `errMsg`   |  `string`      |    仅当命令失败时才返回（即，正常 == 0）。 包含用户友好的错误消息。 这是一个可选的响应参数。      |

## <a name="next-steps"></a>後續步驟

接下来，可以继续学习以下 Azure Cosmos DB 概念： 

* [Azure Cosmos DB 中的編製索引](../cosmos-db/index-policy.md)
* [利用存留時間讓 Azure Cosmos DB 中的資料自動過期](../cosmos-db/time-to-live.md)
