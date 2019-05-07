---
title: 建立區塊 blob 儲存體帳戶-Azure 儲存體 |Microsoft Docs
description: 示範如何使用進階效能特性建立的 Azure 區塊 blob 儲存體帳戶。
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9d8fb8f5f470dc47088efb30b7f823a0b8c624c8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141005"
---
# <a name="create-a-block-blob-storage-account"></a>建立區塊 Blob 儲存體帳戶

區塊 blob 儲存體帳戶類型可讓您使用進階效能特性建立區塊 blob。 這種類型的儲存體帳戶最適合用於高交易率的工作負載，或需要非常快速的存取時間。 這篇文章會示範如何使用 Azure 入口網站、 Azure CLI 或 Azure PowerShell 建立區塊 blob 儲存體帳戶。

如需區塊 blob 儲存體帳戶的詳細資訊，請參閱[Azure 儲存體帳戶概觀](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

## <a name="create-account-in-the-azure-portal"></a>在 Azure 入口網站中建立帳戶

若要在 Azure 入口網站中建立區塊 blob 儲存體帳戶，請遵循下列步驟：

1. 在 Azure 入口網站中，選取**所有的服務**>**儲存體**類別 >**儲存體帳戶**。

1. 底下**儲存體帳戶**，選取**新增**。

1. 在 **訂用帳戶**欄位中，選取要在其中建立儲存體帳戶的訂用帳戶。

1. 在 **資源群組**欄位中，選取現有的資源群組或選取**新建**，並輸入新的資源群組的名稱。

1. 在 **儲存體帳戶名稱**欄位中，輸入帳戶的名稱。 請注意下列指導方針：

   - 跨 Azure，名稱必須是唯一的。
   - 名稱必須介於 3 到 24 個字元。
   - 名稱只能包含數字與小寫字母。

1. 在 **位置**欄位，選取儲存體帳戶位置，或使用預設的位置。

1. 設定其餘部分，設定下列各項：

   |欄位     |Value  |
   |---------|---------|
   |**效能**    |  選取  **Premium**。   |
   |**帳戶類型**    | 選取  **BlockBlobStorage**。      |
   |**複寫**    |  保留預設值**本地備援儲存體 (LRS)**。      |

   ![顯示入口網站 UI 來建立區塊 blob 儲存體帳戶](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. 選取 **檢閱 + 建立**來檢閱儲存體帳戶設定。

1. 選取 [建立] 。

## <a name="create-account-using-azure-powershell"></a>使用 Azure PowerShell 建立帳戶

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 開啟提升權限的 Windows PowerShell 工作階段 （以系統管理員身分執行）。

1. 執行下列命令，以確定最新版的`Az`安裝 PowerShell 模組。

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. 開啟新的 PowerShell 主控台並使用您的 Azure 帳戶登入。

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. 如有需要建立新的資源群組。 將引號中的值，並執行下列命令。

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. 建立區塊 blob 儲存體帳戶。 將引號中的值，並執行下列命令。

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>使用 Azure CLI 建立帳戶

若要使用 Azure CLI 建立區塊 blob 帳戶，您必須先安裝 Azure CLI v。 2.0.46 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

1. 登入您的 Azure 訂用帳戶。

   ```azurecli
   az login
   ```

1. 如有需要建立新的資源群組。 將方括號 （包含括弧） 中的值，並執行下列命令。

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. 建立區塊 blob 儲存體帳戶。 將方括號 （包含括弧） 中的值，並執行下列命令。

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>後續步驟

- 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

- 如需有關資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
