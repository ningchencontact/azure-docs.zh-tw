---
title: 使用 .NET Core 和 VS Code 執行多個相依服務 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: 在 Azure 上使用容器和微服務快速進行 Kubernetes 開發
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器
ms.openlocfilehash: 39c9d2f938dd3e06a328626756ddcc80d0aad47b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821229"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>使用 Azure Dev Spaces 進行多服務開發

在本教學課程中，您將了解如何使用 Azure Dev Spaces 來開發多服務應用程式，以及享有 Dev Spaces 提供的一些附加好處。

## <a name="call-a-service-running-in-a-separate-container"></a>呼叫在個別容器中執行的服務

在本節中，您會建立第二個服務 `mywebapi`，並由 `webfrontend` 加以呼叫。 每個服務都會在個別的容器中執行。 然後，您將在這兩個容器間進行偵錯。

![多個容器](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>下載 *mywebapi* 的範例程式碼
為了節省時間，我們將從 GitHub 存放庫下載範例程式碼。 請移至 https://github.com/Azure/dev-spaces，然後選取 [複製或下載]，從 GitHub 存放庫進行下載。 本節的程式碼位於 `samples/dotnetcore/getting-started/mywebapi`。

### <a name="run-mywebapi"></a>執行 *mywebapi*
1. 在個別的 VS Code 視窗中開啟資料夾 `mywebapi`。
1. 開啟 [命令選擇區] (使用 [檢視 | 命令選擇區] 功能表)，然後使用自動完成功能以輸入及選取以下命令：`Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。 此命令不應與 `azds prep` 命令混淆；後者會設定要部署的專案。
1. 按 F5，並等候服務進行建置和部署。 當 VS Code 偵錯列出現時，表示已就緒。
1. 端點 URL 看起來會像是 http://localhost:\<portnumber\>。 **提示：VS Code 狀態列會顯示可點按的 URL。** 容器可能看起來像在本機執行，但實際是在 Azure 的開發人員空間中執行。 localhost 位址的原因是因為 `mywebapi` 並未定義任何公用端點，而只能從 Kubernetes 執行個體中存取。 為了方便您操作以及與本機電腦上的私人服務互動，Azure 開發人員空間會建立暫存的 SSH 通道，連到在 Azure 中執行的容器。
1. 當 `mywebapi` 就緒時，請開啟瀏覽器並進入 localhost 位址。 請將 `/api/values` 附加至 URL，以叫用 `ValuesController` 的預設 GET API。
1. 如果所有步驟都已成功，您應該會看到 `mywebapi` 服務的回應。

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>從 webfrontend 發出對 mywebapi 的要求
我們現在要在 `webfrontend` 中撰寫對 `mywebapi` 發出要求的程式碼。
1. 切換至 `webfrontend` 的 VS Code 視窗。
1. 在 `HomeController.cs` 中取代 About 方法的程式碼：

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

上述程式碼範例會將傳入要求中的 `azds-route-as` 標頭轉送至傳出要求。 您稍後將了解這對小組的[共同開發](team-development-netcore.md)有何幫助。

### <a name="debug-across-multiple-services"></a>在多個服務間進行偵錯
1. 此時，`mywebapi` 仍應使用附加的偵錯工具執行中。 如果不是，在 `mywebapi` 專案中按 F5。
1. 在處理 `api/values/{id}` GET 要求的 `Get(int id)` 方法中設定中斷點。
1. 在 `webfrontend` 專案中，請將中斷點設定在傳送 GET 要求給 `mywebapi/api/values` 的前一刻。
1. 在 `webfrontend` 專案中按 F5。
1. 叫用 Web 應用程式，並在這兩項服務中逐步執行程式碼。
1. 在 Web 應用程式中，[關於] 頁面會顯示由兩項服務串連的訊息：「來自 webfrontend 的 Hello 和來自 mywebapi 的 Hello」。

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
> [了解 Dev Spaces 中的小組開發](team-development-netcore.md)