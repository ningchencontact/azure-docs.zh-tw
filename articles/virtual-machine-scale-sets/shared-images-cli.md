---
title: 在 Azure 中使用共用 VM 映像來建立擴展集 | Microsoft Docs
description: 了解如何使用 Azure CLI 來建立共用 VM 映像，以用來在 Azure 中部署虛擬機器擴展集。
services: virtual-machine-scale-sets
documentationcenter: ''
author: axayjo
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2018
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 2aa1f71d0e8e73ae793fc5396b8b654b9ca875bc
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733530"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>使用 Azure CLI 2.0 為虛擬機器擴展集建立及使用共用映像

當您建立擴展集時，您會指定部署 VM 執行個體時所要使用的映像。 [共用映像資源庫](shared-image-galleries.md)可大幅簡化跨組織共用自訂映像。 自訂映像類似 Marketplace 映像，但您要自行建立它們。 自訂映像可用於啟動程序設定，例如，預先載入應用程式、應用程式設定和其他 OS 設定。 共用映像資源庫可讓您與 AAD 租用戶中區域內或跨區域組織中的其他人共用自訂 VM 映像。 選擇您要共用的映像、您要開放使用的區域，以及您要共用的對象。 您可以建立多個資源庫，讓您可以根據邏輯群組共用映像。 資源庫是一種頂層資源，可提供完整的角色型存取控制 (RBAC)。 可建立映像版本，並且您可以選擇將每個映像版本複寫到不同的 Azure 區域集合。 資源庫僅適用於受控映像。 在本文中，您將了解如何：

> [!div class="checklist"]
> * 建立共用映像資源庫
> * 建立共用映像定義
> * 建立共用映像版本
> * 從共用映像建立 VM

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。


>[!NOTE]
> 此文章逐步說明使用一般化受控映像的程序。 不支援從特製化 VM 映像建立擴展集。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝並使用 CLI，此文章會要求您執行 Azure CLI 2.0.46 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>從自訂 VM 映像建立擴展集
使用 [az vmss create](/cli/azure/vmss#az-vmss-create) 建立擴展集。 請勿使用 UbuntuLTS 或 CentOS 這類平台映像，而是指定自訂 VM 映像的名稱。 下列範例會建立名為 myScaleSet 的擴展集，其使用前一步驟中名為 myImage 的自訂映像：

```azurecli-interactive
az vmss create \
  -g myGalleryRG \
  -n myScaleSet \
  --image "/subscriptions/<subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
  --admin-username azureuser \
  --generate-ssh-keys
```

建立及設定所有擴展集資源和 VM 需要幾分鐘的時間。


[!INCLUDE [virtual-machines-common-gallery-list-cli](../../includes/virtual-machines-common-gallery-list-cli.md)]


## <a name="clean-up-resources"></a>清除資源
若要移除您的擴展集與其他資源，請使用 [az group delete](/cli/azure/group) 刪除資源群組及其所有資源。 `--no-wait` 參數不會等待作業完成，就會將控制項傳回給提示字元。 `--yes` 參數會確認您想要刪除資源，而不另外對您提示將要進行此作業。

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>後續步驟

您也可以使用範本建立共用映像庫資源。 有數個 Azure 快速入門範本可以使用： 

- [建立共用映像資源庫](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共用映像資源庫中建立映像定義](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共用映像資源庫中建立映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [從映像版本建立 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


若遇到任何問題，您可以[針對共用映像資源庫問題進行疑難排解](troubleshooting-shared-images.md)。
