---
title: Azure Stack 1712 更新 | Microsoft Docs
description: 了解適用於 Azure Stack 整合系統之 1712 更新的新功能、已知問題，以及可下載更新的位置。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: b14f79ad-025f-45d8-9e1d-e53d2b420bb1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2018
ms.author: brenduns
ms.openlocfilehash: e9c39c374d7dfa6759da9f9b9a12816d77c647b1
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="azure-stack-1712-update"></a>Azure Stack 1712 更新

「適用於：Azure Stack 整合系統」

本文說明這個更新程式封裝中的改良功能與修正、此版本的已知問題，以及可從何處下載更新。 已知問題分為直接與更新程序相關的已知問題，以及組建 (安裝後) 相關的已知問題。

> [!IMPORTANT]
> 此更新套件僅適用於 Azure Stack 整合系統。 請勿將此更新套件套用至 Azure Stack 開發套件。

## <a name="build-reference"></a>建置參考

Azure Stack 1712 更新組建編號為 **180106.1**。 如果客戶先前已部署 **180103.2**，您就不需要套用 **180106.1**。

## <a name="before-you-begin"></a>開始之前

> [!IMPORTANT]
> 在 1712 更新安裝程序期間，請勿嘗試建立虛擬機器。 如需詳細資訊，請參閱[管理 Azure Stack 中的更新概觀](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#plan-for-updates)。

### <a name="prerequisites"></a>先決條件

您必須先安裝 Azure Stack [1711 更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1711)，才能套用此更新。

### <a name="post-update-steps"></a>更新後步驟
- 在安裝 1712 之後，安裝任何適用的 Hotfix。 如需詳細資訊，請檢視下列知識庫文章，以及我們的[服務原則](azure-stack-servicing-policy.md)。  
  - [KB 4103348 - 網路控制器 API 服務在您嘗試安裝 Azure Stack 更新時損毀](https://support.microsoft.com/help/4103348)


- 更新 1712 還需要您在完成 1712 Azure Stack 更新安裝之後，安裝 OEM 夥伴的韌體更新。

  > [!NOTE]
  > 請參閱您的 OEM 夥伴網站來下載更新。

### <a name="new-features-and-fixes"></a>新功能和修正

此更新包含下列適用於 Azure Stack 的改良功能與修正。

#### <a name="new-features"></a>新功能

- 可以透過特殊權限的端點取得用來驗證 Azure Stack Cloud 的 Test-AzureStack Cmdlet
- 能夠註冊已中斷連線的 Azure Stack 部署
- 憑證和使用者帳戶到期的監視警示
- 在 PEP 中新增了 Set-BmcPassword Cmdlet，以進行 BMC 密碼輪替
- 網路記錄更新，可支援隨選記錄
- 支援虛擬機器擴展集 (VMSS) 的重新安裝映像作業
- 在 ERCS VM 上啟用 CloudAdmin 登入的 kiosk 模式
- 租用戶可以自動啟動 Windows VM

#### <a name="fixes"></a>修正

- 可在執行修復時，顯示維護中節點作業狀態的修正
- 更正公用 IP 使用量記錄日期/時間戳記的修正
- 各種其他的效能、穩定性和安全性修正
- TimeSource 和 Defender 特殊權限端點模組錯誤修正

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 新功能和修正程式

- [2018 年 1 月 3 日—KB4056890 (OS 組建 14393.2007)](https://support.microsoft.com/help/4056890/windows-10-update-kb4056890)
    - 此更新包含 [MSRC Security Advisory ADV 180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) 所描述之業界安全性問題的軟體修正。

### <a name="known-issues-with-the-update-process"></a>關於更新程序的已知問題

本節包含在 1712 更新安裝時可能遇到的已知問題。

1. **徵兆：**Azure Stack 作業員在更新程序期間可能會看到下列錯誤：*"角色 'VirtualMachines' 的類型 'CheckHealth' 引發了例外狀況：\n\n-ACS01 的虛擬機器健康情況檢查產生下列錯誤。\n從主機取得 VM 資訊時發生錯誤。例外狀況詳細資料：\nGet-VM : 電腦上的 'Node03' 作業失敗：WS-Management 服務無法處理要求。WMI \nservice 或 WMI 提供者傳回未知的錯誤：HRESULT 0x8004106c"。*
    1. **原因：**這個問題是因為 Windows Server 問題所造成，會在後續 Windows Server 更新中獲得解決。
    2. **解決方式：**請連絡 Microsoft 客戶服務與支援中心 (CSS) 以尋求協助。
<br><br>
2. **徵兆：**Azure Stack 作業員在更新程序期間可能會看到下列錯誤：*"在 Host-Node03 節點上啟用種子通道 VM 失敗，並產生下列錯誤：[Host-Node03] 連線至遠端伺服器 Host-Node03 失敗，並產生下列錯誤訊息：WinRM 用戶端收到 HTTP 伺服器錯誤狀態 (500)，但遠端服務並未包含關於失敗原因的任何其他資訊。"*
    1. **原因：**這個問題是因為 Windows Server 問題所造成，會在後續 Windows Server 更新中獲得解決。 
    2. **解決方式：**請連絡 Microsoft 客戶服務與支援中心 (CSS) 以尋求協助。
<br><br>

### <a name="known-issues-post-installation"></a>已知問題 (安裝後)

本節包含關於組建 **180106.1** 的安裝後已知問題。

#### <a name="portal"></a>入口網站

- 它無法在管理員入口網站中檢視計算或儲存體資源。 這表示在更新安裝期間發生錯誤，且誤將更新報告為成功。 如果發生此問題，請連絡 Microsoft CSS 以尋求協助。
- 您可能會在入口網站中看到空白的儀表板。 若要復原儀表板，請選取位於入口網站右上角的齒輪圖示，然後選取 [還原預設設定]。
- 當您檢視資源群組的內容時，會停用 [移動] 按鈕。 這是預期的行為。 目前不支援在訂用帳戶之間移動資源群組。
- 針對任何您可在下拉式清單中選取訂用帳戶、資源群組或位置的工作流程，您可能會遇到一或多個下列問題：

   - 您可能會在清單頂端看到一個空白資料列。 您應該仍能如預期般選取項目。
   - 如果下拉式清單中的項目清單很短，您可能無法檢視任何項目名稱。
   - 如果您有多個使用者訂用帳戶，資源群組的下拉式清單可能是空的。

        > [!NOTE]
        > 若要解決後兩個問題，您可以輸入訂用帳戶或資源群組的名稱 (如果您知道)，或者可以改用 PowerShell。

- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。
- 您無法使用 Azure Stack 入口網站檢視訂用帳戶的權限。 因應措施是，可以使用 PowerShell 確認權限。
- [服務健全狀況] 刀鋒視窗無法載入。 當您在管理員或使用者入口網站中開啟 [服務健全狀況] 刀鋒視窗時，Azure Stack 顯示錯誤而未載入資訊。 這是預期行為。 雖然您可以選取並開啟 [服務健全狀況]，不過此功能尚無法供使用，但在未來的 Azure Stack 版本中會實作此功能。

#### <a name="health-and-monitoring"></a>健康情況和監視

- 如果您重新啟動基礎結構角色執行個體，則可能會收到訊息，指出重新開機失敗。 不過，重新開機實際上是成功的。

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
- 建立 VM 並與公用 IP 位址建立關聯之後，您就無法取消該 IP 位址與虛擬機器 (VM) 的關聯。 取消關聯看似正常運作，但先前指派的公用 IP 位址仍然會與原始 VM 建立關聯。 即使您將 IP 位址重新指派給新的 VM (通常稱為 *VIP 交換*)，還是會發生這種行為。 之後透過此 IP 位址連線的所有嘗試都會導致連線到原先關聯的 VM，而不是新的 VM。 您目前只有在建立新的 VM 時，才能使用新的公用 IP 位址。
- Azure Stack 操作員可能無法部署、刪除、修改 VNET 或網路安全性群組。 此問題主要會出現在相同套件的後續更新嘗試。 這是因目前正在進行調查的更新之封裝問題所致。
- 內部負載平衡 (ILB) 對 MAC 位址的後端 VM 進行不恰當的處理，導致 Linux 執行個體損壞。

#### <a name="sqlmysql"></a>SQL/MySQL
- 這最多可能需要一個小時，然後租用戶才能在新的 SQL 或 MySQL SKU 中建立資料庫。
- 不支援在不是由資源提供者執行的 SQL 和 MySQL 主控伺服器中直接建立項目，且可能導致不相符的狀態。

    > [!NOTE]
    > 當您將 Azure Stack 整合系統更新至 1712 版時，不應該影響您現有的 SQL 或 MySQL 資源提供者使用者。 您可以繼續使用目前的 SQL 或 MySQL 資源提供者組建，直到可以使用新的 Azure Stack 更新為止。

#### <a name="app-service"></a>App Service 方案
- 在訂用帳戶中建立第一個 Azure 函式之前，使用者必須先註冊儲存體資源提供者。

#### <a name="identity"></a>身分識別

在已部署 Azure Active Directory Federation Services (ADFS) 的環境中，**azurestack\azurestackadmin** 帳戶不再是預設提供者訂用帳戶的擁有者。 並非使用 **azurestack\azurestackadmin** 登入 **Admin portal / adminmanagement endpoint**，您可以使用 **azurestack\cloudadmin** 帳戶，因此您可以管理及使用「預設提供者訂用帳戶」。

> [!IMPORTANT]
> 即使 **azurestack\cloudadmin** 帳戶是 ADFS 部署環境中「預設提供者訂用帳戶」的擁有者，但它並沒有 RDP 到主機的權限。 視需要繼續使用 **azurestack\azurestackadmin** 帳戶或本機系統管理員帳戶登入、存取和管理主機。

## <a name="download-the-update"></a>下載更新

您可以從[這裡](https://aka.ms/azurestackupdatedownload)下載 Azure Stack 1712 更新套件。

## <a name="more-information"></a>詳細資訊

Microsoft 已提供一個方式，可使用與更新 1712 搭配安裝的特殊權限結束點 (PEP) 來監視及繼續更新。

- 請參閱[使用具有特殊權限的端點文件來監視 Azure Stack 中的更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update)。 

## <a name="see-also"></a>另請參閱

- 如需 Azure Stack 中的更新管理概觀，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md)。
- 如需如何使用 Azure Stack 套用更新的相關資訊，請參閱[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)。
