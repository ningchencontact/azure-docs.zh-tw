---
title: 在 Azure 中的租用戶之間共用資源庫映像 |Microsoft Docs
description: 了解如何使用共用映像資源庫的 Azure 租用戶之間共用的 VM 映像。
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: c26abe948fa415c780d543c615c34af2091cfbc7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709167"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>在 Azure 租用戶之間共用資源庫的 VM 映像

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> 若要從另一個 azure 租用戶中的映像部署 VM，您無法使用入口網站。 若要從租用戶之間共用映像建立 VM，您必須使用[Azure CLI](../linux/share-images-across-tenants.md)或 Powershell。

## <a name="create-a-vm-using-powershell"></a>使用 PowerShell 建立 VM


登入使用應用程式識別碼，這兩個租用戶祕密和租用戶識別碼。 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

在具有權限的應用程式註冊的資源群組中建立 VM。 使用您自己取代此範例中的資訊。

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $image `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

## <a name="next-steps"></a>後續步驟

您也可以建立共用的映像使用的資源庫資源[Azure 入口網站](shared-images-portal.md)。