---
title: 如何在 Azure Cosmos DB 中設定多重主機
description: 了解如何在 Azure Cosmos DB 中對應用程式設定多重主機
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: effe6fa942ce0cabace08e72dba90baf8646680e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118800"
---
# <a name="how-to-configure-multi-master-in-your-applications-in-azure-cosmos-db"></a>如何在 Azure Cosmos DB 中對應用程式設定多重主機

若要在應用程式中使用多重主機功能，您必須在應用程式中啟用多區域寫入，並藉由設定應用程式目前部署所在的區域，來設定多路連接功能。

## <a id="netv2"></a>.NET SDK v2

若要在應用程式中啟用多重主機，請將 `UseMultipleWriteLocations` 設定為 true，並將 `SetCurrentLocation` 設定為要在其中部署應用程式和複寫 Cosmos DB 的區域。

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

若要在應用程式中啟用多重主機，請將 `UseCurrentRegion` 設定為要在其中部署應用程式和複寫 Cosmos DB 的區域。

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

若要在應用程式中啟用多重主機，請將 `policy.setUsingMultipleWriteLocations(true)` 設定為 true，並將 `policy.setPreferredLocations` 設定為要在其中部署應用程式和複寫 Cosmos DB 的區域。

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

## <a id="javascript"></a>Node.js、JavaScript、TypeScript SDK

若要在應用程式中啟用多重主機，請將 `connectionPolicy.UseMultipleWriteLocations` 設定為 true，並將 `connectionPolicy.PreferredLocations` 設定為要在其中部署應用程式和複寫 Cosmos DB 的區域。

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

若要在應用程式中啟用多重主機，請將 `connection_policy.UseMultipleWriteLocations` 設定為 true，並將 `connection_policy.PreferredLocations` 設定為要在其中部署應用程式和複寫 Cosmos DB 的區域。

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>後續步驟

深入了解 Azure Cosmos DB 中的多重主機、全域散發和一致性。 請參閱下列文章：

* [利用工作階段權杖來管理 Azure Cosmos DB 中的一致性](how-to-manage-consistency.md#utilize-session-tokens)

* [Azure Cosmos DB 中的衝突類型和解決原則](conflict-resolution-policies.md)

* [Azure Cosmos DB 中的高可用性](high-availability.md)

* [在 Azure Cosmos DB 中選擇正確的一致性層級](consistency-levels-choosing.md)

* [Azure Cosmos DB 中一致性、可用性和效能的取捨](consistency-levels-tradeoffs.md)
