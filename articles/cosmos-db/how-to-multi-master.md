---
title: 如何在 Azure Cosmos DB 中設定多重主機
description: 了解如何在 Azure Cosmos DB 中對應用程式設定多重主機。
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: 86f5d64391dd5312d8c51a5b639b790e62b6710d
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560248"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>在使用 Azure Cosmos DB 的應用程式中設定多重主機

若要在應用程式中使用多重主機功能，您必須在 Azure Cosmos DB 中啟用多區域寫入並設定多路連接功能。 若要設定多路連接，請設定應用程式部署所在的區域。

## <a id="netv2"></a>.NET SDK v2

若要在應用程式中啟用多重主機，請將 `UseMultipleWriteLocations` 設定為 `true`。 此外，將 `SetCurrentLocation` 設定為要在其中部署應用程式的區域，以及複寫 Azure Cosmos DB 的所在區域：

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>.NET SDK v3 (預覽)

若要在應用程式中啟用多重主機，請將 `UseCurrentRegion` 設定為要在其中部署應用程式和複寫 Cosmos DB 的區域：

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

若要在應用程式中啟用多重主機，請設定 `policy.setUsingMultipleWriteLocations(true)`，並將 `policy.setPreferredLocations` 設定為要在其中部署應用程式和複寫 Cosmos DB 的區域：

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a id="javascript"></a>Node.js、JavaScript 及 TypeScript SDK

若要在應用程式中啟用多重主機，請將 `connectionPolicy.UseMultipleWriteLocations` 設定為 `true`。 此外，將 `connectionPolicy.PreferredLocations` 設定為要在其中部署應用程式的區域，以及複寫 Cosmos DB 的所在區域：

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a id="python"></a>Python SDK

若要在應用程式中啟用多重主機，請將 `connection_policy.UseMultipleWriteLocations` 設定為 `true`。 此外，將 `connection_policy.PreferredLocations` 設定為要在其中部署應用程式的區域，以及複寫 Cosmos DB 的所在區域。

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>後續步驟

請閱讀下列文章：

* [使用工作階段權杖在 Azure Cosmos DB 中管理一致性](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB 中的衝突類型和解決原則](conflict-resolution-policies.md)
* [Azure Cosmos DB 中的高可用性](high-availability.md)
* [Azure Cosmos DB 中的一致性層級](consistency-levels.md)
* [在 Azure Cosmos DB 中選擇正確的一致性層級](consistency-levels-choosing.md)
* [Azure Cosmos DB 中一致性、可用性和效能的取捨](consistency-levels-tradeoffs.md)
* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
* [全域調整佈建的輸送量](scaling-throughput.md)
* [全球發佈：幕後](global-dist-under-the-hood.md)
