---
title: 使用每個應用程式的高密度裝載調整大小-Azure App Service |Microsoft Docs
description: Azure App Service 上的高密度裝載
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 824abbdfd1b3980b419e6d6c46814bb0318adf13
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602324"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>使用每個應用程式調整的 Azure App Service 上的高密度裝載

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

使用 App Service 時，您可以藉由調整來調整您的應用程式[App Service 方案](overview-hosting-plans.md)它們所執行。 當有多個應用程式執行於相同 App Service 方案中時，每個向外延展的執行個體都會執行方案中的所有應用程式。

*每個應用程式調整*可以在 App Service 方案層級啟用，以便進行調整的應用程式獨立於裝載它的 App Service 方案。 如此一來，App Service 方案可以調整為 10 個執行個體，但是應用程式可以設定為僅使用五個。

> [!NOTE]
> 個別應用程式調整僅適用於**標準**、**進階**、**進階 V2** 及**獨立** 定價層。
>

應用程式會配置給可用的 App Service 方案，最佳的工作方法用於跨執行個體的平均分配。 雖然不保證的平均分配，平台可確保兩個相同的應用程式執行個體未裝載在相同的 App Service 方案執行個體上。

平台不會依賴度量，以決定背景工作角色配置。 加入或移除 App Service 方案執行個體時，才，則會重新平衡的應用程式。

## <a name="per-app-scaling-using-powershell"></a>使用 PowerShell 進行個別應用程式調整

透過將 ```-PerSiteScaling $true``` 參數傳遞給 ```New-AzAppServicePlan``` Cmdlet，建立一個有「個別應用程式調整」的方案。

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

透過將 `-PerSiteScaling $true` 參數傳遞給 ```Set-AzAppServicePlan``` Cmdlet，對現有 App Service 方案啟用「個別應用程式調整」。

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

在應用程式層級，設定應用程式可以在 App Service 方案中使用的執行個體數目。

在以下範例中，應用程式限制為 2 個執行個體，不論其基礎 App Service 方案的規模相應放大到多少個執行個體。

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` 不同於 `$newapp.MaxNumberOfWorkers`。 個別應用程式調整會使用 `$newapp.SiteConfig.NumberOfWorkers` 來決定應用程式的調整特性。

## <a name="per-app-scaling-using-azure-resource-manager"></a>使用 Azure Resource Manager 進行個別應用程式調整

下列「Azure Resource Manager 範本」會建立：

- 規模相應放大到 10 個執行個體的 App Service 方案
- 已設定為將上限調整成 5 個執行個體的應用程式。

App Service 方案會將 **PerSiteScaling** 屬性設為 true (`"perSiteScaling": true`)。 應用程式會將要使用的「背景工作角色數目」設定為 5 (`"properties": { "numberOfWorkers": "5" }`)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>高密度裝載的建議組態

個別應用程式調整是全域 Azure 區域和 [App Service 環境](environment/app-service-app-service-environment-intro.md)中啟用的功能。 不過，建議使用的策略是使用 App Service 環境，以利用其進階的功能和較大的 App Service 規劃產能。  

請遵循下列步驟來設定應用程式的高密度裝載︰

1. 將 App Service 方案指定為高密度的計劃，並相應放大所需的容量。
1. 在 App Service 方案上將 `PerSiteScaling` 旗標設定為 true。
1. 新應用程式會建立並指派給該 App Service 方案，其中 **numberOfWorkers** 屬性會設定為 **1**。
   - 使用此組態會產生可能的最高密度。
1. 背景工作角色數目可依每個應用程式單獨設定，以視需要授與額外資源。 例如：
   - 高用量應用程式可以將 **numberOfWorkers** 設定為 **3**，讓該應用程式具有更多的處理容量。
   - 低用量應用程式會將 **numberOfWorkers** 設定為 **1**。

## <a name="next-steps"></a>後續步驟

- [Azure App Service 方案深入概觀](overview-hosting-plans.md)
- [App Service 環境簡介](environment/app-service-app-service-environment-intro.md)
