---
title: "Azure 服務匯流排與 Event Grid 的整合範例 | Microsoft Docs"
description: "服務匯流排傳訊與 Event Grid 的整合範例"
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 3819a274696762861fbe76a9684b8495f1724f6a
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="azure-service-bus-to-azure-event-grid-examples"></a>Azure 服務匯流排與 Azure Event Grid 的整合範例

在本文件中，您可了解如何設定 Azure 函式和邏輯應用程式，以根據從 Event Grid 接收事件來接收訊息。 此範例假設服務匯流排主題有兩個訂用帳戶，而且以只針對一個服務匯流排訂用帳戶傳送事件的方式建立 Event Grid 訂用帳戶。 您接著會將訊息傳送至訊息主題並確認已針對此服務匯流排訂用帳戶產生事件，然後函式或邏輯應用程式會收到來自該服務匯流排訂用帳戶的訊息並加以完成。

* 先確定您已備妥所有[必要條件](#prerequisites)。
* 建立[簡單測試 Azure 函式](#test-function-setup)，以便偵錯及查看來自 Event Grid 的初始事件流量。  **無論是執行 3 或 4，都應該完成此步驟。**
* 建立 [Azure 函式來接收和處理服務匯流排訊息](#receive-messages-using-azure-function) (以 Event Grid 事件為基礎)。
* 利用 [Logic Apps](#receive-messages-using-azure-logic-app)。

## <a name="prerequisites"></a>先決條件

### <a name="service-bus-namespace"></a>服務匯流排 命名空間

建立服務匯流排進階命名空間。 建立具有兩個訂用帳戶的服務匯流排主題。

### <a name="code-to-send-message-to-the-service-bus-topic"></a>撰寫程式碼以將訊息傳送至服務匯流排主題

您可以使用許多方法，將訊息傳送至服務匯流排主題。 或者，可以使用以下的範例。 範例程式碼假設您目前使用 Visual Studio 2017。

複製[此 GitHub 存放庫](https://github.com/Azure/azure-service-bus/)。

瀏覽到下列資料夾：

\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration 並開啟檔案：SBEventGridIntegration.sln。

然後瀏覽至 MessageSender 專案並開啟 Program.cs。

![8][]

填入主題名稱以及您的連接字串，並執行主控台應用程式：

```CSharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
const string TopicName = "YOUR TOPIC NAME";
```

## <a name="test-function-setup"></a>測試函式設定

在處理整個端對端案例之前，您希望至少有一個小型測試函式，以便用來偵錯及查看哪些事件正在流動。

在入口網站中，建立新的 Azure 函式應用程式。 遵循此[連結](https://docs.microsoft.com/en-us/azure/azure-functions/)以了解 Azure Functions 的基本概念。

在您新建立的函式中，按一下小加號以新增 http 觸發程序函式：

![2][]

![3][]

然後將下列程式碼複製到函式中：

```CSharp
#r "Newtonsoft.Json"
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    // parse query parameter
    var content = req.Content;

    string jsonContent = await content.ReadAsStringAsync(); 
    log.Info($"Received Event with payload: {jsonContent}");

IEnumerable<string> headerValues;
if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
{
var validationHeaderValue = headerValues.FirstOrDefault();
if(validationHeaderValue == "SubscriptionValidation")
{
var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
     var code = events[0].Data["validationCode"];
     return req.CreateResponse(HttpStatusCode.OK,
     new { validationResponse = code });
}
}

    return jsonContent == null
    ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
    : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
}

public class GridEvent
{
    public string Id { get; set; }
    public string EventType { get; set; }
    public string Subject { get; set; }
    public DateTime EventTime { get; set; }
    public Dictionary<string, string> Data { get; set; }
    public string Topic { get; set; }
}
```

按一下儲存並執行。

## <a name="connect-function-and-namespace-via-event-grid"></a>透過 Event Grid 連線函式和命名空間

下一個步驟是將函式與服務匯流排命名空間繫結在一起。 在此範例中，使用 Azure 入口網站。 請參閱[概念](service-bus-to-event-grid-integration-concept.md)頁面，以了解如何使用 PowerShell 或 Azure CLI 來達成相同目的。

若要建立新的 Azure Event Grid 訂用帳戶，請在 Azure 入口網站中瀏覽至您的命名空間並選取 [Event Grid] 刀鋒視窗。 按一下 [+ 事件訂用帳戶]。

下列螢幕擷取畫面顯示一個命名空間，而該命名空間已經有一些 Event Grid 訂用帳戶。

![20][]

下列螢幕擷取畫面顯示如何訂閱 Azure 函式或 Web Hook (不使用任何特定篩選條件)。 請記得在 [後置詞篩選條件] 中，為您的服務匯流排訂用帳戶新增相關的篩選條件：

![21][]

如必要條件所述，將傳送訊息至服務匯流排主題，並透過 Azure 函式的 [監視] 功能來確認事件正在流動。

![9][]

### <a name="receive-messages-using-azure-function"></a>使用 Azure 函式接收訊息

在前一節中，您已看到簡單測試和偵錯案例，並確定事件正在流動。 在文件的這一部分中，您將觀察如何在接收事件時接收和處理訊息。

用下列方式新增 Azure 函式的原因，是因為 Azure Functions 內的服務匯流排函式本身還不支援新的 Event Grid 整合。

在您於必要條件中開啟的相同 Visual Studio 方案中，選取 ReceiveMessagesOnEvent.cs。 在程式碼中輸入您的連接字串：

![10][]

```Csharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
```

然後前往 Azure 入口網站，並針對您在 [2.測試函式設定](#2-test-function-setup)之前建立的 Azure 函式下載發行設定檔。

![11][]

然後在 Visual Studio 中，以滑鼠右鍵按一下 SBEventGridIntegration 並選取 [發佈]。 使用您之前下載的發行設定檔，選取 [匯入設定檔] 並按一下 [發佈]。

![12][]

發佈新的 Azure 函式之後，建立指向新 Azure 函式的新 Azure Event Grid 訂用帳戶。 確定您套用正確的「結尾為」篩選條件，這應該是您的服務匯流排訂用帳戶名稱。

然後將訊息傳送到您之前建立的 Azure 服務匯流排主題，並在入口網站的 Azure 函式記錄中檢查事件是否正在流動並已收到訊息。

![12-1][]

### <a name="receive-messages-using-azure-logic-app"></a>使用 Azure 邏輯應用程式接收訊息

下列指示說明如何將 Azure 邏輯應用程式與 Azure 服務匯流排和 Azure Event Grid 連在一起：

首先，在 Azure 入口網站中建立新的邏輯應用程式，並選取 Event Grid 作為啟動動作。

![13][]

Logic Apps 設計工具中的初始檢視應如下列螢幕擷取畫面所示，而您需要以自己的命名空間名稱取代 [資源名稱]。 此外，務必展開進階選項，並且為您的訂用帳戶新增後置詞篩選條件：

![14][]

然後新增服務匯流排 [接收] 動作，以從主題訂用帳戶進行接收。 最終動作應如以下螢幕擷取畫面所示。

![15][]

然後新增完成事件，其看起來如下所示。

![16][]

如必要條件所述，儲存邏輯應用程式並將訊息傳送至服務匯流排主題。 然後觀察邏輯應用程式的執行狀況。 如果您按一下 [概觀]，則會顯示 [執行歷程記錄]，您也可以取得更多有關執行的資料。

![17][]

![18][]

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/)。
* 深入了解 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)。
* 深入了解 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/)。
* 深入了解 [Azure 服務匯流排](https://docs.microsoft.com/azure/service-bus/)。

[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
