---
title: 教學課程 - 在 Azure Front Door Service 的網域上設定地區篩選 | Microsoft Docs
description: 在本教學課程中，您會了解如何建立簡單的地區篩選原則，並將此原則與現有的 Front Door 前端主機產生關聯
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2018
ms.author: sharadag
ms.openlocfilehash: 68da9a0255cde6cbad5c675901c80193888bf255
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214873"
---
# <a name="how-to-set-up-a-geo-filtering-policy-for-your-front-door"></a>如何為 Front Door 設定地區篩選原則
本教學課程會說明如何使用 Azure PowerShell 來建立簡單的地區篩選原則，並將此原則與現有的 Front Door 前端主機產生關聯。 此地區篩選原則範例會封鎖來自美國以外所有其他國家/地區的要求。

## <a name="1-set-up-your-powershell-environment"></a>1.設定 PowerShell 環境
Azure PowerShell 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 模型來管理 Azure 資源。 

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)，並將其用於任何 PowerShell 工作階段。 請遵循頁面上的指示，使用 Azure 認證進行登入，並安裝 AzureRM。
```
# Connect to Azure with an interactive dialog for sign-in
Connect-AzureRmAccount
Install-Module -Name AzureRM
```
> [!NOTE]
>  [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 支援即將推出。

在安裝 Front Door 模組之前，請確定您已安裝最新版的 PowerShellGet。 執行下列命令，然後重新開啟 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

安裝 AzureRM.FrontDoor 模組。 

```
Install-Module -Name AzureRM.FrontDoor -AllowPrerelease
```

## <a name="2-define-geo-filtering-match-conditions"></a>2.定義地區篩選比對條件
先建立比對條件範例來選取並非來自「美國」的要求。 在建立比對條件時，請參閱 PowerShell 關於參數的[指南](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoormatchconditionobject)。 [此處](front-door-geo-filtering.md)會提供兩個字母的國家/地區代碼對國家/地區對應。

```
$nonUSGeoMatchCondition = New-AzureRmFrontDoorMatchConditionObject -MatchVariable RemoteAddr -OperatorProperty GeoMatch -NegateCondition $true -MatchValue "US"
```
 
## <a name="3-add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>3.使用動作和優先順序在規則中新增地區篩選比對條件

接著，根據比對條件、動作和優先順序來建立 CustomRule 物件 `nonUSBlockRule`。  CustomRule 可以有多個 MatchCondition。  在此範例中，動作會設定為區塊，優先順序會設定為 1 (此為最高優先順序)。

```
$nonUSBlockRule = New-AzureRmFrontDoorCustomRuleObject -Name "geoFilterRule" -RuleType MatchRule -MatchCondition $nonUSGeoMatchCondition -Action Block -Priority 1
```

在建立 CustomRuleObject 時，請參閱 PowerShell 關於參數的[指南](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorcustomruleobject)。

## <a name="4-add-rules-to-a-policy"></a>4.在原則中新增規則
這個步驟會在指定資源群組中建立 `geoPolicy` 原則物件，其包含上一個步驟的 `nonUSBlockRule`。 使用 `Get-AzureRmResourceGroup` 來尋找 ResourceGroupName $resourceGroup。

```
$geoPolicy = New-AzureRmFrontDoorFireWallPolicy -Name "geoPolicyAllowUSOnly" -resourceGroupName $resourceGroup -Customrule $nonUSBlockRule  -Mode Prevention -EnabledState Enabled
```

在建立原則時，請參閱 PowerShell 關於參數的[指南](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorfirewallpolicy)。

## <a name="5-link-policy-to-a-front-door-frontend-host"></a>5.將原則連結至 Front Door 前端主機
最後幾個步驟是將保護原則物件連結至現有的 Front Door 前端主機，並更新 Front Door 屬性。 首先，藉由使用 [Get-AzureRmFrontDoor](https://docs.microsoft.com/azure/frontdoor/get-azurermfrontdoor) 並將 Front Door 物件的前端 WebApplicationFirewallPolicyLink 屬性設定為 `geoPolicy` 的 resourceId，來擷取 Front Door 物件。

```
$geoFrontDoorObjectExample = Get-AzureRmFrontDoor -ResourceGroupName $resourceGroup
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

使用下列[命令](https://docs.microsoft.com/azure/frontdoor/set-azurermfrontdoor)來更新 Front Door 物件。

```
Set-AzureRmFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> 只需要對 WebApplicationFirewallPolicyLink 屬性設定一次，即可將屬性原則連結至 Front Door 前端主機。 後續的原則更新會自動套用至前端主機。

## <a name="next-steps"></a>後續步驟

- 了解 [Front Door 的應用程式層安全性](front-door-application-security.md)。
- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
