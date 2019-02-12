---
title: 包含檔案
description: 包含檔案
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 749120446619bf682d02be0f9290a6d47540c16a
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2019
ms.locfileid: "55664370"
---
### <a name="run-the-service"></a>執行服務

1. 按 F5 (或在終端機視窗中輸入 `azds up`) 來執行服務。 服務會自動在您新選取的空間 dev/scott 中執行。 
1. 您可以藉由再次執行 `azds list-up`，確認您的服務是在其本身的空間中執行。 您會注意到 mywebapi 的執行個體現在是在 dev/scott 空間中執行 (在 dev 中執行的版本仍在執行中，但是不會列出)。

    ```
    Name                      DevSpace  Type     Updated  Status
    mywebapi                  scott     Service  3m ago   Running
    mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
    webfrontend               dev       Service  26m ago  Running
    ```

1. 執行 `azds list-uris`，並注意 webfrontend 的存取點 URL。

    ```
    Uri                                                                        Status
    -------------------------------------------------------------------------  ---------
    http://localhost:53831 => mywebapi.scott:80                                Tunneled
    http://dev.s.dev.webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
    ```

1. 不是以 "dev.s." 作為 URL 前置詞，而是在 Web 瀏覽器中取代為 "scott.s." 。 請注意，此更新後的 URL 仍會進行解析。 此 URL 是 dev/scott 空間專屬的。 此特殊 URL 表示傳送到 "Scott URL" 的要求會嘗試先路由到 dev/scott 空間中的服務，但若失敗，則會切換回 dev 空間中的服務。

<!--
TODO: replace 2 & 3 with below once bug#753164 and PR#158827 get pushed to production.

You can confirm that your service is running in its own space by running `azds list-up` again. First, you'll notice an instance of *mywebapi* is now running in the _dev/scott_ space (the version running in _dev_ is still running but it is not listed). If you run `azds list-uris`, you will notice that the access point URL for *webfrontend* is prefixed with the text "scott.s.". This URL is unique to the _dev/scott_ space. The special URL signifies that requests sent to the "Scott URL" will try to first route to services in the _dev/scott_ space, but if that fails, they will fall back to services in the _dev_ space.

```
Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

```
Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
```
-->

![](../articles/dev-spaces/media/common/space-routing.png)

Azure Dev Spaces 的這項內建功能可讓您在共用空間中測試程式碼，而不需要每位開發人員在其空間中重新建立服務的完整堆疊。 這個路由需要您的應用程式程式碼才能轉送傳播標頭，如本指南的上一個步驟所述。

### <a name="test-code-in-a-space"></a>在空間中測試程式碼
若要搭配 webfrontend 來測試您的新版 mywebapi，請在瀏覽器中開啟到 webfrontend 的公用存取點 URL，並且移至 [關於] 頁面。 您應該會看到新訊息顯示。

現在，移除 URL 的 "scott.s." 部分，然後重新整理瀏覽器。 您應該會看到舊的行為 (使用在 dev 中執行的 mywebapi 版本)。

您擁有的 dev 空間一定會包含最新的變更，而且如果您應用程式的設計目的是要利用 DevSpace 的空間型路由 (如本教學課程這一節所述)，您應該就能輕鬆地發現，Dev Spaces 會對在較大的應用程式內容中測試新功能大有助益。 您不必在私用空間中部署所有服務，而是可以建立衍生自 dev 的私用空間，並只「啟動」您實際使用的服務。 Dev Spaces 的路由基礎結構會處理其餘工作，其會盡可能使用所有可以從私用空間找到的服務，同時將其預設回到 dev 空間中所執行的最新版本。 更棒的是，多個開發人員可以同時在自己的空間積極開發不同的服務，卻又不會干擾到彼此。

### <a name="well-done"></a>做得好！
您已經完成快速入門指南！ 您已了解如何︰

> [!div class="checklist"]
> * 使用 Azure 中受管理的 Kubernetes 叢集，設定 Azure Dev Spaces。
> * 在容器中反覆開發程式碼。
> * 獨立開發兩種不同的服務，並且使用 Kubernetes 的 DNS 服務探索來呼叫另一個服務。
> * 在小組環境中有效率地開發及測試您的程式碼。
> * 使用 Dev Spaces 來建立功能基準，以便輕鬆地在較大型微服務應用程式的內容中，測試隔離的變更

現在您已充分了解 Azure Dev Spaces，請[與小組成員共用您的開發人員空間](../articles/dev-spaces/how-to/share-dev-spaces.md)，並協助他們了解共同作業有多麼容易。

## <a name="clean-up"></a>清除
若要完全刪除叢集上的 Azure Dev Spaces 執行個體，包括所有開發人員空間及其中正在執行的服務，請使用 `az aks remove-dev-spaces` 命令。 請記住此動作無法復原。 您可以在叢集上再次新增 Azure Dev Spaces 的支援，但會如同您再次開始執行。 將不會還原您的舊服務和空間。

下列範例列出您使用中訂用帳戶中的 Azure Dev Spaces 控制項，然後刪除資源群組 'myaks-rg' 中與 AKS 叢集 'myaks' 相關聯的 Azure Dev Spaces 控制項。

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```