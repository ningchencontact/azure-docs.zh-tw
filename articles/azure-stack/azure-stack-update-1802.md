---
title: Azure Stack 1802 更新 | Microsoft Docs
description: 了解適用於 Azure Stack 整合系統之 1802 更新的新功能、已知問題，以及可下載更新的位置。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: c5237f8e97f76e5dc348322abeb16682aee62f3b
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
---
# <a name="azure-stack-1802-update"></a>Azure Stack 1802 更新

「適用於：Azure Stack 整合系統」

本文說明 1802 更新套件中的改良功能與修正、此版本的已知問題，以及可從何處下載更新。 已知問題分為直接與更新程序相關的問題，以及組建 (安裝後) 的相關問題。

> [!IMPORTANT]        
> 這個更新程式封裝僅適用於 Azure Stack 整合系統。 請勿將此更新套件套用至 Azure Stack 開發套件。

## <a name="build-reference"></a>建置參考    
Azure Stack 1802 更新組建編號為 **20180302.1**。  


## <a name="before-you-begin"></a>開始之前    
> [!IMPORTANT]    
> 在此更新安裝期間，請勿嘗試建立虛擬機器。 如需管理更新的詳細資訊，請參閱[在 Azure Stack 中管理更新概觀](/azure-stack-updates#plan-for-updates)。


### <a name="prerequisites"></a>先決條件
- 您必須先套用 Azure Stack 1802 更新，才能安裝 Azure Stack [1712 更新](azure-stack-update-1712.md)。    

- 您必須先套用 Azure Stack 1802 更新，才能安裝 **AzS Hotfix – 1.0.180312.1- Build 20180222.2**。 此 Hotfix 會更新 Windows Defender，您可在下載 Azure Stack 的更新時取得此 Hotfix。

  若要安裝 Hotfix，請遵循[安裝 Azure Stack 的更新](azure-stack-apply-updates.md)的一般程序。 更新的名稱會顯示為 **AzS Hotfix – 1.0.180312.1**，並且包含下列檔案： 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  將這些檔案上傳至儲存體帳戶和容器之後，請從管理入口網站中的 [更新] 圖格執行安裝。 
  
  不同於 Azure Stack 的更新，安裝此更新並不會變更 Azure Stack 的版本。  若要確認此更新已安裝，請檢視**已安裝的更新**清單。
 


### <a name="post-update-steps"></a>更新後步驟
在安裝 1802 之後，安裝任何適用的 Hotfix。 如需詳細資訊，請檢視下列知識庫文章，以及我們的[服務原則](azure-stack-servicing-policy.md)。 
- Azure Stack Hotfix **1.0.180302.4**。 [KB 4131152 - 現有虛擬機器擴展集可能無法使用]( https://support.microsoft.com/help/4131152) 

  此修正也能解決「[KB 4103348 - 網路控制器 API 服務在您嘗試安裝 Azure Stack 更新時損毀](https://support.microsoft.com/help/4103348)」中詳述的問題。


### <a name="new-features-and-fixes"></a>新功能和修正
此更新包含下列適用於 Azure Stack 的改良功能與修正。

- **目前已加入下列 Azure 儲存體服務 API 版本的支援**：
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    如需詳細資訊，請參閱 [Azure Stack 儲存體：差異與注意事項](/azure/azure-stack/user/azure-stack-acs-differences)。

- **支援較大的[區塊 Blob](azure-stack-acs-differences.md)**：
    - 允許的區塊大小上限從 4 MB 增加為 100 MB。
    - Blob 大小上限從 195 MB 增加為 4.75 TB。  

- **基礎結構備份**現在會出現在 [資源提供者] 圖格中，並啟用備份的警示。 如需基礎結構備份服務的詳細資訊，請參閱[使用基礎結構備份服務進行 Azure Stack 的備份和資料復原](azure-stack-backup-infrastructure-backup.md)。

- **更新為*Test-AzureStack* Cmdlet** 以改善儲存體的診斷。 如需此 Cmdlet 的詳細資訊，請參閱 [Azure Stack 的驗證](azure-stack-diagnostic-test.md)。

- **角色型存取控制 (RBAC) 的改善** - 現在，在使用 AD FS 部署 Azure Stack 時，您可以使用 RBAC 將權限委派給通用使用者群組。 若要深入了解 RBAC，請參閱[管理 RBAC](azure-stack-manage-permissions.md)。

- **新增多個容錯網域的支援**。  如需詳細資訊，請參閱 [Azure Stack 的高可用性](azure-stack-key-features.md#high-availability-for-azure-stack)。

- 針對效能、穩定性、安全性和 Azure Stack 所使用的作業系統提供了**多項修正**。

<!--
#### New features
-->


<!--
#### Fixes
-->


### <a name="known-issues-with-the-update-process"></a>關於更新程序的已知問題    
*更新 1802 的安裝沒有已知問題。*


### <a name="known-issues-post-installation"></a>已知問題 (安裝後)
以下是組建 **20180302.1** 安裝後的已知問題

#### <a name="portal"></a>入口網站
- 無法從系統管理員入口網站內，[從下拉式清單開啟新支援要求](azure-stack-manage-portals.md#quick-access-to-help-and-support)。 請改用下列連結：     
    - 針對 Azure Stack 整合式系統，請使用 https://aka.ms/newsupportrequest。

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- 可能無法在管理員入口網站中檢視計算或儲存體資源。 之所以發生此問題，是因為在更新安裝期間發生錯誤，且誤將更新報告為成功。 如果發生此問題，請連絡 Microsoft 客戶支援服務以尋求協助。

- 您可能會在入口網站中看到空白的儀表板。 若要復原儀表板，請選取位於入口網站右上角的齒輪圖示，然後選取 [還原預設設定]。

- 當您檢視資源或資源群組的屬性時，[移動] 按鈕顯示為停用。 這是預期的行為。 目前不支援在資源群組或訂用帳戶之間移動資源或資源群組。

- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。

- 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 PowerShell 來確認權限。

- 在管理員入口網站的儀表板中，[更新] 圖格無法顯示更新的相關資訊。 若要解決此問題，請按一下該圖格來重新整理它。

-   在管理員入口網站中，您可能會看到 Microsoft.Update.Admin 元件的重大警示。 警示名稱、說明及補救方式全部會顯示為：  
    - *錯誤 - 遺失 FaultType ResourceProviderTimeout 的範本。*

    您可以放心地忽略此警示。 

- <!-- 2253274 --> In the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and  manage this information.

- 在管理員入口網站和使用者入口網站中，當您使用舊版 API (例如：2015-06-15) 建立的儲存體帳戶選取 [概觀] 刀鋒視窗時，[概觀] 刀鋒視窗無法載入。 這包括系統儲存體帳戶，例如修補和更新期間所使用的 **updateadminaccount**。 

  因應措施是，使用 PowerShell 來執行 **Start-ResourceSynchronization.ps1** 指令碼，以還原對儲存體帳戶詳細資料的存取權。 [該指令碼可從 GitHub 取得]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)，而且必須在具有特殊權限的端點上使用服務管理員認證執行。 

- [服務健全狀況] 刀鋒視窗無法載入。 當您在管理員或使用者入口網站中開啟 [服務健全狀況] 刀鋒視窗時，Azure Stack 顯示錯誤而未載入資訊。 這是預期行為。 雖然您可以選取並開啟 [服務健全狀況]，不過此功能尚無法供使用，但在未來的 Azure Stack 版本中會實作此功能。


#### <a name="health-and-monitoring"></a>健康情況和監視
更新為 1802 之後沒有已知問題。

#### <a name="marketplace"></a>Marketplace
- 使用者不需訂用帳戶就能瀏覽完整的市集，而且將會看到如方案和供應項目的管理項目。 對使用者而言，這些都是非功能性項目。

#### <a name="compute"></a>計算
- 無法在入口網站中使用虛擬機器擴展集的調整設定。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) 作為因應措施。 由於 PowerShell 版本差異，您必須使用 `-Name` 參數，而不是 `-VMScaleSetName`。

- <!-- 2290877  --> You cannot scale up a virtual machine scale set (VMSS) that was created when using Azure Stack prior to version 1802. This is due to the change in support for using availability sets with virtual machine scale sets. This support was added with version 1802.  When you attempt to add additional instances to scale a VMSS that was created prior to this support being added, the action fails with the message *Provisioning state failed*. 

  版本 1803 已經修正這個問題。 若要解決版本 1802 的這個問題，請安裝 Azure Stack Hotfix **1.0.180302.4**。 如需詳細資訊，請參閱 [KB 4131152：現有虛擬機器擴展集可能無法使用]( https://support.microsoft.com/help/4131152)。 

- Azure Stack 支援僅使用固定類型的 VHD。 Azure Stack 上有些透過市集提供的影像使用動態 VHD，但這些影像已被移除。 對已連結動態磁碟的虛擬機器 (VM) 調整大小，會導致 VM 處於失敗狀態。

  若要解決這個問題，請刪除 VM，但不要刪除 VM 的磁碟 (儲存體帳戶中的 VHD Blob)。 接著，將 VHD 從動態磁碟轉換成固定磁碟，然後重新建立虛擬機器。

- 當您移至 [新增] > [計算] > [可用性設定組] 在入口網站中建立可用性設定組時，您只能以一個容錯網域和一個更新網域建立可用性設定組。 因應措施是在建立新的虛擬機器時，使用 PowerShell、CLI 或從入口網站建立可用性設定組。

- 當您在 Azure Stack 使用者入口網站上建立虛擬機器時，入口網站所顯示可連結至 DS 系列 VM 的資料磁碟數目會不正確。 DS 系列 VM 可容納與 Azure 設定數目一樣多的資料磁碟。

- 當 VM 映像建立失敗時，可能會在 VM 映像計算刀鋒視窗上新增一個您無法刪除的失敗項目。

  因應措施是，建立一個具有虛擬 VHD 的新 VM 映像，您可以透過 Hyper-V 建立此 VHD (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB)。 此程序應該可以修正防止刪除失敗項目的問題。 接著，在建立虛擬映像後的 15 分鐘之後，您便可以順利刪除它。

  您可以接著嘗試下載先前失敗的 VM 映像。

-  如果在 VM 部署上佈建延伸模組所花費的時間太長，使用者應該讓佈建逾時，而不是嘗試停止程序來將 VM 解除配置或刪除。  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  




#### <a name="networking"></a>網路
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
 - 繼續作業前，請檢閱這些版本資訊開頭處的[開始之前](#before-you-begin)所說明的重要注意事項。
- 最多可能需要一個小時的時間，使用者才能在新的 SQL 或 MySQL 部署中建立資料庫。

- 僅支援資源提供者在裝載 SQL 或 MySQL 的伺服器上建立項目。 在不是由資源提供者建立的主機伺服器上建立項目，可能會導致不相符的狀態。  


> [!NOTE]  
> 在更新為 Azure Stack 1802 後，您可以繼續使用先前部署的 SQL 和 MySQL 資源提供者。  建議您在新版本可供使用時更新 SQL 和 MySQL。 如同 Azure Stack，請循序套用 SQL 和 MySQL 資源提供者的更新。  例如，如果您使用 1710 版，請先套用 1711 版、再套用 1712，然後更新為 1802。      
>   
> 安裝更新 1802 並不會影響使用者目前對 SQL 或 MySQL 資源提供者的使用。
> 無論您使用的資源提供者版本為何，使用者在其資料庫中的資料都會保持原狀，且仍可供存取。    


#### <a name="app-service"></a>App Service 方案
- 使用者必須在於訂用帳戶中建立第一個 Azure 函式之前，先註冊儲存體資源提供者。

- 為了向外延展基礎結構 (背景工作、管理、前端角色)，您必須如「計算」的版本資訊所述，使用 PowerShell。

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>從 GitHub 下載 Azure Stack 工具
- 使用 *invoke-webrequest* PowerShell Cmdlet 從 GitHub 下載 Azure Stack 工具時，您會收到下列錯誤：     
    -  *invoke-webrequest: 要求已經中止: 無法建立 SSL/TLS 的安全通道。*     

  之所以發生此錯誤，是因為新近的 GitHub 支援取代 Tlsv1 和 Tlsv1.1 密碼編譯標準 (PowerShell 的預設值)。 如需詳細資訊，請參閱[弱式密碼編譯標準移除通知](https://githubengineering.com/crypto-removal-notice/)。

  若要解決此問題，請將 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` 新增至指令碼頂端，以強制 PowerShell 主控台在從 GitHub 存放庫下載時使用 TLSv1.2。


## <a name="download-the-update"></a>下載更新
您可以從[這裡](https://aka.ms/azurestackupdatedownload)下載 Azure Stack 1802 更新套件。


## <a name="more-information"></a>詳細資訊
Microsoft 已提供一個方式，可使用與更新 1710 搭配安裝的特殊權限結束點 (PEP) 來監視及繼續更新。

- 請參閱[使用具有特殊權限的端點文件來監視 Azure Stack 中的更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update)。

## <a name="see-also"></a>另請參閱

- 如需 Azure Stack 中的更新管理概觀，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md)。
- 如需如何使用 Azure Stack 套用更新的相關資訊，請參閱[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)。
