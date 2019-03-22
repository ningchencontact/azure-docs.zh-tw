---
title: Azure 開發空間的運作方式，並已設定
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 03/04/2019
ms.topic: conceptual
description: 該 power Azure 開發人員空格和 azds.yaml 組態檔中的設定方式說明的程序
keywords: azds.yaml，Azure 開發人員空格、 開發空格、 Docker、 Kubernetes、 Azure，AKS，Azure Kubernetes Service，容器
ms.openlocfilehash: 8d17be31acc6868399e8f785255d28fa2314d48c
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316111"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Azure 開發空間的運作方式，並已設定

開發 Kubernetes 應用程式很有挑戰性。 您需要 Docker 和 Kubernetes 組態檔。 您需要了解如何測試您的應用程式在本機和其他相依的服務進行互動。 您可能需要處理開發和測試一次多個服務上且擁有一組開發人員。

Azure 開發人員的空間可協助您開發、 部署和偵錯 Kubernetes 應用程式直接在 Azure Kubernetes Service (AKS)。 Azure 開發人員的空格也可讓小組共用開發空間。 整個小組共用開發空間可讓在隔離開發，而不需要複寫或模擬相依性或叢集中的其他應用程式的個別小組成員。

Azure 開發人員的空間會建立並部署、 執行和偵錯您在 AKS 的 Kubernetes 應用程式中使用組態檔。 此組態檔位於與您的應用程式程式碼，而且可以加入至版本控制系統。

這篇文章描述的程序，該 power Azure 開發人員空格和這些處理序的 Azure 開發空間的組態檔中的設定方式。 若要取得 Azure 開發人員空間快速地執行，並看到它在實務上，完成其中一個快速入門：

* [使用 CLI 和 Visual Studio Code 的 Java](quickstart-java.md)
* [使用 CLI 和 Visual Studio Code 的.NET core](quickstart-netcore.md)
* [使用 Visual Studio 2017 的.NET core](quickstart-netcore-visualstudio.md)
* [使用 CLI 和 Visual Studio Code 的 Node.js](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Azure 開發空間的運作方式

Azure 的 Dev 空間有兩個不同的元件，與您互動： 控制器和用戶端工具。

![Azure 開發人員空間元件](media/how-dev-spaces-works/components.svg)

控制器會執行下列動作：

* 管理開發人員的空間建立和選取項目。
* 安裝應用程式的 Helm 圖表，並建立 Kubernetes 物件。
* 建置您的應用程式的容器映像。
* 您應用程式部署到 AKS。
* 累加建置，並重新啟動您的程式碼變更時。
* 管理記錄檔和 HTTP 追蹤。
* 將轉送 stdout 和 stderr，用戶端工具。
* 可讓小組成員建立衍生自父代的開發空間的子系開發空間。
* 設定應用程式空間內，以及父和子空間之間的路由。

控制站所在 AKS 之外。 它會驅動的行為和通訊的用戶端工具與 AKS 叢集之間。 控制器會啟用使用 Azure CLI，當您準備您的叢集使用 Azure 開發人員的空間。 一旦啟用，您可以與使用用戶端工具互動。

用戶端工具可讓使用者：
* 產生 Dockerfile、 Helm 圖表，以及應用程式的 Azure 開發人員空間設定檔。
* 建立父和子系開發空間的資訊。
* 讓控制器以建置並啟動您的應用程式。

當您的應用程式執行時，用戶端也工具：
* 接收並顯示 stdout 和 stderr，從您在 AKS 中執行的應用程式。
* 會使用[連接埠轉送](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)允許網頁存取您的應用程式使用 http:\//localhost。
* 將偵錯工具附加至執行中應用程式在 AKS 中。
* 累加建置，即可快速反覆偵測到變更時，同步處理來源到您的開發空間的程式碼。

您可以使用用戶端從命令列工具的一部分`azds`命令。 您也可以使用工具與用戶端：

* Visual Studio 程式碼使用[Azure 開發人員空間延伸模組](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)。
* Visual Studio 2017 [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools)。

以下是設定和使用 Azure 開發空間的基本流程：
1. 適用於 Azure 開發人員空間準備您的 AKS 叢集
1. 準備您的程式碼執行的 Azure 開發人員的空間
1. 開發空間上執行您的程式碼
1. 偵錯您的程式碼，在開發人員分享空間
1. 共用開發空間

我們將討論更多詳細資料中每個 Azure 開發空間的運作方式的下列各節。

## <a name="prepare-your-aks-cluster"></a>準備您的 AKS 叢集

準備您的 AKS 叢集包含：
* 正在驗證您的 AKS 叢集所在的區域[支援的 Azure 開發人員空格](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams)。
* 確認您執行 Kubernetes 1.10.3 或更新版本。
* 啟用 Azure 開發人員空格，在您的叢集使用 `az aks use-dev-spaces`

如需有關如何建立和設定適用於 Azure 開發空間的 AKS 叢集的詳細資訊，請參閱使用者入門指南：
* [開始使用 Java 的 Azure 開發空格](get-started-java.md)
* [開始使用 Azure 開發人員使用.NET Core 和 Visual Studio 的空格](get-started-netcore-visualstudio.md)
* [開始使用 Azure 開發人員使用.NET Core 的空格](get-started-netcore.md)
* [開始使用 Node.js 的 Azure 開發空格](get-started-nodejs.md)

在您的 AKS 叢集上啟用 Azure 開發人員空格時，它會安裝適用於您叢集的控制站。 控制器是您的叢集外的個別 Azure 資源，並會進行下列作業在叢集中的資源：

* 建立或指定 Kubernetes 命名空間，以做為開發人員的空間。
* 移除名為任何 Kubernetes 命名空間*azds*，如果它存在，而且會建立一個新。
* 部署 Kubernetes 初始設定式物件。

![Azure 開發人員空間準備叢集]](media/how-dev-spaces-works/prepare-cluster.svg)

若要使用 Azure 開發人員的空格，必須至少一個開發人員的空間。 Azure 開發人員空間開發空間使用 Kubernetes AKS 叢集內的命名空間。 安裝在控制站時，它會提示您建立新的 Kubernetes 命名空間，或選擇現有的命名空間來做為您的第一個開發空間。 當命名空間指定為開發人員的空間時，控制器便會新增*azds.io/space=true*標籤以將其識別為開發人員空間，該命名空間。 準備您的叢集之後，預設會選取您建立或指定的初始開發空間。 選取空格時，它正由 Azure 開發人員的空間來建立新的工作負載。

根據預設，控制器會建立名為 dev 空間*預設*升級現有*預設*Kubernetes 命名空間。 您可以使用用戶端工具來建立新的開發人員分享空間和移除現有的開發人員空間。 由於在 Kubernetes 中，限制*預設*開發空間無法移除。 控制器也會移除名為任何現有的 Kubernetes 命名空間*azds*若要避免 je v konfliktu`azds`用戶端工具所使用的命令。

Kubernetes 初始設定式物件用來部署檢測期間插入具有三個容器的 pod: devspaces proxy 容器、 devspaces proxy init 容器和 devspaces 組建容器。 **這三個容器執行您的 AKS 叢集根目錄存取權。**

![Azure 開發人員空格 Kubernetes 初始設定式](media/how-dev-spaces-works/kubernetes-initializer.svg)

Devspaces proxy 容器為側車容器處理傳入和傳出的應用程式容器的所有 TCP 流量，並可協助執行路由。 Devspaces proxy 容器 device-mapper HTTP 訊息，如果正在使用特定的空間。 比方說，它可以協助將父和子空間中的應用程式之間的 HTTP 訊息路由傳送。 所有的非 HTTP 流量通過 devspaces proxy 未經修改的狀態。 Devspaces proxy 容器也會記錄所有的傳入和傳出 HTTP 訊息，並將它們傳送到用戶端工具為追蹤。 若要檢查的行為，應用程式的開發人員接著可以檢視這些追蹤。

Devspaces proxy init 容器[init 容器](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)，將加上其他的路由規則，根據您的應用程式容器空間階層。 它會藉由更新應用程式容器的新增路由規則 */etc/resolv.conf*檔案和 iptables 相關自定義組態，再開始。 若要更新 */etc/resolv.conf*允許服務在父空間中的 DNS 解析。 Iptables 相關自定義組態更新確保到的所有 TCP 流量和出應用程式的容器會透過路由 devspaces proxy。 除了將 Kubernetes 的規則，會發生 devspaces proxy init 的所有更新。

Devspaces 組建容器是 init 容器，以及具有專案原始程式碼和掛上的 Docker 通訊端。 專案原始程式碼並存取 Docker 可讓應用程式容器，以供直接建置 pod。

> [!NOTE]
> Azure 開發人員的空間會使用相同的節點，來建置您的應用程式容器，並執行它。 如此一來，Azure 開發人員空間不需要外部的容器登錄庫來建置和執行您的應用程式。

Kubernetes 初始設定式物件會接聽任何新的 pod，會建立在 AKS 叢集中。 如果這個 pod 會部署到所有命名空間的*azds.io/space=true*標籤，它與其他的容器插入這個 pod。 如果使用用戶端工具執行應用程式的容器，只會插入 devspaces 組建容器。

一旦您已備妥您的 AKS 叢集，您可以使用用戶端工具來準備及執行您的程式碼在您開發的空間。

## <a name="prepare-your-code"></a>準備您的程式碼

若要開發空間中執行您的應用程式，它必須將容器化，您需要定義它應該如何部署到 Kubernetes。 若要將容器化應用程式，您會需要 Dockerfile。 若要定義您的應用程式部署至 Kubernetes 的方式，您需要[Helm 圖表](https://docs.helm.sh/)。 若要協助建立您的應用程式的 Dockerfile 和 Helm 圖表，提供用戶端工具`prep`命令：

```cmd
azds prep --public
```

`prep`命令會查看您的專案中的檔案，然後再次嘗試建立在 Kubernetes 中執行您的應用程式的 Dockerfile 和 Helm 圖表。 目前，`prep`命令將產生的 Dockerfile 和 Helm 圖表，以下列語言：

* Java
* Node.js
* .NET Core

您*必須*執行`prep`命令包含原始程式碼的目錄。 執行`prep`從正確的目錄的命令可讓用戶端工具，以識別語言，並建立適當的 Dockerfile，以將容器化應用程式。 您也可以執行`prep`命令包含的目錄*pom.xml* Java 專案的檔案。

如果您執行`prep`不包含原始碼，用戶端工具的目錄中的命令不會產生的 Dockerfile。 它也會顯示錯誤指出：*因為不支援的語言，無法產生 Dockerfile*。 如果用戶端工具無法辨識的專案類型，也會發生此錯誤。

當您執行`prep`命令時，您可以選擇指定`--public`旗標。 此旗標指示來建立此服務可存取網際網路的端點控制站。 如果您未指定此旗標，服務就只能從存取在叢集內，或使用 localhost 通道的用戶端工具所建立。 您可以啟用或停用此行為之後執行`prep`命令藉由更新產生的 Helm 圖表。

`prep`命令將不會取代您的專案中有任何現有的 Dockerfile 或 Helm 圖表。 如果現有的 Dockerfile 或 Helm 圖表所產生的檔案使用相同的命名慣例`prep`命令，`prep`命令將會略過產生這些檔案。 否則，`prep`命令會產生它自己的 Dockerfile 或 Helm 圖表一起現有的檔案。

`prep`命令也會產生`azds.yaml`您的專案根目錄的檔案。 Azure 開發人員的空間會使用此檔案，以建置、 安裝、 設定及執行您的應用程式。 此組態檔會列出 Dockerfile 和 Helm 圖表的位置，並也會提供額外的設定，在這些成品上。

以下是使用建立範例 azds.yaml 檔案[.NET Core 範例應用程式](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

`azds.yaml`所產生的檔案`prep`命令應該就夠用簡單、 單一專案開發案例。 如果您特定的專案已增加複雜度，您可能需要更新這個檔案開始執行之後`prep`命令。 比方說，您的專案可能需要一些調整，以您的組建，或啟動處理序，根據您的開發或偵錯的需求。 您也可能有多個應用程式在您專案中，需要多個組建處理序或不同的組建內容。

## <a name="run-your-code"></a>執行您的程式碼

若要執行您的程式碼開發空間中，發出`up`命令在相同的目錄中您`azds.yaml`檔案：

```cmd
azds up
```

`up`命令會上傳您的應用程式原始程式檔和其他建置和執行您的專案開發空間所需的成品。 從該處，您的開發人員分享空間中的控制站：

1. 建立要部署您的應用程式的 Kubernetes 物件。
1. 建置您的應用程式的容器。
1. 部署您的應用程式的開發人員的空間。
1. 如果設定，請建立可公開存取的 DNS 名稱，為您的應用程式端點。
1. 會使用*連接埠轉送*以存取您的應用程式端點使用 http://locahost。
1. 將轉送 stdout 和 stderr，用戶端工具。


### <a name="starting-a-service"></a>啟動服務

當您啟動服務於開發人員的空間時，用戶端工具和控制站運作協調來同步處理原始程式檔、 建立您的容器和 Kubernetes 物件，並執行應用程式中。

在更細微的層級中，以下是當您執行時，會發生什麼事`azds up`:

1. 檔案會從使用者的電腦同步處理到 Azure 檔案儲存體的唯一使用者的 AKS 叢集。 上傳原始程式碼、 Helm 圖表和組態檔。 更多有關同步處理程序可用於下一節。
1. 控制器會建立啟動新的工作階段的要求。 此要求包含數個屬性，包括唯一識別碼、 空間名稱、 原始碼的路徑和偵錯旗標。
1. 控制器會取代 *$(tag)* Helm 圖表的唯一工作階段識別碼 」 及 「 安裝 Helm 圖表為您的服務中的預留位置。 新增至 Helm 圖表的唯一工作階段識別碼的參考，可讓容器部署至 AKS 叢集，此繫結至工作階段要求的特定工作階段和相關聯的資訊。
1. 在安裝 Helm 圖表時，Kubernetes 初始設定式物件，請將您的應用程式的檢測和存取您的專案原始程式碼的 pod 其他容器。 Devspaces proxy 和 devspaces proxy init 容器會提供 HTTP 追蹤和空間路由加入。 Devspaces 組建容器會加入至提供存取來建置您的應用程式容器的 Docker 執行個體和專案原始程式碼的 pod。
1. 啟動應用程式的 pod 時，devspaces 組建容器和 devspaces proxy init 容器來建置應用程式容器。 然後啟動應用程式容器和 devspaces proxy 容器。
1. 用戶端功能的應用程式容器啟動後，會使用 Kubernetes*連接埠轉送*功能，以提供透過 HTTP 存取您的應用程式 http://localhost。 此連接埠轉送至您的開發人員分享空間中的服務連接您的開發電腦。
1. 當所有容器的 pod 中都啟動時，服務正在執行。 此時，用戶端功能開始串流處理的 HTTP 追蹤、 stdout 和 stderr。 這項資訊會顯示開發人員適用的用戶端功能。

### <a name="updating-a-running-service"></a>更新執行中的服務

當服務執行時，Azure 開發人員空間就會有任何專案來源檔案變更，更新該服務的能力。 開發人員的空格也會處理更新的服務，以不同的方式視變更的檔案類型而定。 有三種方式，開發人員的空間可以更新執行中的服務：

* 直接更新檔案
* 重建並重新啟動執行的應用程式容器內的應用程式的程序
* 重建和重新部署應用程式的容器

![Azure 開發人員空間檔案同步](media/how-dev-spaces-works/file-sync.svg)

是靜態的資產，例如 html、 css 和下的 cshtml 檔案中，某些專案檔案可以直接在應用程式的容器中更新，不需要重新啟動任何項目。 如果變更的靜態資產，新的檔案會同步處理到開發空間，並且接著使用執行中的容器。

例如，原始程式碼的檔案或應用程式組態檔的變更可以套用藉由重新啟動執行容器內的應用程式的程序。 應用程式的程序內執行的容器使用重新啟動這些檔案會同步處理，一旦*devhostagent*程序。 一開始建立應用程式的容器時，控制器會以不同的程序，稱為取代應用程式的啟動命令*devhostagent*。 應用程式的實際程序接著會執行為子處理序之下*devhostagent*，和其輸出會經由管道輸出使用*devhostagent*的輸出。 *Devhostagent*程序也是開發空間的一部分，並在執行中的容器中可執行的命令代表開發人員的空格。 執行重新啟動時*devhostagent*:

* 停止目前的處理序或應用程式相關聯的處理序
* 重建應用程式
* 重新啟動的處理程序或應用程式相關聯的處理序

方式*devhostagent*執行上述步驟中設定`azds.yaml`組態檔。 下節會詳細說明此組態。

專案檔，例如 Dockerfile、 csproj 檔案或 Helm 圖表的任何部分更新需要重新建置並重新部署的應用程式的容器。 其中一個檔案同步開發空間處理時，控制器會執行[helm 升級](https://helm.sh/docs/helm/#helm-upgrade)命令和應用程式的容器已重建和重新部署。

### <a name="file-synchronization"></a>檔案同步處理

第一次啟動應用程式開發人員在空間中，所有應用程式的原始程式檔上傳。 雖然應用程式正在執行，並在稍後重新啟動時，只變更的檔案上傳。 兩個檔案用來協調此程序： 用戶端檔案和控制站端檔案。

用戶端檔案會儲存在暫存目錄和名稱會根據您正在開發空間中的專案目錄的雜湊。 比方說，在 Windows 上您會有檔案，以類似*Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog*為您的專案。 在 Linux 上，用戶端檔案會儲存在 */tmp*目錄。 您可以在 macOS 上找到目錄，執行`echo $TMPDIR`命令。

此檔案採用 JSON 格式，而且包含：

* 每個專案檔會與開發空間同步處理項目
* 同步處理識別碼
* 上次同步處理作業的時間戳記

每個專案檔案項目包含檔案路徑和其時間戳記。

控制站端檔案會儲存在 AKS 叢集。 它包含同步處理識別碼和上次同步處理的時間戳記。

同步處理時間戳記不相符用戶端之間控制站端檔案時，會發生同步處理。 同步處理期間，用戶端工具逐一查看檔案中的項目用戶端檔案。 如果檔案的時間戳記是同步處理時間戳記之後，該檔案會同步處理到開發人員的空間。 在同步處理完成之後，同步處理時間戳記會更新用戶端和控制站端的檔案。

如果用戶端檔案不存在，所有專案檔案會同步處理。 此行為可讓您刪除用戶端檔案來強制完整同步處理。

### <a name="how-routing-works"></a>路由的運作方式

開發空間建置在 AKS 之上，並使用相同[網路功能概念](../aks/concepts-network.md)。 Azure 開發人員的空格也有一種集中式*ingressmanager*服務，並將它自己的輸入控制器部署至 AKS 叢集。 *Ingressmanager*服務的監視 AKS 叢集與開發人員的空格，然後再行 Azure 開發人員空間輸入控制器，在叢集中使用的路由傳送至應用程式 pod 的輸入物件。 Devspaces proxy 容器，每個 pod 中的新增`azds-route-as`根據 URL 的 HTTP 流量的開發空間的 HTTP 標頭。 例如，要求至 URL *http://azureuser.s.default.serviceA.fedcba09...azds.io*會取得與 HTTP 標頭`azds-route-as: azureuser`。 不會加入 devspaces proxy 容器`azds-route-as`如果已有的標頭。

從叢集外部的服務對 HTTP 要求，要求會以輸入控制器。 輸入控制器的要求會直接路由至適當的 pod，依據其輸入物件和規則。 Devspaces proxy 容器 pod 中的收到要求時，將`azds-route-as`標頭根據 URL，並再將要求路由傳送至應用程式容器。

HTTP 要求對服務中，從叢集內的另一個服務，要求第一次會透過呼叫服務的 devspaces proxy 容器。 Devspaces proxy 容器會查看 HTTP 要求和檢查`azds-route-as`標頭。 根據標頭，將會查詢與標頭值相關聯之服務的 IP 位址 devspaces proxy 容器。 如果找到的 IP 位址，則 devspaces proxy 容器 device-mapper 該 IP 位址的要求。 如果找不到的 IP 位址，devspaces proxy 容器會傳送要求至父系應用程式容器。

例如，應用程式*serviceA*並*serviceB*會部署到稱為父開發空間*預設*。 *serviceA*依賴*serviceB*和 HTTP 呼叫。 Azure 的使用者建立的子系開發空間，以根據*預設*稱為空間*azureuser*。 Azure 的使用者也會部署其自己的版本*serviceA*及其子空間。 若要提出要求時*http://azureuser.s.default.serviceA.fedcba09...azds.io*:

![Azure 開發人員空間路由](media/how-dev-spaces-works/routing.svg)

1. 輸入控制器查閱 pod 的 URL，也就是相關聯的 IP *serviceA.azureuser*。
1. 輸入控制器尋找開發人員的 Azure 使用者的空間中的 pod IP，並將路由傳送的要求*serviceA.azureuser* pod。
1. 中的 devspaces proxy 容器*serviceA.azureuser* pod 接收要求，以及新增`azds-route-as: azureuser`做為 HTTP 標頭。
1. 中的 devspaces proxy 容器*serviceA.azureuser* pod 路由傳送要求以*serviceA*中的應用程式容器*serviceA.azureuser* pod。
1. *ServiceA*應用程式*serviceA.azureuser* pod 會呼叫*serviceB*。 *ServiceA*應用程式也包含程式碼來保留現有`azds-route-as`標頭，在此情況下是`azds-route-as: azureuser`。
1. 中的 devspaces proxy 容器*serviceA.azureuser* pod 接收要求，並查閱的 IP *serviceB*的值為基礎`azds-route-as`標頭。
1. 中的 devspaces proxy 容器*serviceA.azureuser* pod 找不到的 IP *serviceB.azureuser*。
1. 中的 devspaces proxy 容器*serviceA.azureuser* pod 的 IP 會查閱*serviceB*在父空間中，這是*serviceB.default*。
1. 中的 devspaces proxy 容器*serviceA.azureuser* pod 找到的 IP *serviceB.default*並將要求路由傳送*serviceB.default* pod。
1. 中的 devspaces proxy 容器*serviceB.default* pod 接收要求，並將路由傳送的要求*serviceB*中的應用程式容器*serviceB.default*pod。
1. *ServiceB*應用程式*serviceB.default* pod 將回應傳回給*serviceA.azureuser* pod。
1. 中的 devspaces proxy 容器*serviceA.azureuser* pod 會在收到回應，並將路由傳送的回應*serviceA*中的應用程式容器*serviceA.azureuser* pod。
1. *ServiceA*應用程式接收回應，然後再傳回自己的回應。
1. 中的 devspaces proxy 容器*serviceA.azureuser* pod 會接收來自回應*serviceA*應用程式容器和叢集外的原始呼叫端的回應將路由傳送。

所有其他不是 HTTP 的 TCP 流量會透過輸入控制器和 devspaces proxy 容器未經修改的狀態傳遞。

### <a name="how-running-your-code-is-configured"></a>如何執行您的程式碼設定

Azure 開發人員的空間會使用`azds.yaml`檔案來安裝和設定您的服務。 控制器會使用`install`屬性中的`azds.yaml`安裝 Helm 圖表，並建立 Kubernetes 物件的檔案：

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

根據預設，`prep`命令會產生 Helm 圖表。 它也會設定*install.chart*之目錄的 Helm 圖表的屬性。 如果您想要在不同的位置使用 Helm 圖表，您可以更新此屬性，以使用該位置。

當安裝 Helm 圖表，Azure 開發人員的空間會提供方法，以覆寫 Helm 圖表中的值。 Helm 圖表的預設值都是`charts/APP_NAME/values.yaml`。

使用*install.values*屬性，您可以列出一或多個檔案定義您想在 Helm 圖表中已取代的值。 比方說，如果您想要的主機名稱或資料庫組態，特別當開發空間中執行您的應用程式，您可以使用這項覆寫功能。 您也可以新增*嗎？* 在結束時的任何檔案名稱，以將它設為選擇性。

*Install.set*屬性可讓您設定您想要取代 Helm 圖表中的一或多個值。 在設定的任何值*install.set*將會覆寫中列出的檔案中設定值*install.values*。 下的屬性*install.set* Helm 圖表中的值而定，而且可能產生的 Helm 圖表而有所不同。

在上述範例中， *install.set.replicaCount*屬性就會告知控制器，您的應用程式開發人員空間中執行的執行個體數目。 根據您的案例中，您可以增加此值，但其會影響偵錯工具附加至您的應用程式 pod。 如需詳細資訊，請參閱 <<c0> [ 疑難排解文章](troubleshooting.md)。

在產生的 Helm 圖表中，容器映像設定為 *{{。Values.image.repository}}:{{。Values.image.tag}}*。 `azds.yaml`檔案會定義*install.set.image.tag*屬性設為 *$(tag)* 預設情況下，它會使用做為值 *{{。Values.image.tag}}*。 藉由設定*install.set.image.tag*以這種方式的屬性，它可讓您的應用程式執行 Azure 開發人員空格時，要以不同方式標記容器映像。 這種情況下，將映像會標記為 *<value from image.repository>: $(tag)*。 您必須使用 *$(tag)* 變數的值設定為*install.set.image.tag*為了讓開發人員空間辨識，並找出在 AKS 叢集中的容器。

在上述範例中，`azds.yaml`定義*install.set.ingress.hosts*。 *Install.set.ingress.hosts*屬性會定義公用端點的主機名稱格式。 這個屬性也會使用 *$(spacePrefix)*， *$(rootSpacePrefix)*，並 *$(hostSuffix)*，這是控制器所提供的值。 

*$(SpacePrefix)* 的子系開發空間，其使用的格式名稱*SPACENAME.s*。 *$(RootSpacePrefix)* 父空間的名稱。 例如，如果*azureuser*的子空間*預設*，值 *$(rootSpacePrefix)* 是*預設*的值 *$(spacePrefix)* 是*azureuser.s*。 如果沒有子空間，這種空間。 *$(spacePrefix)* 是空白。 比方說，如果*預設*空間有沒有父空間，而值 *$(rootSpacePrefix)* 會*預設*的值 *$(spacePrefix)* 是空白。 *$(HostSuffix)* 是指向 Azure 開發人員空間輸入控制器在 AKS 叢集中執行的 DNS 尾碼。 此 DNS 尾碼對應到萬用字元 DNS 項目，例如 *\*。RANDOM_VALUE.eus.azds.io*，Azure 開發人員空格控制站新增至您的 AKS 叢集時所建立。

在上述`azds.yaml`檔案中，您可以也更新*install.set.ingress.hosts*若要變更您的應用程式的主機名稱。 例如，如果您想要簡化您的應用程式的主機名稱 *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* 到 *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)*.

若要建置您的應用程式的容器，控制器會使用下列各節的`azds.yaml`組態檔：

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

控制器會使用 Dockerfile 來建置並執行您的應用程式。

*Build.context*屬性清單所在的 Dockerfile 的目錄。 *Build.dockerfile*屬性定義 Dockerfile 來建置應用程式的實際執行版本的名稱。 *Configurations.develop.build.dockerfile*屬性會設定 Dockerfile 的開發版本的應用程式的名稱。

擁有不同 Dockerfile 的開發和生產環境，可讓您在開發期間啟用某些項目，並停用用於生產環境部署這些項目。 例如，您可以啟用偵錯，或在開發和生產環境中的停用期間的更詳細資訊記錄。 如果您的 Dockerfile 會以不同方式命名，或位於不同的位置，您也可以更新這些屬性。

為了協助您快速地逐一查看在開發期間，Azure 開發人員空間將同步處理從本機專案的變更，並以累加方式更新您的應用程式。 下一節中`azds.yaml`組態檔用來設定同步處理和更新：

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

檔案和目錄同步處理變更所述*configurations.develop.container.sync*屬性。 這些目錄同步處理一開始執行時`up`命令以及偵測到變更時。 如果有其他或不同的目錄。 您想要同步處理至您的開發人員分享空間，您可以變更這個屬性。

*Configurations.develop.container.iterate.buildCommands*屬性會指定如何建置應用程式，在開發案例。 *Configurations.develop.container.command*屬性提供用來在開發案例中執行應用程式的命令。 您可能想要更新上述任一個屬性，如果有其他的建置或執行階段旗標或您想要在開發期間使用的參數。

*Configurations.develop.container.iterate.processesToKill*列出要停止的應用程式終止的處理程序。 若要更新此屬性，如果您想要變更您的應用程式，在開發期間的重新啟動行為。 例如，如果您更新*configurations.develop.container.iterate.buildCommands*或是*configurations.develop.container.command*屬性，以變更應用程式的建置方式或啟動，您可能需要變更哪些處理程序會停止。

在準備程式碼中使用時才`azds prep`命令時，您可以選擇新增`--public`旗標。 新增`--public`旗標會建立可公開存取的 URL，您的應用程式。 如果您省略此旗標時，就只能存取叢集，或使用 localhost 通道內的應用程式。 在執行後`azds prep`命令時，您可以變更此設定修改*ingress.enabled*屬性中的`charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>偵錯程式碼

對於 Java、.NET 和 Node.js 應用程式，您可以偵錯您直接在您使用 Visual Studio Code 或 Visual Studio 2017 的開發人員空間中執行的應用程式。 Visual Studio Code 和 Visual Studio 2017 提供工具，以連接到您開發的空間、 啟動您的應用程式，並附加偵錯工具。 執行後`azds prep`，您可以在 Visual Studio Code 或 Visual Studio 2017 中開啟您的專案。 Visual Studio Code 或 Visual Studio 2017 會產生自己的設定檔，以連接即分開執行`azds prep`。 從 Visual Studio Code 或 Visual Studio 2017，可以設定中斷點，以及啟動您的應用程式，您的開發人員分享空間。

![偵錯您的程式碼](media/get-started-node/debug-configuration-nodejs2.png)

當您啟動您的應用程式進行偵錯使用 Visual Studio Code 或 Visual Studio 2017 時，它們會處理啟動並執行相同的方式連接到您的開發人員空間`azds up`。 在 Visual Studio Code 和 Visual Studio 2017 中的用戶端工具也會提供額外的參數，以偵錯的特定資訊。 參數會包含偵錯工具映像，在偵錯工具的映像，在偵錯工具的位置和目的地位置掛接偵錯工具資料夾的應用程式的容器內的名稱。 

偵錯工具的映像自動取決於用戶端工具。 它會使用的方法類似於用於 Dockerfile 和 Helm 圖表產生執行時`azds prep`。 偵錯工具會在應用程式的映像掛接後，就會執行使用`azds exec`。

## <a name="sharing-a-dev-space"></a>共用開發空間

當使用小組，您可以[跨整個小組共用開發空間](how-to/share-dev-spaces.md)建立衍生的開發人員的空間。 開發空間可用來開發空間的資源群組的參與者存取權的任何人。

您也可以建立新的開發人員空間衍生自另一個開發人員的空間。 當您建立衍生的開發人員空格*azds.io/父空間 = 父空間名稱*標籤就會加入至衍生的開發空間的命名空間。 此外，從父代的開發空間的所有應用程式會共用以衍生的開發空間。 如果您部署的應用程式，以衍生的開發空間的更新的版本時，它只會存在於衍生的開發空間，而且父開發空間會維持不會受到影響。 您可以有最多三個層級的衍生的開發空格或*祖系*空格。

衍生的開發空間也以智慧方式會將它自己的應用程式和從其父代共用的應用程式之間的要求路由傳送。 嘗試衍生的開發空間中的應用程式的路由要求，並回到共用的應用程式從父代的開發人員空間路由的運作。 路由會回復為祖系空間中共用的應用程式如果應用程式不在父空間中。

例如︰
* 開發空間*預設*的應用程式*serviceA*並*serviceB* 。
* 開發空間*azureuser*衍生自*預設*。
* 更新的版本*serviceA*部署到*azureuser*。

使用時*azureuser*，所有要求*serviceA*將會路由至更新版本中*azureuser*。 若要要求*serviceB*會先嘗試路由傳送至*azureuser*版本*serviceB*。 因為不存在，它將會路由至*預設*新版*serviceB*。 如果*azureuser*新版*serviceA*移除時，所有要求*serviceA*會切換回使用*預設*版本*serviceA*。

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure 開發人員的空間，請參閱下列快速入門：

* [使用 CLI 和 Visual Studio Code 的 Java](quickstart-java.md)
* [使用 CLI 和 Visual Studio Code 的.NET core](quickstart-netcore.md)
* [使用 Visual Studio 2017 的.NET core](quickstart-netcore-visualstudio.md)
* [使用 CLI 和 Visual Studio Code 的 Node.js](quickstart-nodejs.md)

若要開始進行小組開發，請參閱下列的使用說明文章：

* [小組開發-使用 CLI 和 Visual Studio Code 的 Java](team-development-java.md)
* [小組開發-使用 CLI 和 Visual Studio Code 的.NET Core](team-development-netcore.md)
* [小組開發-使用 Visual Studio 2017 的.NET Core](team-development-netcore-visualstudio.md)
* [小組開發-使用 CLI 和 Visual Studio Code 的 Node.js](team-development-nodejs.md)
