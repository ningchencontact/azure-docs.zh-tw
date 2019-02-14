---
title: Microsoft Azure Stack 開發套件版本資訊 | Microsoft Docs
description: Azure Stack 開發套件的增強功能、修正及已知問題。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 12/21/2018
ms.openlocfilehash: c60ba4f4106ddd0c3fc643288894fb55d3d27f8c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816453"
---
# <a name="asdk-release-notes"></a>ASDK 版本資訊 
 
本文提供「Azure Stack 開發套件」(ASDK) 中改良功能、修正及已知問題的相關資訊。 如果您不確定所執行的版本，可以使用[入口網站來進行檢查](../azure-stack-updates.md#determine-the-current-version)。

> 請訂閱 [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [摘要](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)，以便隨時收到 ASDK 的最新訊息。

## <a name="build-118110101"></a>組建 1.1811.0.101

### <a name="new-features"></a>新功能

此組建包含下列適用於 Azure Stack 的改良功能與修正：  

- 針對 ASDK，有一組新的最基本且建議的硬體與軟體需求。 這些新的建議規格記載於 [Azure Stack 部署規劃考量](asdk-deploy-considerations.md)。 由於 Azure Stack 平台已經演進，因此現在有更多服務可供使用，也可能需要更多資源。 增加的規格皆反映在這些已修訂的建議事項中。

### <a name="fixed-issues"></a>已修正的問題

<!-- TBD - IS ASDK --> 
- 已修正此問題：公用 IP 位址使用計量資料針對每一筆記錄都顯示相同的 **EventDateTime** 值，而不是顯示建立記錄時所顯示的 **TimeDate** 時間戳記。 您現在可以使用此資料來執行公用 IP 位址使用情況的精確計算。

<!-- 3099544 – IS, ASDK --> 
- 已修正使用 Azure Stack 入口網站來建立新虛擬機器 (VM) 時所發生的問題。 選取 VM 大小導致 [美元/月] 資料行顯示「無法使用」訊息。 此資料行不再出現；Azure Stack 不支援顯示 VM 定價資料行。

<!-- 2930718 - IS ASDK --> 
- 已修正此問題：於系統管理員入口網站中存取任何使用者訂用帳戶的詳細資料時，在關閉刀鋒視窗並按一下 [最近] 之後，並未顯示使用者訂用帳戶名稱。 現在已會顯示使用者訂用帳戶名稱。

<!-- 3060156 - IS ASDK --> 
- 已修正此問題：在系統管理員和使用者入口網站中，按一下入口網站設定並選取 [刪除所有設定和私人儀表板] 之後，並未如預期般運作，而且顯示錯誤通知。 

<!-- 2930799 - IS ASDK --> 
- 已修正此問題：在系統管理員和使用者入口網站中，[所有服務] 底下不正確地列出 Azure Stack 中無法使用的 [DDoS 保護計劃] 資產。
 
<!--2760466 – IS  ASDK --> 
- 已修正安裝新 Azure Stack 環境時發生的問題，亦即未顯示指出「需要啟用」的警示。 現在已可正確顯示。

<!--1236441 – IS  ASDK --> 
- 已修正使用 ADFS 時防止將 RBAC 原則套用至使用者群組的問題。

<!--3463840 - IS, ASDK --> 
- 已修正因無法從公用 VIP 網路存取檔案伺服器而導致基礎結構備份失敗的問題。 此修正會將基礎結構備份服務移回到公用基礎結構網路。 如果您已套用解決此問題的最新 Azure Stack 1809 Hotfix，1811 更新將不會進行任何進一步的修改。 

<!-- 2967387 – IS, ASDK --> 
- 已修正此問題：您用來登入 Azure Stack 系統管理員或使用者入口網站的帳戶顯示為「無法辨識的使用者」。 當帳戶未指定「名字」或「姓氏」時，就會顯示此訊息。   

<!--  2873083 - IS ASDK --> 
- 已修正此問題：使用 Internet Explorer 透過入口網站建立虛擬機器擴展集 (VMSS) 時，[執行個體大小] 下拉式清單無法正確載入。 此瀏覽器現在已正確運作。  

<!-- 3190553 - IS ASDK -->
- 已修正產生指出某個「基礎結構角色執行個體」無法供使用或「縮放單位節點」已離線等雜訊警示的問題。

### <a name="known-issues"></a>已知問題

#### <a name="portal"></a>入口網站  

<!-- 2930820 - IS ASDK --> 
- 在系統管理員和使用者入口網站中，如果您搜尋 "Docker"，項目未正確傳回。 它無法在 Azure Stack 中使用。 如果您嘗試建立它，則會顯示具有錯誤指示的刀鋒視窗。 

<!-- 2931230 – IS  ASDK --> 
- 無法刪除以附加方案形式新增至使用者訂用帳戶的方案 (即使您從使用者訂用帳戶中移除此方案)。 此方案會持續保留，直到參考附加方案的訂用帳戶也遭到刪除為止。 

<!-- TBD - IS ASDK --> 
- 不應該使用隨 1804 版導入的兩個系統管理訂用帳戶類型。 這些訂用帳戶類型為「計量訂用帳戶」和「取用訂用帳戶」。 自 1804 版起，這些訂用帳戶類型就已經會在新的 Azure Stack 環境中顯示，只是還未就緒以供使用。 請繼續使用「預設提供者」訂用帳戶類型。

<!-- TBD - IS ASDK --> 
- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。

<!-- TBD - IS ASDK --> 
- 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 PowerShell 來確認權限。

#### <a name="compute"></a>計算 

<!-- 3235634 – IS, ASDK -->
- 若要部署大小包含 **v2** 後置詞的 VM (例如 **Standard_A2_v2**)，請將後置詞指定為 **Standard_A2_v2** (小寫 v)。 請勿使用 **Standard_A2_V2** (大寫 V)。 這適用於全域 Azure，並且在 Azure Stack 上有不一致的問題。

<!-- 2869209 – IS, ASDK --> 
- 當使用 [**Add-AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0) 時，您必須使用 **-OsUri** 參數作為要上傳磁碟的儲存體帳戶 URI。 如果您使用磁碟的本機路徑，此 Cmdlet 就會失敗，且具有下列錯誤：*長時間執行的作業失敗，狀態為「失敗」*。 

<!--  2795678 – IS, ASDK --> 
- 當您使用入口網站以進階 VM 大小 (DS、Ds_v2、FS、FSv2) 建立虛擬機器 (VM) 時，會在標準儲存體帳戶中建立 VM。 在標準儲存體帳戶中建立不會影響功能、IOP 或帳單。 您可以安心忽略指出下列訊息的警告：**您選擇在支援進階磁碟的大小上使用標準磁碟。這樣可能會影響作業系統效能，並不建議使用。請考慮改為使用進階儲存體 (SSD)。」**

<!-- 2967447 - IS, ASDK --> 
- 虛擬機器擴展集 (VMSS) 建立體驗會提供 CentOS 架構 7.2 作為部署選項。 因為該映像在 Azure Stack 上無法使用，所以請為您的部署選取其他作業系統，或使用 Azure Resource Manager 範本指定另一個 CentOS 映像，但操作員必須在部署之前已從市集下載該映像。  

<!-- TBD - IS ASDK --> 
- 如果在 VM 部署上佈建延伸模組所花費的時間太長，使用者應該讓佈建逾時，而不是嘗試停止程序來將 VM 解除配置或刪除。  

<!-- 1662991 IS ASDK --> 
- Azure Stack 不支援 Linux VM 診斷。 當您部署啟用了 VM 診斷的 Linux VM 時，部署會失敗。 如果您透過診斷設定啟用 Linux VM 基本計量，部署也會失敗。  

<!-- 2724961- IS ASDK --> 
- 當您在「訂用帳戶」設定中註冊 **Microsoft.Insight** 資源提供者，並建立已啟用「客體 OS 診斷」功能的 Windows VM 時，VM 概觀頁面中的「CPU 百分比」圖表不會顯示計量資料。

   若要尋找該 VM 的計量資料 (例如「CPU 百分比」圖表)，請移至 [計量] 視窗，並顯示所有支援的 Windows VM 客體計量。

<!-- 3507629 - IS, ASDK --> 
- 受控磁碟會建立兩個新的[計算配額類型](../azure-stack-quota-types.md#compute-quota-types)，以限制可以佈建的受控磁碟最大容量。 根據預設，會為每個受控磁碟配額類型配置 2048 GiB。 但是，您可能會遇到下列問題：

   - 針對在 1808 更新之前建立的配額，雖然配置了 2048 GiB，但是在系統管理員入口網站中，受控磁碟配額會顯示 0 值。 您可以根據實際需求增加或減少該值，新設定的配額值會覆寫預設值 2048 GiB。
   - 如果您將配額值更新為 0，它等於預設值 2048 GiB。 因應措施是將配額值設為 1。

<!-- TBD - IS ASDK --> 
- 套用 1811 更新之後，在部署具有「受控磁碟」的 VM 時，您可能會遇到下列問題：

   1. 如果訂用帳戶是在 1808 更新之前建立的，部署具有受控磁碟的 VM 可能會失敗，且會有內部錯誤訊息。 若要解決此錯誤，請針對每個訂用帳戶遵循下列步驟：
      1. 在租用戶入口網站中，移至 [訂用帳戶] 並尋找訂用帳戶。 按一下 [資源提供者]，按一下 [Microsoft.Compute]，然後按一下 [重新註冊]。
      2. 在相同的訂用帳戶底下，移至 [存取控制 (IAM)]，並確認 [Azure Stack - 受控磁碟] 已列出。
   2. 如果您已設定多租用戶環境，則在與來賓目錄相關聯的訂用帳戶中部署 VM 可能會失敗，且會有內部錯誤訊息。 若要解決此錯誤，請依照[這篇文章](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的步驟重新設定您的每個來賓目錄。

#### <a name="networking"></a>網路功能

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

#### <a name="app-service"></a>App Service 方案

<!-- 2352906 - IS ASDK --> 
- 使用者必須在於訂用帳戶中建立第一個 Azure 函式之前，先註冊儲存體資源提供者。

<!-- #### Usage -->  

<!-- #### Identity -->

## <a name="build-11809090"></a>組建 1.1809.0.90

### <a name="new-features"></a>新功能
此組建包含下列適用於 Azure Stack 的改良功能與修正。  

<!--  2712869   | IS  ASDK -->  
- **Azure Stack syslog 用戶端 (正式運作)**  此用戶端允許將與 Azure Stack 基礎結構相關的稽核和安全性記錄，轉寄到 Azure Stack 外的 syslog 伺服器或安全性資訊與事件管理 (SIEM) 軟體。 Syslog 用戶端現在支援 syslog 伺服器目前接聽的指定連接埠。

此版本中，syslog 用戶端已正式運作，且可用於實際執行環境。

如需詳細資訊，請參閱 [Azure Stack Syslog 轉送](../azure-stack-integrate-security.md)。

### <a name="fixed-issues"></a>已修正的問題

<!-- TBD - IS ASDK -->
- 在入口網站中，記憶體圖表報表免費/已使用容量目前是正確的。 您現在可以更可靠地預測您可以建立的 VM 數量。

<!-- TBD - IS ASDK --> 
- 已修正當您在 Azure Stack 使用者入口網站上建立虛擬機器時，入口網站所顯示可連結至 DS 系列 VM 的資料磁碟數目會不正確的問題。 DS 系列 VM 可容納與 Azure 設定數目一樣多的資料磁碟。

- <!-- 2702741 -  IS, ASDK --> 過去針對使用「動態」配置方法來部署的公用 IP，不保證在發出 Stop-Deallocate 之後還能予以保留，此問題已修正。 現在這些 IP 會予以保留。

- <!-- 3078022 - IS, ASDK --> 如果 VM 在 1808 版本以前停止解除配置，則無法在 1808 更新後重新配置。  此問題已在 1809 版本修正。 經過此次修正，過去在此狀態的執行個體和無法啟動的執行個體將可於 1809 版本中啟動。 此修正也可避免此問題再次發生。

<!-- TBD -  IS ASDK --> 
- 當 VM 映像建立失敗時，可能會在 VM 映像計算刀鋒視窗上新增一個您無法刪除的失敗項目。

  因應措施是，建立一個具有虛擬 VHD 的新 VM 映像，您可以透過 Hyper-V 建立此 VHD (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB)。 此程序應該可以修正防止刪除失敗項目的問題。 接著，在建立虛擬映像後的 15 分鐘之後，您便可以順利刪除它。

  您可以接著嘗試下載先前失敗的 VM 映像。

- 針對效能、穩定性、安全性和 Azure Stack 所使用的作業系統提供了**多項修正**

### <a name="changes"></a>變更

### <a name="known-issues"></a>已知問題

#### <a name="portal"></a>入口網站  

<!-- 2515955   | IS ,ASDK--> 
- Azure Stack 系統管理員和使用者入口網站中的「更多服務」已取代為「所有服務」。 您現在可以使用「所有服務」作為在 Azure Stack 入口網站中瀏覽的替代方法，就像是在 Azure 入口網站中執行的動作一樣。

<!--  TBD – IS, ASDK --> 
- 透過入口網站[建立虛擬機器擴展集](../azure-stack-compute-add-scalesets.md) (VMSS) 時，無法再使用「基本 A」虛擬機器大小。 若要建立具有此大小的 VMSS，請使用 PowerShell 或範本。

#### <a name="health-and-monitoring"></a>健康情況和監視

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

<!-- 2368581 - IS. ASDK --> 
- 身為 Azure Stack 操作員，如果您收到記憶體不足的警示，而且租用戶虛擬機器無法部署並出現「Fabric VM 建立錯誤」，則可能表示 Azure Stack 戳記的可用記憶體不足。 請使用 [Azure Stack 容量規劃工具](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)來充分了解可供工作負載使用的容量。


#### <a name="compute"></a>計算 

<!-- 3164607 – IS, ASDK -->
- 將已中斷連結的磁碟重新連結到具有相同名稱的相同虛擬機器 (VM)，並且發生如**無法將資料磁碟 'datadisk' 連結到 VM 'vm1'** 錯誤的 LUN 失敗。 此錯誤的發生原因是磁碟目前正在中斷連結，或上次的中斷連結作業失敗。 請等候磁碟完全中斷連結後再試一次，或再次明確將磁碟刪除/中斷連結。 因應措施是使用其他名稱或在其他 LUN 上將它重新連結。 

<!-- 3235634 – IS, ASDK -->
- 若要部署大小包含 **v2** 後置詞的 VM (例如 **Standard_A2_v2**)，請將後置詞指定為 **Standard_A2_v2** (小寫 v)。 請勿使用 **Standard_A2_V2** (大寫 V)。 這適用於全域 Azure，並且在 Azure Stack 上有不一致的問題。

<!-- 3099544 – IS, ASDK --> 
- 當您使用 Azure Stack 入口網站建立新的虛擬機器 (VM) 並選取 VM 大小時，會顯示美元/月欄位和「無法使用」訊息。 此欄位不應該出現；Azure Stack 不支援顯示 VM 定價欄位。

<!-- 2869209 – IS, ASDK --> 
- 當使用 [**Add-AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0) 時，您必須使用 **-OsUri** 參數作為要上傳磁碟的儲存體帳戶 URI。 如果您使用磁碟的本機路徑，此 Cmdlet 就會失敗，且具有下列錯誤：「長時間執行的作業失敗，狀態為「失敗」」。 

<!--  2966665 – IS, ASDK --> 
- 將 SSD 資料磁碟連結至進階大小受控磁碟虛擬機器 (DS、DSv2、Fs、Fs_V2) 失敗，具有錯誤：*無法更新虛擬機器 ‘vmname’ 的磁碟。錯誤：因為對 VM 大小 ‘Standard_DS/Ds_V2/FS/Fs_v2) 不支援儲存體帳戶類型 ‘Premium_LRS’，所以無法執行要求的作業*

   若要解決此問題，請使用 Standard_LRS 資料磁碟，而不是 Premium_LRS 磁碟。 使用 Standard_LRS 資料磁碟不會變更 IOP 或帳單成本。  

<!--  2795678 – IS, ASDK --> 
- 當您使用入口網站以進階 VM 大小 (DS、Ds_v2、FS、FSv2) 建立虛擬機器 (VM) 時，會在標準儲存體帳戶中建立 VM。 在標準儲存體帳戶中建立不會影響功能、IOP 或帳單。 

   您可以安心忽略指出下列訊息的警告：*您選擇在支援進階磁碟的大小上使用標準磁碟。這樣可能會影響作業系統效能，並不建議使用。請考慮改為使用進階儲存體 (SSD)。」*

<!-- 2967447 - IS, ASDK --> 
- 虛擬機器擴展集 (VMSS) 建立體驗會提供 CentOS 架構 7.2 作為部署選項。 因為該映像在 Azure Stack 上無法使用，所以請為您的部署選取其他作業系統，或使用 Azure Resource Manager 範本指定另一個 CentOS 映像，但操作員必須在部署之前已從市集下載該印象。

<!-- TBD -  IS ASDK --> 
- 無法在入口網站中使用虛擬機器擴展集的調整設定。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) 作為因應措施。 由於 PowerShell 版本差異，您必須使用 `-Name` 參數，而不是 `-VMScaleSetName`。



<!-- TBD -  IS ASDK --> 
- 如果在 VM 部署上佈建延伸模組所花費的時間太長，使用者應該讓佈建逾時，而不是嘗試停止程序來將 VM 解除配置或刪除。  

<!-- 1662991 - IS ASDK --> 
- Azure Stack 不支援 Linux VM 診斷。 當您部署啟用了 VM 診斷的 Linux VM 時，部署會失敗。 如果您透過診斷設定啟用 Linux VM 基本計量，部署也會失敗。

<!-- 2724961- IS ASDK --> 
- 當您在「訂用帳戶」設定中註冊 **Microsoft.Insight** 資源提供者，並建立一個已啟用「客體 OS 診斷」功能的 Windows VM 時，VM 概觀頁面中的「CPU 百分比」圖表將無法顯示計量資料。
 
  若要尋找該 VM 的「CPU 百分比」圖表，請移至 [計量] 刀鋒視窗並顯示所有支援的 Windows VM 客體計量。

 

#### <a name="networking"></a>網路功能
<!-- 1766332 - IS, ASDK --> 
- 如果您在 [網路] 下按一下 [建立 VPN 閘道] 來設定 VPN 連線，系統就會將 [原則式] 列為 VPN 類型。 請勿選取此選項。 Azure Stack 只支援 [路由式] 選項。

<!-- 1902460 -  IS ASDK --> 
- Azure Stack 支援每一 IP 位址有一個「區域網路閘道」。 這適用於所有租用戶訂用帳戶。 建立第一個區域網路閘道連線之後，後續若嘗試使用相同的 IP 位址來建立區域網路閘道資源，就會被封鎖。

<!-- 16309153 -  IS ASDK --> 
- 在 DNS 伺服器設定為 [自動] 的已建立虛擬網路上，變更成自訂 DNS 伺服器時失敗。 更新的設定並未推送至該 Vnet 中的 VM。

<!-- 2702741 -  IS ASDK --> 
- 針對使用「動態」配置方法來部署的公用 IP，不保證在發出 Stop-Deallocate 之後還能予以保留。

<!-- 2529607 - IS ASDK --> 
- 在 Azure Stack「祕密輪替」期間，會有一段 2 到 5 分鐘的期間無法存取「公用 IP 位址」。

<!-- 2664148 - IS ASDK --> 
- 在租用戶使用 S2S VPN 通道來存取其虛擬機器的案例中，如果在已經建立閘道之後，才將內部部署子網路新增至「區域網路閘道」，租用戶就可能會遇到連線嘗試失敗的情況。 


<!--  #### SQL and MySQL  -->


#### <a name="app-service"></a>App Service 方案
<!-- 2352906 - IS ASDK --> 
- 使用者必須在於訂用帳戶中建立第一個 Azure 函式之前，先註冊儲存體資源提供者。

<!-- TBD - IS ASDK --> 
- 為了向外延展基礎結構 (背景工作、管理、前端角色)，您必須如「計算」的版本資訊所述，使用 PowerShell。  


#### <a name="usage"></a>使用量  
<!-- TBD -  IS ASDK --> 
- 使用量公用 IP 位址使用計量資料會針對每一筆記錄顯示相同的 *EventDateTime* 值，而不是建立記錄時顯示的 *TimeDate* 時間戳記。 目前，您無法使用這項資料來執行公用 IP 位址使用狀況的正確計量。

<!-- #### Identity -->

## <a name="build-11808097"></a>組建 1.1808.0.97

### <a name="new-features"></a>新功能
此組建包含下列適用於 Azure Stack 的改良功能與修正。  

<!-- 1658937 | ASDK, IS --> 
- **根據預先定義的排程啟動備份** - 作為設備的 Azure Stack 現在可以自動地定期觸發基礎結構備份，無須人為介入。 若備份超過定義的保留期限，Azure Stack 也會自動清除其外部共用。 了解如何[使用 PowerShell 來啟用 Azure Stack 的備份](../azure-stack-backup-enable-backup-powershell.md)以取得更多資訊。

<!-- 2496385 | ASDK, IS -->  
- **已將資料轉送時間新增到備份時間總計中。** 了解如何[使用 PowerShell 來啟用 Azure Stack 的備份](../azure-stack-backup-enable-backup-powershell.md)以取得更多資訊。

<!-- 1702130 | ASDK, IS --> 
- **備份外部容量現在會顯示正確的外部共用容量。** (以前是以硬式編碼將其設為 10 GB。)了解如何[使用 PowerShell 來啟用 Azure Stack 的備份](../azure-stack-backup-enable-backup-powershell.md)以取得更多資訊。
 
<!-- 2753130 |  IS, ASDK   -->  
- **Azure Resource Manager 範本現在支援條件元素** - 您現在可以使用條件在 Azure Resource Manger 範本中部署資源。 您可以將範本設計成根據條件 (例如評估某個參數值是否存在) 來部署資源。 如需有關使用範本作為條件的資訊，請參閱 Azure 文件中的[依條件部署資源](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy)和 [Azure Resource Manager 範本的 Variables 區段](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables)。 

   您也可以使用範本[將資源部署至多個訂用帳戶或資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment)。  

<!--2753073 | IS, ASDK -->  
- **Microsoft.Network API 資源版本支援已更新**，從適用於 Azure Stack 網路資源的 2015-06-15 更新成包含對 API 2017-10-01 版的支援。 此版本未包含對 2017-10-01 與 2015-06-15 之間資源版本的支援。 如需了解功能差異，請參閱 [Azure Stack 網路服務的注意事項](../user/azure-stack-network-differences.md)。

<!-- 2272116 | IS, ASDK   -->  
- **Azure Stack 已針對對外的 Azure Stack 基礎結構端點 (亦即針對 portal、adminportal、management 及 adminmanagement) 新增反向 DNS 查閱支援**。 這可允許從 IP 位址解析 Azure Stack 外部端點名稱。

<!-- 2780899 |  IS, ASDK   --> 
- **Azure Stack 現在支援為現有的 VM 新增額外的網路介面。**  藉由使用入口網站、PowerShell 及 CLI，即可使用此功能。 如需詳細資訊，請參閱 Azure 文件中的[新增或移除網路介面](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm)。 

<!-- 2222444 | IS, ASDK   -->  
- **已提升網路使用量計量的精確度和彈性**。 網路使用量計量現在更精確，並將暫停的訂用帳戶、中斷期及競爭條件納入考慮。

<!-- 2297790 | IS, ASDK -->  
- **改進 Azure Stack Syslog 用戶端 (預覽功能)**。 此用戶端允許將與 Azure Stack 基礎結構相關的稽核和記錄轉送到 Azure Stack 外的 Syslog 伺服器或安全性資訊與事件管理 (SIEM) 軟體。 Syslog 用戶端現在支援搭配純文字或 TLS 1.2 加密的 TCP 通訊協定，後者為預設設定。 您可以為 TLS 連線設定僅限伺服器的驗證或相互驗證。

  若要設定 Syslog 用戶端與 Syslog 伺服器的通訊方式 (例如通訊協定、加密及驗證)，請使用 Set-SyslogServer Cmdlet。 您可以從具特殊權限的端點 (PEP) 使用此 Cmdlet。 

- <!-- ASDK --> **虛擬機器擴展集的資源庫項目現已內建**。  虛擬機器擴展集資源庫項目現在可於使用者和系統管理員入口網站中取用，不需要下載。 

- <!-- IS, ASDK --> **虛擬機器擴展集縮放**.  您可以使用入口網站來[縮放虛擬機器擴展集](../azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS)。   

- <!-- 2489570 | IS ASDK--> **支援自訂 IPSec/IKE 原則組態**，適用於 [Azure Stack 中的 VPN 閘道](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways)。

- <!-- | IS ASDK--> **Kubernetes Marketplace 項目**。 您現在可以使用 [Kubernetes Marketplace 項目](/azure/azure-stack/azure-stack-solution-template-kubernetes-cluster-add)來部署 Kubernetes 叢集。 使用者可以選取 Kubernetes 項目並且填入幾個參數，以將 Kubernetes 叢集部署至 Azure Stack。 範本的目的是讓使用者以少數幾個步驟輕鬆設定開發/測試 Kubernetes 部署。

<!-- ####### | IS, ASDK -->  
- **Azure Resource Manager 包含區域名稱。** 在此版本中，從 Azure Resource Manager 擷取的物件現在會包含區域名稱屬性。 如果現有的 PowerShell 指令碼會將物件直接傳遞給另一個 Cmdlet，則此指令碼可能會產生錯誤並失敗。 這是符合 Azure Resource Manager 規範的行為，而會需要發出呼叫的用戶端去除區域屬性。 如需有關 Azure Resource Manager 的詳細資訊，請參閱 [Azure Resource Manager 文件](https://docs.microsoft.com/azure/azure-resource-manager/)。

<!-- TBD | IS, ASDK -->  
- **在委派的提供者之間移動訂用帳戶。** 您現在可以在屬於相同目錄租用戶之新的或現有的「委派的提供者」訂用帳戶之間，移動訂用帳戶。 您也可以將屬於「預設提供者訂用帳戶」的訂用帳戶移至相同目錄租用戶中的「委派的提供者訂用帳戶」。 如需詳細資訊，請參閱 [Azure Stack 中的委派供應項目](../azure-stack-delegated-provider.md)。
 
<!-- 2536808 IS ASDK --> 
- 針對使用從 Azure Marketplace 下載之映像建立的 VM，**縮短 VM 建立時間**。

### <a name="fixed-issues"></a>已修正的問題
- <!-- IS ASDK--> 我們已修正在入口網站中建立可用性設定組時，設定組中只有 1 個容錯網域和 1 個更新網域的問題。

<!-- TBD | ASDK, IS --> 
- 已對更新程序進行各種改進，使其變得更可靠。 此外，也已對基底基礎結構進行修正，改善了節點清空狀況，因而可將更新期間工作負載的可能停機時間縮到最短。

<!--2292271 | ASDK, IS --> 
- 修正問題：已修改的配額限制無法套用到現有訂用帳戶。  現在，如果「供應項目」和「方案」與使用者訂用帳戶相關聯，而您將該「供應項目」和「方案」所含網路資源的配額限制提高，則新限制不但會套用到新的訂用帳戶，也會套用到既有的訂用帳戶。

<!-- 2448955 | IS ASDK --> 
- 您現在可以順利地對部署在 UTC+N 時區的系統查詢活動記錄。    

<!-- 2319627 |  ASDK, IS --> 
- 備份組態參數 (路徑/使用者名稱/密碼/加密金鑰) 的前置檢查不會再對備份組態進行不正確的設定。 (之前，如果在備份中設定了不正確的設定，當觸發備份時，備份就會發生失敗)。

<!-- 2215948 |  ASDK, IS --> 
- 現在，當您從外部共用手動刪除備份時，備份清單會進行重新整理。

<!-- 2360715 |  ASDK, IS -->  
- 當您設定資料中心整合時，您無法再從共用中存取 AD FS 中繼資料檔案。 如需詳細資訊，請參閱[藉由提供同盟中繼資料檔案設定 AD FS 整合](../azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file)。 

<!-- 2388980 | ASDK, IS --> 
- 修正問題：防止使用者將先前已指派給網路介面或 Load Balancer 的現有公用 IP 位址，指派給新的網路介面或 Load Balancer。  

<!-- 2551834 - IS, ASDK --> 
- 當您在系統管理員或使用者入口網站中選取儲存體帳戶的 [概觀] 時，[基本資訊] 窗格現在會正確地顯示所有預期的資訊。 

<!-- 2551834 - IS, ASDK --> 
- 當您在系統管理員或使用者入口網站中選取儲存體帳戶的 [標籤] 時，資訊現在會正確地顯示。

<!-- TBD - IS ASDK --> 
- 此版本的 Azure Stack 修正了導致無法從 OEM 擴充功能套件套用驅動程式更新的問題。

<!-- 2055809- IS ASDK --> 
- 我們已修正導致您在 VM 建立失敗時無法從 [計算] 刀鋒視窗刪除 VM 的問題。  

<!--  2643962 IS ASDK -->  
- 「記憶體容量不足」的警示不再不正確地顯示。

<!--  TBD ASDK --> 
- 裝載具特殊權限端點 (PEP) 的虛擬機器已增加至 4 GB。 在 ASDK 中，此虛擬機器名為 AzS ERCS01。

- <!--  TBD – IS, ASDK --> 透過入口網站[建立虛擬機器擴展集](../azure-stack-compute-add-scalesets.md) (VMSS) 時，無法再使用「基本 A」虛擬機器大小。 若要建立具有此大小的 VMSS，請使用 PowerShell 或範本。 

### <a name="known-issues"></a>已知問題

#### <a name="portal"></a>入口網站  
- <!-- 2967387 – IS, ASDK --> 您用來登入 Azure Stack 系統管理員或使用者入口網站的帳戶，顯示為**無法辨識的使用者**。 當帳戶未指定「名字」或「姓氏」時，就會發生這種情形。 若要解決此問題，請編輯使用者帳戶，提供「名字」或「姓氏」。 您必須登出，然後再登入回入口網站。 

-  <!--  2873083 - IS ASDK --> 當您使用入口網站來建立虛擬機器擴展集 (VMSS) 時，如果使用 Internet Explorer，[執行個體大小] 下拉式清單無法正確載入。 若要解決此問題，請在使用入口網站來建立 VMSS 時使用另一個瀏覽器。  

#### <a name="portal"></a>入口網站  
<!-- 2931230 – IS  ASDK --> 
- 無法刪除以附加方案形式新增至使用者訂用帳戶的方案 (即使您從使用者訂用帳戶中移除此方案)。 此方案會持續保留，直到參考附加方案的訂用帳戶也遭到刪除為止。 

<!--2760466 – IS  ASDK --> 
- 當您安裝執行此版本的新 Azure Stack 環境時，可能不會顯示指出「需要啟用」的警示。 必須[啟用](../azure-stack-registration.md)，才能使用市集摘要整合。 

<!-- TBD - IS ASDK --> 
- 不應該使用隨 1804 版導入的兩個系統管理訂用帳戶類型。 這些訂用帳戶類型為「計量訂用帳戶」和「取用訂用帳戶」。 這些訂用帳戶類型為「計量訂用帳戶」和「取用訂用帳戶」。 自 1804 版起，這些訂用帳戶類型就已經會在新的 Azure Stack 環境中顯示，只是還未就緒以供使用。 您應該繼續使用「預設提供者訂用帳戶」類型。

<!-- 2403291 - IS ASDK --> 
- 您可能還無法使用系統管理員和使用者入口網站底部的水平捲軸。 如果您無法存取水平捲軸，請使用階層連結來瀏覽至入口網站中的上一個刀鋒視窗，方法是從入口網站左上角的階層連結清單中，選取您想要檢視的刀鋒視窗名稱。
  ![階層連結](media/asdk-release-notes/breadcrumb.png)

<!-- TBD -  IS ASDK --> 
- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。

<!-- TBD -  IS ASDK --> 
- 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 PowerShell 來確認權限。

<!--  TBD | ASDK -->  
- 您 Azure Stack 部署的預設時區現在會設定為 UTC。 安裝 Azure Stack 時，您可以選取時區，不過，在安裝期間，它會自動還原成以 UTC 作為預設值。

#### <a name="health-and-monitoring"></a>健康情況和監視
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

<!-- 2368581 - IS. ASDK --> 
- 身為 Azure Stack 操作員，如果您收到記憶體不足的警示，而且租用戶虛擬機器無法部署並出現「Fabric VM 建立錯誤」，則可能表示 Azure Stack 戳記的可用記憶體不足。 請使用 [Azure Stack 容量規劃工具](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)來充分了解可供工作負載使用的容量。

<!-- TBD - IS. ASDK --> 
- 在特殊權線端點 (PEP) 執行 **Test-AzureStack** cmdlet 時，**Azure Stack 基礎結構角色執行個體效能**測試會針對 ERCS VM 產生 WARN 訊息。 您可以放心地忽略 WARN 訊息，並繼續使用 ASDK。

#### <a name="compute"></a>計算
<!-- 2494144 - IS, ASDK --> 
- 在為虛擬機器部署選取虛擬機器大小時，某些 F 系列的 VM 大小不會在您建立 VM 時顯示在大小選取器中。 選取器中不會顯示下列 VM 大小：*F8s_v2*、*F16s_v2*、*F32s_v2* 和 *F64s_v2*。  
  因應措施是使用下列其中一個方法來部署虛擬機器。 在每個方法中，您需要指定想要使用的虛擬機器大小。

- <!-- 3099544 – IS, ASDK --> 當您使用 Azure Stack 入口網站建立新的虛擬機器 (VM) 並選取 VM 大小時，會顯示美元/月欄位和「無法使用」訊息。 此欄位不應該出現；Azure Stack 不支援顯示 VM 定價欄位。

- <!-- 2869209 – IS, ASDK --> 當使用 [**Add-AzsPlatformImage** Cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0) 時，您必須使用 **-OsUri** 參數作為要上傳磁碟的儲存體帳戶 URI。 如果您使用磁碟的本機路徑，此 Cmdlet 就會失敗，且具有下列錯誤：「長時間執行的作業失敗，狀態為「失敗」」。 

- <!--  2966665 – IS, ASDK -->將 SSD 資料磁碟連結至進階大小受控磁碟虛擬機器 (DS、DSv2、Fs、Fs_V2) 失敗，具有錯誤：*無法更新虛擬機器 ‘vmname’ 的磁碟。錯誤：因為對 VM 大小 ‘Standard_DS/Ds_V2/FS/Fs_v2) 不支援儲存體帳戶類型 ‘Premium_LRS’，所以無法執行要求的作業*

   若要解決此問題，請使用 Standard_LRS 資料磁碟，而不是 Premium_LRS 磁碟。 使用 Standard_LRS 資料磁碟不會變更 IOP 或帳單成本。  

- <!--  2795678 – IS, ASDK --> 當您使用入口網站以進階 VM 大小 (DS、Ds_v2、FS、FSv2) 建立虛擬機器 (VM) 時，會在標準儲存體帳戶中建立 VM。 在標準儲存體帳戶中建立不會影響功能、IOP 或帳單。 

   您可以安心忽略指出下列訊息的警告：*您選擇在支援進階磁碟的大小上使用標準磁碟。這樣可能會影響作業系統效能，並不建議使用。請考慮改為使用進階儲存體 (SSD)。」*

- <!-- 2967447 - IS, ASDK --> 虛擬機器擴展集 (VMSS) 建立體驗會提供 CentOS 架構 7.2 作為部署選項。 因為該映像在 Azure Stack 上無法使用，所以請為您的部署選取其他作業系統，或使用 ARM 範本指定另一個 CentOS 映像，但操作員必須在部署之前已從市集下載該印象。

<!-- TBD -  IS ASDK --> 
- 無法在入口網站中使用虛擬機器擴展集的調整設定。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) 作為因應措施。 由於 PowerShell 版本差異，您必須使用 `-Name` 參數，而不是 `-VMScaleSetName`。

<!-- TBD -  IS ASDK --> 
- 當您在 Azure Stack 使用者入口網站上建立虛擬機器時，入口網站所顯示可連結 D 系列 VM 的資料磁碟數目會不正確。 所有受支援的 D 系列 VM 均可容納與 Azure 設定數目一樣多的資料磁碟。

<!-- TBD -  IS ASDK --> 
- 當 VM 映像建立失敗時，可能會在 VM 映像計算刀鋒視窗上新增一個您無法刪除的失敗項目。

  因應措施是，建立一個具有虛擬 VHD 的新 VM 映像，您可以透過 Hyper-V 建立此 VHD (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB)。 此程序應該可以修正防止刪除失敗項目的問題。 接著，在建立虛擬映像後的 15 分鐘之後，您便可以順利刪除它。

  您可以接著嘗試下載先前失敗的 VM 映像。

<!-- TBD -  IS ASDK --> 
- 如果在 VM 部署上佈建延伸模組所花費的時間太長，使用者應該讓佈建逾時，而不是嘗試停止程序來將 VM 解除配置或刪除。  

<!-- 1662991 - IS ASDK --> 
- Azure Stack 不支援 Linux VM 診斷。 當您部署啟用了 VM 診斷的 Linux VM 時，部署會失敗。 如果您透過診斷設定啟用 Linux VM 基本計量，部署也會失敗。

<!-- 2724961- IS ASDK --> 
- 當您在「訂用帳戶」設定中註冊 **Microsoft.Insight** 資源提供者，並建立已啟用「客體 OS 診斷」功能的 Windows VM 時，VM 概觀頁面不會顯示計量資料。 

 

#### <a name="networking"></a>網路功能
<!-- 1766332 - IS, ASDK --> 
- 如果您在 [網路] 下按一下 [建立 VPN 閘道] 來設定 VPN 連線，系統就會將 [原則式] 列為 VPN 類型。 請勿選取此選項。 Azure Stack 只支援 [路由式] 選項。

<!-- 1902460 -  IS ASDK --> 
- Azure Stack 支援每一 IP 位址有一個「區域網路閘道」。 這適用於所有租用戶訂用帳戶。 建立第一個區域網路閘道連線之後，後續若嘗試使用相同的 IP 位址來建立區域網路閘道資源，就會被封鎖。

<!-- 16309153 -  IS ASDK --> 
- 在 DNS 伺服器設定為 [自動] 的已建立虛擬網路上，變更成自訂 DNS 伺服器時失敗。 更新的設定並未推送至該 Vnet 中的 VM。

<!-- 2702741 -  IS ASDK --> 
- 針對使用「動態」配置方法來部署的公用 IP，不保證在發出 Stop-Deallocate 之後還能予以保留。

<!-- 2529607 - IS ASDK --> 
- 在 Azure Stack「祕密輪替」期間，會有一段 2 到 5 分鐘的期間無法存取「公用 IP 位址」。

<!-- 2664148 - IS ASDK --> 
- 在租用戶使用 S2S VPN 通道來存取其虛擬機器的案例中，如果在已經建立閘道之後，才將內部部署子網路新增至「區域網路閘道」，租用戶就可能會遇到連線嘗試失敗的情況。 


#### <a name="sql-and-mysql"></a>SQL 和 MySQL
<!-- TBD - ASDK --> 
- 裝載伺服器的資料庫必須專供資源提供者和使用者工作負載使用。 您無法使用任何其他取用者 (包括「應用程式服務」) 正在使用的執行個體。

<!-- IS, ASDK --> 
- 當您為 SQL 和 MySQL 資源提供者建立 SKU 時，[系列] 名稱不支援特殊字元，包括空格和句點。

#### <a name="app-service"></a>App Service 方案
<!-- 2352906 - IS ASDK --> 
- 使用者必須在於訂用帳戶中建立第一個 Azure 函式之前，先註冊儲存體資源提供者。

<!-- TBD - IS ASDK --> 
- 為了向外延展基礎結構 (背景工作、管理、前端角色)，您必須如「計算」的版本資訊所述，使用 PowerShell。  

<!-- TBD - IS ASDK --> 
- 目前，App Service 只能部署到「預設提供者訂用帳戶」。

#### <a name="usage"></a>使用量  
<!-- TBD -  IS ASDK --> 
- 使用量公用 IP 位址使用計量資料會針對每一筆記錄顯示相同的 *EventDateTime* 值，而不是建立記錄時顯示的 *TimeDate* 時間戳記。 目前，您無法使用這項資料來執行公用 IP 位址使用狀況的正確計量。

<!-- #### Identity -->

