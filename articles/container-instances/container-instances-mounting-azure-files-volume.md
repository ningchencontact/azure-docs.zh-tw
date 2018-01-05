---
title: "在 Azure 容器執行個體中掛接 Azure 檔案磁碟區"
description: "了解如何掛接 Azure 檔案磁碟區來保存 Azure 容器執行個體的狀態"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 06a6e91725e751fbea97d9a3b60f48fa50121fc4
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>使用 Azure 容器執行個體來掛接 Azure 檔案共用

根據預設，Azure 容器執行個體都是無狀態的。 如果容器損毀或停止，其所有狀態都會遺失。 若要在容器超過存留期後保存其狀態，您必須從外部存放區掛接磁碟區。 本文說明如何掛接 Azure 檔案共用以便與 Azure 容器執行個體搭配使用。

> [!NOTE]
> 裝載 Azure 檔案共用僅限於目前 Linux 容器。 雖然我們正在將 Windows 容器中的所有功能，您可以找到目前平台差異[配額和 Azure 容器執行個體的區域可用性](container-instances-quotas.md)。

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用

在搭配使用 Azure 檔案共用與 Azure 容器執行個體前，您必須先建立 Azure 檔案共用。 請執行下列指令碼來建立儲存體帳戶，以裝載檔案共用和共用本身。 儲存體帳戶名稱必須為全域獨有，指令碼才能為基礎字串加上隨機值。

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $ACI_PERS_RESOURCE_GROUP --name $ACI_PERS_STORAGE_ACCOUNT_NAME --output tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="get-storage-credentials"></a>取得儲存體認證

若要在 Azure 容器執行個體中掛接 Azure 檔案共用來作為磁碟區，您需要三個值：儲存體帳戶名稱、共用名稱和儲存體存取金鑰。

如果您使用上述指令碼，所建立的儲存體帳戶名稱尾端會加上隨機值。 若要查詢最終字串 (包括隨機部分)，請使用下列命令：

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" --output tsv)
echo $STORAGE_ACCOUNT
```

我們已知道共用名稱 (在上述指令碼中定義為 acishare)，因此只差儲存體帳戶金鑰，此金鑰可以使用下列命令來找到：

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>部署容器及掛接的磁碟區

若要裝載 Azure 檔案共用做為磁碟區容器中，指定 共用與磁碟區掛接點時建立的容器[az 容器建立][az-container-create]。 如果您已遵循上述步驟，您可以使用下列命令來掛接先前建立的共用並建立容器：

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image seanmckenna/aci-hellofiles \
    --ip-address Public \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

## <a name="manage-files-in-mounted-volume"></a>管理已掛接磁碟區中的檔案

一旦啟動容器，您可以使用簡單的 web 應用程式透過部署[seanmckenna/aci-hellofiles] [ aci-hellofiles]映像需要管理您所指定的掛接路徑上的 Azure 檔案共用中的檔案。 取得與 web 應用程式的 IP 位址[az 容器顯示][ az-container-show]命令：

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --output table
```

您可以使用[Azure 入口網站][ portal]或是工具，像是[Microsoft Azure 儲存體總管][ storage-explorer]擷取和檢查要寫入的檔案檔案共用。

## <a name="next-steps"></a>後續步驟

了解 [Azure 容器執行個體與容器 Orchestrator 之間的關聯性](container-instances-orchestrator-relationship.md)。

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/r/seanmckenna/aci-hellofiles/
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show