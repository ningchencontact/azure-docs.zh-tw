---
title: 建立區塊 blob 儲存體帳戶-Azure 儲存體 |Microsoft Docs
description: 說明如何建立具有 premium 效能特性的 Azure BlockBlobStorage 帳戶。
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1df1d5180d951e7a720ec82c548438892a47a426
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881854"
---
# <a name="create-a-blockblobstorage-account"></a>建立 BlockBlobStorage 帳戶

BlockBlobStorage 帳戶種類可讓您建立具有 premium 效能特性的區塊 blob。 這種類型的儲存體帳戶已針對具有高交易率或需要極快速存取時間的工作負載優化。 本文說明如何使用 [Azure 入口網站]、[Azure CLI] 或 [Azure PowerShell] 來建立 BlockBlobStorage 帳戶。

如需 BlockBlobStorage 帳戶的詳細資訊，請參閱[Azure 儲存體帳戶總覽](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

## <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)
若要在 Azure 入口網站中建立 BlockBlobStorage 帳戶，請遵循下列步驟：

1. 在 [Azure 入口網站中，選取 [**所有服務**] >**儲存體**類別目錄 >**儲存體帳戶**]。

1. 在 [**儲存體帳戶**] 底下，選取 [**新增**]。

1. 在 [**訂閱**] 欄位中，選取要在其中建立儲存體帳戶的訂用帳戶。

1. 在 [**資源群組**] 欄位中，選取現有的資源群組 **，或**選取 [新建]，然後輸入新資源群組的名稱。

1. 在 [**儲存體帳戶名稱**] 欄位中，輸入帳戶的名稱。 請注意下列指導方針：

   - 此名稱在整個 Azure 中必須是唯一的。
   - 名稱的長度必須介於3到24個字元之間。
   - 名稱只能包含數位和小寫字母。

1. 在 [**位置**] 欄位中，選取儲存體帳戶的位置，或使用預設位置。

1. 針對其餘的設定，請設定下列各項：

   |欄位     |Value  |
   |---------|---------|
   |**效能**    |  選取 [ **Premium**]。   |
   |**帳戶類型**    | 選取 [ **BlockBlobStorage**]。      |
   |**複寫**    |  保留 [**本機-多餘儲存體（LRS）** ] 的預設設定。      |

   ![顯示用來建立區塊 blob 儲存體帳戶的入口網站 UI](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. 選取 [**審查 + 建立**] 以查看儲存體帳戶設定。

1. 選取 [建立]。

## <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 開啟提升許可權的 Windows PowerShell 會話（以系統管理員身分執行）。

1. 執行下列命令，確定已安裝最新版本的 `Az` PowerShell 模組。

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. 開啟新的 PowerShell 主控台，並使用您的 Azure 帳戶登入。

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. 如有需要，請建立新的資源群組。 取代引號中的值，然後執行下列命令。

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. 建立 BlockBlobStorage 帳戶。 取代引號中的值，然後執行下列命令。

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 建立區塊 blob 帳戶，您必須先安裝 Azure CLI v。 2.0.46 版或更新版本。 執行 `az --version` 找出版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

1. 登入您的 Azure 訂用帳戶。

   ```azurecli
   az login
   ```

1. 如有需要，請建立新的資源群組。 以方括弧（包括括弧）取代值，然後執行下列命令。

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. 建立 BlockBlobStorage 帳戶。 以方括弧（包括括弧）取代值，然後執行下列命令。

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>後續步驟

- 如需儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

- 如需有關資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
