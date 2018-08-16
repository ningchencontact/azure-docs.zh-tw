---
title: 包含檔案
description: 包含檔案
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 0d3bdb6cc56b90d1975af73be1bb8cc1f73e1213
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129080"
---
目前為止，您已如同您是應用程式唯一的開發人員一般，執行您的應用程式程式碼。 在本節中，您將會了解 Azure Dev Spaces 如何簡化小組開發：
* 讓開發人員小組能夠依需求在共用的開發人員空間中或相異的開發人員空間中工作，而在相同的環境中工作。
* 支援每位開發人員獨立在其程式碼中反覆執行，而不需要擔心會打斷其他開發人員。
* 在程式碼認可之前端對端測試程式碼，不需要建立模擬或模擬相依性。

### <a name="challenges-with-developing-microservices"></a>開發微服務的挑戰
您的範例應用程式目前並不複雜。 但是在實務開發中，隨著您新增更多服務以及開發小組成長，很快就會出現挑戰。

請想像一下您要處理與其他十多個服務互動的服務。

- 在本機執行所有項目來進行開發，變得不切實際。 您的開發機器可能沒有足夠的資源可以執行整個應用程式。 或者，也許您的應用程式具有必須可公開存取的端點 (例如，您的應用程式會回應來自 SaaS 應用程式的 Webhook)。

- 您可以嘗試只執行您依賴的服務，但是這表示您需要知道完整的相依性 (例如，相依性的相依性)。 否則，如果您不是在處理這些服務，無法輕易得知如何建置及執行您的相依性就會是問題。
- 某些開發人員會針對他們的許多服務相依性，進行模擬或製作模型。 這個方法有時候有幫助，但是管理這些模型很快會佔用開發的投入時間。 再者，這個方法會導致您的開發人員空間與生產環境大相徑庭，讓微小的錯誤 (bug) 有機可趁。
- 其後果就是，執行任何類型的端對端測試都變得非常困難。 整合測試實際上只能在認可後發生，這表示您稍後會在開發週期中看到問題。

![](../media/common/microservices-challenges.png)


### <a name="work-in-a-shared-dev-space"></a>在共用的開發人員空間中工作
透過 Azure Dev Spaces，您可以在 Azure 中設定「共用的」開發人員空間。 每位開發人員可以只專注於他們自己的應用程式部分，並且可以在開發人員空間 (已經包含其他所有服務和他們的案例依賴的雲端資源) 中反覆地開發「認可前程式碼」。 相依性會永遠保持最新狀態，且開發人員會以能反映生產環境的方式工作。

### <a name="work-in-your-own-space"></a>在您自己的空間工作
當您為服務開發程式碼時，且在您準備將其簽入之前，程式碼通常不會處於良好狀態。 您仍在反覆地使其成形、進行測試，並且實驗解決方案。 Azure Dev Spaces 提供**空間**的概念，可讓您獨立作業，而不需要擔心打斷您的小組成員。

> [!Note]
> 在您繼續之前，請關閉這兩項服務的所有 VS Code 視窗，然後在每個服務的根資料夾中執行 `azds up -d`。 (這是預覽限制。)

讓我們進一步查看目前執行中的服務。 執行 `azds list-up` 命令，您會看到如下的輸出：

```
Name                          DevSpace  Type     Updated      Status
----------------------------  --------  -------  -----------  ----------------
mywebapi                      default   Service  10m 1s ago   Running
mywebapi-54f9cf5b59-bjnkm     default   Pod      10m 4s ago   Running
webfrontend-5b697958d6-b6v96  default   Pod      26m 38s ago  Init:1/3:mindaro-build
```

DevSpace 資料行會顯示在名為 `default` 的空間中所執行的兩個服務。 開啟公用 URL 並瀏覽至 Web 應用程式的任何人，都會叫用您先前撰寫、透過兩個服務執行的程式碼路徑。 現在假設您想要繼續開發 `mywebapi`。 您要如何使用開發環境進行程式碼變更及測試，又不會打斷其他開發人員？ 為此，您應設定自己的空間。

### <a name="create-a-dev-space"></a>建立開發人員空間
若要在 `default` 以外的空間執行您自己的 `mywebapi` 版本，您可以使用下列命令建立您自己的空間：

``` 
azds space select --name scott
```

出現提示時，選取 `default` 作為 **父開發人員空間**。 這表示我們的新空間 (`default/scott`) 會衍生自 `default` 空間。 我們很快就會看到這如何協助我們進行測試。 

在上述範例中，我已對新空間使用我的名稱，以便讓我的同儕識別那是我工作的空間，但是您可以使用您喜歡的任何名稱來稱呼它，而且它的意義也很有彈性，例如 'sprint4' 或 'demo'。

請執行 `azds space list` 命令以查看開發環境中所有空間的清單。 目前選取的空間旁邊會出現星號 (*)。 在您的案例中，建立名為 'default/scott' 的空間時會自動加以選取。 您隨時可以使用 `azds space select` 命令選取其他空間。
