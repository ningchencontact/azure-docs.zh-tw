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
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: fd488d475e24bc1aeebfa49b9d81b04ebae449ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445603"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>在 Azure App Service 中設定預備環境
<a name="Overview"></a>

當您部署您的 web 應用程式、 Linux、 行動後端或 API 應用程式上的 web 應用程式[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)，您可以使用個別的部署位置而不是預設的生產位置，當您正在中執行**標準**， **premium**，或**隔離**App Service 方案層。 部署位置是具有專屬主機名稱的即時應用程式。 兩個部署位置 (包括生產位置) 之間的應用程式內容與設定項目可以互相交換。 

將應用程式部署至非生產位置具有下列優點：

* 您可以先驗證預備部署位置中的應用程式變更，再將它與生產位置進行交換。
* 先將應用程式部署至某個位置，然後再將它交換到生產位置，可確保該位置的所有執行個體在交換到生產位置之前都已準備就緒。 這麼做可以排除部署應用程式時的停機情況。 流量能夠順暢地重新導向，且不會因為交換作業而捨棄任何要求。 您可以藉由設定自動化這整個工作流程[自動交換](#Auto-Swap)時不需要預先交換驗證。
* 交換之後，先前具有預備應用程式的位置，現在已經有之前的生產應用程式。 若交換到生產位置的變更不是您需要的變更，您可以立即執行相同的交換，以恢復「上一個已知的良好網站」。

每個 App Service 方案層所支援的部署位置個數都不一樣。 沒有使用部署位置需額外付費。 若要了解數目您的應用程式層所支援的位置，請參閱 < [App Service 限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits)。 

若要調整為不同層應用程式，請確定目標層支援的應用程式已經使用的位置數目。 例如，如果您的應用程式有超過五個位置，您無法調整它以**標準**層，因為**標準**層支援只有五個部署位置。 

<a name="Add"></a>

## <a name="add-a-slot"></a>新增位置
應用程式必須在 [標準]  、[進階]  或 [隔離]  層中執行，您才能啟用多個部署位置。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟應用程式的[資源頁面](../azure-resource-manager/manage-resources-portal.md#manage-resources)。

2. 在左窗格中，選取**部署位置** > **加入位置**。
   
    ![新增部署位置](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > 如果該應用程式尚未處於**標準**， **Premium**，或**隔離**層，您會收到一個訊息，指出啟用預備的發行的支援的層。 此時，您可以選擇選取**升級**並移至**擴展**] 索引標籤的 [您的應用程式，然後再繼續。
   > 

3. 在 [**新增位置**] 對話方塊中，指定位置的名稱，然後選取是否要複製其他部署位置的應用程式組態。 選取 **新增**以繼續。
   
    ![組態來源](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    您可以複製任何現有的位置設定。 可複製的設定包括應用程式設定、連接字串、語言架構版本、Web 通訊端、HTTP 版本和平台位元。

4. 加入位置之後，請選取**關閉**以關閉對話方塊。 現在會顯示新的位置**部署位置**頁面。 根據預設，**流量 %** 設為 0 的新位置，所有客戶流量路由傳送至生產位置。

5. 選取新的部署位置，以開啟該位置的 [資源] 頁面。
   
    ![部署位置標題](./media/web-sites-staged-publishing/StagingTitle.png)

    預備位置和任何其他 App Service 應用程式一樣，具有管理頁面。 您可以變更位置的組態。 頁面頂端會顯示位置的名稱，提醒您正在檢視部署位置。

6. 選取位置的資源頁面上的應用程式 URL。 部署位置有自己的主機名稱，同時也是即時的應用程式。 若要限制對部署位置的公用存取，請參閱[Azure App Service IP 限制](app-service-ip-restrictions.md)。

新的部署位置中沒有任何內容，即使您複製其他位置的設定，仍是如此。 例如，您可以[發行至此位置，使用 Git](app-service-deploy-local-git.md)。 您可以從不同的存放庫分支或不同的存放庫部署至位置。 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>交換期間發生什麼事

### <a name="swap-operation-steps"></a>交換作業步驟

當您交換兩個位置 （通常是從預備位置到生產位置） 時，App Service 會執行下列命令以確保目標位置不會產生停機時間：

1. 目標位置 （例如，生產位置） 的下列設定套用至來源位置的所有執行個體中： 
    - [位置特定](#which-settings-are-swapped)應用程式設定和連接字串，如果適用的話。
    - [持續部署](deploy-continuous-deployment.md)設定時，如果已啟用。
    - [App Service 驗證](overview-authentication-authorization.md)設定時，如果已啟用。
    
    任何這些情況下觸發重新啟動來源位置中的所有執行個體。 期間[使用預覽交換](#Multi-Phase)，這會標示出第一個階段的結束。 交換作業會暫停，而且您可以驗證來源位置與目標位置的設定正確運作。

1. 等候完成其重新啟動來源位置中的每個執行個體。 如果任何執行個體，就無法重新啟動，交換作業就會還原至來源位置的所有變更，並停止作業。

1. 如果[本機快取](overview-local-cache.md)已啟用，觸發程序藉由提出 HTTP 要求，應用程式根目錄 （"/"） 的來源位置的每個執行個體上的本機快取初始化。 等待，直到每個執行個體傳回任何 HTTP 回應。 本機快取初始化會導致每個執行個體上的另一個重新啟動。

1. 如果[自動交換](#Auto-Swap)已啟用[自訂準備](#Warm-up)，觸發程序[應用程式起始](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)提出 HTTP 要求，應用程式根目錄 （"/"） 上之來源的每個執行個體插槽。

    如果`applicationInitialization`未指定，會觸發 HTTP 要求，以在每個執行個體上的來源位置的應用程式根目錄。 
    
    如果執行個體傳回任何 HTTP 回應，其認為已準備就緒。

1. 如果來源位置上的所有執行個體都已準備就緒成功交換兩個位置，藉由切換兩個位置的路由規則。 這個步驟之後，目標位置 （例如，生產位置） 會有先前的來源位置中準備就緒的應用程式。

1. 既然來源位置中的目標位置，請以先前已預先交換應用程式，請套用所有設定，然後重新啟動執行個體中執行相同的作業。

在交換作業的任何時間點，初始化已交換的應用程式的所有工作都會在來源位置。 目標位置會保持在線上時，來源位置就緒，並已準備就緒，無論交換成功或失敗的位置。 若要交換預備位置與生產位置，請確定生產位置一律是目標位置。 如此一來，交換作業不會影響生產應用程式。

### <a name="which-settings-are-swapped"></a>哪些設定已交換？
當您複製其他部署位置的組態時，可以編輯複製的組態。 某些組態項目會遵循內容在交換 （非位置特定），而其他組態項目後，交換 （位置特定） 保持相同的位置。 以下清單顯示當您交換位置時會變更的設定。

**交換的設定**：

* 一般設定，例如 framework 版本、 32/64 位元、 web 通訊端
* 應用程式設定 (可以設定為停在某一個位置)
* 連接字串 (可以設定為停在某一個位置)
* 處理常式對應
* 監視與診斷設定
* 公開憑證
* WebJobs 內容
* 混合式連線 *
* 虛擬網路整合 *
* 服務端點 *
* Azure 內容傳遞網路 *

以星號 （*） 標記的功能已計劃進行自黏便箋的插槽。 

**無法交換的設定**：

* 發行端點
* 自訂網域名稱
* 私人憑證與 SSL 繫結
* 擴充設定
* WebJobs 排程器
* IP 限制
* Always On
* 通訊協定設定 (HTTPS、 TLS 版本，用戶端憑證)
* 診斷記錄設定
* 跨原始資源共用 (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->

若要設定為停在某特定位置 （未交換） 的應用程式設定或連接字串，請前往**組態**該位置的頁面。 新增或編輯設定，然後按**部署位置設定**。 選取此核取方塊，會告訴 App Service 未抽換的設定。 

![位置設定](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>交換兩個位置 
您可以在您的應用程式中交換部署位置**部署位置**頁面並**概觀**頁面。 如需位置交換的技術詳細資訊，請參閱[交換期間發生什麼事](#AboutConfiguration)。

> [!IMPORTANT]
> 您的應用程式，從部署位置交換到生產環境之前，請確定生產環境是您的目標位置和來源位置中的所有設定已完全依照您想要在生產環境時，才都設定。
> 
> 

若要交換部署位置：

1. 移至您的應用程式**部署位置**頁面，然後選取**交換**。
   
    ![交換 按鈕](./media/web-sites-staged-publishing/SwapButtonBar.png)

    **交換**對話方塊會顯示將會變更所選來源和目標位置中的設定。

2. 選取所需的 [來源]  和 [目標]  位置。 目標通常是生產位置。 此外，請選取**來源變更**並**目標變更**索引標籤，然後確認組態變更符合預期。 當您完成時，您才能交換位置立即選取**交換**。

    ![完整的交換](./media/web-sites-staged-publishing/SwapImmediately.png)

    若要查看如何您的目標位置會以新的設定之前執行交換實際執行過程，請勿選取**交換**，請依照下列中的指示，但[使用預覽交換](#Multi-Phase)。

3. 當您完成時，關閉對話方塊，選取**關閉**。

如果您有任何問題，請參閱[疑難排解交換](#troubleshoot-swaps)。

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>使用預覽交換 (多階段交換)

> [!NOTE]
> Linux 上的 Web 應用程式不支援「使用預覽交換」。

在您交換到生產環境與目標位置之前，先驗證應用程式執行的已交換的設定。 交換完成時，也就是適合任務關鍵性應用程式之前的來源位置也已準備就緒。

當您執行使用預覽交換時，應用程式服務會執行相同[交換作業](#AboutConfiguration)但暫停之後的第一個步驟。 接著，您就可以確認預備位置上的結果之前完成交換。 

如果您取消交換時，App Service 會重新組態項目套用至來源位置。

若要使用預覽交換：

1. 請依照下列中的步驟[交換部署位置](#Swap)但選取**使用預覽執行交換**。

    ![使用預覽交換](./media/web-sites-staged-publishing/SwapWithPreview.png)

    對話方塊會顯示您在階段 1，來源位置中的組態變更的方式，以及第 2 階段中的來源和目標位置如何變更。

2. 當你準備好開始交換時，請選取**開始交換**。

    第 1 階段完成時，請在對話方塊中會通知您。 預覽交換來源位置中的，移至`https://<app_name>-<source-slot-name>.azurewebsites.net`。 

3. 當您準備好完成擱置中的交換時時，請選取**完成交換**中**交換動作**，然後選取**完成交換**。

    若要取消擱置中的交換，請選取**取消交換**改。

4. 當您完成時，關閉對話方塊，選取**關閉**。

如果您有任何問題，請參閱[疑難排解交換](#troubleshoot-swaps)。

若要自動執行多階段交換，請參閱[使用 PowerShell 自動執行](#automate-with-powershell)。

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>回復交換
在交換位置後，若目標位置 (例如生產位置) 發生任何錯誤，請立即交換相同的兩個位置，將位置還原成交換前的狀態。

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>設定自動交換

> [!NOTE]
> 在 Linux 上的 web 應用程式不支援自動交換。

自動交換會簡化 Azure DevOps 案例中，您將持續與零冷啟動及零停機的應用程式部署的應用程式的客戶。 自動交換啟用時從一個位置到實際執行環境，每次您的程式碼變更推送至該位置時，App Service 自動[交換到生產環境的應用程式](#swap-operation-steps)它的來源位置中準備就緒之後。

   > [!NOTE]
   > 設定自動交換為生產位置之前，請考慮測試自動交換，在非生產的目標位置上。
   > 

若要設定自動交換：

1. 移至您的應用程式資源頁面。 選取 **部署位置** >  *\<所需的來源位置 >*  > **組態** >  **一般設定**。
   
2. 針對**啟用自動交換**，選取**上**。 然後選取所要的目標位置**自動交換部署位置**，然後選取**儲存**命令列上。 
   
    ![選取項目來設定自動交換](./media/web-sites-staged-publishing/AutoSwap02.png)

3. 執行推送至來源位置的程式碼。 自動交換會在一段時間後，更新會反映在目標位置的 URL。

如果您有任何問題，請參閱[疑難排解交換](#troubleshoot-swaps)。

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>指定自訂的準備
當您使用[自動交換](#Auto-Swap)，某些應用程式可能需要交換前的自訂的準備動作。 `applicationInitialization` Web.config 中的組態項目可讓您指定的自訂初始化動作。 [交換作業](#AboutConfiguration)等候此自訂準備完成，然後再將與目標位置進行交換。 以下是範例 web.config 片段。

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

如需有關自訂`applicationInitialization`項目，請參閱 <<c2> [ 最常見的部署位置交換失敗，以及如何加以修正](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)。

您也可以自訂一個或兩個以下的熱身行為[應用程式設定](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`:要 ping 以準備您的站台的路徑。 請指定開頭為斜線的自訂路徑作為值，以新增此應用程式設定。 例如 `/statuscheck`。 預設值為 `/`。 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`:準備作業的有效 HTTP 回應碼。 請以 HTTP 代碼的逗號分隔清單方式新增此應用程式設定。 例如， `200,202` 。 如果傳回的狀態碼不在清單中，會停止 「 暖機和交換作業。 預設是所有回應碼都有效。

如果您有任何問題，請參閱[疑難排解交換](#troubleshoot-swaps)。

## <a name="monitor-a-swap"></a>監視交換

如果[交換作業](#AboutConfiguration)需要長時間才能完成，您可以取得詳細資訊中的交換作業[活動記錄](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)。

在您的應用程式資源頁面上，在入口網站中，在左窗格中，選取**活動記錄**。

交換作業在記錄查詢中會顯示為 `Swap Web App Slots`。 您可以展開它，然後選取其中一個子作業或錯誤，以查看詳細資料。

## <a name="route-traffic"></a>路由流量

根據預設，對應用程式生產 URL (`http://<app_name>.azurewebsites.net`) 的所有用戶端要求都會路由至生產位置。 您可以將部分流量路由至其他位置。 如果您需要使用者針對新的更新提供意見反應，但尚未準備好要將更新發行至生產環境，這項功能將有其效用。

### <a name="route-production-traffic-automatically"></a>自動路由生產流量

若要自動路由傳送生產流量：

1. 請移至您的應用程式資源網頁，然後選取**部署位置**。

2. 針對您要路由到的位置，在其 [流量百分比]  資料行中指定百分比 (介於 0 到 100 之間)，以代表您要路由的總流量。 選取 [ **儲存**]。

    ![設定流量百分比](./media/web-sites-staged-publishing/RouteTraffic.png)

此設定會儲存之後，則指定的百分比的用戶端會隨機路由傳送至非生產位置中。 

用戶端自動路由傳送至特定位置之後, 它的 「 釘選 」 至該位置的存留期間該用戶端工作階段。 用戶端瀏覽器中，您可以查看 HTTP 標頭中的 `x-ms-routing-name` Cookie，以確認您的工作階段固定到哪個位置。 路由至「預備」位置的要求具有 Cookie `x-ms-routing-name=staging`。 路由至生產位置的要求具有 Cookie `x-ms-routing-name=self`。

### <a name="route-production-traffic-manually"></a>手動路由生產流量

除了自動流量路由以外，App Service 也可以將要求路由至特定位置。 當您希望使用者能夠選擇加入或退出 beta 應用程式時，這非常有用。 若要手動路由生產流量，您可以使用 `x-ms-routing-name` 查詢參數。

若要讓使用者選擇退出 beta 應用程式，比方說，您可以將此連結，在您的網頁上：

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

字串 `x-ms-routing-name=self` 會指定生產位置。 用戶端瀏覽器存取該連結之後，它會重新導向至生產位置。 每個後續的要求都`x-ms-routing-name=self`釘選到生產位置的工作階段的 cookie。

若要讓使用者選擇加入 beta 應用程式，將相同的查詢參數設為非生產位置的名稱。 以下是範例：

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

根據預設，新的位置指定的路由規則`0%`、 以灰色顯示。 當您明確將此值設`0%`（以黑色文字顯示），您的使用者可以手動存取預備位置，使用`x-ms-routing-name`查詢參數。 但它們不會路由至位置自動因為路由百分比設定為 0。 這是進階的案例中，您可以 「 隱藏 」 您的預備位置，從公用，同時讓內部團隊來測試在位置上的變更。

<a name="Delete"></a>

## <a name="delete-a-slot"></a>刪除位置

移至您的應用程式資源頁面。 選取 **部署位置** >  *\<若要刪除的位置 >*  > **概觀**。 選取 **刪除**命令列上。  

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
### <a name="create-a-slot"></a>建立位置
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>起始使用預覽 （多階段交換） 交換，並將目的地位置組態套用至來源位置
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>取消擱置中的交換 （使用預覽交換），並還原來源位置組態
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>交換部署位置
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>監視器活動記錄檔中的交換事件
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>刪除位置
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>使用 CLI 進行自動化

如需適用於部署位置的 [Azure CLI](https://github.com/Azure/azure-cli) 命令，請參閱 [az webapp deployment slot](/cli/azure/webapp/deployment/slot)。

## <a name="troubleshoot-swaps"></a>疑難排解交換

如果發生任何錯誤[位置交換](#AboutConfiguration)，它會記錄在*D:\home\LogFiles\eventlog.xml*。 它也會記錄在應用程式特有的錯誤記錄檔中。

以下是一些常見的分頁錯誤：

- 應用程式根目錄的 HTTP 要求已逾時。 交換作業會等候 90 秒內針對每個 HTTP 要求和重試最多 5 次。 如果所有重試會逾時，會停止交換作業。

- 當應用程式內容超過指定的本機快取的本機磁碟配額時，本機快取初始化可能會失敗。 如需詳細資訊，請參閱 <<c0> [ 本機快取概觀](overview-local-cache.md)。

- 期間[的自訂準備](#Warm-up)，HTTP 要求會在內部 （而不會通過的外部 URL）。 它們可能會失敗並在特定 URL 重寫規則*Web.config*。例如，重新導向網域名稱，或者強制使用 HTTPS 的規則可以防止準備要求到達應用程式程式碼。 若要解決此問題，請新增下列兩項條件修改重寫規則：

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 而不需要自訂的準備，URL 重寫規則仍然可以封鎖 HTTP 要求。 若要解決此問題，請修改重寫規則; 藉由新增下列條件：

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 有些[IP 限制規則](app-service-ip-restrictions.md)可能會導致無法將 HTTP 要求傳送至您的應用程式交換作業。 IPv4 位址範圍開頭`10.`和`100.`內部部署。 您應該允許它們連線到您的應用程式。

## <a name="next-steps"></a>後續步驟
[封鎖對非生產位置的存取](app-service-ip-restrictions.md)
