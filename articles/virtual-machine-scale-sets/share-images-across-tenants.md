---
title: 在 Azure 中的租用戶之間共用資源庫映像 |Microsoft Docs
description: 了解如何使用共用映像資源庫的 Azure 租用戶之間共用的 VM 映像。
services: virtual-machine-scale-sets
author: cynthn
manager: jeconnoc
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: cbaaac629fd013602eed75cc7dc357f13a62e3b1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160121"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>在 Azure 租用戶之間共用資源庫的 VM 映像

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>使用 Azure CLI 建立擴展集

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

建立擴展集。 使用您自己取代範例中的資訊。

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>後續步驟

若遇到任何問題，您可以[針對共用映像資源庫問題進行疑難排解](troubleshooting-shared-images.md)。