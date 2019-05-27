---
title: 教學課程-部署多容器群組在 Azure Container Instances YAML
description: 在本教學課程中，您將了解如何使用 Azure CLI 使用 YAML 檔案來部署 Azure Container Instances 中的多個容器的容器群組。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.openlocfilehash: a0a91ece4f219cf822673cd457c064c326b89478
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66149084"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>教學課程：部署多容器群組使用 YAML 檔案

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure 容器執行個體支援使用[容器群組](container-instances-container-groups.md)將多個容器部署至單一主機。 容器群組時，建置應用程式 sidecar 記錄、 監視或任何其他設定的服務需要第二個附加的處理序。

在本教學課程中，您可以遵循步驟來部署 YAML 檔案，使用 Azure CLI 執行簡單的兩個容器 sidecar 設定。 YAML 檔案會提供精簡的格式來指定執行個體設定。 您會了解如何：

> [!div class="checklist"]
> * 設定 YAML 檔案
> * 部署容器群組
> * 檢視容器記錄

> [!NOTE]
> 多容器群組目前僅限於 Linux 容器。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>設定 YAML 檔案

若要部署多容器群組[az 容器建立][ az-container-create]命令在 Azure CLI 中，您必須在 YAML 檔案中指定的容器群組設定。 然後將 YAML 檔案，做為參數傳遞的命令。

一開始先將下列 YAML 複製到名為 **deploy-aci.yaml** 的新檔案中。 在 Azure Cloud Shell 中，您可以使用 Visual Studio Code，在您的工作目錄中建立檔案：

```
code deploy-aci.yaml
```

此 YAML 檔案會定義名為 "myContainerGroup"，且含有兩個容器 (一個公用 IP 位址和兩個公開連接埠) 的容器群組。 容器會從公用 Microsoft 映像進行部署。 群組中第一個容器會執行網際網路對向的 Web 應用程式。 第二個容器 sidecar 會透過容器群組的區域網路，定期對第一個容器中執行的 Web 應用程式執行 HTTP 要求。

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

若要使用私用容器映像登錄，新增`imageRegistryCredentials`屬性，以在容器群組，修改為您的環境的值：

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>部署容器群組

使用 [az group create][az-group-create] 命令來建立資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az container create][az-container-create] 命令部署容器群組，並以引數的形式傳遞 YAML 檔案：

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

在幾秒內，您應該會從 Azure 收到首次回應。

## <a name="view-deployment-state"></a>檢視部署狀態

若要檢視部署的狀態，請使用下列 [az container show][az-container-show] 命令：

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

若要檢視執行中的應用程式，請在瀏覽器中瀏覽至其公用 IP 位址。 例如，在此範例輸出中，IP 是 `52.168.26.124`：

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>檢視容器記錄

使用 [az container logs][az-container-logs] 命令，檢視容器的記錄輸出。 `--container-name` 引數會指定要從中提取記錄的容器。 在此範例中，`aci-tutorial-app`指定容器。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

輸出：

```console
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

若要查看 sidecar 容器的記錄，執行類似的命令指定`aci-tutorial-sidecar`容器。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

輸出：

```console
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

如您所見，Sidecar 會透過群組的區域網路定期地向主要 Web 應用程式提出 HTTP 要求，以確保它正在執行。 可以擴充此 sidecar 範例，以觸發警示，如果它收到的 HTTP 回應碼以外`200 OK`。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您可以使用 YAML 檔案來部署 Azure Container Instances 中的多容器群組。 您已了解如何︰

> [!div class="checklist"]
> * 設定多容器群組的 YAML 檔案
> * 部署容器群組
> * 檢視容器記錄

您也可以指定多個容器群組，使用[Resource Manager 範本](container-instances-multi-container-group.md)。 當您需要部署額外的 Azure 服務與容器群組的資源時，Resource Manager 範本，可輕易地調整案例。

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
