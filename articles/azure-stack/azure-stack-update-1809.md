---
title: Azure Stack 1809 更新 | Microsoft Docs
description: 了解 Azure Stack 整合系統 1809 更新中的新功能，包括已知問題和更新下載位置。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2019
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: f4a1bf9e2fee9278713315c98f25dbc820a553a1
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352403"
---
# <a name="azure-stack-1809-update"></a>Azure Stack 1809 更新

*適用於：Azure Stack 整合式系統*

本文將說明 1809 更新套件的內容。 此更新套件包括此版 Azure Stack 的改良功能、修正及已知問題。 本文也包含連結，讓您下載更新。 已知問題分為直接與更新程序相關的問題，以及組建 (安裝後) 的相關問題。

> [!IMPORTANT]  
> 這個更新程式封裝僅適用於 Azure Stack 整合系統。 請勿將此更新套件套用至 Azure Stack 開發套件。

## <a name="build-reference"></a>建置參考

Azure Stack 1809 更新組建編號為 **1.1809.0.90**。  

### <a name="new-features"></a>新功能

此更新包含下列適用於 Azure Stack 的改良功能：

- 在此版本中，Azure Stack 整合系統支援 4-16 個節點的設定。 您可以使用 [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) 來協助規劃 Azure Stack 容量與設定。

- <!--  2712869   | IS  ASDK -->  **Azure Stack syslog 用戶端 (正式運作)**  此用戶端允許將與 Azure Stack 基礎結構相關的稽核、警示和安全性記錄，轉寄到 Azure Stack 外的 syslog 伺服器或安全性資訊與事件管理 (SIEM) 軟體。 Syslog 用戶端現在支援 syslog 伺服器目前接聽的指定連接埠。

   此版本中，syslog 用戶端已正式運作，且可用於實際執行環境。

   如需詳細資訊，請參閱 [Azure Stack Syslog 轉送](azure-stack-integrate-security.md)。

- 您現在可以在 Azure 上的資源群組之間[移動註冊資源](azure-stack-registration.md#move-a-registration-resource)，而不需要重新註冊。 只要新和舊訂用帳戶都對應至相同的 CSP 合作夥伴識別碼，雲端解決方案提供者 (CSP) 也可以在訂用帳戶之間移動註冊資源。 這不會影響現有的客戶租用戶對應。 

- 已新增可每個網路介面指派多個 IP 位址的支援。  如需詳細資料，請參閱[使用 PowerShell 對虛擬機器指派多個 IP 位址](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-multiple-ip-addresses-powershell)。

### <a name="fixed-issues"></a>已修正的問題

<!-- TBD - IS ASDK -->
- 在入口網站中，記憶體圖表報表免費/已使用容量目前是正確的。 您現在可以更可靠地預測您可以建立的 VM 數量。

<!-- TBD - IS ASDK --> 
- 已修正當您在 Azure Stack 使用者入口網站上建立虛擬機器時，入口網站所顯示可連結至 DS 系列 VM 的資料磁碟數目會不正確的問題。 DS 系列 VM 可容納與 Azure 設定數目一樣多的資料磁碟。

- 下列受控磁碟問題已在 1809 中修正，同時也在 1808 [Azure Stack Hotfix 1.1808.9.117](https://support.microsoft.com/help/4481066/) 中修正： 

   <!--  2966665 – IS, ASDK --> 
   - 已修正將 SSD 資料磁碟連結至進階大小受控磁碟虛擬機器 (DS、DSv2、Fs、Fs_V2) 會失敗並發生錯誤的問題：*無法更新虛擬機器 ‘vmname’ 的磁碟。錯誤：因為對 VM 大小 ‘Standard_DS/Ds_V2/FS/Fs_v2’ 不支援儲存體帳戶類型 ‘Premium_LRS’，所以無法執行要求的作業*。 
   
   - 使用 **createOption** 建立受控磁碟 VM：**連結**失敗，並發生下列錯誤：*長時間執行的作業失敗，狀態為「失敗」。* 其他資訊：「發生內部執行錯誤」。
   ErrorCode：InternalExecutionError ErrorMessage：發生內部執行錯誤。
   
   已修正此問題。

- <!-- 2702741 -  IS, ASDK --> 過去針對使用「動態」配置方法來部署的公用 IP，不保證在發出 Stop-Deallocate 之後還能予以保留，此問題已修正。 現在這些 IP 會予以保留。

- <!-- 3078022 - IS, ASDK --> 如果 VM 在 1808 版本以前停止解除配置，則無法在 1808 更新後重新配置。  此問題已在 1809 版本修正。 經過此次修正，過去在此狀態的執行個體和無法啟動的執行個體將可於 1809 版本中啟動。 此修正也可避免此問題再次發生。

### <a name="changes"></a>變更

<!-- 2635202 - IS, ASDK -->
- 基礎結構備份服務已從[公用基礎結構網路](https://docs.microsoft.com/azure/azure-stack/azure-stack-network#public-infrastructure-network)移至[公用 VIP 網路](https://docs.microsoft.com/azure/azure-stack/azure-stack-network#public-vip-network)。 客戶將必須確定服務已從公用 VIP 網路存取備份儲存體位置。  

> [!IMPORTANT]  
> 若您的防火牆不允許從公用 VIP 網路到檔案伺服器的連線，此變更將會導致基礎結構備份因「錯誤 53 找不到網路路徑。」而失敗。 這是中斷性變更，因此沒有合理的因應措施。 Microsoft 將會以客戶意見反應為基礎在 Hotfix 中還原此變更。 請檢閱[更新後步驟](#post-update-steps)一節以取得 1809 可用之 Hotfix 的詳細資訊。 一旦 Hotfix 推出，只有在您的網路原則不允許公用 VIP 網路存取基礎結構資源的情況下才在更新到 1809 之後套用它。 在 1811 中，此變更將套用到所有系統。 若您在 1809 中套用該 Hotfix，不需要採取進一步的動作。  

### <a name="common-vulnerabilities-and-exposures"></a>常見的弱點與漏洞

此更新會安裝下列安全性更新：  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

如需有關這些弱點的詳細資訊，請按一下上述連結，或參閱 Microsoft 知識庫文章 [4457131](https://support.microsoft.com/help/4457131) 和 [4462917](https://support.microsoft.com/help/4462917)。

### <a name="prerequisites"></a>必要條件

- 在套用 1809 之前，請安裝 1808 的最新 Azure Stack Hotfix。 如需詳細資訊，請參閱 [KB 4481066 – Azure Stack Hotfix Azure Stack Hotfix 1.1808.9.117](https://support.microsoft.com/help/4481066/)。 雖然 Microsoft 建議有最新的 Hotfix 可用，但是安裝 1809 所需的最小版本是 1.1808.5.110。

  > [!TIP]  
  > 訂閱下列 *RRS* 或 *Atom* 摘要，以掌握最新的 Azure Stack Hotfix：
  > - RRS： https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss … 
  > - Atom： https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom …


- 開始安裝此更新之前，請先執行 [Test-AzureStack](azure-stack-diagnostic-test.md) 與下列參數來驗證 Azure Stack 的狀態，並解決所發現的一切運作問題，包括所有警告和失敗。 也請檢閱作用中警示，並將所有需要採取動作的警示解決。  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>關於更新程序的已知問題

- 當您在 1809 更新之後執行 [Test-AzureStack](azure-stack-diagnostic-test.md) 時，來自基礎板管理控制器 (BMC) 的警告訊息隨即顯示。 您可以放心地忽略此警告。

- <!-- 2468613 - IS -->在安裝 1807 更新期間，您可能會看到警示，標題為「錯誤 - FaultType UserAccounts.New 的範本已遺失」  您可以放心地忽略這些警示。 在此更新完成之後，這些警示會自動關閉。

- <!-- 2489559 - IS --> 在此更新安裝期間，請勿嘗試建立虛擬機器。 如需管理更新的詳細資訊，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md#plan-for-updates)。

- <!-- 3139614 | IS --> 如果您已從 OEM 套用 Azure Stack 更新，那麼 [已有可用的更新] 通知可能不會出現在 Azure Stack 系統管理員入口網站中。 若要安裝 Microsoft 更新，請使用此處[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)的指示，手動下載並匯入它。

### <a name="post-update-steps"></a>更新後步驟

> [!Important]  
> 讓您的 Azure Stack 部署準備使用延伸主機，這是由後續的更新套件所支援。 使用下列指導方針準備您的系統：[Azure Stack 的延伸主機準備](azure-stack-extension-host-prepare.md)。

在安裝此更新之後，安裝任何適用的 Hotfix。 如需詳細資訊，請檢視下列知識庫文章，以及我們的[服務原則](azure-stack-servicing-policy.md)。  
- [KB 4481548 – Azure Stack Hotfix Azure Stack Hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/)  

## <a name="known-issues-post-installation"></a>已知問題 (安裝後)

以下是此組建版本的安裝後已知問題。

### <a name="portal"></a>入口網站

- Azure Stack 技術文件會以最新版本為主。 由於各版本的入口網站會有所變更，您在使用 Azure Stack 入口網站時所看到的項目，可能與您在文件中所看到的內容不同。

<!-- 2930718 - IS ASDK --> 
- 在系統管理員入口網站中存取任何使用者訂用帳戶的詳細資料時，在關閉刀鋒視窗之後按一下 [最近]，使用者訂用帳戶名稱未顯示。

<!-- 3060156 - IS ASDK --> 
- 在系統管理員和使用者入口網站中，按一下入口網站設定，然後選取 [刪除所有設定和私人儀表板]，並未如預期運作。 錯誤通知隨即顯示。 

<!-- 2930799 - IS ASDK --> 
- 在系統管理員和使用者入口網站中，[所有服務] 底下的 [DDoS 保護計劃] 資產未正確列出。 它無法在 Azure Stack 中使用。 如果您嘗試建立它，則會顯示錯誤，表示入口網站無法建立 Marketplace 項目。 

<!-- 2930820 - IS ASDK --> 
- 在系統管理員和使用者入口網站中，如果您搜尋 "Docker"，項目未正確傳回。 它無法在 Azure Stack 中使用。 如果您嘗試建立它，則會顯示具有錯誤指示的刀鋒視窗。 

<!-- 2967387 – IS, ASDK --> 
- 您用來登入 Azure Stack 系統管理員或使用者入口網站的帳戶，顯示為**無法辨識的使用者**。 當帳戶未指定「名字」或「姓氏」時，就會顯示此訊息。 若要解決此問題，請編輯使用者帳戶，提供「名字」或「姓氏」。 您必須登出，然後再登入回入口網站。  

<!--  2873083 - IS ASDK --> 
-  當您使用入口網站來建立虛擬機器擴展集 (VMSS) 時，如果使用 Internet Explorer，[執行個體大小] 下拉式清單無法正確載入。 若要解決此問題，請在使用入口網站來建立 VMSS 時使用另一個瀏覽器。  

<!-- 2931230 – IS  ASDK --> 
- 無法刪除以附加方案形式新增至使用者訂用帳戶的方案 (即使您從使用者訂用帳戶中移除此方案)。 此方案會持續保留，直到參考附加方案的訂用帳戶也遭到刪除為止。 

<!--2760466 – IS  ASDK --> 
- 當您安裝執行此版本的新 Azure Stack 環境時，可能不會顯示指出「需要啟用」的警示。 必須[啟用](azure-stack-registration.md)，才能使用市集摘要整合。  

<!-- TBD - IS ASDK --> 
- 不應該使用隨 1804 版導入的兩個系統管理訂用帳戶類型。 這些訂用帳戶類型為「計量訂用帳戶」和「取用訂用帳戶」。 自 1804 版起，這些訂用帳戶類型就已經會在新的 Azure Stack 環境中顯示，只是還未就緒以供使用。 請繼續使用「預設提供者」訂用帳戶類型。

<!-- TBD - IS ASDK --> 
- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。

<!-- TBD - IS ASDK --> 
- 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 PowerShell 來確認權限。


### <a name="health-and-monitoring"></a>健康情況和監視

<!-- TBD - IS -->
- 您可能會看到下列警示重複出現，然後在 Azure Stack 系統上消失：
   - *基礎結構角色執行個體無法使用*
   - *縮放單位節點已離線*
   
  執行 [Test-AzureStack](azure-stack-diagnostic-test.md) Cmdlet 來確認基礎結構角色執行個體的健康情況及縮放單位節點。 如果 [Test-AzureStack](azure-stack-diagnostic-test.md) 偵測不到任何問題，可以忽略這些警示。 如果偵測到問題，可以嘗試使用系統管理員入口網站或 PowerShell 啟動基礎結構角色執行個體或節點。

  此問題已在最新 [1809 Hotfix 版本](https://support.microsoft.com/help/4481548/)中修正，所以如果您遇到此問題，請務必安裝此 Hotfix。 

<!-- 1264761 - IS ASDK -->  
- 您可能會看到「健康情況控制器」元件出現具有下列詳細資料的警示：  

   警示 #1：
   - 名稱：基礎結構角色狀況不良
   - 嚴重性：警告
   - 元件：健康情況控制器
   - 描述：健康情況控制器活動訊號掃描器無法使用。 這可能會影響健康情況報告和計量。  

  警示 #2：
   - 名稱：基礎結構角色狀況不良
   - 嚴重性：警告
   - 元件：健康情況控制器
   - 描述：健康情況控制器錯誤掃描器無法使用。 這可能會影響健康情況報告和計量。

  您可以放心地忽略這兩個警示，這兩個警示會在一段時間過後自動關閉。  


<!-- 2812138 | IS --> 
- 您可能會看到具有下列詳細資料的**儲存體**元件警示：

   - 名稱：儲存體服務內部通訊錯誤  
   - 嚴重性：重要  
   - 元件：儲存體  
   - 描述：將要求傳送至下列節點時，發生儲存體服務內部通訊錯誤。  

    您可以放心地忽略此警示，但必須手動關閉此警示。

<!-- 2368581 - IS, ASDK --> 
- 身為 Azure Stack 操作員，如果您收到記憶體不足的警示，而且租用戶虛擬機器無法部署並出現「Fabric VM 建立錯誤」，則可能表示 Azure Stack 戳記的可用記憶體不足。 請使用 [Azure Stack 容量規劃工具](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)來充分了解可供工作負載使用的容量。

### <a name="compute"></a>計算

- 建立 [Dv2 系列 VM](./user/azure-stack-vm-considerations.md#virtual-machine-sizes) 時，D11-14v2 VM 可讓您分別建立 4、8、16 和 32 個資料磁碟。 不過，建立 VM 窗格會顯示 8、16、32 和 64 個資料磁碟。

<!-- 3235634 – IS, ASDK -->
- 若要部署大小包含 **v2** 後置詞的 VM (例如 **Standard_A2_v2**)，請將後置詞指定為 **Standard_A2_v2** (小寫 v)。 請勿使用 **Standard_A2_V2** (大寫 V)。 這適用於全域 Azure，並且在 Azure Stack 上有不一致的問題。

<!-- 3099544 – IS, ASDK --> 
- 當您使用 Azure Stack 入口網站建立新的虛擬機器 (VM) 並選取 VM 大小時，會顯示美元/月欄位和「無法使用」訊息。 此欄位不應該出現；Azure Stack 不支援顯示 VM 定價欄位。

<!-- 2869209 – IS, ASDK --> 
- 當使用 [**Add-AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0) 時，您必須使用 **-OsUri** 參數作為要上傳磁碟的儲存體帳戶 URI。 如果您使用磁碟的本機路徑，此 Cmdlet 就會失敗，且具有下列錯誤：*長時間執行的作業失敗，狀態為「失敗」*。 

<!--  2795678 – IS, ASDK --> 
- 當您使用入口網站以進階 VM 大小 (DS、Ds_v2、FS、FSv2) 建立虛擬機器 (VM) 時，會在標準儲存體帳戶中建立 VM。 在標準儲存體帳戶中建立不會影響功能、IOP 或帳單。 

   您可以安心忽略指出下列訊息的警告：*您選擇在支援進階磁碟的大小上使用標準磁碟。這樣可能會影響作業系統效能，並不建議使用。請考慮改為使用進階儲存體 (SSD)。」*

<!-- 2967447 - IS, ASDK --> 
- 虛擬機器擴展集 (VMSS) 建立體驗會提供 CentOS 架構 7.2 作為部署選項。 因為該映像在 Azure Stack 上無法使用，所以請為您的部署選取其他作業系統，或使用 Azure Resource Manager 範本指定另一個 CentOS 映像，但操作員必須在部署之前已從市集下載該映像。  

<!-- 2724873 - IS --> 
- 開始使用 PowerShell Cmdlet **Start-AzsScaleUnitNode** 或 **Stop-AzsScaleunitNode** 來管理縮放單位時，第一次嘗試啟動或停止縮放單位可能會失敗。 如果第一次執行 Cmdlet 時發生失敗，請再次執行 Cmdlet。 第二次執行應該會成功地完成此作業。 

<!-- TBD - IS ASDK --> 
- 如果在 VM 部署上佈建延伸模組所花費的時間太長，使用者應該讓佈建逾時，而不是嘗試停止程序來將 VM 解除配置或刪除。  

<!-- 1662991 IS ASDK --> 
- Azure Stack 不支援 Linux VM 診斷。 當您部署啟用了 VM 診斷的 Linux VM 時，部署會失敗。 如果您透過診斷設定啟用 Linux VM 基本計量，部署也會失敗。  

<!-- 2724961- IS ASDK --> 
- 當您在「訂用帳戶」設定中註冊 **Microsoft.Insight** 資源提供者，並建立已啟用「客體 OS 診斷」功能的 Windows VM 時，VM 概觀頁面中的「CPU 百分比」圖表不會顯示計量資料。

   若要尋找該 VM 的計量資料 (例如「CPU 百分比」圖表)，請移至 [計量] 視窗，並顯示所有支援的 Windows VM 客體計量。

<!-- 3507629 - IS, ASDK --> 
- 受控磁碟會建立兩個新的[計算配額類型](azure-stack-quota-types.md#compute-quota-types)，以限制可以佈建的受控磁碟最大容量。 根據預設，會為每個受控磁碟配額類型配置 2048 GiB。 但是，您可能會遇到下列問題：

   - 針對在 1808 更新之前建立的配額，雖然配置了 2048 GiB，但是在系統管理員入口網站中，受控磁碟配額會顯示 0 值。 您可以根據實際需求增加或減少該值，新設定的配額值會覆寫預設值 2048 GiB。
   - 如果您將配額值更新為 0，它等於預設值 2048 GiB。 因應措施是將配額值設為 1。

<!-- TBD - IS ASDK --> 
- 套用 1809 更新之後，在部署具有「受控磁碟」的 VM 時，您可能會遇到下列問題：

   - 如果訂用帳戶是在 1808 更新之前建立的，部署具有受控磁碟的 VM 可能會失敗，且會有內部錯誤訊息。 若要解決此錯誤，請針對每個訂用帳戶遵循下列步驟：
      1. 在租用戶入口網站中，移至 [訂用帳戶] 並尋找訂用帳戶。 按一下 [資源提供者]，按一下 [Microsoft.Compute]，然後按一下 [重新註冊]。
      2. 在相同的訂用帳戶底下，移至 [存取控制 (IAM)]，並確認 [Azure Stack - 受控磁碟] 已列出。
   2. 如果您已設定多租用戶環境，則在與來賓目錄相關聯的訂用帳戶中部署 VM 可能會失敗，且會有內部錯誤訊息。 若要解決此錯誤，請依照[這篇文章](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的步驟重新設定您的每個來賓目錄。

- 所建立的 Ubuntu 18.04 VM 如果已啟用 SSH 授權，將不會允許您使用 SSH 金鑰來登入。 因應措施是，請在佈建後使用「適用於 Linux 的 VM 存取」延伸模組來實作 SSH 金鑰，或使用密碼型驗證。

### <a name="networking"></a>網路功能  

<!-- 1766332 - IS ASDK --> 
- 如果您在 [網路] 下按一下 [建立 VPN 閘道] 來設定 VPN 連線，系統就會將 [原則式] 列為 VPN 類型。 請勿選取此選項。 Azure Stack 只支援 [路由式] 選項。

<!-- 1902460 - IS ASDK --> 
- Azure Stack 支援每一 IP 位址有一個「區域網路閘道」。 這適用於所有租用戶訂用帳戶。 建立第一個區域網路閘道連線之後，後續若嘗試使用相同的 IP 位址來建立區域網路閘道資源，就會被封鎖。

<!-- 16309153 - IS ASDK --> 
- 在 DNS 伺服器設定為 [自動] 的已建立虛擬網路上，變更成自訂 DNS 伺服器時失敗。 更新的設定並未推送至該 Vnet 中的 VM。

<!-- 2529607 - IS ASDK --> 
- 在 Azure Stack「祕密輪替」期間，會有一段 2 到 5 分鐘的期間無法存取「公用 IP 位址」。

<!-- 2664148 - IS ASDK --> 
-   在租用戶使用 S2S VPN 通道來存取其虛擬機器的案例中，如果在已經建立閘道之後，才將內部部署子網路新增至「區域網路閘道」，租用戶就可能會遇到連線嘗試失敗的情況。 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service 方案

<!-- 2352906 - IS ASDK --> 
- 使用者必須在於訂用帳戶中建立第一個 Azure 函式之前，先註冊儲存體資源提供者。


### <a name="usage"></a>使用量  

<!-- TBD - IS ASDK --> 
- 公用 IP 位址使用計量資料會針對每一筆記錄顯示相同的 EventDateTime 值，而不是建立記錄時顯示的 TimeDate 時間戳記。 目前，您無法使用這項資料來執行公用 IP 位址使用狀況的正確計量。


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>下載更新

您可以從[這裡](https://aka.ms/azurestackupdatedownload)下載 Azure Stack 1809 更新套件。
  

## <a name="next-steps"></a>後續步驟

- 若要檢閱 Azure Stack 整合式系統的服務原則，以及必須如何做才能將狀態保持在支援的狀態，請參閱 [Azure Stack 服務原則](azure-stack-servicing-policy.md)。  
- 若要使用具有特殊權限的端點 (PEP) 來監視和繼續更新，請參閱[使用具有特殊權限的端點來監視 Azure Stack 中的更新](azure-stack-monitor-update.md)。  
- 如需 Azure Stack 中的更新管理概觀，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md)。  
- 如需如何使用 Azure Stack 套用更新的相關資訊，請參閱[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)。  
