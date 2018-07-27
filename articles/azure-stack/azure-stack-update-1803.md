---
title: Azure Stack 1803 更新 | Microsoft Docs
description: 了解適用於 Azure Stack 整合式系統之 1803 更新的新功能、已知問題，以及可下載更新的位置。
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 11430a0d194a722c0c0520c936db3c08b1a6b863
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989566"
---
# <a name="azure-stack-1803-update"></a>Azure Stack 1803 更新

「適用於：Azure Stack 整合系統」

本文說明 1803 更新套件中的改良功能與修正、此版本的已知問題，以及可從何處下載更新。 已知問題分為直接與更新程序相關的問題，以及組建 (安裝後) 的相關問題。

> [!IMPORTANT]        
> 這個更新程式封裝僅適用於 Azure Stack 整合系統。 請勿將此更新套件套用至 Azure Stack 開發套件。

## <a name="build-reference"></a>建置參考    
Azure Stack 1803 更新組建編號為 **20180329.1**。


## <a name="before-you-begin"></a>開始之前    
> [!IMPORTANT]    
> 在此更新安裝期間，請勿嘗試建立虛擬機器。 如需管理更新的詳細資訊，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md#plan-for-updates)。


### <a name="prerequisites"></a>必要條件
- 先安裝 Azure Stack [1802 更新](azure-stack-update-1802.md)，然後才套用 Azure Stack 1803 更新。   

- 您必須先套用 Azure Stack 1803 更新，才能安裝 **AzS Hotfix – 1.0.180312.1- Build 20180222.2**。 此 Hotfix 會更新 Windows Defender，您可在下載 Azure Stack 的更新時取得此 Hotfix。

  若要安裝 Hotfix，請遵循[安裝 Azure Stack 的更新](azure-stack-apply-updates.md)的一般程序。 更新的名稱會顯示為 **AzS Hotfix – 1.0.180312.1**，並且包含下列檔案： 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  將這些檔案上傳至儲存體帳戶和容器之後，請從管理入口網站中的 [更新] 圖格執行安裝。 
  
  不同於 Azure Stack 的更新，安裝此更新並不會變更 Azure Stack 的版本。 若要確認此更新已安裝，請檢視**已安裝的更新**清單。



### <a name="new-features"></a>新功能 
此更新包含下列適用於 Azure Stack 的改良功能與修正。

- **更新 Azure Stack 祕密** - (帳戶和憑證)。 如需有關管理祕密的詳細資訊，請參閱[在 Azure Stack 中輪替使用祕密](azure-stack-rotate-secrets.md)。 

- 當您使用 HTTP 來存取系統管理員和使用者入口網站時，<!-- 1914853 --> **自動重新導向到 HTTPS**。 這項改進根據的是 Azure Stack 的 [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) 意見反應。 

- <!-- 2202621  --> **存取 Marketplace** – 您現在可以從系統管理員和使用者入口網站內使用 [[+新增](https://ms.portal.azure.com/#create/hub)] 選項來開啟 Azure Stack Marketplace，就像在 Azure 入口網站中所做的一樣。
 
- <!-- 2202621 --> **Azure 監視器** - Azure Stack 將 [Azure 監視器](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) 新增至系統管理員和使用者入口網站。 這包括新的計量和活動記錄總管。 若要從外部網路存取這個「Azure 監視器」，必須在防火牆設定中開啟連接埠 **13012**。 如需有關 Azure Stack 所需連接埠的詳細資訊，請參閱 [Azure Stack 資料中心整合 - 發佈端點](azure-stack-integrate-endpoints.md)。

   此外，由於這項變更，[更多服務] 底下的 [稽核記錄] 現在已顯示為 [活動記錄]。 此功能現在已與 Azure 入口網站一致。 

- <!-- 1664791 --> **疏鬆檔案** - 當您將新的映像新增至 Azure Stack，或透過市集摘要整合新增映像時，該映像會轉換成疏鬆檔案。 在使用 Azure Stack 1803 版之前新增的映像無法轉換。 取而代之的是，您必須使用市集摘要整合來重新提交這些映像，才能利用此功能。 
 
   疏鬆檔案是一種有效率的檔案格式，可用來減少儲存體空間的使用及改善 I/O。  如需詳細資訊，請參閱適用於 Windows Server 的 [Fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) \(英文\)。 

### <a name="fixed-issues"></a>已修正的問題

- <!-- 1739988 --> 「內部負載平衡」(ILB) 現在會以正確方式處理後端 VM 的 MAC 位址，使得 ILB 在使用後端網路上的 Linux 執行個體時，會將封包丟棄至後端網路。 ILB 可與後端網路上的 Windows 執行個體正常搭配運作。 

- <!-- 1805496 --> Azure Stack 之間的 VPN 連線會因 Azure Stack 使用與 Azure 不同的 IKE 原則而中斷的問題。 SALifetime (時間) 和 SALiftetime (位元組) 與 Azure 不相容，而已在 1803 版中變更為符合 Azure 設定。 1803 版之前的 SALifetime (秒) 是 14,400，現在於 1803 版中已變更為 27,000。 1803 版之前的 SALifetime (位元組) 是 819,200，現在於 1803 版中已變更為 33,553,408。

- <!-- 2209262 --> 先前在入口網站中可以看見 VPN 連線，但啟用或切換 [IP 轉送] 功能卻沒有任何作用的 IP 問題。 此功能預設為開啟，而尚不支援變更此設定。  已從入口網站中移除此控制項。 

- <!-- 1766332 --> 雖然入口網站中有「原則型 VPN 閘道」的選項，但 Azure Stack 不支援此閘道。  已從入口網站中移除此選項。 

- <!-- 1868283 --> Azure Stack 現在會針對搭配動態磁碟建立的虛擬機器防止調整大小。 

- <!-- 1756324 --> 虛擬機器的使用量資料現在以每小時間隔來區分。 這與 Azure 一致。 

- <!--  2253274 --> 在系統管理員和使用者入口網站中無法載入「vNet 子網路」之 [設定] 刀鋒視窗的問題。 因應之道是使用 PowerShell 和 [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) Cmdlet 來檢視和管理此資訊。

- 在建立虛擬機器期間，為 VM 大小選擇大小時，已不再會出現「無法顯示價格」訊息。

- 針對效能、穩定性、安全性和 Azure Stack 所使用的作業系統提供了多項修正。


### <a name="changes"></a>變更
- 將新建立之供應項目的狀態從「私人」變更為「公用」或「解除委任」的方式已變更。 如需詳細資訊，請參閱[建立供應項目](azure-stack-create-offer.md)。


### <a name="known-issues-with-the-update-process"></a>關於更新程序的已知問題    
<!-- 2328416 --> 在安裝 1803 更新的期間，Blob 服務和使用 Blob 服務的內部服務可能會發生停機。 這包括一些虛擬機器作業。 此停機情況可能會造成租用戶作業失敗，或無法存取資料的服務發出警示。 安裝完更新之後，此問題就會自行解決。 



### <a name="post-update-steps"></a>更新後步驟
- 在安裝 1803 之後，安裝任何適用的 Hotfix。 如需詳細資訊，請檢視下列知識庫文章，以及我們的[服務原則](azure-stack-servicing-policy.md)。

  - [KB 4344115 - Azure Stack Hotfix 1.0.180427.15](https://support.microsoft.com/help/4344115).

- 安裝此更新之後，請檢閱防火牆設定，確定已開啟[必要的連接埠](azure-stack-integrate-endpoints.md)。 例如，此更新會導入「Azure 監視器」，這包括將稽核記錄變更為活動記錄。 而這項變更現在會使用連接埠 13012，您也必須開啟此連接埠。  


### <a name="known-issues-post-installation"></a>已知問題 (安裝後)
以下是組建 **20180323.2**的安裝後已知問題。

#### <a name="portal"></a>入口網站
- <!-- 2332636 - IS --> 當您的 Azure Stack 身分識別系統使用 AD FS，而您又更新為這一版的 Azure Stack 時，預設提供者訂用帳戶的預設擁有者會重設為內建的 **CloudAdmin** 使用者。  
  因應措施：若要在安裝此更新後解決此問題，請使用[觸發自動化以在 Azure Stack 中設定宣告提供者信任](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1)程序中的步驟 3，來重設預設提供者訂用帳戶的擁有者。   

- 無法從系統管理員入口網站內，[從下拉式清單開啟新支援要求](azure-stack-manage-portals.md#quick-access-to-help-and-support)。 請改用下列連結：     
    - 針對 Azure Stack 整合式系統，請使用 https://aka.ms/newsupportrequest。

- <!-- 2050709 --> 在系統管理員入口網站中，無法編輯 Blob 服務、表格服務或佇列服務的儲存體計量。 當您移至 [儲存體]，然後選取 Blob、表格或佇列服務的圖格時，會隨即開啟新的刀鋒視窗，其中會顯示該項服務的計量圖表。 如果您接著從 [計量圖表] 圖格上方選取 [編輯]，[編輯圖表] 刀鋒視窗會隨即開啟，但不會顯示用來編輯計量的選項。

- 可能無法在管理員入口網站中檢視計算或儲存體資源。 之所以發生此問題，是因為在更新安裝期間發生錯誤，且誤將更新報告為成功。 如果發生此問題，請連絡 Microsoft 客戶支援服務以尋求協助。

- 您可能會在入口網站中看到空白的儀表板。 若要復原儀表板，請選取位於入口網站右上角的齒輪圖示，然後選取 [還原預設設定]。

- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。

- 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 PowerShell 來確認權限。

- 在管理員入口網站的儀表板中，[更新] 圖格無法顯示更新的相關資訊。 若要解決此問題，請按一下該圖格來重新整理它。

- 在管理員入口網站中，您可能會看到 *Microsoft.Update.Admin* 元件的重大警示。 警示名稱、說明及補救方式全部會顯示為：  
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
- 無法在入口網站中使用虛擬機器擴展集的調整設定。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) 作為因應措施。 由於 PowerShell 版本差異，您必須使用 `-Name` 參數，而不是 `-VMScaleSetName`。

- 當您移至 [新增] > [計算] > [可用性設定組] 在入口網站中建立可用性設定組時，您只能以一個容錯網域和一個更新網域建立可用性設定組。 因應措施是在建立新的虛擬機器時，使用 PowerShell、CLI 或從入口網站建立可用性設定組。

- 當您在 Azure Stack 使用者入口網站上建立虛擬機器時，入口網站所顯示可連結至 D 系列 VM 的資料磁碟數目會不正確。 所有受支援的 D 系列 VM 均可容納與 Azure 設定數目一樣多的資料磁碟。

- 當 VM 映像建立失敗時，可能會在 VM 映像計算刀鋒視窗上新增一個您無法刪除的失敗項目。

  因應措施是，建立一個具有虛擬 VHD 的新 VM 映像，您可以透過 Hyper-V 建立此 VHD (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB)。 此程序應該可以修正防止刪除失敗項目的問題。 接著，在建立虛擬映像後的 15 分鐘之後，您便可以順利刪除它。

  您可以接著嘗試下載先前失敗的 VM 映像。

-  如果在 VM 部署上佈建延伸模組所花費的時間太長，使用者應該讓佈建逾時，而不是嘗試停止程序來將 VM 解除配置或刪除。  

- <!-- 1662991 --> Azure Stack 不支援 Linux VM 診斷。 當您部署啟用了 VM 診斷的 Linux VM 時，部署會失敗。 如果您透過診斷設定啟用 Linux VM 基本計量，部署也會失敗。  


#### <a name="networking"></a>網路功能
- 在建立 VM 並與公用 IP 位址建立關聯之後，您就無法將 VM 與該 IP 位址取消關聯。 取消關聯看似可以運作，但先前指派的公用 IP 位址會繼續與原始 VM 保持關聯。

  目前，您只能將新的公用 IP 位址用於新建立的 VM。

  即使您將 IP 位址重新指派給新的 VM (通常稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。



- Azure Stack 支援每一 IP 位址有一個「區域網路閘道」。 這適用於所有租用戶訂用帳戶。 建立第一個區域網路閘道連線之後，後續若嘗試使用相同的 IP 位址來建立區域網路閘道資源，就會被封鎖。

- 在 DNS 伺服器設定為 [自動] 的已建立虛擬網路上，變更成自訂 DNS 伺服器時失敗。 更新的設定並未推送至該 Vnet 中的 VM。

- Azure Stack 不支援在部署 VM 之後將額外的網路介面新增至 VM 執行個體。 如果 VM 需要多個網路介面，必須在部署階段就定義這些網路介面。

- <!-- 2096388 --> 您無法使用系統管理員入口網站來更新網路安全性群組的規則。 

    App Service 的因應措施：如果您需要透過遠端桌面連線至「控制器」執行個體，則需使用 PowerShell 來修改網路安全性群組內的安全性規則。  以下是一些範例，說明如何「允許」，然後再將設定還原至「拒絕」：  
    
    - *允許：*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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

- <!-- IS, ASDK --> 當您為 SQL 和 MySQL 資源提供者建立 SKU 時，[系列] 名稱不支援特殊字元，包括空格和句點。

> [!NOTE]  
> 在更新至 Azure Stack 1803 之後，您可以繼續使用先前部署的 SQL 和 MySQL 資源提供者。  建議您在新版本可供使用時更新 SQL 和 MySQL。 如同 Azure Stack，請循序套用 SQL 和 MySQL 資源提供者的更新。  例如，如果您使用 1711 版，請先套用 1712 版、再套用 1802 版，然後更新至 1803 版。      
>   
> 安裝更新 1803 並不會影響使用者目前對 SQL 或 MySQL 資源提供者的使用。
> 無論您使用的資源提供者版本為何，使用者在其資料庫中的資料都會保持原狀，且仍可供存取。    



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


## <a name="download-the-update"></a>下載更新
您可以從[這裡](https://aka.ms/azurestackupdatedownload)下載 Azure Stack 1803 更新套件。


## <a name="see-also"></a>另請參閱
- 若要使用具有特殊權限的端點 (PEP) 來監視和繼續更新，請參閱[使用具有特殊權限的端點來監視 Azure Stack 中的更新](azure-stack-monitor-update.md)。
- 如需 Azure Stack 中的更新管理概觀，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md)。
- 如需如何使用 Azure Stack 套用更新的相關資訊，請參閱[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)。
