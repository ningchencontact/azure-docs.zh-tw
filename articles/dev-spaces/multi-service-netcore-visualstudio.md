---
title: 使用 .NET Core 和 Visual Studio 執行多個相依服務
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: 在 Azure 上使用容器和微服務快速進行 Kubernetes 開發
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
ms.openlocfilehash: 66a08ad674477da478ec7037833fe4cb836f9bb0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357024"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>使用 Azure Dev Spaces 進行多服務開發

在本教學課程中，您將了解如何使用 Azure Dev Spaces 來開發多服務應用程式，以及享有 Dev Spaces 提供的一些附加好處。

## <a name="call-another-container"></a>呼叫另一個容器
在本節中，您會建立第二個服務 `mywebapi`，並由 `webfrontend` 加以呼叫。 每個服務都會在個別的容器中執行。 然後，您將在這兩個容器間進行偵錯。

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>下載 *mywebapi* 的範例程式碼
為了節省時間，我們將從 GitHub 存放庫下載範例程式碼。 請移至 https://github.com/Azure/dev-spaces，然後選取 [複製或下載]，從 GitHub 存放庫進行下載。 本節的程式碼位於 `samples/dotnetcore/getting-started/mywebapi`。

### <a name="run-mywebapi"></a>執行 *mywebapi*
1. 在個別的 Visual Studio 視窗中開啟專案 `mywebapi`。
1. 從啟動設定下拉式清單中選取 [Azure 開發人員空間]，如同您先前對 `webfrontend` 專案所做的。 這次請不要建立新的 AKS 叢集，而應選取您已建立的同一個叢集。 與之前相同，將 [空間] 保留為預設的 `default`，然後按一下 [確定]。 在 [輸出] 視窗中，您可能會發現 Visual Studio 開始在開發人員空間中「準備」這項新服務，以在您開始偵錯時加速作業。
1. 按 F5，並等候服務進行建置和部署。 當 Visual Studio 的狀態列變成橙色時，即表示已就緒
1. 記下 [輸出] 視窗的 [適用於 AKS 的 Azure 開發人員空間] 窗格中顯示的 URL 端點。 輸出應該會類似於 `http://localhost:<portnumber>`。 容器可能看起來像在本機執行，但實際是在 Azure 的開發人員空間中執行。
2. 當 `mywebapi` 就緒時，請開啟瀏覽器並進入 localhost 位址，然後將 `/api/values` 附加至 URL，以叫用 `ValuesController` 的預設 GET API。 
3. 如果所有步驟都已成功，您應該會看到 `mywebapi` 服務產生如下的回應。

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>從 webfrontend 發出對 mywebapi 的要求
我們現在要在 `webfrontend` 中撰寫對 `mywebapi` 發出要求的程式碼。 切換至含有 `webfrontend` 專案的 Visual Studio 視窗。 在 `HomeController.cs` 檔案中，將 About 方法的程式碼取代為下列程式碼：

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

上述程式碼範例會將傳入要求中的 `azds-route-as` 標頭轉送至傳出要求。 您稍後將會了解此類別如何促進[小組案例](team-development-netcore-visualstudio.md)中的生產力開發體驗。

### <a name="debug-across-multiple-services"></a>在多個服務間進行偵錯
1. 此時，`mywebapi` 仍應使用附加的偵錯工具執行中。 如果不是，在 `mywebapi` 專案中按 F5。
1. 在 `Controllers/ValuesController.cs` 檔案中負責處理 `api/values/{id}` GET 要求的 `Get(int id)` 方法內設定中斷點。
1. 在您已貼上前述程式碼的 `webfrontend` 專案中，請將中斷點設定在傳送 GET 要求給 `mywebapi/api/values` 的前一刻。
1. 在 `webfrontend` 專案中按 F5。 Visual Studio 會重新開啟瀏覽器並進入適當 localhost 連接埠，且會顯示 Web 應用程式。
1. 按一下頁面頂端的 [關於] 連結，以觸發 `webfrontend` 專案中的中斷點。 
1. 按 F10 繼續作業。 此時會觸發 `mywebapi` 專案中的中斷點。
1. 按 f5 繼續作業，且您會返回 `webfrontend` 專案中的程式碼。
1. 再按一次 F5 將會完成要求，並在瀏覽器中傳回頁面。 在 Web 應用程式中，[關於] 頁面會顯示由兩項服務串連的訊息：「來自 webfrontend 的 Hello 和來自 mywebapi 的 Hello」。

做得好！ 現在，您已有每個容器可以個別開發和部署的多容器應用程式。

### <a name="automatic-tracing-for-http-messages"></a>自動追蹤 HTTP 訊息
您可能已經注意到，雖然 webfrontend 沒有任何特殊程式碼來輸出其對 mywebapi 發出的 HTTP 呼叫，但您可以在輸出視窗中看到 HTTP 追蹤訊息：
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --gyk-> webfrontend:
   GET /Home/About HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend-668b7ddb9f-n5rhj --pu5-> mywebapi:
   GET /api/values/1 HTTP/1.1

// Response from *mywebapi*
webfrontend-668b7ddb9f-n5rhj <-pu5-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-gyk-- webfrontend:
   HTTP/1.1 200 OK
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="utf-8" />
       <meta name="viewport" content="width=device-width, initial-sc...<[TRUNCATED]>
```
這是您可以從 Dev Spaces 檢測設備中取得的「免費」好處之一。 我們插入元件來追蹤通過系統的 HTTP 要求，讓您在開發期間可更輕鬆地追蹤複雜的多服務呼叫。

### <a name="well-done"></a>做得好！
現在，您已有每個容器可以個別開發和部署的多容器應用程式。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解 Dev Spaces 中的小組開發](team-development-netcore-visualstudio.md)
