---
title: Azure Stack 1804 更新 | Microsoft Docs
description: 了解適用於 Azure Stack 整合式系統之 1804 更新的新功能、已知問題，以及可下載更新的位置。
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
ms.date: 08/01/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 0190298cbf6352feeb71e365f5815e174c9e30cc
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413509"
---
# <a name="azure-stack-1804-update"></a>Azure Stack 1804 更新

「適用於：Azure Stack 整合系統」

本文說明 1804 更新套件中的改良功能與修正、此版本的已知問題，以及可下載更新的位置。 已知問題分為直接與更新程序相關的問題，以及組建 (安裝後) 的相關問題。

> [!IMPORTANT]        
> 這個更新程式封裝僅適用於 Azure Stack 整合系統。 請勿將此更新套件套用至 Azure Stack 開發套件。

## <a name="build-reference"></a>建置參考    
Azure Stack 1804 更新組建編號為 **20180513.1**。   

### <a name="new-features"></a>新功能
此更新包含下列適用於 Azure Stack 的改良功能。

- <!-- 15028744 - IS -->  **Visual Studio 支援使用 AD FS 進行已中斷連線的 Azure Stack 部署**。 您現在可以 Visual Studio 中使用 AD FS 同盟使用者認證來新增訂用帳戶和驗證。 
 
- <!-- 1779474, 1779458 - IS --> **使用 Av2 和 F 系列虛擬機器**. Azure Stack 現在可以根據 Av2 系列和 F 系列虛擬機器大小使用虛擬機器。 如需詳細資訊，請參閱 [Azure Stack 中支援的虛擬機器大小](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)。 

- <!-- 1759172 - IS, ASDK --> **新的系統管理訂用帳戶**。 1804 版的入口網站中有兩個新的可用訂用帳戶類型。 這些新的訂用帳戶類型會新增至預設提供者訂用帳戶，從 1804 版開始，新的 Azure Stack 安裝中就能看見這些類型。 請勿在這一版 Azure Stack 中使用這些新的訂用帳戶類型。 我們會在未來的更新中，宣佈開放使用這些訂用帳戶類型。 

  如果您將 Azure Stack 更新為 1804 版，則無法看見兩個新的訂用帳戶類型。 不過，新的 Azure Stack 整合系統部署和 Azure Stack 開發套件 1804 版或更新版本的安裝，則可以存取所有訂用帳戶類型 (共三個)。  

  這些新的訂用帳戶類型是較大變更的一部分，目的是要保護預設提供者訂用帳戶，並讓您更輕鬆地部署共用資源，例如 SQL 主控伺服器。 當我們在 Azure Stack 中新增這較大變更的更多部分 (含有未來更新) 時，這些新的訂用帳戶類型下所部署的資源可能會遺失。 

  現在可見的三個訂用帳戶類型如下：  
  - 預設提供者訂用帳戶：繼續使用此訂用帳戶類型。 
  - 計量訂用帳戶：請勿使用此訂用帳戶類型。
  - 取用訂用帳戶：請勿使用此訂用帳戶類型

  



## <a name="fixed-issues"></a>已修正的問題

- <!-- IS, ASDK --> 您再也不必先重新整理 [更新] 圖格，系統管理員入口網站就會顯示資訊。
 
- <!-- 2050709  --> 您現在可以使用系統管理員入口網站，來編輯 Blob 服務、表格服務和佇列服務的儲存體計量。
 
- <!-- IS, ASDK --> 當您在 [網路] 下按一下 [連線] 來設定 VPN 連線時，目前可用的選項就只有 [站對站 (IPsec)]。

- 針對效能、穩定性、安全性和 Azure Stack 所使用的作業系統提供了**多項修正**。

## <a name="additional-releases-timed-with-this-update"></a>與此更新同時發佈的其他項目  
以下項目現在可用，但是不需要 Azure Stack 更新 1804。
- **Microsoft Azure Stack System Center Operations Manager 監視組件的更新**。 適用於 Azure Stack 的新版本 (1.0.3.0) Microsoft System Center Operations Manager 監視套件，現在可供[下載](https://www.microsoft.com/download/details.aspx?id=55184)。 使用此版本，您可以在您新增已連線的 Azure Stack 部署時使用服務主體。 此版本也提供更新管理體驗，可讓您直接從 Operations Manager 中採取補救措施。 也有新的儀表板，會顯示資源提供者、縮放單位以及縮放單位節點。

- **新的 Azure Stack 管理員 PowerShell 1.3.0 版**。  Azure Stack PowerShell 1.3.0 現在可供安裝。 這個版本會為所有管理員資源提供者，提供用來管理 Azure Stack 的命令。  有了這個版本，我們就會從 Azure Stack Tools GitHub [存放庫](https://github.com/Azure/AzureStack-Tools)取代部分內容。 

   如需安裝詳細資訊，請遵循 Azure Stack 模組 1.3.0 的[指示](azure-stack-powershell-install.md)或[說明](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0)內容。 

- **初始版本的 Azure Stack API Rest 參考**。 [所有 Azure Stack 管理員資源提供者的 API 參考](https://docs.microsoft.com/rest/api/azure-stack/)現已發行。 


## <a name="before-you-begin"></a>開始之前    

### <a name="prerequisites"></a>必要條件
- 請先安裝 Azure Stack [1803 更新](azure-stack-update-1803.md)，然後才套用 Azure Stack 1804 更新。  
  
- 安裝最新的可用 [1803 版更新或 Hotfix](azure-stack-update-1803.md#post-update-steps)。 


### <a name="known-issues-with-the-update-process"></a>關於更新程序的已知問題   
- 在安裝 1804 更新期間，您可能會看到警示，標題為「錯誤 - FaultType UserAccounts.New 的範本已遺失」。  您可以放心地忽略這些警示。 在 1804 更新完成之後，這些警示會自動關閉。   
 
- <!-- TBD - IS --> 在此更新安裝期間，請勿嘗試建立虛擬機器。 如需管理更新的詳細資訊，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md#plan-for-updates)。


### <a name="post-update-steps"></a>更新後步驟
在安裝 1804 之後，安裝任何適用的 Hotfix。 如需詳細資訊，請檢視下列知識庫文章，以及我們的[服務原則](azure-stack-servicing-policy.md)。  
 - [KB 4344114 - Azure Stack Hotfix 1.0.180527.15](https://support.microsoft.com/help/4344114)。




### <a name="known-issues-post-installation"></a>已知問題 (安裝後)
以下是組建 **20180513.1**的安裝後已知問題。

#### <a name="portal"></a>入口網站
- <!-- TBD - IS ASDK --> 您無法搭配此版本的 Azure Stack 使用 OEM 延伸模組套件來套用驅動程式更新。  此問題目前沒有因應方式。

- <!-- 1272111 - IS --> 在安裝或更新為此 Azure Stack 版本之後，您可能無法在管理入口網站中檢視 Azure Stack 縮放單位。  
  因應措施：使用 PowerShell 來檢視縮放單位的相關資訊。 如需詳細資訊，請參閱 Azure Stack 模組 1.3.0 的[說明](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0)內容。 

- <!-- 2332636 - IS --> 當您的 Azure Stack 身分識別系統使用 AD FS，而您又更新為這一版的 Azure Stack 時，預設提供者訂用帳戶的預設擁有者會重設為內建的 **CloudAdmin** 使用者。  
  因應措施：若要在安裝此更新後解決此問題，請使用[觸發自動化以在 Azure Stack 中設定宣告提供者信任](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1)程序中的步驟 3，來重設預設提供者訂用帳戶的擁有者。   

- <!-- TBD - IS ASDK --> 某些系統管理訂用帳戶類型無法使用。  當您將 Azure Stack 升級至此版本時，主控台中並未顯示兩個 [1804 版所導入的](#new-features)訂用帳戶類型。 這是預期行為。 無法使用的訂用帳戶類型為「計量訂用帳戶」和「取用訂用帳戶」。 自 1804 版起，這些訂用帳戶類型就已經會在新的 Azure Stack 環境中顯示，只是還未就緒以供使用。 請繼續使用「預設提供者」訂用帳戶類型。  


- <!-- TBD -  IS ASDK -->無法在系統管理員入口網站內，[從下拉式清單開啟新的支援要求](azure-stack-manage-portals.md#quick-access-to-help-and-support)。 請改用下列連結：     
    - 針對 Azure Stack 整合式系統，請使用 https://aka.ms/newsupportrequest。

- <!-- 2403291 - IS ASDK --> 您可能還無法使用系統管理員和使用者入口網站底部的水平捲軸。 如果您無法存取水平捲軸，請使用階層連結來瀏覽至入口網站中的上一個刀鋒視窗，方法是從入口網站左上角的階層連結清單中，選取您想要檢視的刀鋒視窗名稱。
  ![階層連結](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> 可能無法在管理員入口網站中檢視計算或儲存體資源。 之所以發生此問題，是因為在更新安裝期間發生錯誤，且誤將更新報告為成功。 如果發生此問題，請連絡 Microsoft 客戶支援服務以尋求協助。

- <!-- TBD - IS --> 您可能會在入口網站中看到空白的儀表板。 若要復原儀表板，請選取位於入口網站右上角的齒輪圖示，然後選取 [還原預設設定]。

- <!-- TBD - IS ASDK --> 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。

- <!-- TBD - IS ASDK --> 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 PowerShell 來確認權限。

- <!-- TBD - IS ASDK --> 在管理員入口網站中，您可能會看到 Microsoft.Update.Admin 元件的重大警示。 警示名稱、說明及補救方式全部會顯示為：  
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
 

#### <a name="compute"></a>計算
- <!-- TBD - IS --> 在為虛擬機器部署選取虛擬機器大小時，某些 F 系列的 VM 大小不會在您建立 VM 時顯示在大小選取器中。 選取器中不會顯示下列 VM 大小：*F8s_v2*、*F16s_v2*、*F32s_v2* 和 *F64s_v2*。  
  因應措施是使用下列其中一個方法來部署虛擬機器。 在每個方法中，您需要指定想要使用的虛擬機器大小。

  - **Azure Resource Manager 範本：** 當您使用範本時，請將範本中的 *vmSize* 設定為等於想要的虛擬機器大小。 例如，使用以下項目來部署會使用 *F32s_v2* 大小的虛擬機器：  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI：** 您可以使用 [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 命令，並且指定虛擬機器大小作為參數，類似於 `--size "Standard_F32s_v2"`。

  - **PowerShell：** 使用 PowerShell，您可以使用 [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) 搭配指定虛擬機器大小的參數，類似於 `-VMSize "Standard_F32s_v2"`。


- <!-- TBD - IS ASDK --> 無法在入口網站中使用虛擬機器擴展集的調整設定。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) 作為因應措施。 由於 PowerShell 版本差異，您必須使用 `-Name` 參數，而不是 `-VMScaleSetName`。

- <!-- TBD - IS --> 當您移至 [新增] > [計算] > [可用性設定組] 在入口網站中建立可用性設定組時，您只能以一個容錯網域和一個更新網域建立可用性設定組。 因應措施是在建立新的虛擬機器時，使用 PowerShell、CLI 或從入口網站建立可用性設定組。

- <!-- TBD - IS ASDK --> 當您在 Azure Stack 使用者入口網站上建立虛擬機器時，入口網站所顯示可連結至 D 系列 VM 的資料磁碟數目會不正確。 所有受支援的 D 系列 VM 均可容納與 Azure 設定數目一樣多的資料磁碟。

- <!-- TBD - IS ASDK --> 當 VM 映像建立失敗時，可能會在 VM 映像計算刀鋒視窗上新增一個您無法刪除的失敗項目。

  因應措施是，建立一個具有虛擬 VHD 的新 VM 映像，您可以透過 Hyper-V 建立此 VHD (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB)。 此程序應該可以修正防止刪除失敗項目的問題。 接著，在建立虛擬映像後的 15 分鐘之後，您便可以順利刪除它。

  您可以接著嘗試下載先前失敗的 VM 映像。

- <!-- TBD - IS ASDK --> 如果在 VM 部署上佈建延伸模組所花費的時間太長，使用者應該讓佈建逾時，而不是嘗試停止程序來將 VM 解除配置或刪除。  

- <!-- 1662991 IS ASDK --> Azure Stack 不支援 Linux VM 診斷。 當您部署啟用了 VM 診斷的 Linux VM 時，部署會失敗。 如果您透過診斷設定啟用 Linux VM 基本計量，部署也會失敗。  


#### <a name="networking"></a>網路
- <!-- 1766332 - IS ASDK --> 如果您在 [網路] 下按一下 [建立 VPN 閘道] 來設定 VPN 連線，系統就會將 [原則式] 列為 VPN 類型。 請勿選取此選項。 Azure Stack 只支援 [路由式] 選項。

- <!-- 2388980 - IS ASDK --> 在建立 VM 並與公用 IP 位址建立關聯之後，您就無法將 VM 與該 IP 位址取消關聯。 取消關聯看似可以運作，但先前指派的公用 IP 位址會繼續與原始 VM 保持關聯。

  目前，您只能將新的公用 IP 位址用於新建立的 VM。

  即使您將 IP 位址重新指派給新的 VM (通常稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。

- <!-- 2292271 - IS ASDK --> 如果供應項目和方案與租用戶訂用帳戶相關聯，而您將屬於供應項目和方案一部分的網路資源提高其配額限制，則新的限制不會套用到該訂用帳戶。 不過，新的限制會套用到配額增加後所建立的新訂用帳戶中。 

  若要解決這個問題，請使用附加方案，在方案已與訂用帳戶產生關聯時，增加網路配額。 如需詳細資訊，請參閱如何[提供附加方案](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)。

- <!-- 2304134 IS ASDK --> 您無法刪除有相關聯 DNS 區域資源或路由表資源的訂用帳戶。 若要成功刪除訂用帳戶，您必須先從租用戶訂用帳戶中刪除 DNS 區域和路由表資源。 
  

- <!-- 1902460 - IS ASDK --> Azure Stack 支援每一 IP 位址有一個「區域網路閘道」。 這適用於所有租用戶訂用帳戶。 建立第一個區域網路閘道連線之後，後續若嘗試使用相同的 IP 位址來建立區域網路閘道資源，就會被封鎖。

- <!-- 16309153 - IS ASDK --> 在 DNS 伺服器設定為 [自動] 的已建立虛擬網路上，變更成自訂 DNS 伺服器時失敗。 更新的設定並未推送至該 Vnet 中的 VM。

- <!-- TBD - IS ASDK --> Azure Stack 不支援在部署 VM 之後將額外的網路介面新增至 VM 執行個體。 如果 VM 需要多個網路介面，必須在部署階段就定義這些網路介面。

- <!-- 2096388 IS --> 您無法使用系統管理員入口網站來更新網路安全性群組的規則。 

    App Service 的因應措施：如果您需要透過遠端桌面連線至「控制器」執行個體，則需使用 PowerShell 來修改網路安全性群組內的安全性規則。  以下是一些範例，說明如何「允許」，然後再將設定還原至「拒絕」：  
    
    - *允許：*
 
      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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

- <!-- TBD - IS --> 僅支援資源提供者在裝載 SQL 或 MySQL 的伺服器上建立項目。 在不是由資源提供者建立的主機伺服器上建立項目，可能會導致不相符的狀態。  

- <!-- IS, ASDK --> 當您為 SQL 和 MySQL 資源提供者建立 SKU 時，[系列] 或 [層] 名稱不支援特殊字元，包括空格和句點。


> [!NOTE]  
> <!-- TBD - IS --> 在更新至 Azure Stack 1804 之後，您可以繼續使用先前部署的 SQL 和 MySQL 資源提供者。  建議您在新版本可供使用時更新 SQL 和 MySQL。 如同 Azure Stack，請循序套用 SQL 和 MySQL 資源提供者的更新。  例如，如果您使用 1802 版，請先套用 1803 版，再更新至 1804 版。      
>   
> 安裝更新 1804 並不會影響使用者目前對 SQL 或 MySQL 資源提供者的使用。
> 無論您使用的資源提供者版本為何，使用者在其資料庫中的資料都會保持原狀，且仍可供存取。    



#### <a name="app-service"></a>App Service 方案
- <!-- 2352906 - IS ASDK --> 使用者必須在於訂用帳戶中建立第一個 Azure 函式之前，先註冊儲存體資源提供者。

- <!-- TBD - IS ASDK --> 為了向外延展基礎結構 (背景工作、管理、前端角色)，您必須如「計算」的版本資訊所述，使用 PowerShell。

- <!-- TBD - IS ASDK --> 目前，App Service 只能部署到「預設提供者訂用帳戶」。  在未來的更新中，App Service 會部署至 Azure Stack 1804 所導入的新計量訂用帳戶，而且現有部署也全都會移轉至這個新的訂用帳戶。

#### <a name="usage"></a>使用量  
- <!-- TBD - IS ASDK --> 使用量公用 IP 位址使用計量資料會針對每一筆記錄顯示相同的 EventDateTime 值，而不是建立記錄時顯示的 TimeDate 時間戳記。 目前，您無法使用這項資料來執行公用 IP 位址使用狀況的正確計量。


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>下載更新
您可以從[這裡](https://aka.ms/azurestackupdatedownload)下載 Azure Stack 1804 更新套件。


## <a name="see-also"></a>另請參閱
- 若要使用具有特殊權限的端點 (PEP) 來監視和繼續更新，請參閱[使用具有特殊權限的端點來監視 Azure Stack 中的更新](azure-stack-monitor-update.md)。
- 如需 Azure Stack 中的更新管理概觀，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md)。
- 如需如何使用 Azure Stack 套用更新的相關資訊，請參閱[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)。
