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
ms.openlocfilehash: e0f768b876b49ec006ce98decf121d73d334b6d8
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439512"
---
### <a name="run-the-service"></a>執行服務

若要執行服務，請按 F5 (或在終端機視窗中輸入 `azds up`) 來執行服務。 服務會自動在您新選取的空間 dev/scott 中執行。 藉由執行 `azds list-up` 確認您的服務正在其本身的空間中執行：

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

請注意，mywebapi 的執行個體現在正在 dev/scott 空間中執行。 在 dev 中執行的版本仍在執行，但不會列出。

執行 `azds list-uris` 來列出目前空間的 URL。

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

請注意，webfrontend 的公用存取點 URL 前面會加上 scott.s。 此 URL 是 dev/scott 空間專屬的。 此 URL 前置詞會指示輸入控制器將要求路由傳送至服務的 dev/scott 版本。 當 Dev Spaces 處理具有此 URL 的要求時，輸入控制器會先嘗試將要求路由傳送至 dev/scott 空間中的 webfrontend 服務。 如果該嘗試失敗，則會將要求路由傳送至 dev 空間中的 webfrontend 服務以作為後援。 另請注意，會有 localhost URL 可供使用 Kubernetes 的「連接埠轉送」功能來透過 localhost 存取服務。 如需 Azure Dev Spaces 中 URL 和路由的詳細資訊，請參閱 [Azure Dev Spaces 的運作和設定方式](../articles/dev-spaces/how-dev-spaces-works.md)。



![空間路由](../articles/dev-spaces/media/common/Space-Routing.png)

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

您現在已充分了解 Azure Dev Spaces，請[與小組成員共用您的開發人員空間](../articles/dev-spaces/how-to/share-dev-spaces.md)並開始共同作業。

## <a name="clean-up"></a>清除
若要完全刪除叢集上的 Azure Dev Spaces 執行個體，包括所有開發人員空間及其中正在執行的服務，請使用 `az aks remove-dev-spaces` 命令。 請記住此動作無法復原。 您可以在叢集上再次新增 Azure Dev Spaces 的支援，但會如同您再次開始執行。 將不會還原您的舊服務和空間。

下列範例列出您使用中訂用帳戶中的 Azure Dev Spaces 控制項，然後刪除資源群組 'myaks-rg' 中與 AKS 叢集 'myaks' 相關聯的 Azure Dev Spaces 控制項。

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```