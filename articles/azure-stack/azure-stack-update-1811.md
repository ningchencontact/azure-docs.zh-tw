---
title: Azure Stack 1811 更新 | Microsoft Docs
description: 了解 Azure Stack 整合式系統 1811 更新，包括新功能、已知問題及下載更新的位置。
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
ms.date: 02/28/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: c9f43eb1623ec784bd960fb182dffec48e7b0833
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481241"
---
# <a name="azure-stack-1811-update"></a>Azure Stack 1811 更新

*適用於：Azure Stack 整合式系統*

本文將說明 1811 更新套件的內容。 此更新套件包括此版 Azure Stack 的改良功能、修正及新功能。 本文也說明此版本的已知問題，並包含可供您下載更新的連結。 已知問題分為與更新程序直接相關的問題，以及與組建 (安裝後) 相關的問題。

> [!IMPORTANT]  
> 這個更新程式封裝僅適用於 Azure Stack 整合系統。 請勿將此更新套件套用至 Azure Stack 開發套件。

## <a name="build-reference"></a>建置參考

Azure Stack 1811 更新組建編號為 **1.1811.0.101**。

## <a name="hotfixes"></a>Hotfix

Azure Stack 會定期發行 Hotfix。 將 Azure Stack 更新成 1811 之前，請務必先安裝 1809 的[最新 Azure Stack Hotfix](#azure-stack-hotfixes)。

> [!TIP]  
> 請訂閱下列 *RSS* 或 *Atom* 摘要，以掌握最新的 Azure Stack Hotfix：
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack Hotfix

- **1809**：[KB 4481548 – Azure Stack Hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**：沒有目前的 Hotfix 可供使用。

## <a name="prerequisites"></a>必要條件

> [!IMPORTANT]
> 在安裝 1811 更新的期間，您必須確保所有系統管理員入口網站執行個體都已關閉。 使用者入口網站可以保持開啟，但系統管理員入口網站必須關閉。

- 針對 Azure Stack 延伸主機做好 Azure Stack 部署準備。 使用下列指引來準備您的系統：[Azure Stack 的延伸主機準備](azure-stack-extension-host-prepare.md)。 
 
- 在更新成 1811 之前，請先安裝 1809 的[最新 Azure Stack Hotfix](#azure-stack-hotfixes)。

- 開始安裝此更新之前，請先執行 [Test-AzureStack](azure-stack-diagnostic-test.md) 與下列參數來驗證 Azure Stack 的狀態，並解決所發現的一切運作問題，包括所有警告和失敗。 也請檢閱作用中警示，並將所有需要採取動作的警示解決。  

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    如果未符合延伸主機需求，`Test-AzureStack` 輸出會顯示下列訊息： 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare`

- Azure Stack 1811 更新會要求您已將必要的延伸主機憑證適當地匯入至 Azure Stack 環境。 若要繼續進行 1811 更新安裝，您必須匯入延伸主機所需的 SSL 憑證。 若要匯入憑證，請參閱[這一節](azure-stack-extension-host-prepare.md#import-extension-host-certificates)。

    如果您忽略每個警告並仍選擇安裝 1811 更新，則更新將在大約 1 小時內失敗，並顯示下列訊息：   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    適當地匯入必要的延伸主機憑證之後，您便可以從系統管理員入口網站繼續進行 1811 更新。 雖然 Microsoft 建議 Azure Stack 操作員在更新期間排程維護時段，但因缺少延伸主機憑證而造成的失敗應該不會影響現有的工作負載或服務。  

    在安裝此更新的期間，於設定延伸主機時會無法使用 Azure Stack 使用者入口網站。 延伸主機的設定最多可能需要 5 小時的時間。 在這段期間，您可以檢查更新狀態，或是使用 [Azure Stack 系統管理員 PowerShell 或具有特殊權限的端點](azure-stack-monitor-update.md)來繼續安裝失敗的更新。

- 當 Azure Stack 由 System Center Operations Manager (SCOM) 進行管理時，請務必先將適用於 Microsoft Azure Stack 的管理組件更新為 1.0.3.11 版，再套用 1811。

## <a name="new-features"></a>新功能

此更新包含下列適用於 Azure Stack 的新功能和改良功能：

- 在此版本中，會啟用[延伸主機](azure-stack-extension-host-prepare.md)。 延伸主機可簡化網路整合，並改善 Azure Stack 的安全性狀態。

- 已新增透過「Active Directory 同盟服務」(AD FS) 進行裝置驗證的支援 (特別是使用 Azure CLI 時)。 如需詳細資訊，請參閱[在 Azure Stack 中搭配 Azure CLI 使用 API 版本設定檔](./user/azure-stack-version-profiles-azurecli2.md)

- 已新增搭配「Active Directory 同盟服務」(AD FS) 使用用戶端祕密的服務主體支援。 如需詳細資訊，請參閱[為 AD FS 建立服務主體](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs)。

- 此版本新增適用於下列「Azure 儲存體服務 API」版本的支援：**2017-07-29**、**2017-11-09**。 此外，也新增了適用於下列「Azure 儲存體資源提供者 API」版本的支援：**2016-05-01**、**2016-12-01**、**2017-06-01** 及 **2017-10-01**。 如需詳細資訊，請參閱 [Azure Stack 儲存體：差異與注意事項](./user/azure-stack-acs-differences.md)。

- 已新增新的具特殊權限端點命令來更新和移除 ADFS 的服務主體。 如需詳細資訊，請參閱[為 AD FS 建立服務主體](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs)。

- 已新增可讓 Azure Stack 操作員啟動、停止及關閉縮放單位節點的新「縮放單位節點」作業。 如需詳細資訊，請參閱 [Azure Stack 中的縮放單位節點動作](azure-stack-node-actions.md)。

- 已新增會顯示環境註冊詳細資料的新區域屬性刀鋒視窗。 您可以按一下系統管理員入口網站中預設儀表板上的 [Region Management] \(區域管理\) 圖格，然後選取 [Properties] \(屬性\)，來檢視此資訊。

- 已新增新的具特殊權限端點命令，以將 BMC 認證更新成用來與實體機器進行通訊的使用者名稱和密碼。 如需詳細資訊，請參閱[更新基礎板管理控制器 \(BMC) 認證](azure-stack-rotate-secrets.md)。

- 已新增透過系統管理員和使用者入口網站右上角的說明與支援圖示 (問號) 存取 Azure 藍圖的功能，類似於在 Azure 入口網站中的使用方式。

- 針對已中斷連線的使用者，已新增改善的 Marketplace 管理體驗。 在已中斷連線之環境中發佈 Marketplace 項目的上傳程序已簡化成一個步驟，而無須個別上傳映像和 Marketplace 套件。 已上傳的產品將也會顯示在 Marketplace 管理刀鋒視窗中。 如需詳細資訊，請參閱[本節](azure-stack-download-azure-marketplace-item.md#import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher)。 

- 此版本藉由新增在 [Azure Stack 祕密輪替](azure-stack-rotate-secrets.md)期間只輪替外部憑證的功能，縮短了輪替祕密所需的維護時段。

- [Azure Stack PowerShell](azure-stack-powershell-install.md) 已更新為 1.6.0 版。 此更新包含對 Azure Stack 中新儲存體相關功能的支援。 如需詳細資訊，請參閱 [PowerShell 資源庫中的 Azure Stack 管理模組 1.6.0](https://www.powershellgallery.com/packages/AzureStack/1.6.0) \(英文\)。如需有關更新或安裝 Azure Stack PowerShell 的資訊，請參閱[安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。

- 使用 Azure Stack 入口網站來建立虛擬機器時，現在會預設啟用「受控磁碟」。 如需了解「受控磁碟」所需的額外步驟以避免 VM 建立失敗，請參閱[已知問題](#known-issues-post-installation)一節。

- 此版本為 Azure Stack 操作員導入了警示**修復**動作。 1811 中的一些警示在警示中有提供 [修復] 按鈕，可供您選取來解決問題。 如需詳細資訊，請參閱[在 Azure Stack 中監視健康情況和警示](azure-stack-monitor-health.md)。

- 更新 Azure Stack 中的更新體驗。 更新增強功能包括： 
  - 從更新記錄中分割更新的索引標籤，以便對進行中和已完成的更新進行更好的追蹤。
  - [essentials] 區段中已增強的狀態視覺效果，其中含有適用於目前和 OEM 版本的新圖示和版面配置，以及上次更新日期。
  - 版本資訊資料行的 [檢視] 連結讓使用者能夠直接存取該更新特定的文件，而不是一般更新頁面。
  - [更新記錄] 索引標籤，可用來判斷每個更新的執行次數，以及增強的篩選功能。  
  - 已連線的 Azure Stack 縮放單位仍然將在其變成可用時，自動收到**有可用的更新**。
  - 未連線的 Azure Stack 縮放單位可以像以前一樣匯入更新。 
  - 從入口網站下載 JSON 記錄的程序中沒有任何變更。 Azure Stack 操作員將會看到表示進度的展開步驟。

    如需詳細資訊，請參閱[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)。


## <a name="fixed-issues"></a>已修正的問題

<!-- TBD - IS ASDK --> 
- 已修正此問題：公用 IP 位址使用計量資料針對每一筆記錄都顯示相同的 **EventDateTime** 值，而不是顯示建立記錄時所顯示的 **TimeDate** 時間戳記。 您現在可以使用此資料來執行公用 IP 位址使用情況的精確計算。

<!-- 3099544 – IS, ASDK --> 
- 已修正使用 Azure Stack 入口網站來建立新虛擬機器 (VM) 時所發生的問題。 選取 VM 大小導致 [美元/月] 資料行顯示「無法使用」訊息。 此資料行不再出現；Azure Stack 不支援顯示 VM 定價資料行。

<!-- 2930718 - IS ASDK --> 
- 已修正此問題：於系統管理員入口網站中存取任何使用者訂用帳戶的詳細資料時，在關閉刀鋒視窗並按一下 [最近] 之後，並未顯示使用者訂用帳戶名稱。 現在已會顯示使用者訂用帳戶名稱。

<!-- 3060156 - IS ASDK --> 
- 已修正此問題：在系統管理員和使用者入口網站中，按一下入口網站設定並選取 [刪除所有設定和私人儀表板] 之後，並未如預期般運作，而且顯示錯誤通知。 此選項現在已正確運作。

<!-- 2930799 - IS ASDK --> 
- 已修正此問題：在系統管理員和使用者入口網站中，[所有服務] 底下不正確地列出 [DDoS 保護計劃] 資產。 它無法在 Azure Stack 中使用。 此清單已被移除。
 
<!--2760466 – IS  ASDK --> 
- 已修正安裝新 Azure Stack 環境時發生的問題，亦即未顯示指出「需要啟用」的警示。 現在已可正確顯示。

<!--1236441 – IS  ASDK --> 
- 已修正使用 ADFS 時防止將 RBAC 原則套用至使用者群組的問題。

<!--3463840 - IS, ASDK --> 
- 已修正因無法從公用 VIP 網路存取檔案伺服器而導致基礎結構備份失敗的問題。 此修正會將基礎結構備份服務移回到公用基礎結構網路。 如果您已套用解決此問題的最新 [Azure Stack 1809 Hotfix](#azure-stack-hotfixes)，1811 更新將不會進行任何進一步的修改。 

<!-- 2967387 – IS, ASDK --> 
- 已修正此問題：您用來登入 Azure Stack 系統管理員或使用者入口網站的帳戶顯示為「無法辨識的使用者」。 當帳戶未指定「名字」或「姓氏」時，就會顯示此訊息。   

<!--  2873083 - IS ASDK --> 
- 已修正此問題：使用 Internet Explorer 透過入口網站建立虛擬機器擴展集 (VMSS) 時，導致 [執行個體大小] 下拉式清單無法正確載入。 此瀏覽器現在已正確運作。  

<!-- 3190553 - IS ASDK -->
- 已修正產生指出某個「基礎結構角色執行個體」無法供使用或「縮放單位節點」已離線等雜訊警示的問題。

<!-- 2724961 - IS ASDK -->
- 已修正此問題：VM 概觀頁面無法正確顯示 VM 計量圖表。 

## <a name="changes"></a>變更

- 1811 中已導入新的方式來檢視及編輯方案中的配額。 如需詳細資訊，請參閱[檢視現有的配額](azure-stack-quota-types.md#view-an-existing-quota)。

<!-- 3083238 IS -->
- 此更新中的安全性增強功能導致目錄服務角色的備份大小增加。 如需已更新的外部儲存體位置大小調整指引，請參閱[基礎結構備份文件](azure-stack-backup-reference.md#storage-location-sizing)。 這項變更導致需要更多時間才能完成備份，因為資料傳輸大小變大。 這項變更會影響整合式系統。 

- 現有用來擷取 BitLocker 修復金鑰的 PEP Cmdlet 在 1811 中已從 Get-AzsCsvsRecoveryKeys 重新命名為 Get-AzsRecoveryKeys。 如需有關擷取 BitLocker 修復金鑰的詳細資訊，請參閱[有關如何擷取金鑰的指示](azure-stack-security-bitlocker.md)。

## <a name="common-vulnerabilities-and-exposures"></a>常見的弱點與漏洞

此更新會安裝下列安全性更新：  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)

如需關於這些弱點的詳細資訊，請按一下上述連結，或參閱 Microsoft 知識庫文章 [4478877](https://support.microsoft.com/help/4478877)。

## <a name="known-issues-with-the-update-process"></a>關於更新程序的已知問題

- 當您執行 **Test-AzureStack** 之後，在相同的特殊權限端點 (PEP) 工作階段中執行 **Get-AzureStackLog** PowerShell Cmdlet 時，**Get-AzureStackLog** 會失敗。 若要解決此問題，請關閉您在其中執行 **Test-AzureStack** 的 PEP 工作階段，然後開啟新的工作階段來執行 **Get-AzureStackLog**。

- 在安裝 1811 更新的期間，請確保所有系統管理員入口網站執行個體在此期間都已關閉。 使用者入口網站可以保持開啟，但系統管理員入口網站必須關閉。

- 執行 [Test-AzureStack](azure-stack-diagnostic-test.md) 時，如果 **AzsInfraRoleSummary** 或 **AzsPortalApiSummary** 測試失敗，系統會提示您搭配 `-Repair` 旗標來執行 **Test-AzureStack**。  如果您執行此命令，它會失敗並顯示下列錯誤訊息：`Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  在未來的版本中會修正此問題。

- 在安裝 1811 更新的期間，於設定延伸主機時會無法使用 Azure Stack 使用者入口網站。 延伸主機的設定最多可能需要 5 小時的時間。 在這段期間，您可以檢查更新狀態，或是使用 [Azure Stack 系統管理員 PowerShell 或具有特殊權限的端點](azure-stack-monitor-update.md)來繼續安裝失敗的更新。 

- 在安裝 1811 更新的期間，可能無法使用使用者入口網站，並可能遺失自訂值。 您可以在更新完成之後，開啟入口網站設定，然後選取 [還原預設設定]，將儀表板還原至預設設定。

- 當您執行 [Test-AzureStack](azure-stack-diagnostic-test.md) 時，會顯示來自「基礎板管理控制器」(BMC) 的警告訊息。 您可以放心地忽略此警告。

- <!-- 2468613 - IS --> 安裝此更新時，您可能會看到具有下列標題的警示：`Error – Template for FaultType UserAccounts.New is missing.`。您可以放心地忽略這些警示。 在此更新安裝完成之後，這些警示會自動關閉。

- <!-- 3139614 | IS --> 如果您已從 OEM 套用 Azure Stack 更新，Azure Stack 系統管理員入口網站中可能就不會出現「已有可用的更新」通知。 若要安裝 Microsoft 更新，請使用此處[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)的指示，手動下載並匯入它。

## <a name="post-update-steps"></a>更新後步驟

- 在安裝此更新之後，安裝任何適用的 Hotfix。 如需詳細資訊，請參閱 [Hotfix](#hotfixes)，以及我們的[服務原則](azure-stack-servicing-policy.md)。  

- 擷取待用資料加密金鑰，並將它們安全地儲存在 Azure Stack 部署外部。 請依照[有關如何擷取金鑰的指示](azure-stack-security-bitlocker.md)進行操作。

## <a name="known-issues-post-installation"></a>已知問題 (安裝後)

以下是此組建版本的安裝後已知問題。

### <a name="portal"></a>入口網站

<!-- 2930820 - IS ASDK --> 
- 在系統管理員和使用者入口網站中，如果您搜尋 "Docker"，項目未正確傳回。 它無法在 Azure Stack 中使用。 如果您嘗試建立它，則會顯示具有錯誤指示的刀鋒視窗。 

<!-- 2931230 – IS  ASDK --> 
- 無法刪除以附加方案形式新增至使用者訂用帳戶的方案 (即使您從使用者訂用帳戶中移除此方案)。 此方案會持續保留，直到參考附加方案的訂用帳戶也遭到刪除為止。 

<!-- TBD - IS ASDK --> 
- 不應該使用隨 1804 版導入的兩個系統管理訂用帳戶類型。 這些訂用帳戶類型為「計量訂用帳戶」和「取用訂用帳戶」。 自 1804 版起，這些訂用帳戶類型就已經會在新的 Azure Stack 環境中顯示，只是還未就緒以供使用。 請繼續使用「預設提供者」訂用帳戶類型。

<!-- TBD - IS ASDK --> 
- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。

<!-- TBD - IS ASDK --> 
- 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 [PowerShell 來確認權限](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan)。

### <a name="health-and-monitoring"></a>健康情況和監視

<!-- 1264761 - IS ASDK -->  
- 您可能會看到「健康情況控制器」元件出現具有下列詳細資料的警示：  

  - 警示 #1：
     - 名稱：基礎結構角色狀況不良
     - 嚴重性：警告
     - 元件：健康情況控制器
     - 描述：健康情況控制器活動訊號掃描器無法使用。 這可能會影響健康情況報告和計量。  

  - 警示 #2：
     - 名稱：基礎結構角色狀況不良
     - 嚴重性：警告
     - 元件：健康情況控制器
     - 描述：健康情況控制器錯誤掃描器無法使用。 這可能會影響健康情況報告和計量。

    您可以放心地忽略這兩個警示。 這兩個警示會在一段時間過後自動關閉。  

### <a name="compute"></a>計算

- 建立新的 Windows 虛擬機器 (VM) 時，[設定] 刀鋒視窗會要求您必須選取公用輸入連接埠，才能繼續進行操作。 在 1811 中，這是必要設定，但沒有作用。 這是因為此功能倚賴「Azure 防火牆」，但在 Azure Stack 中並未實作此防火牆。 您可以選取 [沒有任何公用的輸入連接埠] 或任何其他選項來繼續建立 VM。 此設定將不會有任何作用。

- 在建立新的 Windows 虛擬機器 (VM) 時，可能會顯示下列錯誤：

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   如果您啟用 VM 上的開機診斷，但刪除您的開機診斷儲存體帳戶，就會發生此錯誤。 若要解決此問題，請重新建立儲存體帳戶，該帳戶的名稱與您先前使用的名稱相同。

- 建立 [Dv2 系列 VM](./user/azure-stack-vm-considerations.md#virtual-machine-sizes) 時，D11-14v2 VM 可讓您分別建立 4、8、16 和 32 個資料磁碟。 不過，建立 VM 窗格會顯示 8、16、32 和 64 個資料磁碟。

- 在 Azure Stack 上的使用量記錄可能有非預期的大小寫；例如：

   `{"Microsoft.Resources":{"resourceUri":"/subscriptions/<subid>/resourceGroups/ANDREWRG/providers/Microsoft.Compute/
   virtualMachines/andrewVM0002","location":"twm","tags":"null","additionalInfo":
   "{\"ServiceType\":\"Standard_DS3_v2\",\"ImageType\":\"Windows_Server\"}"}}`

   在此範例中，資源群組的名稱應為 **AndrewRG**。 您可以放心地忽略此不一致的狀況。

<!-- 3235634 – IS, ASDK -->
- 若要部署大小包含 **v2** 後置詞的 VM (例如 **Standard_A2_v2**)，請將後置詞指定為 **Standard_A2_v2** (小寫 v)。 請勿使用 **Standard_A2_V2** (大寫 V)。 這適用於全域 Azure，並且在 Azure Stack 上有不一致的問題。

<!-- 2869209 – IS, ASDK --> 
- 當使用 [**Add-AzsPlatformImage** cmdlet](/powershell/module/azs.compute.admin/add-azsplatformimage) 時，您必須使用 **-OsUri** 參數作為要上傳磁碟的儲存體帳戶 URI。 如果您使用磁碟的本機路徑，此 Cmdlet 就會失敗，且具有下列錯誤： 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- 當您使用入口網站建立進階 VM 大小 (DS、Ds_v2、FS、FSv2) 的虛擬機器 (VM) 時，會在標準儲存體帳戶中建立該 VM。 在標準儲存體帳戶中建立不會影響功能、IOP 或帳單。 您可以安心忽略指出下列訊息的警告： 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- 虛擬機器擴展集 (VMSS) 建立體驗會提供 CentOS 架構 7.2 作為部署選項。 因為該映像在 Azure Stack 上無法使用，所以請為您的部署選取其他作業系統，或使用指定另一個 CentOS 映像 (已由操作員在部署前從市集下載) 的 Azure Resource Manager 範本。  

<!-- 2724873 - IS --> 
- 開始使用 PowerShell Cmdlet **Start-AzsScaleUnitNode** 或 **Stop-AzsScaleunitNode** 來管理縮放單位時，第一次嘗試啟動或停止縮放單位可能會失敗。 如果第一次執行 Cmdlet 時發生失敗，請再次執行 Cmdlet。 第二次執行應該會成功地完成此作業。 

<!-- TBD - IS ASDK --> 
- 如果在 VM 部署上佈建延伸模組所花費的時間太長，請讓佈建逾時，而不要嘗試停止程序來將 VM 解除配置或刪除。  

<!-- 1662991 IS ASDK --> 
- Azure Stack 不支援 Linux VM 診斷。 當您部署啟用了 VM 診斷的 Linux VM 時，部署會失敗。 如果您透過診斷設定啟用 Linux VM 基本計量，部署也會失敗。  

<!-- 3507629 - IS, ASDK --> 
- 受控磁碟會建立兩個新的[計算配額類型](azure-stack-quota-types.md#compute-quota-types)，以限制可以佈建的受控磁碟最大容量。 根據預設，會為每個受控磁碟配額類型配置 2048 GiB。 但是，您可能會遇到下列問題：

   - 針對在 1808 更新之前建立的配額，雖然配置了 2048 GiB，但是在系統管理員入口網站中，受控磁碟配額會顯示 0 值。 您可以根據實際需求增加或減少該值，新設定的配額值會覆寫預設值 2048 GiB。
   - 如果您將配額值更新為 0，它等於預設值 2048 GiB。 因應措施是將配額值設為 1。

<!-- TBD - IS ASDK --> 
- 套用 1811 更新之後，在部署具有「受控磁碟」的 VM 時，您可能會遇到下列問題：

   - 如果訂用帳戶是在 1808 更新之前建立的，部署具有受控磁碟的 VM 可能會失敗，且會有內部錯誤訊息。 若要解決此錯誤，請針對每個訂用帳戶遵循下列步驟：
      1. 在租用戶入口網站中，移至 [訂用帳戶] 並尋找訂用帳戶。 選取 [資源提供者]，然後選取 [Microsoft.Compute]，接著按一下 [重新註冊]。
      2. 在相同的訂用帳戶底下，移至 [存取控制 (IAM)]，並確認 [AzureStack-DiskRP-Client] 角色已列出。
   - 如果您已設定多租用戶環境，則在與來賓目錄相關聯的訂用帳戶中部署 VM 可能會失敗，且會有內部錯誤訊息。 若要解決此錯誤，請依照[這篇文章](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的步驟重新設定您的每個來賓目錄。

- 所建立的 Ubuntu 18.04 VM 如果已啟用 SSH 授權，將不會允許您使用 SSH 金鑰來登入。 因應措施是，在佈建後使用「適用於 Linux 的 VM 存取」延伸模組來實作 SSH 金鑰，或使用密碼型驗證。

### <a name="networking"></a>網路功能  

<!-- 1766332 - IS ASDK --> 
- 如果您在 [網路] 下按一下 [建立 VPN 閘道] 來設定 VPN 連線，系統就會將 [原則式] 列為 VPN 類型。 請勿選取此選項。 Azure Stack 只支援 [路由式] 選項。

<!-- 1902460 - IS ASDK --> 
- Azure Stack 支援每一 IP 位址有一個「區域網路閘道」。 這適用於所有租用戶訂用帳戶。 在建立第一個區域網路閘道連線之後，後續若使用相同的 IP 位址來嘗試建立區域網路閘道資源，系統都會予以拒絕。

<!-- 16309153 - IS ASDK --> 
- 在 DNS 伺服器設定為 [自動] 的已建立虛擬網路上，變更成自訂 DNS 伺服器會失敗。 更新的設定並未推送至該 Vnet 中的 VM。

<!-- 2529607 - IS ASDK --> 
- 在 Azure Stack「祕密輪替」期間，會有一段 2 到 5 分鐘的期間無法存取公用 IP 位址。

<!-- 2664148 - IS ASDK --> 
- 在租用戶使用 S2S VPN 通道來存取虛擬機器的案例中，如果在已經建立區域網路閘道之後，才將內部部署子網路新增至該閘道，租用戶就可能遇到連線嘗試失敗的情況。 

- 在 Azure Stack 入口網站中，針對已連結至 VM 執行個體的網路介面卡，當您變更與其繫結之 IP 設定的靜態 IP 位址時，將會看到內容如下的警告訊息： 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...` 。 

    您可以放心地忽略此訊息；即使 VM 執行個體並未重新啟動，IP 位址也將會變更。

- 在入口網站中的 [網路內容] 刀鋒視窗上，每個網路介面卡都有一個 [	有效的安全性規則] 連結。 如果您選取此連結，就會開啟一個新的刀鋒視窗，當中會顯示此錯誤訊息：`Not Found.` 之所以會發生此錯誤，是因為 Azure Stack 尚未支援 [有效的安全性規則]。

- 在入口網站中，如果您新增連入安全性規則並選取 [服務標籤] 作為來源，[服務標籤] 清單中會顯示數個 Azure Stack 無法使用的選項。 在 Azure Stack 中有效的選項僅限於下列幾個：

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    其他選項在 Azure Stack 中不支援用來作為來源標籤。 同樣地，如果您新增連出安全性規則並選取 [服務標籤] 作為目的地，會顯示與 [來源標籤] 相同的選項清單。 僅有的有效選項與 [來源標籤] 的有效選項相同，如以上清單所述。

- **New-AzureRmIpSecPolicy** PowerShell Cmdlet 不支援為 `DHGroup` 參數設定 **DHGroup24**。

- 網路安全性群組 (NSG) 無法以全域 Azure 中的相同方式在 Azure Stack 中運作。 在 Azure 中，您可以在一個 NSG 規則上設定多個連接埠 (使用入口網站、PowerShell 和 Resource Manager 範本)。 在 Azure Stack 中，您無法透過入口網站在一個 NSG 規則上設定多個連接埠。 若要解決此問題，請使用 Resource Manager 範本來設定這些額外的規則。

### <a name="infrastructure-backup"></a>基礎結構備份

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- 在啟用自動備份之後，排程器服務會意外進入已停用狀態。 備份控制器服務將會偵測到自動備份已停用，並在系統管理員入口網站中提出警告。 這是自動備份停用時預期會有的警告。 
    - 原因：之所以會有此問題，是因為服務中有會導致排程器設定遺失的 Bug。 此 Bug 不會變更儲存體位置、使用者名稱、密碼或加密金鑰。   
    - 補救：若要降低此問題的風險，請在「基礎結構備份」資源提供者中，開啟備份控制器設定刀鋒視窗，然後選取 [啟用自動備份]。 請務必設定所需的頻率和保留期限。
    - 發生次數：低 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service 方案

<!-- 2352906 - IS ASDK --> 
- 您必須在於訂用帳戶中建立第一個「Azure 函式」之前，先註冊儲存體資源提供者。


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>下載更新

您可以從[這裡](https://aka.ms/azurestackupdatedownload)下載 Azure Stack 1811 更新套件。 

Azure Stack 部署會定期檢查受保護的端點，並在有您雲端適用的更新時自動通知您，但僅限於已連線的情況下。 如需詳細資訊，請參閱[管理 Azure Stack 的更新](azure-stack-updates.md#using-the-update-tile-to-manage-updates)。

## <a name="next-steps"></a>後續步驟

- 若要檢閱 Azure Stack 整合式系統的服務原則，以及必須如何做才能將狀態保持在支援的狀態，請參閱 [Azure Stack 服務原則](azure-stack-servicing-policy.md)。  
- 若要使用具有特殊權限的端點 (PEP) 來監視和繼續更新，請參閱[使用具有特殊權限的端點來監視 Azure Stack 中的更新](azure-stack-monitor-update.md)。  
- 如需 Azure Stack 中的更新管理概觀，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md)。  
- 如需如何使用 Azure Stack 套用更新的相關資訊，請參閱[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)。  
