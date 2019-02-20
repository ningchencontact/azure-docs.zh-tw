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
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: cephalin
ms.openlocfilehash: 7c12b34f6d735579326d4ccdd95e7831fbb777d6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181417"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>在 Azure App Service 中設定預備環境
<a name="Overview"></a>

> [!NOTE]
> 本操作指南說明如何使用新的預覽管理頁面來管理位置。 習慣使用現有管理頁面的客戶，可以像以前一樣繼續使用現有的位置管理頁面。 
>

當您將 Web 應用程式、Linux 上的 Web 應用程式、行動後端及 API 應用程式部署到 [App Service](https://go.microsoft.com/fwlink/?LinkId=529714) 時，如果是在 [標準]、[進階] 或 [隔離] App Service 方案層中執行，就可以部署到個別的部署位置，而不是預設的生產位置。 部署位置實際上是含有自己主機名稱的作用中應用程式。 兩個部署位置 (包括生產位置) 之間的應用程式內容與設定項目可以互相交換。 將應用程式部署至非生產位置具有下列優點：

* 您可以先驗證預備部署位置中的應用程式變更，再將它與生產位置進行交換。
* 先將應用程式部署至某個位置，然後再將它交換到生產位置，可確保該位置的所有執行個體在交換到生產位置之前都已準備就緒。 這麼做可以排除部署應用程式時的停機情況。 流量能夠順暢地重新導向，且不會因為交換作業而捨棄任何要求。 不需要預先交換驗證時，這整個工作流程可藉由設定[自動交換](#Auto-Swap)來自動化。
* 交換之後，先前具有預備應用程式的位置，現在已經有之前的生產應用程式。 若交換到生產位置的變更不是您需要的變更，您可以立即執行相同的交換，以恢復「上一個已知的良好網站」。

每個 App Service 方案層所支援的部署位置個數都不一樣。 若要找出應用程式層所支援的位置個數，請參閱 [App Service 限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits)。 若要將您的應用程式調整到不同層，目標層必須支援您應用程式已經使用的位置數。 例如，如果您的應用程式有五個以上的位置，您無法將期相應減少為**標準**層，因為**標準**層只支援五部署位置。

<a name="Add"></a>

## <a name="add-slot"></a>新增位置
應用程式必須在 [標準]、[進階] 或 [隔離] 層中執行，您才能啟用多個部署位置。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟應用程式的[資源頁面](../azure-resource-manager/resource-group-portal.md#manage-resources)。

2. 在左側導覽中選擇 [部署位置 (預覽)] 選項，然後按一下 [新增位置]。
   
    ![新增部署位置](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > 如果應用程式尚未處於 [標準]、[進階] 或 [隔離] 層，您將會收到訊息，指出可啟用預備發佈的支援層。 此時，您可以選取 [升級]，並瀏覽至應用程式的 [級別] 索引標籤後再繼續。
   > 

3. 在 [新增位置] 對話方塊中指定位置名稱，然後選取是否要複製其他現有部署位置的應用程式組態。 按一下 [新增] 繼續作業。
   
    ![組態來源](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    您可以從任何現有位置複製組態。 可複製的設定包括應用程式設定、連接字串、語言架構版本、Web 通訊端、HTTP 版本和平台位元。

4. 新增位置之後，請按一下 [關閉] 以關閉對話方塊。 新的位置此時會顯示在 [部署位置 (預覽)] 頁面中。 根據預設，新位置的 [流量百分比] 會設為 0，且所有客戶流量都會路由至生產位置。

5. 按一下新的部署位置，以開啟該位置的資源頁面。
   
    ![Deployment Slot Title](./media/web-sites-staged-publishing/StagingTitle.png)

    預備位置和任何其他 App Service 應用程式一樣，具有管理頁面。 您可以變更位置的組態。 頁面頂端會顯示位置的名稱，提醒您正在檢視部署位置。

6. 在位置的資源頁面中按一下應用程式 URL。 部署位置有自己的主機名稱，同時也是作用中的應用程式。 若要限制對部署位置的公用存取，請參閱 [Azure App Service IP 限制](app-service-ip-restrictions.md)。

新的部署位置中沒有任何內容，即使您複製其他位置的設定，仍是如此。 例如，您可以 [使用 Git 發行至此位置](app-service-deploy-local-git.md)。 您可以從不同的存放庫分支或不同的存放庫部署至位置。 

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>哪些設定已交換？
當您複製其他部署位置的組態時，可以編輯複製的組態。 此外，某些組態元素在交換時會保有內容 (非位置特定)，而其他組態元素則會在交換之後保留於同一個位置中 (位置特定)。 以下清單顯示當您交換位置時會變更的設定。

**交換的設定**：

* 一般設定 - 例如 Framework 版本、32/64 位元、Web 通訊端
* 應用程式設定 (可以設定為停在某一個位置)
* 連接字串 (可以設定為停在某一個位置)
* 處理常式對應
* 監視與診斷設定
* 公開憑證
* WebJobs 內容
* 混合式連線

**無法交換的設定**：

* 發行端點
* 自訂網域名稱
* 私人憑證與 SSL 繫結
* 擴充設定
* WebJobs 排程器

<!-- VNET, IP restrictions, CORS, hybrid connections? -->

若要將應用程式設定或連接字串設定為停留在特定位置 (未交換)，請瀏覽至該位置的 [應用程式設定] 頁面，然後針對應停留在該位置的設定元素，選取 [位置設定] 方塊。 將組態元素標記為「位置特定」，可向 App Service 指出那是無法交換的元素。

![位置設定](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>交換兩個位置 
您可以在應用程式的 [部署位置 (預覽)] 中交換部署位置。 

您也可以在 [概觀] 和 [部署位置] 頁面中交換位置，但目前提供的是舊有的使用體驗。 本指南說明如何在 [部署位置 (預覽)] 頁面中使用新的使用者介面。

> [!IMPORTANT]
> 在您將應用程式從部署位置交換到生產位置之前，請確定所有設定均依照您在交換目標中想要的方式明確地設定。
> 
> 

若要交換部署位置，請遵循下列步驟：

1. 瀏覽至應用程式的 [部署位置 (預覽)] 頁面，然後按一下 [交換]。
   
    ![Swap Button](./media/web-sites-staged-publishing/SwapButtonBar.png)

    [交換] 對話方塊會顯示將會變更的選定來源和目標位置中的設定。

2. 選取所需的 [來源] 和 [目標] 位置。 目標通常是生產位置。 此外，請按一下 [來源變更] 和 [目標變更] 索引標籤，並確認組態變更符合預期。 完成之後，只要按一下 [交換] 即可立即交換位置。

    ![完整的交換](./media/web-sites-staged-publishing/SwapImmediately.png)

    若要在真正執行交換之前查看您的目標位置在使用新設定時將如何執行，請不要選取 [交換]，而依照[使用預覽交換](#Multi-Phase)中的指示操作。

3. 完成之後，請按一下 [關閉] 以關閉對話方塊。

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>使用預覽交換 (多階段交換)

> [!NOTE]
> Linux 上的 Web 應用程式不支援「使用預覽交換」。

交換到生產環境中作為目標位置之前，請驗證應用程式會先以交換後的設定執行，再進行交換。 來源位置也會先進行準備工作再完成交換，這也正是任務關鍵性應用程式所需要的。

當您執行「使用預覽交換」時，App Service 會在您開始交換時進行下列作業︰

- 將目標位置保留不變，使該位置上的現有工作負載 (例如生產) 不受影響。
- 將目標位置的組態元素套用至來源位置，包括位置特定的連接字串和應用程式設定。
- 使用這些組態元素重新啟動來源位置上的背景工作處理序。 您可以瀏覽來源位置，並查看以組態變更執行的應用程式。

如果您在個別步驟中完成交換，則 App Service 會將準備好的來源位置移至目標位置中，並將目標位置移至來源位置。 如果您取消交換，App Service 會將來源位置的組態元素重新套用至來源位置。

若要使用預覽交換，請遵循下列步驟。

1. 依照[交換部署位置](#Swap)中的步驟操作，但選取 [執行使用預覽交換]。

    ![使用預覽交換](./media/web-sites-staged-publishing/SwapWithPreview.png)

    此對話方塊會顯示來源位置中的組態在階段 1 的變更情形，來源和目標位置在階段 2 的變更情形。

2. 準備好要開始交換時，請按一下 [開始交換]。

    階段 1 完成時，您會在對話方塊中收到通知。 瀏覽至 `https://<app_name>-<source-slot-name>.azurewebsites.net` 可預覽來源位置中的交換情形。 

3. 準備好要完成擱置中的交換時，請在 [交換動作] 中選取 [完成交換]，然後按一下 [完成交換]。

    若要取消擱置中的交換，請改為選取 [取消交換]，然後按一下 [取消交換]。

4. 完成之後，請按一下 [關閉] 以關閉對話方塊。

若要自動執行多階段交換，請參閱＜使用 PowerShell 自動執行＞。

<a name="Rollback"></a>

## <a name="roll-back-swap"></a>復原交換
在交換位置後，若目標位置 (例如生產位置) 發生任何錯誤，請立即交換相同的兩個位置，將位置還原成交換前的狀態。

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>設定自動交換

> [!NOTE]
> Linux 上的 Web 應用程式不支援自動交換。

如果您希望為應用程式的客戶持續部署您的應用程式，而不需冷啟動和不需關機，「自動交換」將可簡化此類 DevOps 案例。 在部署位置自動交換至生產位置後，每當您將程式碼變更推送至該位置時，App Service 就會在已於來源位置做好準備後，自動將該應用程式交換至生產位置。

   > [!NOTE]
   > 在設定生產位置的「自動交換」之前，請考慮先在非生產的目標位置上測試「自動交換」。
   > 

若要設定「自動交換」，請遵循下列步驟：

1. 瀏覽至應用程式的資源頁面。 選取 [部署位置 (預覽)] > \<所需的來源位置> > [應用程式設定]。
   
2. 在 [自動交換] 中選取 [開啟]，然後在 [自動交換位置] 中選取所需的目標位置，再按一下命令列中的 [儲存]。 
   
    ![](./media/web-sites-staged-publishing/AutoSwap02.png)

3. 執行推送至來源位置的程式碼。 自動交換不久之後就會發生，而更新會反映於目標位置的 URL。

<a name="Warm-up"></a>

## <a name="custom-warm-up"></a>自訂準備
使用[自動交換](#Auto-Swap)時，某些應用程式在交換之前可能需執行自訂的準備動作。 web.config 中的 `applicationInitialization` 組態元素可讓您指定要執行的自訂初始化動作。 交換作業會等到此自訂準備完成後，才與目標位置進行交換。 以下是範例 web.config 片段。

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

您也可以使用下列一或多個[應用程式設定](https://github.com/MicrosoftDocs/azure-docs-pr/pull/web-sites-configure.md)來自訂準備行為：

- `WEBSITE_SWAP_WARMUP_PING_PATH`：用來準備您網站的偵測路徑。 請指定開頭為斜線的自訂路徑作為值，以新增此應用程式設定。 例如： `/statuscheck`。 預設值為 `/`。 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`：準備作業的有效 HTTP 回應碼。 請以 HTTP 代碼的逗號分隔清單方式新增此應用程式設定。 例如：`200,202`。 如果傳回的狀態碼不在清單中，準備和交換作業就會停止。 預設是所有回應碼都有效。

## <a name="monitor-swap"></a>監視交換

如果交換作業耗時很久才完成，您可以在[活動記錄](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)中取得交換作業的相關資訊。

在入口網站的應用程式資源頁面中，選取左側導覽中的 [活動記錄]。

交換作業在記錄查詢中會顯示為 `Swap Web App Slots`。 您可以展開它，然後選取其中一個子作業或錯誤，以查看詳細資料。

## <a name="route-traffic"></a>路由流量

根據預設，對應用程式生產 URL (`http://<app_name>.azurewebsites.net`) 的所有用戶端要求都會路由至生產位置。 您可以將部分流量路由至其他位置。 如果您需要使用者針對新的更新提供意見反應，但尚未準備好要將更新發行至生產環境，此功能將有其效用。

### <a name="route-production-traffic-automatically"></a>自動路由生產流量

若要自動路由生產流量，請遵循下列步驟：

1. 瀏覽至應用程式的資源頁面，然後選取 [部署位置 (預覽)]。

2. 針對您要路由到的位置，在其 [流量百分比] 資料行中指定百分比 (介於 0 到 100 之間)，以代表您要路由的總流量。 按一下 [檔案] 。

    ![](./media/web-sites-staged-publishing/RouteTraffic.png)

儲存設定之後，即會將用戶端的指定百分比隨機路由至非生產位置。 

一旦用戶端自動路由至特定位置，它在用戶端工作階段存留期間都會「固定」到該位置。 用戶端瀏覽器中，您可以查看 HTTP 標頭中的 `x-ms-routing-name` Cookie，以確認您的工作階段固定到哪個位置。 路由至「預備」位置的要求具有 Cookie `x-ms-routing-name=staging`。 路由至生產位置的要求具有 Cookie `x-ms-routing-name=self`。

### <a name="route-production-traffic-manually"></a>手動路由生產流量

除了自動流量路由以外，App Service 也可以將要求路由至特定位置。 如果您想要讓使用者能夠選擇加入或退出您的 beta 應用程式，這就非常有用。 若要手動路由生產流量，您可以使用 `x-ms-routing-name` 查詢參數。

舉例而言，若要讓使用者選擇退出您的 beta 應用程式，您可以在網頁中放入下列連結：

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

字串 `x-ms-routing-name=self` 會指定生產位置。 一旦用戶端瀏覽器存取此連結，不僅瀏覽器會重新導向至生產位置，後續的每個要求也都會包含將工作階段固定到生產位置的 `x-ms-routing-name=self` Cookie。

若要讓使用者選擇加入您的 beta 應用程式，請將相同的查詢參數設為非生產位置的名稱，例如：

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

<a name="Delete"></a>

## <a name="delete-slot"></a>刪除位置

瀏覽至應用程式的資源頁面。 選取 [部署位置 (預覽)] > \<要刪除的位置> > [概觀]。 在命令列中按一下 [刪除]。  

![刪除部署位置](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>使用 PowerShell 自動執行

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell 模組提供透過 Windows PowerShell 來管理 Azure 的 Cmdlet，包括支援管理 Azure App Service 中的部署位置。

如需安裝與設定 Azure PowerShell，以及使用您的 Azure 訂用帳戶驗證 Azure PowerShell 的詳細資訊，請參閱 [如何安裝和設定 Microsoft Azure PowerShell](/powershell/azure/overview)(英文)。  

- - -
### <a name="create-web-app"></a>建立 Web 應用程式
```PowerShell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-slot"></a>建立位置
```PowerShell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>起始使用預覽交換 (多階段交換)，並將目的地位置組態套用至來源位置
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>取消擱置中的交換 (使用預覽交換)，並還原來源位置組態
```PowerShell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>交換部署位置
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>監視活動記錄中的交換事件
```PowerShell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-slot"></a>刪除位置
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-cli"></a>使用 CLI 自動化

如需適用於部署位置的 [Azure CLI](https://github.com/Azure/azure-cli) 命令，請參閱 [az webapp deployment slot](/cli/azure/webapp/deployment/slot)。

## <a name="next-steps"></a>後續步驟
[封鎖對非生產位置的存取](app-service-ip-restrictions.md)
