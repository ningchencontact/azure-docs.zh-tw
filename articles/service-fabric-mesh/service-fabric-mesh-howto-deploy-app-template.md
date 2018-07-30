---
title: 使用範本將應用程式部署至 Azure Service Fabric Mesh | Microsoft Docs
description: 了解如何使用 Azure CLI，從範本中將 .NET Core 應用程式部署至 Service Fabric Mesh。
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceputal
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 8d813669f2f44fd64db669e9750e3bc064c7f916
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090322"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>使用範本將 Service Fabric Mesh 應用程式部署至 Service Fabric Mesh
本文說明如何使用範本將 .NET Core 應用程式部署至 Service Fabric Mesh。 當您完成時，會有一個投票應用程式，其 ASP.NET Core Web 前端會將投票結果儲存在叢集中的後端服務。 前端會使用 DNS 來解析後端服務的位址。

## <a name="set-up-service-fabric-mesh-cli"></a>設定 Service Fabric Mesh CLI 
您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成此工作。 請依照下列[指示](service-fabric-mesh-howto-setup-cli.md)安裝 Azure Service Fabric Mesh CLI 擴充功能模組。

## <a name="sign-in-to-azure"></a>登入 Azure
登入 Azure 並設定您的訂用帳戶。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>建立資源群組
建立要部署應用程式的資源群組。 您可以使用現有的資源群組，並略過此步驟。 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>部署應用程式
使用 `deployment create` 命令在資源群組中建立您的應用程式。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

上述命令會使用 [mesh_rp.windows.json 範本](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json)來部署 Windows 應用程式。 如果您想要部署 Linux 應用程式，請使用 [mesh_rp.linux.json 範本](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json)。 Windows 容器映像比 Linux 容器映像大，因此部署時可能需要較多時間。

在幾分鐘內，您的命令應該會傳回以下回應：

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>` 

## <a name="open-the-application"></a>開啟應用程式
在應用程式成功部署後，請取得服務端點的公用 IP 位址，並在瀏覽器將它開啟。 以下網頁隨即顯示。 

![投票應用程式](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

您現在可以將投票選項新增至應用程式，並在其中進行投票或刪除投票選項。

部署命令會傳回服務端點的公用 IP 位址。 (選擇性) 您也可以查詢網路資源，來尋找服務端點的公用 IP 位址。 

此應用程式的網路資源名稱是 `VotingAppNetwork`，請使用下列命令擷取其相關資訊。 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="check-the-application-details"></a>查看應用程式詳細資料
您可以使用 `app show` 命令來查看應用程式的狀態。 已部署的應用程式名稱是 "VotingApp"，因此請擷取其詳細資料。 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

## <a name="list-the-deployed-applications"></a>列出已部署的應用程式
您可以使用「應用程式清單」命令，取得您已部署至訂用帳戶的應用程式清單。 

```azurecli-interactive
az mesh app list -o table
```

## <a name="clean-up-resources"></a>清除資源
當您不再需要此應用程式和其相關資源時，請刪除包含這些資源的資源群組。 

```azurecli-interactive
az group delete --resource-group myResourceGroup  
``` 

## <a name="next-steps"></a>後續步驟
- 在 [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) 上檢視投票應用程式範例。
- 若要深入了解 Service Fabric Mesh，請閱讀 [Service Fabric Mesh 概觀](service-fabric-mesh-overview.md)。


