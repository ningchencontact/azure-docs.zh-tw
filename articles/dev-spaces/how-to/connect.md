---
title: Azure Dev Spaces 連接
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: 瞭解如何使用 Azure Dev Spaces Connect
keywords: Azure Dev Spaces，Dev Spaces，Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器，連接
ms.openlocfilehash: 2e1984b838e961239e0533179c34eccb118abd8e
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065869"
---
# <a name="azure-dev-spaces-connect-preview"></a>Azure Dev Spaces 連接（預覽）

Azure Dev Spaces Connect 可讓您在開發電腦上使用或不使用容器來執行和偵錯工具代碼，同時仍會與您的應用程式或服務的其餘部分連線到您的 Kubernetes 叢集。 將您的開發電腦連接到您的叢集，可協助您快速開發應用程式，並執行端對端測試，而不需要建立任何 Docker 或 Kubernetes 設定。 您也可以連線到 AKS 叢集，而不會影響其他可能使用相同叢集的工作負載或使用者。

Azure Dev Spaces 連接會將連線的 AKS 叢集與開發電腦之間的流量重新導向。 此流量重新導向可讓您在 AKS 叢集中執行的開發機器和服務上的程式碼，如同位於相同的 AKS 叢集中一樣進行通訊。 由於您的程式碼是在開發電腦上執行，因此您也可以彈性地使用您用來執行和程式碼的開發工具。 Azure Dev Spaces Connect 也提供一種方法，可將環境變數和掛接的檔案複寫至您的開發電腦中 AKS 叢集內的 pod。

在本指南中，您將了解如何：

- 在 Azure 中於受控 Kubernetes 叢集上設定 Azure Dev Spaces。
- 將具有多個微服務的大型應用程式部署至開發人員空間。
- 使用 Azure Dev Spaces 連接 來重新導向您的 AKS 叢集與在開發電腦上執行的程式碼之間的流量。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="before-you-begin"></a>開始之前

本指南使用[Azure Dev Spaces 自行車分享範例應用程式](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)來示範 Azure Dev Spaces Connect。 如果您想要改為在 AKS 叢集上使用自己的應用程式，您可以從[這裡](#use-azure-dev-spaces-connect)開始。

### <a name="limitations"></a>限制

* Azure Dev Spaces 連接目前不支援 UDP。

### <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
* [已安裝 Azure CLI][azure-cli]。
* [已安裝 Helm 2.13 或更新版本][helm-installed] \(英文\)。
* 已在 MacOS 或 Windows 10 上安裝並執行[Azure Dev Spaces][azds-vs-code]延伸模組的[Visual Studio Code][vs-code] 。

## <a name="create-an-azure-kubernetes-service-cluster"></a>建立 Azure Kubernetes Service 叢集

您必須在[支援的區域][supported-regions]中建立 AKS 叢集。 下列命令會建立名為 MyResourceGroup 的資源群組與名為 MyAKS 的 AKS 叢集。

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>在 AKS 叢集上啟用 Azure Dev Spaces

使用 `use-dev-spaces` 命令在 AKS 叢集上啟用 Dev Spaces，並遵循提示來進行。 下列命令會在 *MyResourceGroup* 群組中的 *MyAKS* 叢集上啟用 Dev Spaces，並建立稱為 *dev* 的開發空間。

> [!NOTE]
> `use-dev-spaces` 命令也會安裝 Azure Dev Spaces CLI (如果尚未安裝)。 您無法在 Azure Cloud Shell 中安裝 Azure Dev Spaces CLI。

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>取得應用程式範例的程式碼

在本文中，您會使用 [Azure Dev Spaces 單車共享範例應用程式](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) \(英文\) 來示範如何使用 Azure Dev Spaces。

從 GitHub 複製應用程式，並瀏覽至其目錄：

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>擷取適用於 *dev* 的 HostSuffix

使用 `azds show-context` 命令來顯示適用於 *dev* 的 HostSuffix。

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>搭配您的 HostSuffix 更新 Helm 圖表

開啟 [charts/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) \(英文\) 並將所有 `<REPLACE_ME_WITH_HOST_SUFFIX>` 項目取代為您先前所擷取的 HostSuffix 值。 儲存變更並關閉該檔案。

## <a name="run-the-sample-application-in-kubernetes"></a>在 Kubernetes 中執行範例應用程式

在 Kubernetes 執行範例應用程式的命令為現有處理程序的一部分，且不具任何與 Azure Dev Spaces 工具的相依性。 在此情況下，Helm 是用來執行此範例應用程式的工具，但您可以使用其他工具來在叢集內的命名空間中執行您的整個應用程式。 Helm 命令是以您先前所建立的 *dev* 開發空間作為目標，但此開發空間也是 Kubernetes 命名空間。 因此，開發空間和其他命名空間相同，可以被其他工具作為目標。

在應用程式于叢集內執行之後，您可以使用 Azure Dev Spaces 進行開發，而不論用來部署它的工具為何。

### <a name="use-helm-to-install-the-sample-application"></a>使用 Helm 來安裝範例應用程式

使用 `helm init` 和 `helm install` 命令來在叢集上設定並安裝範例應用程式。

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic 
```

> [!Note]
> **如果您使用已啟用 RBAC 的叢集**，請務必設定 [Tiller 的服務帳戶](https://helm.sh/docs/using_helm/#role-based-access-control)。 否則，`helm` 命令將會失敗。

`helm install` 命令可能需要幾分鐘的時間才能完成。 命令的輸出會顯示其於完成時部署至叢集之所有伺服器的狀態：

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

### <a name="navigate-to-your-sample-application"></a>流覽至您的範例應用程式

在範例應用程式已安裝至叢集上，且基於您已在叢集上啟用 Dev Spaces 的原因，請使用 `azds list-uris` 命令來顯示目前所選取 *dev* 中之範例應用程式的 URL。

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

開啟來自 `azds list-uris` 命令的公用 URL，來瀏覽至 *bikesharingweb* 服務。 在上述範例中，*bikesharingweb* 服務的公用 URL 是 `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`。 選取 [ *Aurelia Briggs （customer）* ] 作為使用者，然後選取要出租的自行車。 確認您看到自行車的預留位置影像。

## <a name="use-azure-dev-spaces-connect"></a>使用 Azure Dev Spaces Connect

在 Visual Studio Code 中開啟*開發人員空間/samples/BikeSharingApp/自行車*，並使用 Azure Dev Spaces connect 延伸模組，將您的開發電腦連接到您的 AKS 叢集。

若要使用 Azure Dev Spaces Connect 延伸模組，請按一下 [*查看*] 和 [*命令*選擇區]，以在 Visual Studio Code 中開啟命令選擇區。 開始輸入 `Azure Dev Spaces: Redirect`，然後按一下 [`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`]、[`Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`] 或 [`Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`]。

![連接命令](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>選取重新導向選項

如果您執行 `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`，系統會要求您選擇現有的 Kubernetes 服務：

![連接選擇服務](../media/how-to-connect/connect-choose-service.png)

此選項會將此服務的 AKS 叢集中的所有流量，重新導向至開發電腦中執行的應用程式版本。 如果此服務在 AKS 叢集中有多個 pod，則此服務的所有流量只會路由傳送至您的開發電腦。 Azure Dev Spaces Connect 也會將所有輸出流量從應用程式路由傳送回您的 AKS 叢集。

如果您執行 `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`，系統會要求您選擇特定的 pod：

![連接選擇 Pod](../media/how-to-connect/connect-choose-pod.png)

此選項會連接到特定的 pod。 此選項適用于與不傳送或接收流量及複寫終止 pod 的 pod 互動。 如果 pod 會傳送和接收流量，此選項的運作方式類似于 `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`，而且會將所有與所選 pod 之服務相關的 pod 重新導向至 AKS 叢集中的所有流量。

如果您執行 `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`，系統不會提示您選取現有的 pod 或服務。 此選項會將在開發電腦上執行之應用程式的所有輸出流量重新導向至 AKS 叢集。

在此範例中，選擇 [`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`]，然後選取 [*自行車*服務]。

### <a name="select-a-connection-mode"></a>選取連接模式

選取重新導向選項之後，系統會提示您選擇 [*取代*] 或 [*複製*] 連接模式。

![Connect 取代或複製](../media/how-to-connect/connect-replace-clone.png)

*Replace*選項會取代 AKS 叢集中目前的 pod 或服務，並將該服務的所有流量重新導向至您的開發電腦。 在您的開發電腦上啟動應用程式之前，此選項可能會干擾您的 AKS 叢集中，與您要重新導向的服務互動的其他服務無法運作。 [*複製*] 選項可讓您選擇現有的子開發人員空間，或建立新的子開發人員空間，以將 pod 或服務的流量重新導向至您的開發電腦。 此選項可讓您獨立工作，而不會中斷其他服務，因為只有對該子開發人員空間的流量才會重新導向至您的開發電腦。 *複製*選項需要您的 AKS 叢集 Azure Dev Spaces 啟用。

在此範例中，選擇 [*取代*]。

> [!NOTE]
> 如果您現有服務的 pod 有多個容器，系統也會提示您選擇應用程式的容器。

### <a name="select-a-port-for-your-application"></a>選取應用程式的埠

在您選取連線模式之後，系統會提示您輸入本機應用程式的 TCP 埠。 如果您的應用程式開啟多個埠，請以逗號分隔，例如*80，81*。 如果您的應用程式不接受任何網路要求，請輸入*0*。 在此範例中，請輸入*8080*。

![連接選擇埠](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>確認您已連線

選取應用程式的 TCP 埠之後，Azure Dev Spaces 連線 就會建立與 AKS 叢集的連線。 Azure Dev Spaces 連線會將代理程式插入您的 AKS 叢集中，以重新導向 AKS 叢集與開發電腦之間的流量。 建立此連接可能需要幾分鐘的時間。 Azure Dev Spaces Connect 也會要求系統管理員存取權，才能修改開發電腦中的*hosts*檔案。

> [!IMPORTANT]
> 一旦 Azure Dev Spaces 連線建立 AKS 叢集的連線，您的 AKS 叢集中的其他服務可能會無法正常運作，直到您在開發電腦中啟動該服務為止。 此外，如果您的服務有無法在開發電腦中使用的相依性，您可能需要修改您的應用程式或提供[其他](#additional-configuration)設定

Azure Dev Spaces 連線 會在建立 AKS 叢集的連線之後，開啟標題為*AZDS Connect-自行車*的終端機視窗。 此終端機視窗已從您的 AKS 叢集設定所有環境變數和 DNS 專案。 您在此終端機視窗中執行的任何程式碼，或使用 Visual Studio Code 偵錯工具的程式碼，都會連接到 AKS 叢集。

![連接終端機](../media/how-to-connect/connect-terminal.png)

此外，Azure Dev Spaces Connect 會建立標題為*Dev Spaces Connect*的視窗及其所有輸出。

![連接輸出](../media/how-to-connect/connect-output.png)

Azure Dev Spaces 連接也會有顯示連接狀態的狀態列專案。

![連接狀態](../media/how-to-connect/connect-status.png)

確認狀態列顯示*Dev Spaces：已連線到本機埠8080上的開發/自行車*。

### <a name="configure-your-application-on-your-development-machine"></a>在您的開發電腦上設定應用程式

開啟 [ *AZDS] [連接-自行車*終端機] 視窗並執行 `npm install`：

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

開啟[server .js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L352) ，並在第352行更新*埠*到*8080*：

```javascript
var port = 8080;
var server = null;
```

按一下 [ *Debug* ]，然後*開啟*[設定]。 這會建立一個 `.vscode/launch.json` 檔案。 將該檔案的內容取代為下列內容：

```json
{
    "configurations": [
        ...
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

開啟[package. json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json)並新增調試腳本：

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-machine"></a>在開發電腦上啟動您的應用程式

按一下左側的 [*調試*] 圖示，然後按一下頂端的 [透過*NPM 啟動*] 旁的 [開始] 按鈕。

![透過 NPM 啟動](../media/how-to-connect/launch-npm.png)

您的應用程式將會啟動，並 Azure Dev Spaces 連接 AKS 叢集與開發電腦之間的重新導向流量。 您會在*偵錯主控台*中看到類似下面的訊息：

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 8080
```

按一下 [Azure Dev Spaces 連接] 狀態列，然後選擇應用程式的公用 URL，以流覽至 [ *Bikesharingweb* ] 服務。 您也可以從先前執行的 `azds list-uris` 命令中尋找公用 URL。 如果您未在叢集上使用 Azure Dev Spaces，請針對您所使用的命名空間，使用應用程式的 IP 或 URL。 在上述範例中，*bikesharingweb* 服務的公用 URL 是 `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`。 選取 [ *Aurelia Briggs （customer）* ] 作為使用者，然後選取要出租的自行車。

### <a name="set-a-break-point"></a>設定中斷點

開啟[server .js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) ，然後按一下第233行的某處，將游標放在該處。 藉由叫用*F9*或依序按一下 [ *Debug* ] 和 [*切換中斷點*] 來設定中斷點。

開啟公用 URL，以流覽至*bikesharingweb*服務。 選取 [ *Aurelia Briggs （customer）* ] 作為使用者，然後選取要出租的自行車。 請注意，自行車的影像不會載入。 回到 Visual Studio Code，觀察行233已反白顯示。 您設定的中斷點已在第233行暫停服務。 若要讓服務繼續，請按 F5，或依序按一下 [偵錯] 和 [繼續]。 返回您的瀏覽器，並確認您看到自行車的預留位置影像。

將游標放在 `server.js` 的第233行，然後按*F9 鍵*，移除中斷點。

### <a name="update-your-application"></a>更新您的應用程式

編輯 `server.js` 以移除232和233行：

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

區段現在看起來應該像這樣：

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

儲存您的變更，然後按一下 [ *Debug* ]，然後*重新開機調試*程式。 重新整理您的瀏覽器，並確認您不再看到自行車的預留位置影像。

依序按一下 [偵錯] 和 [停止偵錯] 來停止偵錯工具。 按一下 [Azure Dev Spaces 連接] 狀態列，以中斷與 AKS 叢集的連線。

## <a name="additional-configuration"></a>其他設定

Azure Dev Spaces Connect 可以處理路由流量及複寫環境變數，而不需要任何額外的設定。 如果您需要下載掛接至 AKS 叢集中容器的任何檔案（例如 ConfigMap 檔案），您可以建立 `azds-local.env` 將這些檔案下載至您的開發電腦。

以下是 `azds-local.env`的範例：

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development machine,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development machine.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development machine
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

MYAPP1_SERVICE_HOST=${services.myapp1}

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.
```

## <a name="next-steps"></a>後續步驟

瞭解如何使用 Azure Dev Spaces 和 GitHub 動作，直接在 AKS 中測試提取要求的變更，然後將提取要求合併至存放庫的主要分支。

> [!div class="nextstepaction"]
> [GitHub 動作 & Azure Kubernetes Service][gh-actions]

[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download