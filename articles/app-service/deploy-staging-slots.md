---
title: 針對 Azure App Service 中的 Web 應用程式設定預備環境 | Microsoft Docs
description: 了解如何針對 Azure App Service 中的 Web 應用程式使用預備發行。
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/19/2019
ms.author: cephalin
ms.custom: fasttrack-edit
ms.openlocfilehash: 7f98ba9851216737712b6be1ec29156ba0b1a68b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382274"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>在 Azure App Service 中設定預備環境
<a name="Overview"></a>

When you deploy your web app, web app on Linux, mobile back end, or API app to [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), you can use a separate deployment slot instead of the default production slot when you're running in the **Standard**, **Premium**, or **Isolated** App Service plan tier. Deployment slots are live apps with their own host names. 兩個部署位置 (包括生產位置) 之間的應用程式內容與設定項目可以互相交換。 

將應用程式部署至非生產位置具有下列優點：

* 您可以先驗證預備部署位置中的應用程式變更，再將它與生產位置進行交換。
* 先將應用程式部署至某個位置，然後再將它交換到生產位置，可確保該位置的所有執行個體在交換到生產位置之前都已準備就緒。 這麼做可以排除部署應用程式時的停機情況。 流量能夠順暢地重新導向，且不會因為交換作業而捨棄任何要求。 You can automate this entire workflow by configuring [auto swap](#Auto-Swap) when pre-swap validation isn't needed.
* 交換之後，先前具有預備應用程式的位置，現在已經有之前的生產應用程式。 若交換到生產位置的變更不是您需要的變更，您可以立即執行相同的交換，以恢復「上一個已知的良好網站」。

每個 App Service 方案層所支援的部署位置個數都不一樣。 There's no additional charge for using deployment slots. To find out the number of slots your app's tier supports, see [App Service limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). 

To scale your app to a different tier, make sure that the target tier supports the number of slots your app already uses. For example, if your app has more than five slots, you can't scale it down to the **Standard** tier, because the **Standard** tier supports only five deployment slots. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Add a slot
應用程式必須在 [標準]、[進階] 或 [隔離] 層中執行，您才能啟用多個部署位置。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟應用程式的[資源頁面](../azure-resource-manager/manage-resources-portal.md#manage-resources)。

2. In the left pane, select **Deployment slots** > **Add Slot**.
   
    ![新增部署位置](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > If the app isn't already in the **Standard**, **Premium**, or **Isolated** tier, you receive a message that indicates the supported tiers for enabling staged publishing. At this point, you have the option to select **Upgrade** and go to the **Scale** tab of your app before continuing.
   > 

3. In the **Add a slot** dialog box, give the slot a name, and select whether to clone an app configuration from another deployment slot. Select **Add** to continue.
   
    ![Configuration source](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    You can clone a configuration from any existing slot. 可複製的設定包括應用程式設定、連接字串、語言架構版本、Web 通訊端、HTTP 版本和平台位元。

4. After the slot is added, select **Close** to close the dialog box. The new slot is now shown on the **Deployment slots** page. By default, **Traffic %** is set to 0 for the new slot, with all customer traffic routed to the production slot.

5. Select the new deployment slot to open that slot's resource page.
   
    ![Deployment slot title](./media/web-sites-staged-publishing/StagingTitle.png)

    預備位置和任何其他 App Service 應用程式一樣，具有管理頁面。 您可以變更位置的組態。 頁面頂端會顯示位置的名稱，提醒您正在檢視部署位置。

6. Select the app URL on the slot's resource page. The deployment slot has its own host name and is also a live app. To limit public access to the deployment slot, see [Azure App Service IP restrictions](app-service-ip-restrictions.md).

新的部署位置中沒有任何內容，即使您複製其他位置的設定，仍是如此。 For example, you can [publish to this slot with Git](app-service-deploy-local-git.md). 您可以從不同的存放庫分支或不同的存放庫部署至位置。 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>What happens during a swap

### <a name="swap-operation-steps"></a>Swap operation steps

When you swap two slots (usually from a staging slot into the production slot), App Service does the following to ensure that the target slot doesn't experience downtime:

1. Apply the following settings from the target slot (for example, the production slot) to all instances of the source slot: 
    - [Slot-specific](#which-settings-are-swapped) app settings and connection strings, if applicable.
    - [Continuous deployment](deploy-continuous-deployment.md) settings, if enabled.
    - [App Service authentication](overview-authentication-authorization.md) settings, if enabled.
    
    Any of these cases trigger all instances in the source slot to restart. During [swap with preview](#Multi-Phase), this marks the end of the first phase. The swap operation is paused, and you can validate that the source slot works correctly with the target slot's settings.

1. Wait for every instance in the source slot to complete its restart. If any instance fails to restart, the swap operation reverts all changes to the source slot and stops the operation.

1. If [local cache](overview-local-cache.md) is enabled, trigger local cache initialization by making an HTTP request to the application root ("/") on each instance of the source slot. Wait until each instance returns any HTTP response. Local cache initialization causes another restart on each instance.

1. If [auto swap](#Auto-Swap) is enabled with [custom warm-up](#Warm-up), trigger [Application Initiation](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) by making an HTTP request to the application root ("/") on each instance of the source slot.

    If `applicationInitialization` isn't specified, trigger an HTTP request to the application root of the source slot on each instance. 
    
    If an instance returns any HTTP response, it's considered to be warmed up.

1. If all instances on the source slot are warmed up successfully, swap the two slots by switching the routing rules for the two slots. After this step, the target slot (for example, the production slot) has the app that's previously warmed up in the source slot.

1. Now that the source slot has the pre-swap app previously in the target slot, perform the same operation by applying all settings and restarting the instances.

At any point of the swap operation, all work of initializing the swapped apps happens on the source slot. The target slot remains online while the source slot is being prepared and warmed up, regardless of where the swap succeeds or fails. To swap a staging slot with the production slot, make sure that the production slot is always the target slot. This way, the swap operation doesn't affect your production app.

### <a name="which-settings-are-swapped"></a>哪些設定已交換？

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

To configure an app setting or connection string to stick to a specific slot (not swapped), go to the **Configuration** page for that slot. Add or edit a setting, and then select **deployment slot setting**. Selecting this check box tells App Service that the setting is not swappable. 

![位置設定](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>交換兩個位置 
You can swap deployment slots on your app's **Deployment slots** page and the **Overview** page. For technical details on the slot swap, see [What happens during swap](#AboutConfiguration).

> [!IMPORTANT]
> Before you swap an app from a deployment slot into production, make sure that production is your target slot and that all settings in the source slot are configured exactly as you want to have them in production.
> 
> 

To swap deployment slots:

1. Go to your app's **Deployment slots** page and select **Swap**.
   
    ![Swap button](./media/web-sites-staged-publishing/SwapButtonBar.png)

    The **Swap** dialog box shows settings in the selected source and target slots that will be changed.

2. 選取所需的 [來源] 和 [目標] 位置。 目標通常是生產位置。 Also, select the **Source Changes** and **Target Changes** tabs and verify that the configuration changes are expected. When you're finished, you can swap the slots immediately by selecting **Swap**.

    ![完整的交換](./media/web-sites-staged-publishing/SwapImmediately.png)

    To see how your target slot would run with the new settings before the swap actually happens, don't select **Swap**, but follow the instructions in [Swap with preview](#Multi-Phase).

3. When you're finished, close the dialog box by selecting **Close**.

If you have any problems, see [Troubleshoot swaps](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>使用預覽交換 (多階段交換)

Before you swap into production as the target slot, validate that the app runs with the swapped settings. The source slot is also warmed up before the swap completion, which is desirable for mission-critical applications.

When you perform a swap with preview, App Service performs the same [swap operation](#AboutConfiguration) but pauses after the first step. You can then verify the result on the staging slot before completing the swap. 

If you cancel the swap, App Service reapplies configuration elements to the source slot.

To swap with preview:

1. Follow the steps in [Swap deployment slots](#Swap) but select **Perform swap with preview**.

    ![使用預覽交換](./media/web-sites-staged-publishing/SwapWithPreview.png)

    The dialog box shows you how the configuration in the source slot changes in phase 1, and how the source and target slot change in phase 2.

2. When you're ready to start the swap, select **Start Swap**.

    When phase 1 finishes, you're notified in the dialog box. Preview the swap in the source slot by going to `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. When you're ready to complete the pending swap, select **Complete Swap** in **Swap action** and select **Complete Swap**.

    To cancel a pending swap, select **Cancel Swap** instead.

4. When you're finished, close the dialog box by selecting **Close**.

If you have any problems, see [Troubleshoot swaps](#troubleshoot-swaps).

若要自動執行多階段交換，請參閱[使用 PowerShell 自動執行](#automate-with-powershell)。

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Roll back a swap
在交換位置後，若目標位置 (例如生產位置) 發生任何錯誤，請立即交換相同的兩個位置，將位置還原成交換前的狀態。

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configure auto swap

> [!NOTE]
> Auto swap isn't supported in web apps on Linux.

Auto swap streamlines Azure DevOps scenarios where you want to deploy your app continuously with zero cold starts and zero downtime for customers of the app. When auto swap is enabled from a slot into production, every time you push your code changes to that slot, App Service automatically [swaps the app into production](#swap-operation-steps) after it's warmed up in the source slot.

   > [!NOTE]
   > Before you configure auto swap for the production slot, consider testing auto swap on a non-production target slot.
   > 

To configure auto swap:

1. Go to your app's resource page. Select **Deployment slots** >  *\<desired source slot>*  > **Configuration** > **General settings**.
   
2. For **Auto swap enabled**, select **On**. Then select the desired target slot for **Auto swap deployment slot**, and select **Save** on the command bar. 
   
    ![Selections for configuring auto swap](./media/web-sites-staged-publishing/AutoSwap02.png)

3. 執行推送至來源位置的程式碼。 Auto swap happens after a short time, and the update is reflected at your target slot's URL.

If you have any problems, see [Troubleshoot swaps](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Specify custom warm-up

Some apps might require custom warm-up actions before the swap. The `applicationInitialization` configuration element in web.config lets you specify custom initialization actions. The [swap operation](#AboutConfiguration) waits for this custom warm-up to finish before swapping with the target slot. Here's a sample web.config fragment.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

For more information on customizing the `applicationInitialization` element, see [Most common deployment slot swap failures and how to fix them](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

You can also customize the warm-up behavior with one or both of the following [app settings](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: The path to ping to warm up your site. 請指定開頭為斜線的自訂路徑作為值，以新增此應用程式設定。 例如 `/statuscheck`。 預設值為 `/`。 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Valid HTTP response codes for the warm-up operation. 請以 HTTP 代碼的逗號分隔清單方式新增此應用程式設定。 An example is `200,202` . If the returned status code isn't in the list, the warmup and swap operations are stopped. 預設是所有回應碼都有效。

> [!NOTE]
> The `<applicationInitialization>` configuration element is part of each app start-up, whereas the two warm-up behavior app settings apply only to slot swaps.

If you have any problems, see [Troubleshoot swaps](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitor a swap

If the [swap operation](#AboutConfiguration) takes a long time to complete, you can get information on the swap operation in the [activity log](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

On your app's resource page in the portal, in the left pane, select **Activity log**.

交換作業在記錄查詢中會顯示為 `Swap Web App Slots`。 您可以展開它，然後選取其中一個子作業或錯誤，以查看詳細資料。

## <a name="route-traffic"></a>路由流量

根據預設，對應用程式生產 URL (`http://<app_name>.azurewebsites.net`) 的所有用戶端要求都會路由至生產位置。 您可以將部分流量路由至其他位置。 如果您需要使用者針對新的更新提供意見反應，但尚未準備好要將更新發行至生產環境，這項功能將有其效用。

### <a name="route-production-traffic-automatically"></a>自動路由生產流量

To route production traffic automatically:

1. Go to your app's resource page and select **Deployment slots**.

2. 針對您要路由到的位置，在其 [流量百分比] 資料行中指定百分比 (介於 0 到 100 之間)，以代表您要路由的總流量。 選取 [儲存]。

    ![Setting a traffic percentage](./media/web-sites-staged-publishing/RouteTraffic.png)

After the setting is saved, the specified percentage of clients is randomly routed to the non-production slot. 

After a client is automatically routed to a specific slot, it's "pinned" to that slot for the life of that client session. 用戶端瀏覽器中，您可以查看 HTTP 標頭中的 `x-ms-routing-name` Cookie，以確認您的工作階段固定到哪個位置。 路由至「預備」位置的要求具有 Cookie `x-ms-routing-name=staging`。 路由至生產位置的要求具有 Cookie `x-ms-routing-name=self`。

   > [!NOTE]
   > Next to the Azure Portal, you can also use the [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) command in the Azure CLI to set the routing percentages from CI/CD tools like DevOps pipelines or other automation systems.
   > 

### <a name="route-production-traffic-manually"></a>手動路由生產流量

除了自動流量路由以外，App Service 也可以將要求路由至特定位置。 This is useful when you want your users to be able to opt in to or opt out of your beta app. 若要手動路由生產流量，您可以使用 `x-ms-routing-name` 查詢參數。

To let users opt out of your beta app, for example, you can put this link on your webpage:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

字串 `x-ms-routing-name=self` 會指定生產位置。 After the client browser accesses the link, it's redirected to the production slot. Every subsequent request has the `x-ms-routing-name=self` cookie that pins the session to the production slot.

To let users opt in to your beta app, set the same query parameter to the name of the non-production slot. 以下是範例：

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

By default, new slots are given a routing rule of `0%`, shown in grey. When you explicitly set this value to `0%` (shown in black text), your users can access the staging slot manually by using the `x-ms-routing-name` query parameter. But they won't be routed to the slot automatically because the routing percentage is set to 0. This is an advanced scenario where you can "hide" your staging slot from the public while allowing internal teams to test changes on the slot.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Delete a slot

Go to your app's resource page. Select **Deployment slots** >  *\<slot to delete>*  > **Overview**. Select **Delete** on the command bar.  

![刪除部署位置](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>使用 PowerShell 自動執行

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell 模組提供透過 Windows PowerShell 來管理 Azure 的 Cmdlet，包括支援管理 Azure App Service 中的部署位置。

如需安裝與設定 Azure PowerShell，以及使用您的 Azure 訂用帳戶驗證 Azure PowerShell 的詳細資訊，請參閱 [如何安裝和設定 Microsoft Azure PowerShell](/powershell/azure/overview)(英文)。  

---
### <a name="create-a-web-app"></a>建立 Web 應用程式
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Create a slot
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Initiate a swap with a preview (multi-phase swap), and apply destination slot configuration to the source slot
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Cancel a pending swap (swap with review) and restore the source slot configuration
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>交換部署位置
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitor swap events in the activity log
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Delete a slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-arm-templates"></a>Automate with ARM templates

[ARM Templates](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview) are declarative JSON files used to automate the deployment and configuration of Azure resources. To swap slots using ARM templates, you will set two properties on the *Microsoft.Web/sites/slots* and *Microsoft.Web/sites* resources:

- `buildVersion`: this is a string property which represents the current version of the app deployed in the slot. For example: "v1", "1.0.0.1", or "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: this is a string property that specifies what `buildVersion` the slot should have. If the targetBuildVersion does not equal the current `buildVersion`, then this will trigger the swap operation by finding the slot which has the specified `buildVersion`.

### <a name="example-arm-template"></a>Example ARM Template

The following ARM template will update the `buildVersion` of the staging slot and set the `targetBuildVersion` on the production slot. This will swap the two slots. The template assumes you already have a webapp created with a slot named "staging".

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

This ARM template is idempotent, meaning that it can be executed repeatedly and produce the same state of the slots. After the first execution, `targetBuildVersion` will match the current `buildVersion`, so a swap will not be triggered.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automate with the CLI

如需適用於部署位置的 [Azure CLI](https://github.com/Azure/azure-cli) 命令，請參閱 [az webapp deployment slot](/cli/azure/webapp/deployment/slot)。

## <a name="troubleshoot-swaps"></a>Troubleshoot swaps

If any error occurs during a [slot swap](#AboutConfiguration), it's logged in *D:\home\LogFiles\eventlog.xml*. It's also logged in the application-specific error log.

Here are some common swap errors:

- An HTTP request to the application root is timed. The swap operation waits for 90 seconds for each HTTP request, and retries up to 5 times. If all retries are timed out, the swap operation is stopped.

- Local cache initialization might fail when the app content exceeds the local disk quota specified for the local cache. For more information, see [Local cache overview](overview-local-cache.md).

- During [custom warm-up](#Warm-up), the HTTP requests are made internally (without going through the external URL). They can fail with certain URL rewrite rules in *Web.config*. For example, rules for redirecting domain names or enforcing HTTPS can prevent warm-up requests from reaching the app code. To work around this issue, modify your rewrite rules by adding the following two conditions:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Without a custom warm-up, the URL rewrite rules can still block HTTP requests. To work around this issue, modify your rewrite rules by adding the following condition:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Some [IP restriction rules](app-service-ip-restrictions.md) might prevent the swap operation from sending HTTP requests to your app. IPv4 address ranges that start with `10.` and `100.` are internal to your deployment. You should allow them to connect to your app.

- After slot swaps, the app may experience unexpected restarts. This is because after a swap, the hostname binding configuration goes out of sync, which by itself doesn't cause restarts. However, certain underlying storage events (such as storage volume failovers) may detect these discrepancies and force all worker processes to restart. To minimize these types of restarts, set the [`WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` app setting](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) on *all slots*. However, this app setting does *not* work with Windows Communication Foundation (WCF) apps.

## <a name="next-steps"></a>後續步驟
[封鎖對非生產位置的存取](app-service-ip-restrictions.md)
