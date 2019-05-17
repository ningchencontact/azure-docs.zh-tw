---
title: 管理 Azure Functions 中的连接
description: 了解如何使用靜態連線用戶端來避免 Azure Functions 中的效能問題。
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: e15d6ad445c3fdde0632c3ad468eee7da836a394
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785967"
---
# <a name="manage-connections-in-azure-functions"></a>管理 Azure Functions 中的连接

函数应用中的函数共享资源。 这些共享的资源包括连接：HTTP 连接、数据库连接以及到 Azure 存储之类服务的连接。 同時執行許多函式時，可能會將可用的連線用完。 本文介绍如何编写函数，避免使用超出需要的连接数。

## <a name="connection-limit"></a>连接限制

可用连接数量受到限制，部分原因是函数应用在[沙盒环境](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)中运行。 每個執行個體是沙箱加諸於您的程式碼的限制之一 （目前為 600 的作用中連接和 1,200 中連線總數） 的連線數目上限。 當您到達此限制時，函式執行階段會建立包含下列訊息的記錄：`Host thresholds exceeded: Connections`。

此限制是按实例施加的。  [缩放控制器添加函数应用实例](functions-scale.md#how-the-consumption-and-premium-plans-work)以处理更多请求时，每个实例都有单独的连接限制。 这意味着没有全局连接限制，你可以跨所有活动实例建立比 600 个活动连接多得多的连接。

進行疑難排解時，請確定您已啟用 Application Insights 函式應用程式。 Application Insights 可讓您檢視您的函式應用程式，例如執行的計量。 如需詳細資訊，請參閱 < [Application Insights 中檢視遙測](functions-monitoring.md#view-telemetry-in-application-insights)。  

## <a name="static-clients"></a>静态客户端

若要避免保有超過所需的連線，請重複使用用戶端執行個體，而不是在每次函式引動過程建立新的執行個體。 不管编写函数时使用什么语言，建议重复使用客户端连接。 例如，如果使用单个静态客户端，[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)、[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) 和 Azure 存储客户端等 .NET 客户端可以管理连接。

在 Azure Functions 应用程序中使用特定于服务的客户端时，请遵循以下准则：

- 不要在每次调用函数时创建新的客户端。
- 应创建一个可在每次调用函数时使用的静态客户端。
- 如果不同的函数使用相同的服务，请考虑在共享帮助程序类中创建单个静态客户端。

## <a name="client-code-examples"></a>用戶端程式碼範例

本節示範如何從您的函式程式碼建立和使用用戶端的最佳做法。

### <a name="httpclient-example-c"></a>HttpClient 範例 (C#)

下面是用于创建静态 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) 实例的 C# 函数代码示例：

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

关于 .NET 中的 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) 的一个常见问题是“我应该释放我的客户端吗？” 一般情况下，在使用对象实现了 `IDisposable` 之后，你会释放这些对象。 但是，你不会释放静态客户端，因为在函数结束后还需要使用它。 您希望靜態用戶端在您應用程式的使用期間存留。

### <a name="http-agent-examples-javascript"></a>HTTP 代理示例 (JavaScript)

因為它提供更好的連線管理選項，所以，您應該使用原生的 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) \(英文\) 類別而不是非原生的方法，例如 `node-fetch` 模組。 连接参数通过 `http.agent` 类上的选项配置。 有关 HTTP 代理的可用详细选项，请参阅 [new Agent(\[options\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)。

`http.request()` 使用的全局 `http.globalAgent` 类将这些值全部设置为各自的默认值。 在 Functions 中設定連線限制的建議方式是全域設定最大數目。 下列範例會針對函式應用程式設定通訊端數目上限：

```js
http.globalAgent.maxSockets = 200;
```

 以下示例创建一个新的 HTTP 请求，并采用仅用于该请求的自定义 HTTP 代理：

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient 程式碼範例 (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) \(英文\) 會連線到 Azure Cosmos DB 執行個體。 Azure Cosmos DB 文件建議您[在應用程式存留期內使用單一 Azure Cosmos DB 用戶端](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage)。 下列範例顯示在函式中執行該作業的一種模式：

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>CosmosClient 代码示例 (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) 连接到 Azure Cosmos DB 实例。 Azure Cosmos DB 文件建議您[在應用程式存留期內使用單一 Azure Cosmos DB 用戶端](../cosmos-db/performance-tips.md#sdk-usage)。 下列範例顯示在函式中執行該作業的一種模式：

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const masterKey = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, auth: { masterKey } });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { result: itemArray } = await container.items.readAll().toArray();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient 連線

函数代码可使用 SQL Server 的 .NET Framework 数据提供程序 ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) 连接到 SQL 关系数据库。 这也是依赖于 ADO.NET 的数据框架（例如[实体框架](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)）的基础提供程序。 不同於 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) \(英文\) 和 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) \(英文\) 連線，ADO.NET 預設會實作連線共用。 但是，由于连接仍可能耗尽，因此应优化数据库连接。 如需詳細資訊，請參閱 [SQL Server 連線共用 (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling) \(機器翻譯\)。

> [!TIP]
> 某些数据框架（例如实体框架）通常从配置文件的 **ConnectionStrings** 节获取连接字符串。 在此情況下，您必須明確地將 SQL 資料庫連接字串新增至函數應用程式設定的**連接字串**集合，以及您本機專案的 [local.settings.json 檔案](functions-run-local.md#local-settings-file)中。 如果要在函数代码中创建 [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) 的实例，则应将连接字符串值与其他连接一起存储在应用程序设置中。

## <a name="next-steps"></a>後續步驟

如需有關為什麼建議靜態用戶端的詳細資訊，請參閱[具現化反模式](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)。

如需更多 Azure Functions 效能祕訣，請參閱[將 Azure Functions 效能和可靠性最佳化](functions-best-practices.md)。
