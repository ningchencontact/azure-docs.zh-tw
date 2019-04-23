---
title: 設定 web 應用程式防火牆的自訂回應在 Azure 大門
description: 了解如何設定自訂的回應碼和訊息，當 web 應用程式防火牆 (WAF) 會封鎖要求。
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 2d16893420f27caf4f8b00dc32069e3296d7c236
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795741"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>設定 Azure web 應用程式防火牆的自訂回應

根據預設，當 Azure web 應用程式防火牆 (WAF) 與 Azure 前端會封鎖要求比對的規則，因為它會傳回 403 狀態碼**要求會被封鎖**訊息。 本文說明如何設定自訂的回應狀態碼和回應訊息時，WAF 會封鎖要求。

## <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境
Azure PowerShell 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 模型來管理 Azure 資源。 

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)，並將其用於任何 PowerShell 工作階段。 請遵循頁面上的指示，使用 Azure 認證進行登入，並安裝 Az PowerShell 模組。

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用互動式登入對話方塊連線至 Azure
```
Connect-AzAccount
Install-Module -Name Az
```
請確定您已安裝目前的 PowerShellGet 版本。 執行下列命令，然後重新開啟 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>安裝 Az.FrontDoor 模組 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>建立資源群組

在 Azure 中，您可以將相關資源配置到資源群組。 您可以在此範例中，建立資源群組來使用[新增 AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>使用自訂的回應建立新的 WAF 原則 

以下是使用自訂的回應狀態碼設為 405 和訊息以建立新的 WAF 原則範例**您遭到封鎖。** 使用[新增 AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy)。

```azurepowershell
# WAF policy setting
New-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

修改自訂的回應碼或回應主體設定的現有 WAF 原則，使用[組 AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor)。

```azurepowershell
# modify WAF response code
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>後續步驟
- 深入了解[大門](front-door-overview.md)