---
title: 使用 .NET Core 和 VS Code 在雲端建立 Kubernetes 開發人員空間 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: 在 Azure 上使用容器和微服務快速進行 Kubernetes 開發
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器
manager: douge
ms.openlocfilehash: bd42268c36f44dc20b88d27d19cbf378e848b82f
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823141"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core"></a>在使用 .NET Core 的 Azure 開發人員空間上開始使用

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

您現在可以在 Azure 中建立以 Kubernetes 為基礎的開發人員空間。

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>安裝 Azure CLI
Azure 開發人員空間需要基本的本機電腦設定。 大部分開發人員空間的組態都會儲存在雲端，而且可與其他使用者共用。 從下載和執行 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 著手。 

> [!IMPORTANT]
> 如果您已安裝 Azure CLI，請確定您使用的是 2.0.33 版或更高版本。

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

在您等候建立叢集時，您可以開始開發程式碼。

## <a name="create-a-web-app-running-in-a-container"></a>建立在容器中執行的 Web 應用程式

在本節中，您會建立 ASP.NET Core Web 應用程式，並使其在 Kubernetes 的容器中執行。

### <a name="create-an-aspnet-core-web-app"></a>建立 ASP.NET 核心 Web 應用程式
如果您已安裝 [.NET Core](https://www.microsoft.com/net)，您可以在名為 `webfrontend` 的資料夾中快速建立 ASP.NET Core Web 應用程式。
    
```cmd
dotnet new mvc --name webfrontend
```

或者，瀏覽至 https://github.com/Azure/dev-spaces 以**從 GitHub 下載範例程式碼**，然後選取 [複製或下載]，將 GitHub 存放庫下載到您的本機環境。 本指南的程式碼位於 `samples/dotnetcore/getting-started/webfrontend`。

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>更新內容檔案
Azure 開發人員空間不只讓程式碼中在 Kubernetes 中執行 - 還可讓您快速地反覆查看您的程式碼變更是否在雲端 Kubernetes 環境中生效。

1. 找出檔案 `./Views/Home/Index.cshtml` 並進行 HTML 編輯。 例如，將第 70 行 `<h2>Application uses</h2>` 變更如下：`<h2>Hello k8s in Azure!</h2>`
1. 儲存檔案。 稍後，您會在終端機視窗中看到一則訊息，指出執行中容器中的檔案已更新。
1. 移至您的瀏覽器並重新整理頁面。 您應該會看到網頁顯示更新後的 HTML。

發生什麼情形？ 編輯內容檔案 (例如 HTML 和 CSS) 時，不需要在 .NET Core Web 應用程式中重新編譯，所以作用中 `azds up` 命令會自動將任何修改過的內容檔案，直接同步處理到 Azure 中的執行中容器，您即可立即查看內容編輯。

### <a name="update-a-code-file"></a>更新程式碼檔案
更新程式碼檔案需要更多的工作，因為.NET Core 應用程式需要重建及產生更新後的應用程式二進位檔。

1. 在終端機視窗中，按 `Ctrl+C` (以停止 `azds up`)。
1. 開啟名為 `Controllers/HomeController.cs` 的程式碼檔案，然後編輯 [關於] 頁面將顯示的訊息：`ViewData["Message"] = "Your application description page.";`
1. 儲存檔案。
1. 在終端機視窗中執行 `azds up`。 

此命令會重建容器映像，並重新部署 Helm 圖表。 若要查看程式碼變更是否在執行中應用程式中生效，請移至 Web 應用程式中的 [關於] 功能表。


但是還有「更加快速的方法」可開發程式碼，您將在下一節中加以探索。 

## <a name="debug-a-container-in-kubernetes"></a>在 Kubernetes 中進行容器偵錯

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]


### <a name="select-the-azds-debug-configuration"></a>選取 AZDS 偵錯組態
1. 若要開啟 [偵錯] 檢視，請按一下 VS Code 側邊的 [活動列] 中的 [偵錯] 圖示。
1. 選取 [.NET Core Launch (AZDS)] 作為作用中偵錯組態。

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> 如果您未在 [命令選擇區] 中看到任何 Azure 開發人員空間命令，請確定您已安裝適用於 Azure 開發人員空間的 VS Code 擴充功能。 請確定您在 VS Code 中開啟的工作區是包含 azds.yaml 的資料夾。


### <a name="debug-the-container-in-kubernetes"></a>在 Kubernetes 中進行容器偵錯
按 **F5** 可在 Kubernetes 中進行程式碼偵錯。

與 `up` 命令相同，程式碼也會同步到開發人員空間，且容器會建置並部署到 Kubernetes。 此時，偵錯工具當然會連結至遠端容器。

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

在伺服器端程式碼檔案中設定中斷點，例如在 `Controllers/HomeController.cs` 來源檔案的 `Index()` 函式內。 重新整理瀏覽器頁面會導致叫用中斷點。

就如同已在本機執行程式碼一樣，您擁有偵錯資訊的完整存取權，例如呼叫堆疊、區域變數、例外狀況資訊等等。

### <a name="edit-code-and-refresh"></a>編輯程式碼並重新整理
使用有效的偵錯工具，進行程式碼編輯。 例如，在 `Controllers/HomeController.cs` 中修改 [關於] 頁面的訊息。 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

儲存檔案，然後在 [偵錯動作] 窗格中，按一下 [重新整理] 按鈕。 

![](media/get-started-netcore/debug-action-refresh.png)

Azure 開發人員空間會以累加方式重新編譯現有容器中的程式碼，以提供更快的編輯/偵錯迴圈，而不是在每次進行程式碼編輯時重新建置及重新部署新的容器映像 (這通常要花費相當長的時間)。

請在瀏覽器中重新整理 Web 應用程式，並移至 [關於] 頁面。 您應會看到自訂訊息出現在 UI 中。

**您現在有辦法在 Kubernetes 中快速逐一查看程式碼及直接進行偵錯！** 接下來，您將了解如何建立和呼叫第二個容器。

## <a name="call-a-service-running-in-a-separate-container"></a>呼叫在個別容器中執行的服務

在本節中，您會建立第二個服務 `mywebapi`，並由 `webfrontend` 加以呼叫。 每個服務都會在個別的容器中執行。 然後，您將在這兩個容器間進行偵錯。

![多個容器](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>下載 *mywebapi* 的範例程式碼
為了節省時間，我們將從 GitHub 存放庫下載範例程式碼。 請移至 https://github.com/Azure/dev-spaces，然後選取 [複製或下載]，從 GitHub 存放庫進行下載。 本節的程式碼位於 `samples/dotnetcore/getting-started/mywebapi`。

### <a name="run-mywebapi"></a>執行 *mywebapi*
1. 在個別的 VS Code 視窗中開啟資料夾 `mywebapi`。
1. 按 F5，並等候服務進行建置和部署。 當 VS Code 偵錯列出現時，表示已就緒。
1. 記下端點 URL，它看起來會像是 http://localhost:\<portnumber\>。 **提示：VS Code 狀態列會顯示可點按的 URL。** 容器可能看起來像在本機執行，但實際是在 Azure 的開發人員空間中執行。 localhost 位址的原因是因為 `mywebapi` 並未定義任何公用端點，而只能從 Kubernetes 執行個體中存取。 為了方便您操作以及與本機電腦上的私人服務互動，Azure 開發人員空間會建立暫存的 SSH 通道，連到在 Azure 中執行的容器。
1. 當 `mywebapi` 就緒時，請開啟瀏覽器並進入 localhost 位址。 請將 `/api/values` 附加至 URL，以叫用 `ValuesController` 的預設 GET API。 
1. 如果所有步驟都已成功，您應該會看到 `mywebapi` 服務的回應。

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>從 webfrontend 發出對 mywebapi 的要求
我們現在要在 `webfrontend` 中撰寫對 `mywebapi` 發出要求的程式碼。
1. 切換至 `webfrontend` 的 VS Code 視窗。
1. 取代 About 方法的程式碼：

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

上述程式碼範例會將傳入要求中的 `azds-route-as` 標頭轉送至傳出要求。 您稍後將了解這對小組的共同開發有何幫助。

### <a name="debug-across-multiple-services"></a>在多個服務間進行偵錯
1. 此時，`mywebapi` 仍應使用附加的偵錯工具執行中。 如果不是，在 `mywebapi` 專案中按 F5。
1. 在處理 `api/values/{id}` GET 要求的 `Get(int id)` 方法中設定中斷點。
1. 在 `webfrontend` 專案中，請將中斷點設定在傳送 GET 要求給 `mywebapi/api/values` 的前一刻。
1. 在 `webfrontend` 專案中按 F5。
1. 叫用 Web 應用程式，並在這兩項服務中逐步執行程式碼。
1. 在 Web 應用程式中，[關於] 頁面會顯示由兩項服務串連的訊息：「來自 webfrontend 的 Hello 和來自 mywebapi 的 Hello」。


做得好！ 現在，您已有每個容器可以個別開發和部署的多容器應用程式。

## <a name="learn-about-team-development"></a>了解小組開發

[!INCLUDE[](includes/team-development-1.md)]

我們要了解它是如何運作的。 請移至 `mywebapi` 的 VS Code 視窗，並對 `string Get(int id)` 方法進行程式碼編輯，例如：

    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
