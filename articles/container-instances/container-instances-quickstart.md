---
title: 快速入門 - 建立您的第一個 Azure Container Instances 容器
description: 在本快速入門中，您會使用 Azure CLI 在 Azure Container Instances 中部署容器
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: b68468cd8174d658d04d8e67433a8f18884493bd
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>快速入門：在 Azure Container Instances 中建立您的第一個容器

Azure Container Instances 能讓您在 Azure 中輕鬆建立及管理 Docker 容器，不需要佈建虛擬機器或採用高階服務即可完成。 在本快速入門中，您會在 Azure 中建立容器，並使用完整網域名稱 (FQDN) 向網際網路公開此容器。 只需要一個命令就能完成這項作業。 只在幾秒內，您就能在瀏覽器中看到下列結果：

![在瀏覽器中檢視使用 Azure Container Instances 所部署的應用程式][aci-app-browser]

如果您沒有 Azure 訂用帳戶，請在開始前建立 [[免費帳戶]][azure-account]。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成此快速入門。 如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.27 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0][azure-cli-install]。

## <a name="create-a-resource-group"></a>建立資源群組

和所有 Azure 資源一樣，Azure Container Instances 必須放在資源群組中，這是可用來部署和管理 Azure 資源的邏輯集合。

使用 [az group create][az-group-create] 命令來建立資源群組。

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>建立容器

您可以向 [az container create][az-container-create] 命令提供名稱、Docker 映像和 Azure 資源群組來建立容器。 您可藉由指定 DNS 名稱標籤，選擇性地向網際網路公開容器。 在本快速入門中，您會部署一個裝載以 [Node.js][node-js] 撰寫之小型 Web 應用程式的容器。

執行下列命令以啟動容器執行個體。 `--dns-name-label` 值必須是您建立執行個體的 Azure 區域中的唯一值，因此您可能需要修改這個值以確保唯一性。

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

在幾秒內，您就能取得要求的回應。 一開始，容器會處於**建立中**狀態，但應該會在幾秒鐘內啟動。 您可以使用 [az container show][az-container-show] 命令來檢查狀態：

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

當您執行命令時，就會顯示容器的完整網域名稱 (FQDN) 及其佈建狀態：

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

容器變更為 [成功] 狀態後，請在瀏覽器中瀏覽至其 FQDN：

![顯示在 Azure 容器執行個體中執行之應用程式的瀏覽器螢幕擷取畫面][aci-app-browser]

## <a name="pull-the-container-logs"></a>提取容器記錄

在對您的容器或其執行的應用程式排解問題時，檢視的容器執行個體的記錄會很有幫助。

使用 [az container logs][az-container-logs] 命令提取容器的記錄：

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

輸出會顯示容器的記錄，且應該會顯示您在瀏覽器中檢視應用程式時產生的 HTTP GET 要求。

```console
$ az container logs --resource-group myResourceGroup -n mycontainer
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="attach-output-streams"></a>附加輸出資料流

除了加到記錄尾部外，您還可以將您的本機標準輸出和標準錯誤資料流附加至容器的這些項目。

首先，請執行 [az container attach][az-container-attach] 命令將本機主控台附加至容器的輸出資料流：

```azurecli-interactive
az container attach --resource-group myResourceGroup -n mycontainer
```

在附加之後，重新整理瀏覽器幾次以產生一些額外的輸出。 最後，使用 `Control+C` 將主控台中斷連結。 您應該會看到如下所示的輸出：

```console
$ az container attach --resource-group myResourceGroup -n mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-15 21:17:59+00:00) pulling image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Successfully pulled image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Created container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45
(count: 1) (last timestamp: 2018-03-15 21:18:06+00:00) Started container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45

Start streaming logs:
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:44 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:47 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="clean-up-resources"></a>清除資源

當容器使用完畢後，請使用 [az container delete][az-container-delete] 命令來移除容器：

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

若要確認否已將容器刪除，請執行 [az container list](/cli/azure/container#az_container_list) 命令：

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

**mycontainer** 不應該出現在命令的輸出中。 如果您的資源群組中沒有任何其他容器，則不會顯示任何輸出。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已透過來自公用 Docker Hub 登錄中的映像建立 Azure 容器執行個體。 如果您想要自行建置容器映像，並從私人的 Azure 容器登錄將其部署至 Azure 容器執行個體，請繼續進行 Azure 容器執行個體教學課程。

> [!div class="nextstepaction"]
> [Azure 容器執行個體教學課程](./container-instances-tutorial-prepare-app.md)

若要試用在 Azure 上的協調流程系統中執行容器的選項，請參閱 [Service Fabric][service-fabric] 或 [Azure Kubernetes Service (ACS)][container-service] 快速入門。

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-create]: /cli/azure/container#az_container_create
[az-container-delete]: /cli/azure/container#az_container_delete
[az-container-list]: /cli/azure/container#az_container_list
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
