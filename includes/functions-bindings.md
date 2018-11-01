---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 17e17b9847306de1971ebcfbb79f1d95516cba57
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133351"
---
下表顯示兩個主要版本的 Azure Functions 執行階段中所支援的繫結。

| 類型 | 1.x | 2.x<sup>1</sup> | 觸發程序 | 輸入 | 輸出 |  
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob 儲存體](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|  
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|  
| [Event Grid](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |  
| [事件中樞](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|  
| [外部檔案](../articles/azure-functions/functions-bindings-external-file.md)<sup>2</sup>    |✔|| |✔|✔|  
| [外部資料表](../articles/azure-functions/functions-bindings-external-table.md)<sup>2</sup>  |✔|| |✔|✔|  
| [HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Microsoft Graph<br/>Excel 資料表](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft Graph<br/>OneDrive 檔案](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft Graph<br/>Outlook 電子郵件](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Microsoft Graph<br/>事件](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft Graph<br/>驗證權杖](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [行動應用程式](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|  
| [通知中樞](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [佇列儲存體](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|  
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [服務匯流排](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|  
| [資料表儲存體](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|  
| [計時器](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|
| [Webhook](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔||✔| |✔|
  
<sup>1</sup> 在 2.x 中，必須註冊 HTTP 和計時器以外的所有繫結。 請參閱[註冊繫結延伸模組](../articles/azure-functions/functions-triggers-bindings.md#register-binding-extensions)。

<sup>2</sup>不支援實驗性的 &mdash;，而且在未來可能會放棄。
