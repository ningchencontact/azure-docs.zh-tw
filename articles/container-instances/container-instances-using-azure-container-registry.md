---
title: "從 Azure Container Registry 部署至 Azure Container Instances"
description: "從 Azure Container Registry 部署 Azure Container Instances"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 4205b47dc67920021812c1e573a98de64ad198ec
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>從 Azure Container Registry 部署至 Azure Container Instances

Azure Container Registry 是以 Azure 為基礎的私人登錄，用於裝載 Docker 容器映像。 本文說明如何將儲存於 Azure Container Registry 的容器映像部署至 Azure Container Instances。

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

Azure CLI 包括可用於建立和管理 Azure Container Instances 中容器的命令。 如果您指定的私人映像中[az 容器建立][ az-container-create]命令，您可以指定向容器登錄中所需的映像登錄密碼。

```azurecli-interactive
az container create --resource-group myResourceGroup --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword
```

[Az 容器建立][ az-container-create]命令也支援指定`--registry-login-server`和`--registry-username`。 不過，Azure Container Registry 的登入伺服器永遠會是 registryname.azurecr.io，而預設使用者名稱是 registryname，因此如果未明確提供，您仍可從映像名稱推斷這些值。

## <a name="deploy-with-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本部署

您可以在容器群組定義中加入 `imageRegistryCredentials` 屬性，以在 Azure Resource Manager 範本中指定 Azure Container Registry 的屬性：

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

為了避免在範本中直接儲存容器登錄密碼，我們建議您將其儲存為 [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) 中的祕密，並在範本中使用 [Azure Resource Manager 和 Key Vault 之間的原生整合](../azure-resource-manager/resource-manager-keyvault-parameter.md)參考該祕密。

## <a name="deploy-with-azure-portal"></a>使用 Azure 入口網站部署

如果您在 Azure Container Registry 中保有容器映像，您就可以使用 Azure 入口網站在 Azure Container Instances 中輕鬆地建立容器。

1. 在 Azure 入口網站中，瀏覽到您的容器登錄。

2. 選取**儲存機制**，然後選取您想要從部署的儲存機制以滑鼠右鍵按一下您想要部署，而且選取的容器映像的標記**執行執行個體**。

    ![「 執行個體 」 Azure 容器登錄中在 Azure 入口網站][acr-runinstance-contextmenu]

3. 分別為容器和資源群組輸入名稱。 如有需要，您也可以變更預設值。

    ![建立 Azure Container Instances 的功能表][acr-create-deeplink]

4. 部署完成之後，您可以從 [通知] 窗格瀏覽至容器群組，以尋找其 IP 位址和其他屬性。

    ![Azure Container Instances 容器群組的詳細資料檢視][aci-detailsview]

## <a name="next-steps"></a>後續步驟

透過[完成教學課程](container-instances-tutorial-prepare-app.md)，了解如何建置容器、將其推送至私人容器登錄和將其部署至 Azure Container Instances。

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create