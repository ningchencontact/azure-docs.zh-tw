---
title: Microsoft Azure Stack 開發套件版本資訊 | Microsoft Docs
description: Azure Stack 開發套件的增強功能、修正及已知問題。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 6b08c1793857fd6c6a6a04c0d450e76a36357597
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack 開發套件版本資訊
這些版本資訊提供 Azure Stack 開發套件中的增強功能、修正和已知問題的相關資訊。 如果您不確定所執行的版本，可以使用[入口網站來進行檢查](.\.\azure-stack-updates.md#determine-the-current-version)。

> 請訂閱 [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [摘要](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)，以便隨時收到 ASDK 的最新訊息。

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

- 當您檢視資源或資源群組的屬性時，[移動] 按鈕顯示為停用。 這是預期的行為。 目前不支援在資源群組或訂用帳戶之間移動資源或資源群組。
 
- 您會看到 [需要啟用] 警告警示，通知您註冊「Azure Stack 開發套件」。 這是預期的行為。

- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。

- 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 PowerShell 來確認權限。

- 在管理員入口網站的儀表板中，[更新] 圖格無法顯示更新的相關資訊。 若要解決此問題，請按一下該圖格來重新整理它。

-   在管理員入口網站中，您可能會看到 Microsoft.Update.Admin 元件的重大警示。 警示名稱、描述及補救方式全部會顯示為：  
    - *錯誤 - 遺失 FaultType ResourceProviderTimeout 的範本。*

    您可以放心地忽略此警示。 

- 在管理員入口網站和使用者入口網站中，當您使用舊版 API (例如：2015-06-15) 建立的儲存體帳戶選取 [概觀] 刀鋒視窗時，[概觀] 刀鋒視窗無法載入。 

  因應措施是，使用 PowerShell 來執行 **Start-ResourceSynchronization.ps1** 指令碼，以還原對儲存體帳戶詳細資料的存取權。 [該指令碼可從 GitHub 取得]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)，而且如果您使用 ASDK，則必須在開發套件主機上使用服務管理員認證執行。  


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

- 「內部負載平衡」(ILB) 以不正確的方式處理後端 VM 的 MAC 位址，導致 ILB 在使用後端網路上的 Linux 執行個體時發生中斷。  ILB 可與後端網路上的 Windows 執行個體正常搭配運作。

-   在入口網站中可以看見 [IP 轉送] 功能，但啟用 [IP 轉送] 並沒有任何作用。 目前尚不支援此功能。

- Azure Stack 支援每一 IP 位址有一個「區域網路閘道」。 這適用於所有租用戶訂用帳戶。 建立第一個區域網路閘道連線之後，後續若嘗試使用相同的 IP 位址來建立區域網路閘道資源，就會被封鎖。

- 在 DNS 伺服器設定為 [自動] 的已建立虛擬網路上，變更成自訂 DNS 伺服器時失敗。 更新的設定並未推送至該 Vnet 中的 VM。
 
- Azure Stack 不支援在部署 VM 之後將額外的網路介面新增至 VM 執行個體。 如果 VM 需要多個網路介面，必須在部署階段就定義這些網路介面。

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    App Service 的因應措施：如果您需要透過遠端桌面連線至「控制器」執行個體，則需使用 PowerShell 來修改網路安全性群組內的安全性規則。  以下是一些範例，說明如何「允許」，然後再將設定還原至「拒絕」： 
    
    - *允許：*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *拒絕：*

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```


#### <a name="sql-and-mysql"></a>SQL 和 MySQL 
- 最多可能需要一個小時的時間，使用者才能在新的 SQL 或 MySQL SKU 中建立資料庫。

- 裝載伺服器的資料庫必須專供資源提供者和使用者工作負載使用。 您無法使用任何其他取用者 (包括「應用程式服務」) 正在使用的執行個體。

#### <a name="app-service"></a>App Service 方案
- 使用者必須在於訂用帳戶中建立第一個 Azure 函式之前，先註冊儲存體資源提供者。

- 為了向外延展基礎結構 (背景工作、管理、前端角色)，您必須如「計算」的版本資訊所述，使用 PowerShell。
 
#### <a name="usage-and-billing"></a>使用量與計費
- 公用 IP 位址使用計量資料會針對每一筆記錄顯示相同的 EventDateTime 值，而不是建立記錄時顯示的 TimeDate 時間戳記。 目前，您無法使用這項資料來執行公用 IP 位址使用狀況的正確計量。

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>從 GitHub 下載 Azure Stack 工具
- 使用 *invoke-webrequest* PowerShell Cmdlet 從 GitHub 下載 Azure Stack 工具時，您會收到下列錯誤：     
    -  *invoke-webrequest: 要求已經中止: 無法建立 SSL/TLS 的安全通道。*     

  之所以發生此錯誤，是因為新近的 GitHub 支援取代 Tlsv1 和 Tlsv1.1 密碼編譯標準 (PowerShell 的預設值)。 如需詳細資訊，請參閱[弱式密碼編譯標準移除通知](https://githubengineering.com/crypto-removal-notice/)。

  若要解決此問題，請將 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` 新增至指令碼頂端，以強制 PowerShell 主控台在從 GitHub 存放庫下載時使用 TLSv1.2。


## <a name="build-201801032"></a>組建 20180103.2

### <a name="new-features-and-fixes"></a>新功能和修正

- 請參閱 Azure Stack 整合系統之 Azure Stack 1712 更新版本資訊的[新功能和修正](.\.\azure-stack-update-1712.md#new-features-and-fixes)一節。

    > [!IMPORTANT]
    > **新功能和修正**一節中所列的項目之中，有部分僅與 Azure Stack 整合系統相關。

### <a name="known-issues"></a>已知問題
 
#### <a name="deployment"></a>部署
- 在部署期間，您必須使用 IP 位址指定時間伺服器。

#### <a name="infrastructure-management"></a>基礎結構管理
- 請勿在 [基礎架構備份] 刀鋒視窗上啟用基礎結構備份。
- 縮放單位節點的基本資訊中不會顯示基礎板管理控制器 (BMC) IP 位址和模型。 這是 Azure Stack 開發套件中的預期行為。

#### <a name="portal"></a>入口網站
- 您可能會在入口網站中看到空白的儀表板。 若要復原儀表板，請選取位於入口網站右上角的齒輪圖示，然後選取 [還原預設設定]。
- 當您檢視資源群組的內容時，會停用 [移動] 按鈕。 這是預期的行為。 目前不支援在訂用帳戶之間移動資源群組。
-  針對任何您可在下拉式清單中選取訂用帳戶、資源群組或位置的工作流程，您可能會遇到一或多個下列問題：

   - 您可能會在清單頂端看到一個空白資料列。 您應該仍能如預期般選取項目。
   - 如果下拉式清單中的項目清單很短，您可能無法檢視任何項目名稱。
   - 如果您有多個使用者訂用帳戶，資源群組的下拉式清單可能是空的。 

   若要解決後兩個問題，您可以輸入訂用帳戶或資源群組的名稱 (如果您知道)，或者可以改用 PowerShell。

- 您會看到 [需要啟用] 警告警示，提示您註冊 Azure Stack 開發套件。 這是預期的行為。
- 如果從任何 [基礎結構角色] 警示按一下 [元件] 連結，產生的 [概觀] 刀鋒視窗就會嘗試載入並且失敗。 此外，[概觀] 刀鋒視窗不會逾時。
- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。
- 您無法使用 Azure Stack 入口網站檢視訂用帳戶的權限。 因應措施是，可以使用 PowerShell 確認權限。
 
#### <a name="marketplace"></a>Marketplace
- 基於相容性考量，將移除此版本中的某些市集項目。 在進一步驗證後，會重新啟用這些項目。
- 使用者不需訂用帳戶就能瀏覽完整的市集，而且將會看到如方案和供應項目的管理項目。 對使用者而言，這些都是非功能性項目。
 
#### <a name="compute"></a>計算
- 系統會提供選項，供使用者建立具備異地備援儲存體的虛擬機器。 此設定會導致虛擬機器建立失敗。 
- 您可以設定只含一個容錯網域和一個更新網域的虛擬機器可用性設定組。
- 沒有任何可用以建立虛擬機器擴展集的市集體驗。 您可以使用範本來建立擴展集。
- 無法在入口網站中使用虛擬機器擴展集的調整設定。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) 作為因應措施。 由於 PowerShell 版本差異，您必須使用 `-Name` 參數，而不是 `-VMScaleSetName`。

#### <a name="networking"></a>網路
- 您無法使用入口網站，利用公用 IP 位址來建立負載平衡器。 作為因應措施，您可以使用 PowerShell 來建立負載平衡器。
- 當您建立網路負載平衡器時，必須建立網路位址轉譯 (NAT) 規則。 如果沒有，在建立負載平衡器之後嘗試新增 NAT 規則時會收到錯誤。
- 如果您在 [網路] 下按一下 [連線] 來設定 VPN 連線，就會列出 **VNet 對 VNet** 作為可能的連線類型。 請勿選取此選項。 目前，僅支援**站對站 (IPsec)** 選項。
- 建立 VM 並與公用 IP 位址建立關聯之後，您就無法取消該 IP 位址與虛擬機器 (VM) 的關聯。 取消關聯看似正常運作，但先前指派的公用 IP 位址仍然會與原始 VM 建立關聯。 即使您將 IP 位址重新指派給新的 VM (通常稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。 您目前只有在建立新的 VM 時，才能使用新的公用 IP 位址。
- Azure Stack 操作員可能無法部署、刪除、修改 VNET 或網路安全性群組。 此問題主要會出現在相同套件的後續更新嘗試。 這是因目前正在進行調查的更新之封裝問題所致。
- 內部負載平衡 (ILB) 對 MAC 位址的後端 VM 進行不恰當的處理，導致 Linux 執行個體損壞。
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- 這最多可能需要一個小時，然後租用戶才能在新的 SQL 或 MySQL SKU 中建立資料庫。 
- 不支援在不是由資源提供者執行的 SQL 和 MySQL 主控伺服器中直接建立項目，且可能導致不相符的狀態。

#### <a name="app-service"></a>App Service 方案
- 在訂用帳戶中建立第一個 Azure 函式之前，使用者必須先註冊儲存體資源提供者。
 
#### <a name="usage-and-billing"></a>使用量與計費
- 公用 IP 位址使用計量資料會針對每一筆記錄顯示相同的 EventDateTime 值，而不是建立記錄時顯示的 TimeDate 時間戳記。 目前，您無法使用這項資料來執行公用 IP 位址使用狀況的正確計量。

#### <a name="identity"></a>身分識別

在已部署 Azure Active Directory Federation Services (ADFS) 的環境中，**azurestack\azurestackadmin** 帳戶不再是預設提供者訂用帳戶的擁有者。 並非使用 **azurestack\azurestackadmin** 登入 **Admin portal / adminmanagement endpoint**，您可以使用 **azurestack\cloudadmin** 帳戶，因此您可以管理及使用「預設提供者訂用帳戶」。

> [!IMPORTANT]
> 即使 **azurestack\cloudadmin** 帳戶是 ADFS 部署環境中「預設提供者訂用帳戶」的擁有者，但它並沒有 RDP 到主機的權限。 視需要繼續使用 **azurestack\azurestackadmin** 帳戶或本機系統管理員帳戶登入、存取和管理主機。


## <a name="build-201711221"></a>組建 20171122.1

### <a name="new-features-and-fixes"></a>新功能和修正

- 請參閱 Azure Stack 整合系統之 Azure Stack 1711 更新版本資訊的[新功能和修正](.\.\azure-stack-update-1711.md#new-features-and-fixes)一節。

    > [!IMPORTANT]
    > **新功能和修正**一節中所列的項目之中，有部分僅與 Azure Stack 整合系統相關。

### <a name="known-issues"></a>已知問題

 
#### <a name="deployment"></a>部署
- 在部署期間，您必須使用 IP 位址指定時間伺服器。
- 從 1711 版本開始，**CloudAdmin** 是保留的帳戶名稱，而且當您部署開發套件時，不應該以手動方式指定。 

#### <a name="infrastructure-management"></a>基礎結構管理
- 請勿在 [基礎架構備份] 刀鋒視窗上啟用基礎結構備份。
- 縮放單位節點的基本資訊中不會顯示基礎板管理控制器 (BMC) IP 位址和模型。 這是 Azure Stack 開發套件中的預期行為。

#### <a name="portal"></a>入口網站
- 您可能會在入口網站中看到空白的儀表板。 若要復原儀表板，請選取位於入口網站右上角的齒輪圖示，然後選取 [還原預設設定]。
- 當您檢視資源群組的內容時，會停用 [移動] 按鈕。 這是預期的行為。 目前不支援在訂用帳戶之間移動資源群組。
-  針對任何您可在下拉式清單中選取訂用帳戶、資源群組或位置的工作流程，您可能會遇到一或多個下列問題：

   - 您可能會在清單頂端看到一個空白資料列。 您應該仍能如預期般選取項目。
   - 如果下拉式清單中的項目清單很短，您可能無法檢視任何項目名稱。
   - 如果您有多個使用者訂用帳戶，資源群組的下拉式清單可能是空的。 

   若要解決後兩個問題，您可以輸入訂用帳戶或資源群組的名稱 (如果您知道)，或者可以改用 PowerShell。

- 您會看到 [需要啟用] 警告警示，提示您註冊 Azure Stack 開發套件。 這是預期的行為。
- 如果從任何 [基礎結構角色] 警示按一下 [元件] 連結，產生的 [概觀] 刀鋒視窗就會嘗試載入並且失敗。 此外，[概觀] 刀鋒視窗不會逾時。
- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。
- 您無法使用 Azure Stack 入口網站檢視訂用帳戶的權限。 因應措施是，可以使用 PowerShell 確認權限。
 
#### <a name="marketplace"></a>Marketplace
- 當您嘗試使用 [從 Azure 新增] 選項來將項目新增至 Azure Stack 市集時，可能不會看見所有可供下載的項目。
- 使用者不需訂用帳戶就能瀏覽完整的市集，而且將會看到如方案和供應項目的管理項目。 對使用者而言，這些都是非功能性項目。
 
#### <a name="compute"></a>計算
- 系統會提供選項，供使用者建立具備異地備援儲存體的虛擬機器。 此設定會導致虛擬機器建立失敗。 
- 您可以設定只含一個容錯網域和一個更新網域的虛擬機器可用性設定組。
- 沒有任何可用以建立虛擬機器擴展集的市集體驗。 您可以使用範本來建立擴展集。
- 無法在入口網站中使用虛擬機器擴展集的調整設定。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) 作為因應措施。 由於 PowerShell 版本差異，您必須使用 `-Name` 參數，而不是 `-VMScaleSetName`。

#### <a name="networking"></a>網路
- 您無法使用入口網站，利用公用 IP 位址來建立負載平衡器。 作為因應措施，您可以使用 PowerShell 來建立負載平衡器。
- 當您建立網路負載平衡器時，必須建立網路位址轉譯 (NAT) 規則。 如果沒有，在建立負載平衡器之後嘗試新增 NAT 規則時會收到錯誤。
- 如果您在 [網路] 下按一下 [連線] 來設定 VPN 連線，就會列出 **VNet 對 VNet** 作為可能的連線類型。 請勿選取此選項。 目前，僅支援**站對站 (IPsec)** 選項。
- 建立 VM 並與公用 IP 位址建立關聯之後，您就無法取消該 IP 位址與虛擬機器 (VM) 的關聯。 取消關聯看似正常運作，但先前指派的公用 IP 位址仍然會與原始 VM 建立關聯。 即使您將 IP 位址重新指派給新的 VM (通常稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。 您目前只有在建立新的 VM 時，才能使用新的公用 IP 位址。
- Azure Stack 操作員可能無法部署、刪除、修改 VNET 或網路安全性群組。 此問題主要會出現在相同套件的後續更新嘗試。 這是因目前正在進行調查的更新之封裝問題所致。
- 內部負載平衡 (ILB) 對 MAC 位址的後端 VM 進行不恰當的處理，導致 Linux 執行個體損壞。
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- 這最多可能需要一個小時，然後租用戶才能在新的 SQL 或 MySQL SKU 中建立資料庫。 
- 不支援在不是由資源提供者執行的 SQL 和 MySQL 主控伺服器中直接建立項目，且可能導致不相符的狀態。

    > [!NOTE]
    > 請分別參閱 [SQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy) 和 [MySQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy) 安裝文章，取得版本相容性指南。

#### <a name="app-service"></a>App Service 方案
- 在訂用帳戶中建立第一個 Azure 函式之前，使用者必須先註冊儲存體資源提供者。
 
#### <a name="usage-and-billing"></a>使用量與計費
- 公用 IP 位址使用計量資料會針對每一筆記錄顯示相同的 EventDateTime 值，而不是建立記錄時顯示的 TimeDate 時間戳記。 目前，您無法使用這項資料來執行公用 IP 位址使用狀況的正確計量。

#### <a name="identity"></a>身分識別

在已部署 Azure Active Directory Federation Services (ADFS) 的環境中，**azurestack\azurestackadmin** 帳戶不再是預設提供者訂用帳戶的擁有者。 並非使用 **azurestack\azurestackadmin** 登入 **Admin portal / adminmanagement endpoint**，您可以使用 **azurestack\cloudadmin** 帳戶，因此您可以管理及使用「預設提供者訂用帳戶」。

> [!IMPORTANT]
> 即使 **azurestack\cloudadmin** 帳戶是 ADFS 部署環境中「預設提供者訂用帳戶」的擁有者，但它並沒有 RDP 到主機的權限。 視需要繼續使用 **azurestack\azurestackadmin** 帳戶或本機系統管理員帳戶登入、存取和管理主機。

