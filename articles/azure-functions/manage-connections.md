---
title: 如何管理 Azure Functions 中的連線
description: 了解如何使用靜態連線用戶端來避免 Azure Functions 中的效能問題。
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: glenga
ms.openlocfilehash: 9780f98be7d488f87e280a1f391f7cc536ce0f8c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999573"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>如何管理 Azure Functions 中的連線

函式應用程式中的函式會共用資源，而這些共用資源之中有下列連線 &mdash; HTTP 連線、資料庫連線，以及對 Azure 服務 (例如儲存體) 的連線。 同時執行許多函式時，可能會將可用的連線用完。 本文說明如何將您的函式編碼，以避免使用超過實際需要的連線。

## <a name="connections-limit"></a>連線限制

可用的連線數目受限，部分是因為函式應用程式會在 [Azure App Service 沙箱](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)中執行。 沙箱加諸於程式碼的其中一個限制就是[連線數目上限，目前為 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits)。 當您到達此限制時，函式執行階段會建立包含下列訊息的記錄：`Host thresholds exceeded: Connections`。

當[調整控制器新增函數應用程式執行個體](functions-scale.md#how-the-consumption-plan-works)以處理更多要求時，超過限制的可能性就會增加。 每個函數應用程式執行個體一次可執行許多函式，而所有函式都會使用計入 300 限制的連線。

## <a name="use-static-clients"></a>使用靜態用戶端

若要避免保有超過所需的連線，請重複使用用戶端執行個體，而不是在每次函式引動過程建立新的執行個體。 如果您使用單一靜態用戶端，則 .NET 用戶端 (例如 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) \(英文\)、[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) \(英文\) 和 Azure 儲存體用戶端) 可以管理連線。

以下是在 Azure Functions 應用程式中使用特定服務用戶端時所要遵循的一些指導方針：

- **請勿**在每次函式引動過程建立新的用戶端。
- **請**建立單一靜態用戶端，以供每次函式引動過程使用。
- 如果不同函式使用相同的服務，**請考慮**在共用協助程式類別中建立單一靜態用戶端。

## <a name="client-code-examples"></a>用戶端程式碼範例

本節示範如何從您的函式程式碼建立和使用用戶端的最佳做法。

### <a name="httpclient-example-c"></a>HttpClient 範例 (C#)

以下 C# 函式程式碼範例會建立靜態 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)：

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

.NET [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) \(英文\) 的常見問題是「我應該處置我的用戶端嗎？」 在一般情況下，您會在使用完實作 `IDisposable` 的物件時加以處置。 但您不會處置靜態用戶端，因為在函式結束時，您還為使用完畢。 您希望靜態用戶端在您應用程式的使用期間存留。

### <a name="http-agent-examples-nodejs"></a>HTTP 代理程式範例 (Node.js)

因為它提供更好的連線管理選項，所以，您應該使用原生的 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) \(英文\) 類別而不是非原生的方法，例如 `node-fetch` 模組。 連線參數是使用 `http.agent` 類別上的選項來設定的。 如需使用 HTTP 代理程式提供的詳細選項，請參閱 [new Agent(\[options\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options) \(英文\)。

`http.request()` 所使用的全域 `http.globalAgent` 會將這些值全都設定為其各自的預設值。 在 Functions 中設定連線限制的建議方式是全域設定最大數目。 下列範例會針對函式應用程式設定通訊端數目上限：

```js
http.globalAgent.maxSockets = 200;
```

 下列範例只會針對該要求，使用自訂 HTTP 代理程式來建立新的 HTTP 要求。

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

您的函式程式碼可能會使用 .NET Framework Data Provider for SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) \(英文\)) 來連線到 SQL 關聯式資料庫。 這也是依賴 ADO.NET 之資料架構的基礎提供者，例如 Entity Framework。 不同於 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) \(英文\) 和 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) \(英文\) 連線，ADO.NET 預設會實作連線共用。 不過，由於您仍然可將連線用完，因此，您應該將與資料庫的連線最佳化。 如需詳細資訊，請參閱 [SQL Server 連線共用 (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling) \(機器翻譯\)。

> [!TIP]
> 部分資料架構 (例如 [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx) \(英文\)) 通常會從設定檔的 **ConnectionStrings** 區段取得連接字串。 在此情況下，您必須明確地將 SQL 資料庫連接字串新增至函數應用程式設定的**連接字串**集合，以及您本機專案的 [local.settings.json 檔案](functions-run-local.md#local-settings-file)中。 如果您要在函式程式碼中建立 [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) \(英文\)，您應該將連接字串值以及您的其他連線一起儲存於**應用程式設定**中。

## <a name="next-steps"></a>後續步驟

如需有關為何建議使用靜態用戶端的詳細資訊，請參閱[不適當的具現化反模式](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)。

如需更多 Azure Functions 效能祕訣，請參閱[將 Azure Functions 效能和可靠性最佳化](functions-best-practices.md)。
