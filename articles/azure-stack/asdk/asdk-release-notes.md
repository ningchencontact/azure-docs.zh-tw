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
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 9a396c51e8eb6262726b7590498500bfb4528924
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258399"
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack 開發套件版本資訊
這些版本資訊提供 Azure Stack 開發套件中的增強功能、修正和已知問題的相關資訊。 如果您不確定所執行的版本，可以使用[入口網站來進行檢查](.\.\azure-stack-updates.md#determine-the-current-version)。

> 請訂閱 [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [摘要](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)，以便隨時收到 ASDK 的最新訊息。


## <a name="build-201805131"></a>組建 20180513.1

### <a name="new-features"></a>新功能 
此組建包含下列適用於 Azure Stack 的改良功能與修正。  

- <!-- 1759172 - IS, ASDK --> **More granular administrative subscriptions**. With version 1804 and later, the Default Provider subscription is now complemented with two additional subscriptions. The additions facilitate separating the management of core infrastructure, additional resource providers, and workloads. The following three subscriptions are available:
  - 預設的提供者訂用帳戶。 請僅針對核心基礎結構使用此訂用帳戶。 請勿在此訂用帳戶上部署資源或資源提供者。
  - 計量訂用帳戶。 請針對資源提供者部署使用此訂用帳戶。 在此訂用帳戶上部署的資源不會計費。
  - 取用訂用帳戶。 請針對您想要部署的其他任何工作負載使用此訂用帳戶。 在此部署的資源會收取正常使用價格。


### <a name="fixed-issues"></a>已修正的問題
- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information. 

- <!-- 2050709 - IS, ASDK -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.

- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option. 

- 針對效能、穩定性、安全性和 Azure Stack 所使用的作業系統提供了**多項修正**

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>與此更新同時發佈的其他項目  
以下項目現在可用，但是不需要 Azure Stack 更新 1804。
- **Microsoft Azure Stack System Center Operations Manager 監視組件的更新**。 適用於 Azure Stack 的新版本 (1.0.3.0) Microsoft System Center Operations Manager 監視套件，現在可供[下載](https://www.microsoft.com/download/details.aspx?id=55184)。 使用此版本，您可以在您新增已連線的 Azure Stack 部署時使用服務主體。 此版本也提供更新管理體驗，可讓您直接從 Operations Manager 內採取補救措施。 也有新的儀表板，會顯示資源提供者、縮放單位以及縮放單位節點。

- **新的 Azure Stack 管理員 PowerShell 1.3.0 版**。  Azure Stack PowerShell 1.3.0 現在可供安裝。 這個版本會為所有管理員資源提供者，提供用來管理 Azure Stack 的命令。  有了這個版本，我們就會從 Azure Stack Tools GitHub [存放庫](https://github.com/Azure/AzureStack-Tools)取代部分內容。 

   如需安裝詳細資訊，請遵循 Azure Stack 模組 1.3.0 的[指示](.\.\azure-stack-powershell-install.md)或[說明](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0)內容。 

- **初始版本的 Azure Stack API Rest 參考**。 [所有 Azure Stack 管理員資源提供者的 API 參考](https://docs.microsoft.com/rest/api/azure-stack/)現已發行。 

### <a name="known-issues"></a>已知問題
 
#### <a name="portal"></a>入口網站
- <!-- TBD - IS ASDK --> The ability [to open a new support request from the dropdown](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - 針對 Azure Stack 開發套件，請使用 https://aka.ms/azurestackforum。    

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![階層連結](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

-   <!-- TBD -  IS ASDK --> In the admin portal, you might see a critical alert for the Microsoft.Update.Admin component. The Alert name, description, and remediation all display as:  
    - *錯誤 - 遺失 FaultType ResourceProviderTimeout 的範本。*

    您可以放心地忽略此警示。 

#### <a name="compute"></a>計算
- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  因應措施是，建立一個具有虛擬 VHD 的新 VM 映像，您可以透過 Hyper-V 建立此 VHD (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB)。 此程序應該可以修正防止刪除失敗項目的問題。 接著，在建立虛擬映像後的 15 分鐘之後，您便可以順利刪除它。

  您可以接著嘗試下載先前失敗的 VM 映像。

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>網路
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  目前，您只能將新的公用 IP 位址用於新建立的 VM。

  即使您將 IP 位址重新指派給新的 VM (通常稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  若要解決這個問題，請使用附加方案，在方案已與訂用帳戶產生關聯時，增加網路配額。 如需詳細資訊，請參閱如何[提供附加方案](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)。

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>SQL 和 MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>App Service 方案
- <!-- TBD -  IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD -  IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.
 
#### <a name="usage"></a>使用量  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!-- #### Identity -->






## <a name="build-201803291"></a>組建 20180329.1

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

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- 您會看到 [需要啟用] 警告警示，通知您註冊「Azure Stack 開發套件」。 這是預期的行為。

- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。

- 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 PowerShell 來確認權限。

- 在管理員入口網站的儀表板中，[更新] 圖格無法顯示更新的相關資訊。 若要解決此問題，請按一下該圖格來重新整理它。

-   在管理員入口網站中，您可能會看到 Microsoft.Update.Admin 元件的重大警示。 警示名稱、描述及補救方式全部會顯示為：  
    - *錯誤 - 遺失 FaultType ResourceProviderTimeout 的範本。*

    您可以放心地忽略此警示。 



#### <a name="marketplace"></a>Marketplace
- 使用者不需訂用帳戶就能瀏覽完整的市集，而且將會看到如方案和供應項目的管理項目。 對使用者而言，這些都是非功能性項目。
 
#### <a name="compute"></a>計算
- 無法在入口網站中使用虛擬機器擴展集的調整設定。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) 作為因應措施。 由於 PowerShell 版本差異，您必須使用 `-Name` 參數，而不是 `-VMScaleSetName`。

- 當您在 Azure Stack 使用者入口網站上建立虛擬機器時，入口網站所顯示可連結至 DS 系列 VM 的資料磁碟數目會不正確。 DS 系列 VM 可容納與 Azure 設定數目一樣多的資料磁碟。

- 當 VM 映像建立失敗時，可能會在 VM 映像計算刀鋒視窗上新增一個您無法刪除的失敗項目。

  因應措施是，建立一個具有虛擬 VHD 的新 VM 映像，您可以透過 Hyper-V 建立此 VHD (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB)。 此程序應該可以修正防止刪除失敗項目的問題。 接著，在建立虛擬映像後的 15 分鐘之後，您便可以順利刪除它。

  您可以接著嘗試下載先前失敗的 VM 映像。

-  如果在 VM 部署上佈建延伸模組所花費的時間太長，使用者應該讓佈建逾時，而不是嘗試停止程序來將 VM 解除配置或刪除。  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


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

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.

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






## <a name="build-201803021"></a>組建 20180302.1

### <a name="new-features-and-fixes"></a>新功能和修正
請參閱 Azure Stack 整合式系統之 Azure Stack 1802 更新版本資訊的[新功能和修正](.\.\azure-stack-update-1802.md#new-features-and-fixes)一節。

> [!IMPORTANT]    
> **新功能和修正**一節中所列的項目之中，有部分僅與 Azure Stack 整合系統相關。


### <a name="known-issues"></a>已知問題
 
#### <a name="portal"></a>入口網站
- 無法從系統管理員入口網站內，[從下拉式清單開啟新支援要求](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support)。 請改用下列連結：     
    - 針對 Azure Stack 開發套件，請使用 https://aka.ms/azurestackforum。    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

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
在 Azure Stack 管理入口網站中，您可能會看到名稱為**外部憑證即將到期**的重大警示。  您可以放心忽略此警示，此警示不會影響「Azure Stack 開發套件」的作業。 


#### <a name="marketplace"></a>Marketplace
- 使用者不需訂用帳戶就能瀏覽完整的市集，而且將會看到如方案和供應項目的管理項目。 對使用者而言，這些都是非功能性項目。
 
#### <a name="compute"></a>計算
- 無法在入口網站中使用虛擬機器擴展集的調整設定。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) 作為因應措施。 由於 PowerShell 版本差異，您必須使用 `-Name` 參數，而不是 `-VMScaleSetName`。

- Azure Stack 支援僅使用固定類型的 VHD。 Azure Stack 上有些透過市集提供的影像使用動態 VHD，但這些影像已被移除。 對已連結動態磁碟的虛擬機器 (VM) 調整大小，會導致 VM 處於失敗狀態。

  若要解決這個問題，請刪除 VM，但不要刪除 VM 的磁碟 (儲存體帳戶中的 VHD Blob)。 接著，將 VHD 從動態磁碟轉換成固定磁碟，然後重新建立虛擬機器。

- 當您在 Azure Stack 使用者入口網站上建立虛擬機器時，入口網站所顯示可連結至 DS 系列 VM 的資料磁碟數目會不正確。 DS 系列 VM 可容納與 Azure 設定數目一樣多的資料磁碟。

- 當 VM 映像建立失敗時，可能會在 VM 映像計算刀鋒視窗上新增一個您無法刪除的失敗項目。

  因應措施是，建立一個具有虛擬 VHD 的新 VM 映像，您可以透過 Hyper-V 建立此 VHD (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB)。 此程序應該可以修正防止刪除失敗項目的問題。 接著，在建立虛擬映像後的 15 分鐘之後，您便可以順利刪除它。

  您可以接著嘗試下載先前失敗的 VM 映像。

-  如果在 VM 部署上佈建延伸模組所花費的時間太長，使用者應該讓佈建逾時，而不是嘗試停止程序來將 VM 解除配置或刪除。  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>網路
- 如果您在 [網路] 下按一下 [連線] 來設定 VPN 連線，就會列出 **VNet 對 VNet** 作為可能的連線類型。 請勿選取此選項。 目前，僅支援**站對站 (IPsec)** 選項。

- 在建立 VM 並與公用 IP 位址建立關聯之後，您就無法將 VM 與該 IP 位址取消關聯。 取消關聯看似可以運作，但先前指派的公用 IP 位址會繼續與原始 VM 保持關聯。

  目前，您只能將新的公用 IP 位址用於新建立的 VM。

  即使您將 IP 位址重新指派給新的 VM (通常稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。

-   在入口網站中可以看見 [IP 轉送] 功能，但啟用 [IP 轉送] 並沒有任何作用。 目前尚不支援此功能。

- Azure Stack 支援每一 IP 位址有一個「區域網路閘道」。 這適用於所有租用戶訂用帳戶。 建立第一個區域網路閘道連線之後，後續若嘗試使用相同的 IP 位址來建立區域網路閘道資源，就會被封鎖。

- 在 DNS 伺服器設定為 [自動] 的已建立虛擬網路上，變更成自訂 DNS 伺服器時失敗。 更新的設定並未推送至該 Vnet 中的 VM。
 
- Azure Stack 不支援在部署 VM 之後將額外的網路介面新增至 VM 執行個體。 如果 VM 需要多個網路介面，必須在部署階段就定義這些網路介面。



#### <a name="sql-and-mysql"></a>SQL 和 MySQL 
- 最多可能需要一個小時的時間，使用者才能在新的 SQL 或 MySQL SKU 中建立資料庫。

- 裝載伺服器的資料庫必須專供資源提供者和使用者工作負載使用。 您無法使用任何其他取用者 (包括「應用程式服務」) 正在使用的執行個體。

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers.

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

