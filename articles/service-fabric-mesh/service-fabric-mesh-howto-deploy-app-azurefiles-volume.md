---
title: 在 Service Fabric Mesh 應用程式的容器中，裝載 Azure 檔案服務形式的磁碟區來儲存狀態 | Microsoft Docs
description: 了解如何在 Service Fabric Mesh 應用程式的容器中，使用 Azure CLI 裝載 Azure 檔案服務形式的磁碟區來儲存狀態。
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 94a4e17e6285893520a2f6482b32a69b1229e2fa
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090627"
---
# <a name="store-state-by-mounting-azure-files-based-volume-in-service-fabric-mesh-application"></a>在 Service Fabric Mesh 應用程式中裝載 Azure 檔案服務形式的磁碟區來儲存狀態

本文會說明如何在 Service Fabric Mesh 應用程式的容器中裝載磁碟區，以儲存 Azure 檔案服務中的狀態。 在此範例中，計數器應用程式會透過 ASP.NET Core 服務建立網頁，如此可在瀏覽器中顯示計數器的值。 

`counterService` 會定期從檔案讀取計數器的值、增加該值並將其寫回檔案。 儲存檔案的資料夾會裝載在由 Azure 檔案服務共用支援的磁碟區中。 

## <a name="set-up-service-fabric-mesh-cli"></a>設定 Service Fabric Mesh CLI 
您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成此工作。 請依照下列[指示](service-fabric-mesh-howto-setup-cli.md)安裝 Azure Service Fabric Mesh CLI 擴充功能模組。

## <a name="sign-in-to-azure"></a>登入 Azure
登入 Azure 並設定您的訂用帳戶。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-file-share"></a>建立檔案共用 
依照這些[指示](/azure/storage/files/storage-how-to-create-file-share)來建立 Azure 檔案共用。 在下列指示中，儲存體帳戶名稱、儲存體帳戶金鑰和檔案共用名稱會以 `<storageAccountName>`、`<storageAccountKey>` 和 `<fileShareName>` 來表示。

## <a name="create-resource-group"></a>建立資源群組
建立要部署應用程式的資源群組。 您可以使用現有的資源群組，並略過此步驟。 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>部署範本

請使用下列命令建立應用程式和相關的資源，並針對上一個步驟中的 `storageAccountName`、`storageAccountKey` 和 `fileShareName` 提供值。

範本中的 `storageAccountKey` 參數是 `securestring`。 這不會顯示在部署狀態和 `az mesh service show` 命令中。 請確定已在下列命令中正確指定此參數。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

上述命令會使用 [mesh_rp.linux.json 範本](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json)來部署 Linux 應用程式。 如果您想要部署 Windows 應用程式，請使用 [mesh_rp.windows.json 範本](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json)。 Windows 容器映像比 Linux 容器映像大，因此部署時可能需要較多時間。

在幾分鐘內，您的命令應該會傳回以下回應：

`counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>開啟應用程式
在應用程式成功部署後，請取得服務端點的公用 IP 位址，並在瀏覽器將它開啟。 網頁會隨即出現，其中包含每秒更新的計數器值。

部署命令會傳回服務端點的公用 IP 位址。 (選擇性) 您也可以查詢網路資源，來尋找服務端點的公用 IP 位址。 
 
此應用程式的網路資源名稱是 `counterAppNetwork`，請使用下列命令擷取其相關資訊。 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>確認應用程式都可以使用磁碟區
應用程式會在 `counter/counterService` 資料夾的檔案共用內建立名為 `counter.txt` 的檔案。 此檔案的內容就是正在網頁上顯示計的數器值。

您可以下載此檔案，但用來下載的工具須能瀏覽 Azure 檔案服務的檔案共用。 例如，[Microsoft Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)就是屬於這類工具。

## <a name="delete-the-resources"></a>刪除資源

為了節省指派給預覽計畫的有限資源，請經常刪除資源。 若要刪除此範例的相關資源，請將已在其中部署的資源群組刪除。

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>後續步驟

- 在 [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) 上檢視 Azure 檔案服務磁碟區的範例應用程式。
- 若要深入了解 Service Fabric 資源模型，請參閱 [Service Fabric Mesh 資源模型](service-fabric-mesh-service-fabric-resources.md)。
- 若要深入了解 Service Fabric Mesh，請閱讀 [Service Fabric Mesh 概觀](service-fabric-mesh-overview.md)。
