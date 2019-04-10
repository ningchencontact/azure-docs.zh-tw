---
title: Azure Stack 1902 更新 | Microsoft Docs
description: 了解 Azure Stack 整合式系統 1902 更新，包括新功能、已知問題及下載更新的位置。
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
ms.date: 04/03/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/03/2019
ms.openlocfilehash: 5971692b3e6447bc790b2e34cf84eae66979f7f5
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862075"
---
# <a name="azure-stack-1902-update"></a>Azure Stack 1902 更新

*適用於：Azure Stack 整合系統*

本文將說明 1902 更新套件的內容。 此更新包括此版 Azure Stack 的改良功能、修正及新功能。 本文也說明此版本的已知問題，並包含下載更新的連結。 已知問題分為與更新程序直接相關的問題，以及與組建 (安裝後) 相關的問題。

> [!IMPORTANT]  
> 這個更新程式封裝僅適用於 Azure Stack 整合系統。 請勿將此更新套件套用至 Azure Stack 開發套件。

## <a name="build-reference"></a>建置參考

Azure Stack 1902 更新組建編號為 **1.1902.0.69**。

## <a name="hotfixes"></a>Hotfix

Azure Stack 會定期發行 Hotfix。 將 Azure Stack 更新成 1902 之前，請務必先安裝 1901 的[最新 Azure Stack Hotfix](#azure-stack-hotfixes)。

Azure Stack Hotfix 僅適用於 Azure Stack 整合系統，請勿嘗試在 ASDK 上安裝 Hotfix。

> [!TIP]  
> 請訂閱下列 *RSS* 或 *Atom* 摘要，以掌握最新的 Azure Stack Hotfix：
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack Hotfix

- **1809**：[KB 4481548 – Azure Stack Hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**：沒有目前的 Hotfix 可供使用。
- **1901**:[KB 4495662 – Azure Stack Hotfix 1.1901.3.105](https://support.microsoft.com/help/4495662)
- **1902**：[KB 4494719 – Azure Stack Hotfix 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="prerequisites"></a>必要條件

> [!IMPORTANT]
> 您不需要先安裝任何 1901 Hotfix，就可以從 [1.1901.0.95 或 1.1901.0.99](azure-stack-update-1901.md#build-reference) 版直接安裝 1902。 不過，如果您已安裝舊版 **1901.2.103** Hotfix，在繼續安裝 1902 之前，您必須先安裝較新的 [1901.3.105 Hotfix](https://support.microsoft.com/help/4495662)。

- 開始安裝此更新之前，請先執行 [Test-AzureStack](azure-stack-diagnostic-test.md) 與下列參數來驗證 Azure Stack 的狀態，並解決所發現的一切運作問題，包括所有警告和失敗。 也請檢閱作用中警示，並將所有需要採取動作的警示解決：

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- 當 Azure Stack 由 System Center Operations Manager (SCOM) 進行管理時，請務必先將[適用於 Microsoft Azure Stack 的管理組件](https://www.microsoft.com/download/details.aspx?id=55184)更新為 1.0.3.11 版，再套用 1902。

- 從 1902 版本開始，Azure Stack 更新的套件格式已從 **.bin/.exe/.xml** 變更為 **.zip/.xml**。 具有已連線 Azure Stack 縮放單位的客戶會在入口網站中看到**有可用更新**的訊息。 未連線的客戶現在可以直接下載並匯入具有對應 .xml 的 .zip 檔案。

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>改進

- 1902 組建在 Azure Stack 系統管理員入口網站上導入了新的使用者介面，可供建立方案、供應項目、配額和附加方案。 如需詳細資訊 (包括螢幕擷取畫面)，請參閱[建立方案、供應項目和配額](azure-stack-create-plan.md)。

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- 改善新增節點期間，縮放單位狀態從「正在擴充儲存體」切換至執行中狀態時的容量擴充可靠性。

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- 為了改進套件的完整性和安全性，以及簡化離線擷取的管理工作，Microsoft 已將更新套件的格式從 .exe 和 .bin 檔案變更為 .zip 檔案。 新的格式讓有時會導致更新的準備工作延滯的解壓縮程序變得更可靠。 相同的套件格式也適用於來自 OEM 的更新套件。
- 相對於在 Include 陳述式後面另外傳遞 10 個參數，操作員現在可以直接使用 "Test-AzureStack -Group UpdateReadiness"，來改善其在執行 Test-AzureStack 時的 Azure Stack 操作員體驗。

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- 為了改善核心基礎結構服務在更新程序進行期間的整體可靠性和可用性，屬於更新動作方案一部分的原生更新資源提供者會在需要時偵測並叫用自動的全域補救。 全域補救「修復」工作流程包括：
    - 檢查並非處於最佳狀態的基礎結構虛擬機器，並視需要嘗試進行修復 
    - 檢查屬於控制方案一部分的 SQL 服務問題，並視需要嘗試進行修復
    - 檢查屬於網路控制卡 (NC) 一部分的軟體負載平衡器 (SLB) 服務是什麼狀態，並視需要嘗試進行修復
    - 檢查網路控制卡 (NC) 服務的狀態，並視需要嘗試進行修復
    - 檢查緊急修復主控台服務 (ERCS) 服務網狀架構節點的狀態，並視需要進行修復
    - 檢查 XRP 服務網狀架構節點的狀態，並視需要進行修復
    - 檢查 Azure Consistent Storage (ACS) 服務網狀架構節點的狀態，並視需要進行修復

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- 改善新增節點期間，縮放單位狀態從「正在擴充儲存體」切換至執行中狀態時的容量擴充可靠性。    

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- 改善 Azure Stack 的診斷工具，以提升記錄收集的可靠性和效能。 網路和識別服務有了其他記錄功能。 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- 改善 Test-AzureStack 在進行祕密輪替整備測試時的可靠性。

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- 已進行改進以提升 AD Graph 在與客戶的 Active Directory 環境通訊時的可靠性

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- 改善 Get-AzureStackStampInformation 的硬體清查收集功能。

- 為了改進 ERCS 基礎結構上所執行作業的可靠性，每個 ERCS 執行個體的記憶體已從 8 GB 增加至 12 GB。 在 Azure Stack 整合系統安裝中，這會導致整體增加量達 12 GB。

> [!IMPORTANT]
> 為了確保修補和更新程序可將租用戶停機時間降至最低，請確定 Azure Stack 戳記在 [容量] 刀鋒視窗中具有超過 12 GB 的可用空間。 成功安裝更新後，您會在 [容量] 刀鋒視窗中看到此記憶體會隨之增加。

## <a name="common-vulnerabilities-and-exposures"></a>常見的弱點與漏洞

此更新會安裝下列安全性更新：  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

如需關於這些弱點的詳細資訊，請按一下上述連結，或參閱 Microsoft 知識庫文章 [4487006](https://support.microsoft.com/en-us/help/4487006)。

## <a name="known-issues-with-the-update-process"></a>關於更新程序的已知問題

- 當您執行 [Test-AzureStack](azure-stack-diagnostic-test.md) 時，會顯示來自「基礎板管理控制器」(BMC) 的警告訊息。 您可以放心地忽略此警告。

- <!-- 2468613 - IS --> 安裝此更新時，您可能會看到具有下列標題的警示：`Error – Template for FaultType UserAccounts.New is missing.`。您可以放心地忽略這些警示。 在此更新安裝完成之後，這些警示會自動關閉。

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

<!-- 3557860 - IS ASDK --> 
- 刪除使用者訂用帳戶會產生孤立的資源。 因應措施是，先刪除使用者資源或整個資源群組，然後再刪除使用者訂用帳戶。

<!-- 1663805 - IS ASDK --> 
- 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 [PowerShell 來確認權限](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan)。

<!-- ### Health and monitoring -->

### <a name="compute"></a>計算

- 在建立新的 Windows 虛擬機器 (VM) 時，可能會顯示下列錯誤：

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   如果您啟用 VM 上的開機診斷，但刪除您的開機診斷儲存體帳戶，就會發生此錯誤。 若要解決此問題，請重新建立儲存體帳戶，該帳戶的名稱與您先前使用的名稱相同。

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- 虛擬機器擴展集 (VMSS) 建立體驗會提供 CentOS 架構 7.2 作為部署選項。 因為該映像在 Azure Stack 上無法使用，所以請為您的部署選取其他作業系統，或使用指定另一個 CentOS 映像 (已由操作員在部署前從市集下載) 的 Azure Resource Manager 範本。  

<!-- TBD - IS ASDK --> 
- 套用 1902 更新之後，在部署具有「受控磁碟」的 VM 時，您可能會遇到下列問題：

   - 如果訂用帳戶是在 1808 更新之前建立的，部署具有受控磁碟的 VM 可能會失敗，且會有內部錯誤訊息。 若要解決此錯誤，請針對每個訂用帳戶遵循下列步驟：
      1. 在租用戶入口網站中，移至 [訂用帳戶] 並尋找訂用帳戶。 選取 [資源提供者]，然後選取 [Microsoft.Compute]，接著按一下 [重新註冊]。
      2. 在相同的訂用帳戶底下，移至 [存取控制 (IAM)]，並確認 [Azure Stack - 受控磁碟] 已列出。
   - 如果您已設定多租用戶環境，則在與來賓目錄相關聯的訂用帳戶中部署 VM 可能會失敗，且會有內部錯誤訊息。 若要解決此錯誤，請依照[這篇文章](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的步驟重新設定您的每個來賓目錄。

- 所建立的 Ubuntu 18.04 VM 如果已啟用 SSH 授權，將不會允許您使用 SSH 金鑰來登入。 因應措施是，在佈建後使用「適用於 Linux 的 VM 存取」延伸模組來實作 SSH 金鑰，或使用密碼型驗證。

### <a name="networking"></a>網路功能  

<!-- 3239127 - IS, ASDK -->
- 在 Azure Stack 入口網站中，針對已連結至 VM 執行個體的網路介面卡，當您變更與其繫結之 IP 設定的靜態 IP 位址時，將會看到內容如下的警告訊息： 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`上也提供本文中使用的原始碼。

    您可以放心地忽略此訊息；即使 VM 執行個體並未重新啟動，IP 位址也將會變更。

<!-- 3632798 - IS, ASDK -->
- 在入口網站中，如果您新增連入安全性規則並選取 [服務標籤] 作為來源，[服務標籤] 清單中會顯示數個 Azure Stack 無法使用的選項。 在 Azure Stack 中有效的選項僅限於下列幾個：

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  其他選項在 Azure Stack 中不支援用來作為來源標籤。 同樣地，如果您新增連出安全性規則並選取 [服務標籤] 作為目的地，會顯示與 [來源標籤] 相同的選項清單。 僅有的有效選項與 [來源標籤] 的有效選項相同，如以上清單所述。

- 網路安全性群組 (NSG) 無法以全域 Azure 中的相同方式在 Azure Stack 中運作。 在 Azure 中，您可以在一個 NSG 規則上設定多個連接埠 (使用入口網站、PowerShell 和 Resource Manager 範本)。 但是，在 Azure Stack 中，您無法透過入口網站在一個 NSG 規則上設定多個連接埠。 若要解決此問題，請使用 Resource Manager 範本或 PowerShell 來設定這些額外的規則。

<!-- 3203799 - IS, ASDK -->
- 目前 Azure Stack 不支援將 4 個以上的網路介面 (NIC) 連線至 VM 執行個體，無論執行個體的大小是多少。

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service 方案

<!-- 2352906 - IS ASDK --> 
- 您必須在於訂用帳戶中建立第一個「Azure 函式」之前，先註冊儲存體資源提供者。


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>下載更新

您可以從[這裡](https://aka.ms/azurestackupdatedownload)下載 Azure Stack 1902 更新套件。 

Azure Stack 部署會定期檢查受保護的端點，並在有您雲端適用的更新時自動通知您，但僅限於已連線的情況下。 如需詳細資訊，請參閱[管理 Azure Stack 的更新](azure-stack-updates.md#using-the-update-tile-to-manage-updates)。

## <a name="next-steps"></a>後續步驟

- 如需 Azure Stack 中的更新管理概觀，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md)。  
- 如需如何使用 Azure Stack 套用更新的相關資訊，請參閱[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)。
- 若要檢閱 Azure Stack 整合式系統的服務原則，以及必須如何做才能將狀態保持在支援的狀態，請參閱 [Azure Stack 服務原則](azure-stack-servicing-policy.md)。  
- 若要使用具有特殊權限的端點 (PEP) 來監視和繼續更新，請參閱[使用具有特殊權限的端點來監視 Azure Stack 中的更新](azure-stack-monitor-update.md)。  
