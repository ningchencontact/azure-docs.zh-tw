---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: e319356d555f26354ea29dc7be068bf6168abb17
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455157"
---
## <a name="local-settings-file"></a>本機設定檔

Local.settings.json 檔案會儲存應用程式設定、 連接字串和本機開發工具所使用的設定。 在本機執行時，才會使用 local.settings.json 檔案中的設定。 本機設定檔具有下列結構：

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

在本機執行時，會支援下列設定：

| 設定      | 描述                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | 當設定為`true`，所有的值會使用本機電腦金鑰加密。 需搭配 `func settings` 命令使用。 預設值為 `false`。 |
| **`Values`** | 應用程式設定和在本機執行時所使用的連接字串的陣列。 這些索引鍵 / 值 （字串字串） 組對應至在 Azure 中，函數應用程式中的應用程式設定這類[ `AzureWebJobsStorage` ]。 許多觸發程序和繫結具有的屬性，是指連接字串的應用程式設定，像是`Connection`for [Blob 儲存體觸發程序](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration)。 對於這類屬性中，您需要應用程式設定中定義`Values`陣列。 <br/>[`AzureWebJobsStorage`] 必要的應用程式，設定 HTTP 以外的觸發程序。 <br/>版本 2.x 的 Functions 執行階段需要 [`FUNCTIONS_WORKER_RUNTIME`] 設定，以產生您專案的核心工具。 <br/> 當您擁有[Azure 儲存體模擬器](../articles/storage/common/storage-use-emulator.md)安裝在本機，您可以設定[ `AzureWebJobsStorage` ]至`UseDevelopmentStorage=true`和 Core Tools 會使用模擬器。 此功能在開發期間非常實用，但您應在部署前先透過實際的儲存體連接進行測試。<br/> 值必須是字串並不是 JSON 物件或陣列。 設定名稱不能包含冒號 (`:`) 或雙底線 (`__`); 這些保留的執行階段。  |
| **`Host`** | 此區段中的設定能自訂於本機執行的 Functions 主機處理序。 這些都是個別從 host.json 設定中，也適用於 Azure 中執行時。 |
| **`LocalHttpPort`** | 設定於執行本機 Functions 主機 (`func host start` 和 `func run`) 時所使用的預設連接埠。 `--port` 命令列選項的優先順序高於此值。 |
| **`CORS`** | 定義針對[跨來源資源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 所允許的來源。 來源是以不含空格的逗號分隔清單提供。 支援萬用字元值 (\*)，它允許來自任何來源的要求。 |
| **`CORSCredentials`** |  將它設定為 true，允許`withCredentials`要求。 |
| **`ConnectionStrings`** | 請勿將此集合用於您函式繫結所使用的連接字串。 這個集合只能由通常會從連接字串的架構`ConnectionStrings`一節中的某個組態檔，例如[Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)。 此物件中的連接字串會新增至具有 [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) 提供者類型的環境。 此集合中的項目不會發佈至具備其他應用程式設定的 Azure。 您必須明確將這些值來`Connection strings`函式應用程式設定的集合。 如果您要建立[ `SqlConnection` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx)函式程式碼中，您應該儲存中的連接字串值**應用程式設定**在入口網站中與您其他的連線。 |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
