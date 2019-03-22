---
title: 在 Azure Functions 中管理連線
description: 了解如何使用靜態連線用戶端來避免 Azure Functions 中的效能問題。
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 965fa1e82be3fb87bf58a0114f97091bad212738
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450731"
---
# <a name="manage-connections-in-azure-functions"></a>在 Azure Functions 中管理連線

函式應用程式中的函式會共用資源。 這些共用的資源包括連線：HTTP 連接、 資料庫連線和連線至服務，例如 Azure 儲存體。 同時執行許多函式時，可能會將可用的連線用完。 這篇文章說明如何撰寫您的函式，以避免使用超過所需的更多連線程式碼。

## <a name="connection-limit"></a>連線限制

可用的連接數目很有限，部分原因函式應用程式中執行時，才[沙箱環境](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)。 沙箱加諸於您的程式碼的限制之一就是[（目前為 600 的作用中連接和 1,200 中連線總數） 的連線數目上限](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits)每個執行個體。 當您到達此限制時，函式執行階段會建立包含下列訊息的記錄：`Host thresholds exceeded: Connections`。

這項限制是每個執行個體。  當[縮放控制器會將函式應用程式執行個體](functions-scale.md#how-the-consumption-plan-works)若要處理更多的要求，每個執行個體有獨立的連線限制。 這表示沒有全域連線限制，且您可以在所有作用中的執行個體遠超過 600 個作用中的連線。

## <a name="static-clients"></a>靜態用戶端

若要避免保有超過所需的連線，請重複使用用戶端執行個體，而不是在每次函式引動過程建立新的執行個體。 我們建議您重複使用任何語言，您可以撰寫您的函式中的用戶端連線。 例如，.NET 用戶端想[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)， [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)，與 Azure 儲存體用戶端可以管理連線，如果您使用單一的靜態用戶端。

以下是一些可在 Azure Functions 應用程式中使用的特定服務的用戶端時，請依照下列指導方針：

- *不這麼做*每個函式引動過程建立新的用戶端。
- *請勿*建立單一的靜態的用戶端，可以使用每個函式引動過程。
- *請考慮*建立單一的靜態用戶端共用的協助程式類別中，如果不同的函式會使用相同的服務。

## <a name="client-code-examples"></a>用戶端程式碼範例

本節示範如何從您的函式程式碼建立和使用用戶端的最佳做法。

### <a name="httpclient-example-c"></a>HttpClient 範例 (C#)

以下是範例C#函式程式碼會建立靜態[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)執行個體：

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

關於常見的問題[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)在.NET 中是"應該我處置我的用戶端嗎？ 」 一般情況下，您處置的物件，可實作`IDisposable`當您完成使用它們。 因為您未完成，未處置靜態用戶端，但函式結束時，請使用它。 您希望靜態用戶端在您應用程式的使用期間存留。

### <a name="http-agent-examples-nodejs"></a>HTTP 代理程式範例 (Node.js)

因為它提供更好的連線管理選項，所以，您應該使用原生的 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) \(英文\) 類別而不是非原生的方法，例如 `node-fetch` 模組。 連接參數上時，已透過選項`http.agent`類別。 如需詳細選項可以使用與 HTTP 代理程式，請參閱[新的代理程式 (\[選項\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)。

全域`http.globalAgent`所使用的類別`http.request()`具有所有這些值設為其各自的預設值。 在 Functions 中設定連線限制的建議方式是全域設定最大數目。 下列範例會針對函式應用程式設定通訊端數目上限：

```js
http.globalAgent.maxSockets = 200;
```

 下列範例會建立新的 HTTP 要求，只針對該要求的自訂 HTTP 代理程式：

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

## <a name="sqlclient-connections"></a>SqlClient 連線

您的函式程式碼可以使用.NET Framework Data Provider for SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) 連線至 SQL 關聯式資料庫。 這也是依賴 ADO.NET 中，例如資料架構的基礎提供者[Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)。 不同於 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) \(英文\) 和 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) \(英文\) 連線，ADO.NET 預設會實作連線共用。 但是，因為您仍然可以執行沒有足夠的連線，您應該最佳化資料庫的連接。 如需詳細資訊，請參閱 [SQL Server 連線共用 (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling) \(機器翻譯\)。

> [!TIP]
> 有些資料架構，例如 Entity Framework 通常會從連接字串**ConnectionStrings**組態檔區段。 在此情況下，您必須明確地將 SQL 資料庫連接字串新增至函數應用程式設定的**連接字串**集合，以及您本機專案的 [local.settings.json 檔案](functions-run-local.md#local-settings-file)中。 如果您要建立的執行個體[SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx)函式程式碼中，您應該儲存中的連接字串值**應用程式設定**與您其他的連線。

## <a name="next-steps"></a>後續步驟

如需有關為什麼建議靜態用戶端的詳細資訊，請參閱[具現化反模式](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)。

如需更多 Azure Functions 效能祕訣，請參閱[將 Azure Functions 效能和可靠性最佳化](functions-best-practices.md)。
