---
title: 使用 Gremlin 搭配 Azure Cosmos DB 資源權杖
description: 了解如何建立資源權杖，並使用它們來存取圖形資料庫
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: fcb18fb14cf787713735da07ca2048d0853fa46c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2019
ms.locfileid: "70807009"
---
# <a name="azure-cosmos-db-resource-tokens-with-gremlin"></a>使用 Gremlin 搭配 Azure Cosmos DB 資源權杖
本文說明如何使用 [Cosmos DB 的資源權杖](secure-access-to-data.md)，透過 Gremlin SDK 存取圖形資料庫。

## <a name="create-a-resource-token"></a>建立資源權杖

TinkerPop Gremlin SDK 沒有 API 可建立資源權杖。 資源權杖是 Cosmos DB 的概念。 若要建立資源權杖，請下載 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)。 如果您的應用程式需要建立資源權杖，並使用它們來存取圖形資料庫，則需要 2 個不同的 SDK。

資源權杖上方的物件模型階層：
- **Cosmos DB 帳戶** - 具有相關聯 DNS 的最上層實體，例如 `contoso.gremlin.cosmos.azure.com`
  - **Cosmos DB 資料庫**
    - **使用者**
      - **權限**
        - *Token* - **Permission** 物件的屬性，代表允許或拒絕的動作。

資源權杖的格式為 `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`。 此字串對用戶端而言是不透明的，而且應該在不修改或解譯的情況下使用。

```csharp
// Notice that document client is created against .NET SDK end-point rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // Token will not be returned during ReadPermissionReedAsync() call.
  // This call will succeed only if database id, user id and permission id already exist. 
  // Note that <database id> is not a database name, it is a base64 string that represents database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>使用資源權杖
當您建立 `GremlinServer` 類別時，可以直接使用資源權杖作為 "password" 屬性。

```csharp
// Gremlin application needs to be given a resource token. It can't discover the token on its own.
// Token can be obtained for a given permission using Cosmos DB SDK or passed into the application as command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure gremlin servier to use resource token rather than master key
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

相同的方法適用於所有的 TinkerPop Gremlin SDK。

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>限制

單一 Gremlin 帳戶可以發出不限數量的權杖，但在 **1 小時**內只能同時使用最多 **100** 個權杖。 如果應用程式超過每小時的權杖限制，系統將會拒絕驗證要求，並出現錯誤訊息 `"Exceeded allowed resource token limit of 100 that can be used concurrently"`。 關閉使用特定權杖的作用中連線不會為新權杖釋出位置。 Cosmos DB Gremlin 資料庫引擎會在過去一小時內追蹤不同的權杖，然後再進行驗證要求。

## <a name="permission"></a>權限

應用程式在使用資源權杖時，會遇到的常見錯誤為 `"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header."`。 當 Gremlin 周遊嘗試寫入邊線或頂點，但資源權杖僅授與 `Read` 權限時，就會傳回此錯誤。 檢查您的周遊是否包含下列任一步驟：`.addV()`、`.addE()`、`.drop()` 或 `.property()`。

## <a name="next-steps"></a>後續步驟
* Azure Cosmos DB 中的[角色型存取控制](role-based-access-control.md)
* [了解如何保護對 Azure Cosmos DB 中資料的存取](secure-access-to-data.md)
