---
title: Microsoft Azure Stack 開發套件版本資訊 | Microsoft Docs
description: Azure Stack 開發套件的增強功能、修正及已知問題。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 2f732dfdfe9bf4aff2753114c3041f8f646421c2
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344727"
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack 開發套件版本資訊
這些版本資訊提供 Azure Stack 開發套件中的增強功能、修正和已知問題的相關資訊。 如果您不確定所執行的版本，可以使用[入口網站來進行檢查](.\.\azure-stack-updates.md#determine-the-current-version)。

> 請訂閱 [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [摘要](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)，以便隨時收到 ASDK 的最新訊息。

## <a name="build-11805142"></a>組建 1.1805.1.42

<!-- ### New features 
This build includes the following improvements and fixes for Azure Stack.  
-->


### <a name="fixed-issues"></a>已修正的問題

- 針對效能、穩定性、安全性和 Azure Stack 所使用的作業系統提供了**多項修正**


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>已知問題
 
#### <a name="portal"></a>入口網站
- <!-- TBD - IS ASDK --> 您無法搭配此版本的 Azure Stack 使用 OEM 延伸模組套件來套用驅動程式更新。  此問題目前沒有因應方式。
 
- <!-- TBD - IS ASDK --> 請勿使用新系統管理訂用帳戶類型的「計量訂用帳戶」和「取用訂用帳戶」。 這些新的訂用帳戶類型已於 1804 版導入，但還未就緒以供使用。 請繼續使用「預設提供者」訂用帳戶類型。  

- <!-- 2403291 - IS ASDK --> 您可能還無法使用系統管理員和使用者入口網站底部的水平捲軸。 如果您無法存取水平捲軸，請使用階層連結來瀏覽至入口網站中的上一個刀鋒視窗，方法是從入口網站左上角的階層連結清單中，選取您想要檢視的刀鋒視窗名稱。
  ![階層連結](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。

- <!-- TBD -  IS ASDK --> 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 PowerShell 來確認權限。


#### <a name="health-and-monitoring"></a>健康情況和監視
- <!-- 1264761 - IS ASDK --> 您可能會看到「健康情況控制器」元件出現具有下列詳細資料的警示：  

   警示 #1：
   - NAME：基礎結構角色狀況不良
   - SEVERITY：警告
   - COMPONENT：健康情況控制器
   - DESCRIPTION：健康情況控制器活動訊號掃描器無法使用。 這可能會影響健康情況報告和計量。  

  警示 #2：
   - NAME：基礎結構角色狀況不良
   - SEVERITY：警告
   - COMPONENT：健康情況控制器
   - DESCRIPTION：健康情況控制器錯誤掃描器無法使用。 這可能會影響健康情況報告和計量。

  您可以放心地忽略這兩個警示。 這兩個警示會在一段時間過後自動關閉。  

#### <a name="compute"></a>計算
- <!-- TBD -  IS ASDK --> 無法在入口網站中使用虛擬機器擴展集的調整設定。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) 作為因應措施。 由於 PowerShell 版本差異，您必須使用 `-Name` 參數，而不是 `-VMScaleSetName`。

- <!-- TBD -  IS ASDK --> 當您在 Azure Stack 使用者入口網站上建立虛擬機器時，入口網站所顯示可連結 D 系列 VM 的資料磁碟數目會不正確。 所有受支援的 D 系列 VM 均可容納與 Azure 設定數目一樣多的資料磁碟。

- <!-- TBD -  IS ASDK --> 當 VM 映像建立失敗時，可能會在 VM 映像計算刀鋒視窗上新增一個您無法刪除的失敗項目。

  因應措施是，建立一個具有虛擬 VHD 的新 VM 映像，您可以透過 Hyper-V 建立此 VHD (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB)。 此程序應該可以修正防止刪除失敗項目的問題。 接著，在建立虛擬映像後的 15 分鐘之後，您便可以順利刪除它。

  您可以接著嘗試下載先前失敗的 VM 映像。

- <!-- TBD -  IS ASDK --> 如果在 VM 部署上佈建延伸模組所花費的時間太長，使用者應該讓佈建逾時，而不是嘗試停止程序來將 VM 解除配置或刪除。  

- <!-- 1662991 - IS ASDK --> Azure Stack 不支援 Linux VM 診斷。 當您部署啟用了 VM 診斷的 Linux VM 時，部署會失敗。 如果您透過診斷設定啟用 Linux VM 基本計量，部署也會失敗。 

#### <a name="networking"></a>網路
- <!-- 1766332 - IS, ASDK --> 如果您在 [網路] 下按一下 [建立 VPN 閘道] 來設定 VPN 連線，系統就會將 [原則式] 列為 VPN 類型。 請勿選取此選項。 Azure Stack 只支援 [路由式] 選項。

- <!-- 2388980 -  IS ASDK --> 在建立 VM 並與公用 IP 位址建立關聯之後，您就無法將 VM 與該 IP 位址取消關聯。 取消關聯看似可以運作，但先前指派的公用 IP 位址會繼續與原始 VM 保持關聯。

  目前，您只能將新的公用 IP 位址用於新建立的 VM。

  即使您將 IP 位址重新指派給新的 VM (通常稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。

- <!-- 2292271 - IS ASDK --> 如果供應項目和方案與租用戶訂用帳戶相關聯，而您將屬於供應項目和方案一部分的網路資源提高其配額限制，則新的限制不會套用到該訂用帳戶。 不過，新的限制會套用到配額增加後所建立的新訂用帳戶中。 

  若要解決這個問題，請使用附加方案，在方案已與訂用帳戶產生關聯時，增加網路配額。 如需詳細資訊，請參閱如何[提供附加方案](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)。

- <!-- 2304134 IS ASDK --> 您無法刪除有相關聯 DNS 區域資源或路由表資源的訂用帳戶。 若要成功刪除訂用帳戶，您必須先從租用戶訂用帳戶中刪除 DNS 區域和路由表資源。 


- <!-- 1902460 -  IS ASDK --> Azure Stack 支援每一 IP 位址有一個「區域網路閘道」。 這適用於所有租用戶訂用帳戶。 建立第一個區域網路閘道連線之後，後續若嘗試使用相同的 IP 位址來建立區域網路閘道資源，就會被封鎖。

- <!-- 16309153 -  IS ASDK --> 在 DNS 伺服器設定為 [自動] 的已建立虛擬網路上，變更成自訂 DNS 伺服器時失敗。 更新的設定並未推送至該 Vnet 中的 VM。
 
- <!-- TBD -  IS ASDK --> Azure Stack 不支援在部署 VM 之後將額外的網路介面新增至 VM 執行個體。 如果 VM 需要多個網路介面，必須在部署階段就定義這些網路介面。


#### <a name="sql-and-mysql"></a>SQL 和 MySQL 
- <!-- TBD - ASDK --> 裝載伺服器的資料庫必須專供資源提供者和使用者工作負載使用。 您無法使用任何其他取用者 (包括「應用程式服務」) 正在使用的執行個體。

- <!-- IS, ASDK --> 當您為 SQL 和 MySQL 資源提供者建立 SKU 時，[系列] 名稱不支援特殊字元，包括空格和句點。 

#### <a name="app-service"></a>App Service 方案
- <!-- 2352906 - IS ASDK --> 使用者必須在於訂用帳戶中建立第一個 Azure 函式之前，先註冊儲存體資源提供者。

- <!-- TBD - IS ASDK --> 為了向外延展基礎結構 (背景工作、管理、前端角色)，您必須如「計算」的版本資訊所述，使用 PowerShell。  

- <!-- TBD - IS ASDK --> 目前，App Service 只能部署到「預設提供者訂用帳戶」。 在未來的更新中，App Service 會部署至 Azure Stack 1804 所導入的新「計量訂用帳戶」。 當系統支援使用計量時，現有部署就會全部移轉至這個新的訂用帳戶類型。

#### <a name="usage"></a>使用量  
- <!-- TBD -  IS ASDK --> 使用量公用 IP 位址使用計量資料會針對每一筆記錄顯示相同的 EventDateTime 值，而不是建立記錄時顯示的 TimeDate 時間戳記。 目前，您無法使用這項資料來執行公用 IP 位址使用狀況的正確計量。

<!-- #### Identity -->



## <a name="build-201805131"></a>組建 20180513.1

### <a name="new-features"></a>新功能 
此組建包含下列適用於 Azure Stack 的改良功能與修正。  

- <!-- 1759172 - IS, ASDK --> **新的系統管理訂用帳戶**。 1804 版的入口網站中有兩個新的可用訂用帳戶類型。 這些新的訂用帳戶類型會新增至預設提供者訂用帳戶，從 1804 版開始，新的 Azure Stack 安裝中就能看見這些類型。 請勿在這一版 Azure Stack 中使用這些新的訂用帳戶類型。 我們會在未來的更新中，宣佈開放使用這些訂用帳戶類型。 

  這些新的訂用帳戶類型會顯示出來，但會是較大變更的一部分，目的是要保護預設提供者訂用帳戶，並讓您更輕鬆地部署共用資源，例如 SQL 主控伺服器。 

  目前可用的三種訂用帳戶類型如下：  
  - 預設提供者訂用帳戶：繼續使用此訂用帳戶類型。 
  - 計量訂用帳戶：請勿使用此訂用帳戶類型。
  - 取用訂用帳戶：請勿使用此訂用帳戶類型

### <a name="fixed-issues"></a>已修正的問題
- <!-- IS, ASDK --> 您再也不必先重新整理 [更新] 圖格，系統管理員入口網站就會顯示資訊。 

- <!-- 2050709 - IS, ASDK --> 您現在可以使用系統管理員入口網站，來編輯 Blob 服務、表格服務和佇列服務的儲存體計量。

- <!-- IS, ASDK --> 當您在 [網路] 下按一下 [連線] 來設定 VPN 連線時，目前可用的選項就只有 [站對站 (IPsec)]。 

- 針對效能、穩定性、安全性和 Azure Stack 所使用的作業系統提供了**多項修正**

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>與此更新同時發佈的其他項目  
以下項目現在可用，但是不需要 Azure Stack 更新 1804。
- **Microsoft Azure Stack System Center Operations Manager 監視組件的更新**。 適用於 Azure Stack 的新版本 (1.0.3.0) Microsoft System Center Operations Manager 監視套件，現在可供[下載](https://www.microsoft.com/download/details.aspx?id=55184)。 使用此版本，您可以在您新增已連線的 Azure Stack 部署時使用服務主體。 此版本也提供更新管理體驗，可讓您直接從 Operations Manager 中採取補救措施。 也有新的儀表板，會顯示資源提供者、縮放單位以及縮放單位節點。

- **新的 Azure Stack 管理員 PowerShell 1.3.0 版**。  Azure Stack PowerShell 1.3.0 現在可供安裝。 這個版本會為所有管理員資源提供者，提供用來管理 Azure Stack 的命令。  有了這個版本，我們就會從 Azure Stack Tools GitHub [存放庫](https://github.com/Azure/AzureStack-Tools)取代部分內容。 

   如需安裝詳細資訊，請遵循 Azure Stack 模組 1.3.0 的[指示](.\.\azure-stack-powershell-install.md)或[說明](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0)內容。 

- **初始版本的 Azure Stack API Rest 參考**。 [所有 Azure Stack 管理員資源提供者的 API 參考](https://docs.microsoft.com/rest/api/azure-stack/)現已發行。 

### <a name="known-issues"></a>已知問題
 
#### <a name="portal"></a>入口網站
- <!-- TBD - IS ASDK --> 您無法搭配此版本的 Azure Stack 使用 OEM 延伸模組套件來套用驅動程式更新。  此問題目前沒有因應方式。
 
- <!-- TBD - IS ASDK --> 無法在系統管理員入口網站內，[從下拉式清單開啟新的支援要求](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support)。 請改用下列連結：     
    - 針對 Azure Stack 開發套件，請使用 https://aka.ms/azurestackforum。    

- <!-- 2403291 - IS ASDK --> 您可能還無法使用系統管理員和使用者入口網站底部的水平捲軸。 如果您無法存取水平捲軸，請使用階層連結來瀏覽至入口網站中的上一個刀鋒視窗，方法是從入口網站左上角的階層連結清單中，選取您想要檢視的刀鋒視窗名稱。
  ![階層連結](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。

- <!-- TBD -  IS ASDK --> 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 PowerShell 來確認權限。

-   <!-- TBD -  IS ASDK --> 在管理員入口網站中，您可能會看到 Microsoft.Update.Admin 元件的重大警示。 警示名稱、說明及補救方式全部會顯示為：  
    - *錯誤 - 遺失 FaultType ResourceProviderTimeout 的範本。*

    您可以放心地忽略此警示。 

#### <a name="health-and-monitoring"></a>健康情況和監視
- <!-- 1264761 - IS ASDK --> 您可能會看到「健康情況控制器」元件出現具有下列詳細資料的警示：  

   警示 #1：
   - NAME：基礎結構角色狀況不良
   - SEVERITY：警告
   - COMPONENT：健康情況控制器
   - DESCRIPTION：健康情況控制器活動訊號掃描器無法使用。 這可能會影響健康情況報告和計量。  

  警示 #2：
   - NAME：基礎結構角色狀況不良
   - SEVERITY：警告
   - COMPONENT：健康情況控制器
   - DESCRIPTION：健康情況控制器錯誤掃描器無法使用。 這可能會影響健康情況報告和計量。

  您可以放心地忽略這兩個警示。 這兩個警示會在一段時間過後自動關閉。  

#### <a name="marketplace"></a>Marketplace
- 使用者不需訂用帳戶就能瀏覽完整的市集，而且將會看到如方案和供應項目的管理項目。 對使用者而言，這些都是非功能性項目。
 
#### <a name="compute"></a>計算
- <!-- TBD -  IS ASDK --> 無法在入口網站中使用虛擬機器擴展集的調整設定。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) 作為因應措施。 由於 PowerShell 版本差異，您必須使用 `-Name` 參數，而不是 `-VMScaleSetName`。

- <!-- TBD -  IS ASDK --> 當您在 Azure Stack 使用者入口網站上建立虛擬機器時，入口網站所顯示可連結至 DS 系列 VM 的資料磁碟數目會不正確。 DS 系列 VM 可容納與 Azure 設定數目一樣多的資料磁碟。

- <!-- TBD -  IS ASDK --> 當 VM 映像建立失敗時，可能會在 VM 映像計算刀鋒視窗上新增一個您無法刪除的失敗項目。

  因應措施是，建立一個具有虛擬 VHD 的新 VM 映像，您可以透過 Hyper-V 建立此 VHD (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB)。 此程序應該可以修正防止刪除失敗項目的問題。 接著，在建立虛擬映像後的 15 分鐘之後，您便可以順利刪除它。

  您可以接著嘗試下載先前失敗的 VM 映像。

- <!-- TBD -  IS ASDK --> 如果在 VM 部署上佈建延伸模組所花費的時間太長，使用者應該讓佈建逾時，而不是嘗試停止程序來將 VM 解除配置或刪除。  

- <!-- 1662991 - IS ASDK --> Azure Stack 不支援 Linux VM 診斷。 當您部署啟用了 VM 診斷的 Linux VM 時，部署會失敗。 如果您透過診斷設定啟用 Linux VM 基本計量，部署也會失敗。 

#### <a name="networking"></a>網路
- <!-- 1766332 - IS, ASDK --> 如果您在 [網路] 下按一下 [建立 VPN 閘道] 來設定 VPN 連線，系統就會將 [原則式] 列為 VPN 類型。 請勿選取此選項。 Azure Stack 只支援 [路由式] 選項。

- <!-- 2388980 -  IS ASDK --> 在建立 VM 並與公用 IP 位址建立關聯之後，您就無法將 VM 與該 IP 位址取消關聯。 取消關聯看似可以運作，但先前指派的公用 IP 位址會繼續與原始 VM 保持關聯。

  目前，您只能將新的公用 IP 位址用於新建立的 VM。

  即使您將 IP 位址重新指派給新的 VM (通常稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。

- <!-- 2292271 - IS ASDK --> 如果供應項目和方案與租用戶訂用帳戶相關聯，而您將屬於供應項目和方案一部分的網路資源提高其配額限制，則新的限制不會套用到該訂用帳戶。 不過，新的限制會套用到配額增加後所建立的新訂用帳戶中。 

  若要解決這個問題，請使用附加方案，在方案已與訂用帳戶產生關聯時，增加網路配額。 如需詳細資訊，請參閱如何[提供附加方案](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)。

- <!-- 2304134 IS ASDK --> 您無法刪除有相關聯 DNS 區域資源或路由表資源的訂用帳戶。 若要成功刪除訂用帳戶，您必須先從租用戶訂用帳戶中刪除 DNS 區域和路由表資源。 


- <!-- 1902460 -  IS ASDK --> Azure Stack 支援每一 IP 位址有一個「區域網路閘道」。 這適用於所有租用戶訂用帳戶。 建立第一個區域網路閘道連線之後，後續若嘗試使用相同的 IP 位址來建立區域網路閘道資源，就會被封鎖。

- <!-- 16309153 -  IS ASDK --> 在 DNS 伺服器設定為 [自動] 的已建立虛擬網路上，變更成自訂 DNS 伺服器時失敗。 更新的設定並未推送至該 Vnet 中的 VM。
 
- <!-- TBD -  IS ASDK --> Azure Stack 不支援在部署 VM 之後將額外的網路介面新增至 VM 執行個體。 如果 VM 需要多個網路介面，必須在部署階段就定義這些網路介面。


#### <a name="sql-and-mysql"></a>SQL 和 MySQL 
- <!-- TBD - ASDK --> 裝載伺服器的資料庫必須專供資源提供者和使用者工作負載使用。 您無法使用任何其他取用者 (包括「應用程式服務」) 正在使用的執行個體。

- <!-- IS, ASDK --> 當您為 SQL 和 MySQL 資源提供者建立 SKU 時，[系列] 名稱不支援特殊字元，包括空格和句點。 

#### <a name="app-service"></a>App Service 方案
- <!-- TBD -  IS ASDK --> 使用者必須在於訂用帳戶中建立第一個 Azure 函式之前，先註冊儲存體資源提供者。

- <!-- TBD -  IS ASDK --> 為了向外延展基礎結構 (背景工作、管理、前端角色)，您必須如「計算」的版本資訊所述，使用 PowerShell。
 
#### <a name="usage"></a>使用量  
- <!-- TBD -  IS ASDK --> 使用量公用 IP 位址使用計量資料會針對每一筆記錄顯示相同的 EventDateTime 值，而不是建立記錄時顯示的 TimeDate 時間戳記。 目前，您無法使用這項資料來執行公用 IP 位址使用狀況的正確計量。

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>從 GitHub 下載 Azure Stack 工具
- 使用 *invoke-webrequest* PowerShell Cmdlet 從 GitHub 下載 Azure Stack 工具時，您會收到下列錯誤：     
    -  *invoke-webrequest: 要求已經中止: 無法建立 SSL/TLS 的安全通道。*     

  之所以發生此錯誤，是因為新近的 GitHub 支援取代 Tlsv1 和 Tlsv1.1 密碼編譯標準 (PowerShell 的預設值)。 如需詳細資訊，請參閱[弱式密碼編譯標準移除通知](https://githubengineering.com/crypto-removal-notice/)。

<!-- #### Identity -->



## <a name="build-201803021"></a>組建 20180302.1

### <a name="new-features-and-fixes"></a>新功能和修正
針對 Azure Stack 整合式系統版本 1803 發行的新功能和修正適用於「Azure Stack 開發套件」。 如需詳細資料，請參閱 Azure Stack 1803 更新版本資訊的[新功能](.\.\azure-stack-update-1803.md#new-features)和[已修正的問題](.\.\azure-stack-update-1803.md#fixed-issues)小節。  
> [!IMPORTANT]    
> 在**新功能**和**已修正的問題**小節中所列的項目中，部分項目僅與 Azure Stack 整合式系統相關。

### <a name="changes"></a>變更
- 將新建立之供應項目的狀態從「私人」變更為「公用」或「解除委任」的方式已變更。 如需詳細資訊，請參閱[建立供應項目](.\.\azure-stack-create-offer.md)。 


### <a name="known-issues"></a>已知問題
 
#### <a name="portal"></a>入口網站
- 無法從系統管理員入口網站內，[從下拉式清單開啟新支援要求](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support)。 請改用下列連結：     
    - 針對 Azure Stack 開發套件，請使用 https://aka.ms/azurestackforum。    

- <!-- 2050709 --> 在系統管理員入口網站中，無法編輯 Blob 服務、表格服務或佇列服務的儲存體計量。 當您移至 [儲存體]，然後選取 Blob、表格或佇列服務的圖格時，會隨即開啟新的刀鋒視窗，其中會顯示該項服務的計量圖表。 如果您接著從 [計量圖表] 圖格上方選取 [編輯]，[編輯圖表] 刀鋒視窗會隨即開啟，但不會顯示用來編輯計量的選項。  

- 您會看到 [需要啟用] 警告警示，通知您註冊「Azure Stack 開發套件」。 這是預期的行為。

- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。

- 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 PowerShell 來確認權限。

- 在管理員入口網站的儀表板中，[更新] 圖格無法顯示更新的相關資訊。 若要解決此問題，請按一下該圖格來重新整理它。

-   在管理員入口網站中，您可能會看到 Microsoft.Update.Admin 元件的重大警示。 警示名稱、描述及補救方式全部會顯示為：  
    - *錯誤 - 遺失 FaultType ResourceProviderTimeout 的範本。*

    您可以放心地忽略此警示。 

- 在管理員入口網站和使用者入口網站中，當您使用舊版 API (例如：2015-06-15) 建立的儲存體帳戶選取 [概觀] 刀鋒視窗時，[概觀] 刀鋒視窗無法載入。 

  因應措施是，使用 PowerShell 來執行 **Start-ResourceSynchronization.ps1** 指令碼，以還原對儲存體帳戶詳細資料的存取權。 [該指令碼可從 GitHub 取得]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)，而且如果您使用 ASDK，則必須在開發套件主機上使用服務管理員認證執行。  

- [服務健全狀況] 刀鋒視窗無法載入。 當您在管理員或使用者入口網站中開啟 [服務健全狀況] 刀鋒視窗時，Azure Stack 顯示錯誤而未載入資訊。 這是預期行為。 雖然您可以選取並開啟 [服務健全狀況]，不過此功能尚無法供使用，但在未來的 Azure Stack 版本中會實作此功能。


#### <a name="health-and-monitoring"></a>健康情況和監視
- <!-- 1264761 - IS ASDK --> 您可能會看到「健康情況控制器」元件出現具有下列詳細資料的警示：  

   警示 #1：
   - NAME：基礎結構角色狀況不良
   - SEVERITY：警告
   - COMPONENT：健康情況控制器
   - DESCRIPTION：健康情況控制器活動訊號掃描器無法使用。 這可能會影響健康情況報告和計量。  

  警示 #2：
   - NAME：基礎結構角色狀況不良
   - SEVERITY：警告
   - COMPONENT：健康情況控制器
   - DESCRIPTION：健康情況控制器錯誤掃描器無法使用。 這可能會影響健康情況報告和計量。

  您可以放心地忽略這兩個警示。 這兩個警示會在一段時間過後自動關閉。  

- 在 Azure Stack 管理入口網站中，您可能會看到名稱為**外部憑證即將到期**的重大警示。  您可以放心忽略此警示，此警示不會影響「Azure Stack 開發套件」的作業。 


#### <a name="marketplace"></a>Marketplace
- 使用者不需訂用帳戶就能瀏覽完整的市集，而且將會看到如方案和供應項目的管理項目。 對使用者而言，這些都是非功能性項目。
 
#### <a name="compute"></a>計算
- 無法在入口網站中使用虛擬機器擴展集的調整設定。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) 作為因應措施。 由於 PowerShell 版本差異，您必須使用 `-Name` 參數，而不是 `-VMScaleSetName`。

- 當您在 Azure Stack 使用者入口網站上建立虛擬機器時，入口網站所顯示可連結至 DS 系列 VM 的資料磁碟數目會不正確。 DS 系列 VM 可容納與 Azure 設定數目一樣多的資料磁碟。

- 當 VM 映像建立失敗時，可能會在 VM 映像計算刀鋒視窗上新增一個您無法刪除的失敗項目。

  因應措施是，建立一個具有虛擬 VHD 的新 VM 映像，您可以透過 Hyper-V 建立此 VHD (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB)。 此程序應該可以修正防止刪除失敗項目的問題。 接著，在建立虛擬映像後的 15 分鐘之後，您便可以順利刪除它。

  您可以接著嘗試下載先前失敗的 VM 映像。

-  如果在 VM 部署上佈建延伸模組所花費的時間太長，使用者應該讓佈建逾時，而不是嘗試停止程序來將 VM 解除配置或刪除。  

- <!-- 1662991 --> Azure Stack 不支援 Linux VM 診斷。 當您部署啟用了 VM 診斷的 Linux VM 時，部署會失敗。 如果您透過診斷設定啟用 Linux VM 基本計量，部署也會失敗。 


#### <a name="networking"></a>網路
- 如果您在 [網路] 下按一下 [連線] 來設定 VPN 連線，就會列出 **VNet 對 VNet** 作為可能的連線類型。 請勿選取此選項。 目前，僅支援**站對站 (IPsec)** 選項。

- 在建立 VM 並與公用 IP 位址建立關聯之後，您就無法將 VM 與該 IP 位址取消關聯。 取消關聯看似可以運作，但先前指派的公用 IP 位址會繼續與原始 VM 保持關聯。

  目前，您只能將新的公用 IP 位址用於新建立的 VM。

  即使您將 IP 位址重新指派給新的 VM (通常稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。



- Azure Stack 支援每一 IP 位址有一個「區域網路閘道」。 這適用於所有租用戶訂用帳戶。 建立第一個區域網路閘道連線之後，後續若嘗試使用相同的 IP 位址來建立區域網路閘道資源，就會被封鎖。

- 在 DNS 伺服器設定為 [自動] 的已建立虛擬網路上，變更成自訂 DNS 伺服器時失敗。 更新的設定並未推送至該 Vnet 中的 VM。
 
- Azure Stack 不支援在部署 VM 之後將額外的網路介面新增至 VM 執行個體。 如果 VM 需要多個網路介面，必須在部署階段就定義這些網路介面。



#### <a name="sql-and-mysql"></a>SQL 和 MySQL 
- 最多可能需要一個小時的時間，使用者才能在新的 SQL 或 MySQL SKU 中建立資料庫。

- 裝載伺服器的資料庫必須專供資源提供者和使用者工作負載使用。 您無法使用任何其他取用者 (包括「應用程式服務」) 正在使用的執行個體。

- <!-- IS, ASDK --> 當您為 SQL 和 MySQL 資源提供者建立 SKU 時，[系列] 或 [層] 名稱不支援特殊字元，包括空格和句點。

#### <a name="app-service"></a>App Service 方案
- 使用者必須在於訂用帳戶中建立第一個 Azure 函式之前，先註冊儲存體資源提供者。

- 為了向外延展基礎結構 (背景工作、管理、前端角色)，您必須如「計算」的版本資訊所述，使用 PowerShell。
 
#### <a name="usage"></a>使用量  
- 使用量公用 IP 位址使用計量資料會針對每一筆記錄顯示相同的 *EventDateTime* 值，而不是建立記錄時顯示的 *TimeDate* 時間戳記。 目前，您無法使用這項資料來執行公用 IP 位址使用狀況的正確計量。
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>從 GitHub 下載 Azure Stack 工具
- 使用 *invoke-webrequest* PowerShell Cmdlet 從 GitHub 下載 Azure Stack 工具時，您會收到下列錯誤：     
    -  *invoke-webrequest: 要求已經中止: 無法建立 SSL/TLS 的安全通道。*     

  之所以發生此錯誤，是因為新近的 GitHub 支援取代 Tlsv1 和 Tlsv1.1 密碼編譯標準 (PowerShell 的預設值)。 如需詳細資訊，請參閱[弱式密碼編譯標準移除通知](https://githubengineering.com/crypto-removal-notice/)。

  若要解決此問題，請將 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` 新增至指令碼頂端，以強制 PowerShell 主控台在從 GitHub 存放庫下載時使用 TLSv1.2。
