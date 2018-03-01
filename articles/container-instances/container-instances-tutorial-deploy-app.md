---
title: "Azure 容器執行個體教學課程 - 部署應用程式"
description: "Azure 容器執行個體教學課程第 3 部分 (共 3 部分) - 部署應用程式"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 99bd03bf4c3ca2d7b1ced51ebfe8be669f271c1c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="deploy-a-container-to-azure-container-instances"></a>將容器部署至 Azure 容器執行個體

這是三部分系列的最後一個教學課程。 在系列的較前段，我們[已建立容器映像](container-instances-tutorial-prepare-app.md)並[推送至 Azure Container Registry](container-instances-tutorial-prepare-acr.md)。 本文會將容器部署至 Azure 容器執行個體，以完成教學課程系列。

在本教學課程中，您：

> [!div class="checklist"]
> * 使用 Azure CLI 從 Azure Container Registry 部署容器
> * 在瀏覽器中檢視應用程式
> * 檢視容器記錄

## <a name="before-you-begin"></a>開始之前

本教學課程需要您執行 Azure CLI 2.0.27 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0][azure-cli-install]。

若要完成本教學課程，您需要在本機安裝 Docker 開發環境。 Docker 提供可輕鬆在 [Mac][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系統上設定 Docker 的套件。

Azure Cloud Shell 不包括完成本教學課程每個步驟所需的 Docker 元件。 您必須在本機電腦上安裝 Azure CLI 和 Docker 開發環境，才能完成本教學課程。

## <a name="deploy-the-container-using-the-azure-cli"></a>使用 Azure CLI 來部署容器

Azure CLI 能夠透過單一命令將容器部署至 Azure 容器執行個體。 由於容器映像裝載在私人 Azure Container Registry 中，因此您必須納入所需的認證才能存取該映像。 使用下列 Azure CLI 命令取得認證。

容器登錄的登入伺服器 (以登錄名稱來更新)：

```azurecli
az acr show --name <acrName> --query loginServer
```

容器登錄密碼：

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

若要從容器登錄中部署要求 1 個 CPU 核心和 1 GB 記憶體資源的容器映像，請執行下列命令。 以您從先前兩個命令取得的值取代 `<acrLoginServer>` 和 `<acrPassword>`。 以您的容器登錄名稱取代 `<acrName>`。

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

在幾秒內，您應該就會從 Azure Resource Manager 收到首次的回應。 `--dns-name-label` 值在您建立容器執行個體的 Azure 區域中必須是唯一的。 如果您在執行命令時收到 **DNS 名稱標籤**錯誤訊息，請更新上述範例中的值。

若要檢視部署的狀態，請使用 [az container show][az-container-show]：

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

重複執行 [az container show][az-container-show] 命令，直到狀態從 Pending (暫止) 變更為 Running (執行中) 為止，這應該會在一分鐘內完成。 當容器狀態為 *Running* (執行中) 時，請繼續進行下一個步驟。

## <a name="view-the-application-and-container-logs"></a>檢視應用程式和容器記錄

部署成功之後，請使用 [az container show][az-container-show] 命令來顯示容器的完整網域名稱 (FQDN)：

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

範例輸出：`"aci-demo.eastus.azurecontainer.io"`

若要查看執行中的應用程式，在您慣用的瀏覽器中瀏覽至顯示的 DNS 名稱：

![瀏覽器中的 Hello World 應用程式][aci-app-browser]

您也可以檢視容器的記錄輸出：

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

輸出：

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>清除資源

如果您已不再需要這個教學課程系列中建立的任何資源，便可以執行 [az group delete][az-group-delete] 命令來移除資源群組及其包含的所有資源。 此命令除了會刪除執行中的容器和所有相關資源之外，也會刪除您所建立的容器登錄。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成將容器部署至 Azure 容器執行個體的程序。 已完成下列步驟：

> [!div class="checklist"]
> * 使用 Azure CLI 從 Azure Container Registry 部署容器
> * 在瀏覽器中檢視應用程式
> * 檢視容器記錄

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
