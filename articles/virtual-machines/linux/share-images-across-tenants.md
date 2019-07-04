---
title: 在 Azure 中的租用戶之間共用資源庫映像 |Microsoft Docs
description: 了解如何使用共用映像資源庫的 Azure 租用戶之間共用的 VM 映像。
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 88596a4bda3c9ebae99403e95b3539f068cad665
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465468"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>在 Azure 租用戶之間共用資源庫的 VM 映像

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> 若要從另一個 azure 租用戶中的映像部署 VM，您無法使用入口網站。 若要從租用戶之間共用映像建立 VM，您必須使用 Azure CLI 或[Powershell](../windows/share-images-across-tenants.md)。

## <a name="create-a-vm-using-azure-cli"></a>使用 Azure CLI 建立 VM

登入租用戶使用 appID、 應用程式金鑰和租用戶 1 的識別碼 1 的服務主體。 您可以使用`az account show --query "tenantId"`取得租用戶識別碼，如有需要。

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
登入租用戶使用 appID、 應用程式金鑰和租用戶 2 的識別碼 2 的服務主體：

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

建立 VM。 使用您自己取代範例中的資訊。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>後續步驟

若遇到任何問題，您可以[針對共用映像資源庫問題進行疑難排解](troubleshooting-shared-images.md)。