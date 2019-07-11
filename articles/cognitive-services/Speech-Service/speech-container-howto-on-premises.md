---
title: 使用 Kubernetes 內部
titleSuffix: Azure Cognitive Services
description: 使用 Kubernetes 與 Helm 來定義語音轉換文字和文字轉換語音的容器映像，我們將建立一個 Kubernetes 套件。 此套件將會部署到 Kubernetes 叢集 在內部部署環境。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/10/2019
ms.author: dapine
ms.openlocfilehash: 33d9de956a6d43145fc68f4ec46b09b8e8bf0188
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786246"
---
# <a name="use-kubernetes-on-premises"></a>使用 Kubernetes 內部

使用 Kubernetes 與 Helm 來定義語音轉換文字和文字轉換語音的容器映像，我們將建立一個 Kubernetes 套件。 此套件將會部署到 Kubernetes 叢集 在內部部署環境。 最後，我們將探討如何測試已部署的服務和各種組態選項。

## <a name="prerequisites"></a>先決條件

使用語音容器內部部署之前，您必須符合下列必要條件：

|必要|用途|
|--|--|
| Azure 帳戶 | 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶][free-azure-account] 。 |
| 容器登錄存取 | 為了讓提取 docker 映像到叢集中的 Kubernetes，它必須使用容器登錄的存取權。 您需要[要求至容器登錄的存取權][speech-preview-access]第一次。 |
| Kubernetes CLI | [Kubernetes CLI][kubernetes-cli]做是為了從容器登錄管理共用的認證。 Helm 是 Kubernetes 的套件管理員之前，也需要 Kubernetes。 |
| Helm CLI | 做為一部分[Helm CLI][helm-install] install, you'll also need to initialize Helm which will install [Tiller][tiller-install]。 |
|語音資源 |若要使用這些容器，您必須具備：<br><br>A_語音_Azure 資源，以取得相關聯的計費金鑰和計費的端點 URI。 這兩個值都位於 Azure 入口網站**語音**概觀] 和 [索引鍵頁面且需要啟動容器。<br><br>**{API_KEY}** ： 資源索引鍵<br><br>**{ENDPOINT_URI}** ： 端點 URI 範例是： `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>建議的主機電腦設定

請參閱[語音服務容器主機電腦][speech-container-host-computer]做為參考的詳細資料。 這*helm 圖表*會自動計算多少解碼 （並行要求） 為基礎的 CPU 和記憶體需求，使用者所指定。 此外，它將會調整根據音訊/文字輸入的最佳化會設定為`enabled`。 Helm 圖表，預設值為兩個並行要求，停用最佳化。

| 服務 | CPU / 容器 | 記憶體 / 容器 |
|--|--|--|
| **語音轉文字** | 1,150 millicores 至少需要一個解碼器。 如果`optimizedForAudioFile`已啟用，則 1,950 millicores 所需。 (預設： 兩個解碼器) | 必要：2 GB<br>限制：4 GB |
| **文字轉換語音** | 500 millicores 至少需要一個並行要求。 如果`optimizeForTurboMode`已啟用，則 1000 millicores 所需。 (預設： 兩個並行要求) | 必要：1 GB<br> 限制：2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>連線到 Kubernetes 叢集

在主機電腦必須有可用的 Kubernetes 叢集。 請參閱本教學課程[部署 Kubernetes 叢集](../../aks/tutorial-kubernetes-deploy-cluster.md)概念了解如何將 Kubernetes 叢集部署到主機電腦。

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>與 Kubernetes 叢集的共用 Docker 認證

若要允許 Kubernetes 叢集`docker pull`已設定的映像從`containerpreview.azurecr.io`容器登錄中，您需要將 docker 認證移轉到叢集中。 執行[ `kubectl create` ][kubectl-create]下列命令來建立*docker 登錄密碼*根據從容器登錄存取必要條件所提供的認證。

從選擇的命令列介面，執行下列命令。 請務必取代`<username>`， `<password>`，和`<email-address>`以容器登錄認證。

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> 如果您已經有存取權`containerpreview.azurecr.io`容器登錄中，您可以建立 Kubernetes 祕密，改為使用一般的旗標。 請考慮針對您的 Docker 組態 JSON 執行下列命令。
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

已成功建立祕密時，下列的輸出被列印到主控台。

```console
secret "containerpreview" created
```

若要確認已建立的祕密，請執行[ `kubectl get` ][kubectl-get]使用`secrets`旗標。

```console
kuberctl get secrets
```

執行`kubectl get secrets`會列印所有設定的密碼。

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>設定針對部署 Helm 圖表值

請瀏覽[Microsoft Helm 中樞][ms-helm-hub]Microsoft 所提供的所有公開可用的 helm 圖表。 從 Microsoft Helm 中樞中，您會發現**認知服務語音內部圖表**。 **認知服務語音內部**圖表，我們會進行安裝，但我們必須先建立`config-values.yaml`具有明確組態的檔案。 現在就開始將過 Microsoft 存放庫新增至 Helm 執行個體。

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

接下來，我們將設定我們的 Helm 圖表值。 複製並貼入名為的檔案中的下列 YAML `config-values.yaml`。 如需有關自訂**認知服務語音內部部署 Helm 圖表**，請參閱[自訂 helm 圖表](#customize-helm-charts)。 取代`billing`和`apikey`以您自己的值。

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >
```

> [!IMPORTANT]
> 如果`billing`和`apikey`未提供值，服務將在 15 分鐘後到期。 同樣地，驗證也會失敗，因為服務將無法使用。

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes 套件 （Helm 圖表）

*Helm 圖表*包含哪一個 docker 映像檔從提取組態`containerpreview.azurecr.io`容器登錄。

> A [Helm 圖表][helm-charts]是描述一組相關的 Kubernetes 資源檔案的集合。 在單一圖表可能會用來部署簡單的項目，例如 memcached pod，或複雜，例如使用 HTTP 伺服器、 資料庫、 快取和等等的完整的 web 應用程式堆疊。

所提供*Helm 圖表*提取 docker 映像的語音服務，文字轉換語音以及語音文字服務`containerpreview.azurecr.io`容器登錄。

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>在 Kubernetes 叢集中安裝 Helm 圖表

若要安裝*helm 圖表*我們需要執行[ `helm install` ][helm-install-cmd]命令，將`<config-values.yaml>`搭配適當的路徑和檔案名稱引數。 `microsoft/cognitive-services-speech-onpremise`下列參考的 Helm 圖表位於[Microsoft Helm 中樞][ms-helm-hub-speech-chart]。

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

以下是您可能會看到安裝成功執行範例輸出：

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

Kubernetes 部署可能需要幾分鐘才能完成。 若要確認 pod 和服務都已經正確部署，而且可用，請執行下列命令：

```console
kubectl get all
```

您應該會看到類似下列輸出：

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>使用 Helm 測試驗證 Helm 部署

定義已安裝的 Helm 圖表*Helm 測試*，其做為讓您輕鬆驗證。 這些測試會驗證服務整備性。 若要確認這兩**語音轉換文字**並**文字轉換語音**服務，我們將執行[Helm 測試][helm-test]命令。

```console
helm test onprem-speech
```

> [!IMPORTANT]
> 如果 POD 狀態不是，這些測試將會失敗`Running`或如果部署不會列在`AVAILABLE`資料行。 請耐心等候，這可能需要超過 10 分鐘才能完成。

這些測試會輸出各種不同的狀態結果：

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

除了執行*helm 測試*，您可以收集*外部 IP*位址和相關的連接埠從`kubectl get all`命令。 使用 IP 和連接埠，開啟網頁瀏覽器並瀏覽至`http://<external-ip>:<port>:/swagger/index.html`API swagger 頁面的檢視。

## <a name="customize-helm-charts"></a>自訂 Helm 圖表

Helm 圖表是階層式。 階層式的是可讓圖表繼承，它也已經考慮到它明確性，其中是更為專用的設定會覆寫繼承的規則的概念。

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>後續步驟

如需使用 Helm 在 Azure Kubernetes Service (AKS) 中，安裝應用程式的詳細[瀏覽此處][installing-helm-apps-in-aks]。

> [!div class="nextstepaction"]
> [認知服務容器][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[speech-preview-access]: https://aka.ms/speechcontainerspreview
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
