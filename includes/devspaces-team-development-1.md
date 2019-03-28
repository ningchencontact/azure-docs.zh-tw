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
ms.openlocfilehash: 40c1be20df845b975c023616e38cbb932c985735
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439513"
---
# <a name="team-development-with-azure-dev-spaces"></a>使用 Azure Dev Spaces 進行小組開發

在本教學課程中，您將了解一組開發人員如何使用 Dev Spaces 同時在相同的 Kubernetes 叢集中共同作業。

## <a name="learn-about-team-development"></a>了解小組開發
目前為止，您已如同您是應用程式唯一的開發人員一般，執行您的應用程式程式碼。 在本節中，您將會了解 Azure Dev Spaces 如何簡化小組開發：
* 藉由在在共用的開發人員空間中或相異的開發人員空間中工作 (依需求)，讓開發人員小組能夠在相同的環境中工作。
* 支援每位開發人員獨立在其程式碼中反覆執行，而不需要擔心會打斷其他開發人員。
* 在程式碼認可之前端對端測試程式碼，不需要建立模擬或模擬相依性。

### <a name="challenges-with-developing-microservices"></a>開發微服務的挑戰
您的範例應用程式目前並不複雜。 但是在實務開發中，隨著您新增更多服務以及開發小組成長，很快就會出現挑戰。 在本機執行所有項目來進行開發，變得不切實際。

* 您的開發機器可能沒有足夠的資源可以一次執行每一個您所需的應用程式。
* 某些服務可能需要能夠公開連線。 例如，某個服務可能需要有端點來回應 Webhook。
* 如果您想要執行一部分的服務，就必須知道所有服務之間的完整相依性階層。 要判斷此相依性階層並不容易，尤其是隨著服務的數目增加時。
* 某些開發人員會針對他們的許多服務相依性，進行模擬或製作模型。 這個方法會有幫助，但管理這些模型很快就會影響到開發成本。 再者，此方法會導致開發環境與生產環境出現很大的差異，進而提高發生微小錯誤 (bug) 的機率。
* 其後果就是，執行任何類型的整合測試都變得非常困難。 整合測試實際上只能在認可後發生，這表示您稍後會在開發週期中看到問題。

    ![](../articles/dev-spaces/media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>在共用的開發人員空間中工作
透過 Azure Dev Spaces，您可以在 Azure 中設定「共用的」開發人員空間。 每位開發人員可以只專注於他們自己的應用程式部分，並且可以在開發人員空間 (已經包含其他所有服務和他們的案例依賴的雲端資源) 中反覆地開發「認可前程式碼」。 相依性會永遠保持最新狀態，且開發人員會以能反映生產環境的方式工作。

### <a name="work-in-your-own-space"></a>在您自己的空間工作
當您為服務開發程式碼時，且在您準備將其簽入之前，程式碼通常不會處於良好狀態。 您仍在反覆地使其成形、進行測試，並且實驗解決方案。 Azure Dev Spaces 提供**空間**的概念，可讓您獨立作業，而不需要擔心打斷您的小組成員。

## <a name="use-dev-spaces-for-team-development"></a>使用 Dev Spaces 進行小組開發
讓我們示範一下這些想法，具體的範例會使用 webfrontend -> mywebapi 應用程式範例。 我們會設想一個案例，Scott 這位開發人員需要變更 mywebapi 服務，而且需要變更的也只有該服務。 Scott 的更新過程並不需要變更 webfrontend。

如果不使用 Dev Spaces，Scott 有幾種方式可以用來開發和測試其更新，但不論是哪一種方式都不理想：
* 在本機執行所有元件。 這種方式需要用到安裝了 Docker 的開發電腦，可能的選項是 MiniKube。
* 在 Kubernetes 叢集上的隔離命名空間中執行所有元件。 由於 webfrontend 不會變更，因此這會浪費叢集資源。
* 只執行 mywebapi，並手動發出 REST 呼叫來進行測試。 此方式不會測試完整的端對端流程。
* 將著重在開發用途的程式碼新增至 webfrontend，讓開發人員能夠傳送要求給 mywebapi 的不同執行個體。 此方式會讓 webfrontend 服務變得複雜。

### <a name="set-up-your-baseline"></a>設定基準
首先，我們必須部署服務基準。 此部署所代表的是「已知良好的最近配置」，因此您可以輕鬆地比較本機程式碼與所簽入版本的行為。 接著，我們會根據此基準建立子空間，以便在較大的應用程式內容中，測試我們對 mywebapi 所做的變更。

1. 複製 [Dev Spaces 應用程式範例](https://github.com/Azure/dev-spaces)：`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. 簽出遠端分支 azds_updates：`git checkout -b azds_updates origin/azds_updates`
1. 選取 dev 空間：`azds space select --name dev`。 出現選取父代開發人員空間的提示時，請選取\<無\>。
1. 瀏覽至 mywebapi 目錄並執行：`azds up -d`
1. 瀏覽至 webfrontend 目錄並執行：`azds up -d`
1. 執行 `azds list-uris` 以查看 webfrontend 的公用端點

> [!TIP]
> 上述步驟會手動設定基準，但我們對小組的建議是，使用 CI/CD 讓基準自動與所認可的程式碼保持最新狀態。
>
> 請參閱我們的[使用 Azure DevOps 來設定 CI/CD 的指南](../articles/dev-spaces/how-to/setup-cicd.md)，以建立類似下圖的工作流程。
>
> ![CI/CD 圖表範例](../articles/dev-spaces/media/common/ci-cd-complex.png)

此時您的基準應在執行中。 執行 `azds list-up --all` 命令，您會看到如下的輸出：

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

DevSpace 資料行會顯示兩個服務正在名為 dev 的空間中執行。 開啟公用 URL 並瀏覽至 Web 應用程式的任何人，都會叫用透過兩個服務執行的已簽入程式碼路徑。 現在假設您想要繼續開發 mywebapi。 您要如何使用開發環境進行程式碼變更及測試，又不會打斷其他開發人員？ 為此，您應設定自己的空間。

### <a name="create-a-dev-space"></a>建立開發人員空間
若要在 dev 以外的空間執行您自己的 mywebapi 版本，您可以使用下列命令建立您自己的空間：

```cmd
azds space select --name scott
```

出現提示時，選取 dev 作為**父代開發人員空間**。 這表示我們的新空間 (dev/scott) 會衍生自 dev 空間。 我們很快就會看到這如何協助我們進行測試。

依照我們前面的假設，我們已使用 scott 名稱作為新空間，所以同事可以分辨是誰在使用新空間。 但您也可以使用任何您喜歡的名稱，並讓其意義更有彈性，例如 sprint4 或 demo。 無論如何，dev 會作為基準，讓所有開發人員都能在此應用程式的某部分上進行作業：

![](../articles/dev-spaces/media/common/ci-cd-space-setup.png)

請執行 `azds space list` 命令以查看開發環境中所有空間的清單。 [已選取] 資料行會指出您目前所選的空間 (true/false)。 在您的案例中，系統會在名為 dev/scott 的空間建立時，就自動選取此空間。 您隨時可以使用 `azds space select` 命令選取其他空間。

我們要了解它是如何運作的。