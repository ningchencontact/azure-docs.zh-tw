---
title: 從私人登錄將應用程式部署至 Azure Service Fabric Mesh | Microsoft Docs
description: 了解如何使用 Azure CLI 將使用私人容器登錄的應用程式部署至 Service Fabric Mesh。
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/16/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: af92d3c6ea881d00ec687a5560bf4db35aa431c5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089481"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>從私人容器映像登錄部署 Service Fabric Mesh 應用程式

本文說明如何部署使用私人容器映像登錄的 Azure Service Fabric Mesh 應用程式。

## <a name="prerequisites"></a>必要條件

### <a name="set-up-service-fabric-mesh-cli"></a>設定 Service Fabric Mesh CLI 
您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成此工作。 請依照下列[指示](service-fabric-mesh-howto-setup-cli.md)安裝 Azure Service Fabric Mesh CLI 擴充功能模組。

### <a name="install-docker"></a>安裝 Docker

安裝 Docker 以支援 Service Fabric Mesh 所使用的容器化 Service Fabric 應用程式。

### <a name="windows-10"></a>Windows 10

下載並安裝最新版的 [Docker Community Edition for Windows][download-docker]。 

在安裝期間，當系統詢問時，請選取 [使用 Windows 容器，而非 Linux 容器]。 接著您必須登出，然後再重新登入。 在重新登入後，如果您先前未啟用 Hyper-V，系統可能會提示您啟用 Hyper-V。 請啟用 Hyper-V，然後重新啟動電腦。

在電腦重新啟動後，Docker 會提示您啟用 [容器] 功能，請加以啟用，然後重新啟動電腦。

### <a name="windows-server-2016"></a>Windows Server 2016

使用下列 PowerShell 命令安裝 Docker。 如需詳細資訊，請參閱 [Docker Enterprise Edition for Windows Server][download-docker-server]。

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

重新啟動您的電腦。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure，並設定使用中的訂用帳戶：

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>建立容器登錄，然後將映像推送至其中

遵循[使用 Azure CLI 在 Azure 中建立私人 Docker 登錄](../container-registry/container-registry-get-started-azure-cli.md)中的指示來建立 Azure Container Registry。 執行到[登入 ACR](../container-registry/container-registry-get-started-azure-cli.md#log-in-to-acr) 為止的步驟。 

### <a name="push-image-to-acr"></a>推送映像到 ACR

若要推送映像到 Azure Container Registry，您必須先有映像。 如果您還沒有任何本機容器映像，請執行下列命令，從 Docker 中樞提取現有的映像。

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

您必須使用 ACR 登入伺服器的完整名稱來標記映像，才能將映像推送至您的登錄。 請執行下列命令，取得 ACR 執行個體的完整登入伺服器名稱。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

使用 [docker tag][docker-tag] 命令來標記映像。 將 `<acrLoginServer>` 取代為 ACR 執行個體的登入伺服器名稱。

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/azure-mesh-helloworld:1.1-alpine
```
### <a name="list-container-images"></a>列出容器映像

下列範例會列出登錄中的存放庫：

```azurecli
az acr repository list --name <acrName> --output table
```

輸出：

```bash
Result
----------------
azure-mesh-helloworld
```

下列範例會列出在 **azure-mesh-helloworld** 存放庫上的標籤。

```azurecli
az acr repository show-tags --name <acrName> --repository azure-mesh-helloworld --output table
```

輸出：

```bash
Result
--------
1.1-alpine
```
上述輸出會確認私人容器登錄中的 `azure-mesh-helloworld:1.1-alpine` 是否存在。

## <a name="retrieve-credentials-for-the-registry"></a>擷取登錄的認證

若要從已建立的登錄中部署容器執行個體，必須在部署期間提供認證。 請使用下列命令啟用您登錄上的管理使用者：

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

請使用下列命令，取得登錄伺服器名稱、使用者名稱和密碼：

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

上述命令所提供的值，在下列命令中會稱為 `<acrLoginServer>`、`<acrUserName>` 和 `<acrPassword>`。


## <a name="deploy-the-template"></a>部署範本

請使用下列命令建立應用程式和相關的資源，並提供上一個步驟中的認證。

範本中的 `registry-password` 參數是 `securestring`。 將不會顯示在部署狀態和 `az mesh service show` 命令中。 請確定已在下列命令中正確指定它。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}" 
```

在幾分鐘內，您的命令應該會傳回：

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>開啟應用程式
在應用程式成功部署後，請取得服務端點的公用 IP 位址，並在瀏覽器中將其開啟。 會顯示含有 Service Fabric Mesh 標誌的網頁。

部署命令會傳回服務端點的公用 IP 位址。 (選擇性) 您也可以查詢網路資源，來尋找服務端點的公用 IP 位址。 
 
此應用程式的網路資源名稱是 `helloWorldPrivateRegistryNetwork`，請使用下列命令擷取其相關資訊。 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>刪除資源

為了節省指派給預覽程式的有限資源，請經常刪除資源。 若要刪除此範例的相關資源，請將已在其中部署的資源群組刪除。

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>後續步驟
- 檢視 [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) 上的 Hello World 應用程式範例。
- 若要深入了解 Service Fabric 資源模型，請參閱 [Service Fabric Mesh 資源模型](service-fabric-mesh-service-fabric-resources.md)。
- 若要深入了解 Service Fabric Mesh，請閱讀 [Service Fabric Mesh 概觀](service-fabric-mesh-overview.md)。

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/