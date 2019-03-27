---
title: Azure Stack 1901 更新 | Microsoft Docs
description: 了解 Azure Stack 整合式系統 1901 更新，包括新功能、已知問題及下載更新的位置。
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
ms.date: 03/20/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: e02a09bdc8bd80b93f7fa33632c32a75c1d705bd
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226856"
---
# <a name="azure-stack-1901-update"></a>Azure Stack 1901 更新

*適用於：Azure Stack 整合式系統*

本文將說明 1901 更新套件的內容。 此更新包括此版 Azure Stack 的改良功能、修正及新功能。 本文也說明此版本的已知問題，並包含下載更新的連結。 已知問題分為與更新程序直接相關的問題，以及與組建 (安裝後) 相關的問題。

> [!IMPORTANT]  
> 這個更新程式封裝僅適用於 Azure Stack 整合系統。 請勿將此更新套件套用至 Azure Stack 開發套件。

## <a name="build-reference"></a>建置參考

在 2019 年 2 月 26 日後，Azure Stack 1901 更新的組建編號為 **1.1901.0.95** 或 **1.1901.0.99**。 請參閱下列附註：

> [!IMPORTANT]  
> Microsoft 已發現到有問題會影響從 1811 (1.1811.0.101) 更新至 1901 的客戶，並因此發行了更新的 1901 套件來解決該問題：組建 1.1901.0.99 (從 1.1901.0.95 更新)。 已更新至 1.1901.0.95 的客戶不需要再採取動作。
>
> 1811 上的連線客戶會自動在系統管理員入口網站中看到可用的新 1901 (1.1901.0.99) 套件，請於準備好時進行安裝。 已中斷連線的客戶則可使用[此處所述](azure-stack-apply-updates.md)的相同程序下載並匯入新的 1901 套件。
>
> 使用任一 1901 版本的客戶不會在安裝下一個完整套件或 Hotfix 套件時受到影響。

## <a name="hotfixes"></a>Hotfix

Azure Stack 會定期發行 Hotfix。 將 Azure Stack 更新成 1901 之前，請務必先安裝 1811 的[最新 Azure Stack Hotfix](#azure-stack-hotfixes)。

Azure Stack Hotfix 僅適用於 Azure Stack 整合系統，請勿嘗試在 ASDK 上安裝 Hotfix。

> [!TIP]  
> 請訂閱下列 *RSS* 或 *Atom* 摘要，以掌握最新的 Azure Stack Hotfix：
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack Hotfix

- **1809**：[KB 4481548 – Azure Stack Hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**：沒有目前的 Hotfix 可供使用。
- **1901**:[KB 4481548 – Azure Stack Hotfix 1.1901.2.103](https://support.microsoft.com/help/4494720)

## <a name="prerequisites"></a>必要條件

> [!IMPORTANT]
> - 在更新成 1901 之前，請先安裝 1811 的[最新 Azure Stack Hotfix](#azure-stack-hotfixes) (如果有的話)。

- 開始安裝此更新之前，請先執行 [Test-AzureStack](azure-stack-diagnostic-test.md) 與下列參數來驗證 Azure Stack 的狀態，並解決所發現的一切運作問題，包括所有警告和失敗。 也請檢閱作用中警示，並將所有需要採取動作的警示解決：

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- 當 Azure Stack 由 System Center Operations Manager (SCOM) 進行管理時，請務必先將適用於 Microsoft Azure Stack 的管理組件更新為 1.0.3.11 版，再套用 1901。

## <a name="new-features"></a>新功能

此更新包含下列適用於 Azure Stack 的新功能和改良功能：

- Azure Stack 上的受控映像可讓您在一般化 VM (非受控和受控) 上建立受控映像物件，往後只能建立受控磁碟 VM。 如需詳細資訊，請參閱 [Azure Stack 受控磁碟](user/azure-stack-managed-disk-considerations.md#managed-images)。

- **AzureRm 2.4.0**
   * **AzureRm.Profile**  
         錯誤修正 - `Import-AzureRmContext` 以正確還原序列化儲存的權杖。  
   * **AzureRm.Resources**  
         錯誤修正 - `Get-AzureRmResource` 以依據資源類型不區分大小寫查詢。  
   * **Azure.Storage**  
         AzureRm 彙總模組現在包含已發佈的 4.5.0 版，支援 **api-version 2017-07-29**。  
   * **AzureRm.Storage**  
         AzureRm 彙總模組現在包含已發佈的 5.0.4 版，支援 **api-version 2017-10-01**。  
   * **AzureRm.Compute**  
         在 `New-AzureRmVM` 和 `New-AzureRmVmss` 中新增簡易參數集，`-Image` 參數支援指定使用者映像。  
   * **AzureRm.Insights**  
         AzureRm 彙總模組現在包含已發佈的 5.1.5 版，針對計量、計量定義資源類型支援 **api-version 2018-01-01**。

- **AzureStack 1.7.0** 這是重大變更版本。 如需重大變更的詳細資訊，請參閱 https://aka.ms/azspshmigration170
   * **Azs.Backup.Admin 模組**  
         重大變更：備份對憑證型加密模式所做的變更。 對於對稱金鑰的支援已被取代。  
   * **Azs.Fabric.Admin 模組**  
         `Get-AzsInfrastructureVolume` 已被取代。 使用新的 Cmdlet `Get-AzsVolume`。  
         `Get-AzsStorageSystem` 已被取代。  使用新的 Cmdlet `Get-AzsStorageSubSystem`。  
         `Get-AzsStoragePool` 已被取代。 `StorageSubSystem` 物件包含容量屬性。  
   * **Azs.Compute.Admin 模組**  
         錯誤修正 - `Add-AzsPlatformImage`，`Get-AzsPlatformImage`：呼叫 `ConvertTo-PlatformImageObject` 只能在成功的路徑。  
         錯誤修正 - `Add-AzsVmExtension`，`Get-AzsVmExtension`：呼叫 ConvertTo-VmExtensionObject 只能在成功的路徑。  
   * **Azs.Storage.Admin 模組**  
         錯誤修正 - 如果未提供，則新的儲存體配額會使用預設值。

若要檢閱已更新模組的參考，請參閱 [Azure Stack 模組參考](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0)。

## <a name="fixed-issues"></a>已修正的問題

- 已修正以下問題：入口網站顯示建立原則式 VPN 閘道的選項，該選項在 Azure Stack 中不支援。 已從入口網站中移除此選項。

<!-- 16523695 – IS, ASDK -->
- 已修正以下問題：在將虛擬網路的 DNS 設定從 [使用 Azure Stack DNS] 更新為 [自訂 DNS] 之後，執行個體未隨著新設定更新。

- <!-- 3235634 – IS, ASDK -->
  已修正以下問題：部署包含 **v2** 後置詞大小的 VM (例如 **Standard_A2_v2**) 需要將後置詞指定為 **Standard_A2_v2** (小寫 v)。 使用全域 Azure 時，您現在可以使用 **Standard_A2_V2** (大寫 V)。

<!-- 2869209 – IS, ASDK --> 
- 已修正以下問題：當使用 [Add-AzsPlatformImage Cmdlet](/powershell/module/azs.compute.admin/add-azsplatformimage) 時您必須使用 **-OsUri** 參數作為儲存體帳戶 URI，磁碟上傳到該儲存體帳戶。 您現在也可以使用磁碟的本機路徑。

<!--  2795678 – IS, ASDK --> 
- 已修正以下問題：當您使用入口網站來建立進階 VM 大小 (DS、Ds_v2、FS、FSv2) 的虛擬機器 (VM) 時產生警告。 VM 是建立在標準儲存體帳戶中。 雖然這不會影響功能、IOPs 或計費，但是已修正警告。

<!-- 1264761 - IS ASDK -->  
- 已修正以下問題：**健康情況控制器**元件產生下列警示。 您可以放心地忽略警示：

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


<!-- 3507629 - IS, ASDK --> 
- 已修正以下問題：將[計算配額類型](azure-stack-quota-types.md#compute-quota-types)底下的受控磁碟配額設定為 0 時，它等於預設值 2048 GiB。 現在會遵守零的配額值。

<!-- 2724873 - IS --> 
- 已修正以下問題：使用 PowerShell Cmdlet **Start-AzsScaleUnitNode** 或 **Stop-AzsScaleunitNode** 來管理縮放單位時，第一次嘗試啟動或停止縮放單位可能會失敗。

<!-- 2724961- IS ASDK --> 
- 已修正以下問題：當您在訂用帳戶設定中註冊 **Microsoft.Insight** 資源提供者，並建立已啟用「客體 OS 診斷」功能的 Windows VM 時，VM 概觀頁面中的「CPU 百分比」圖表不會顯示計量資料。 現在已可正確顯示資料。

- 已修正以下問題：在相同的特殊權限端點 (PEP) 工作階段中執行 **Test-AzureStack** 之後，執行 **Get-AzureStackLog** Cmdlet 會失敗。 您現在可以使用在其中執行 **Test-AzureStack** 的相同 PEP 工作階段。

<!-- bug 3615401, IS -->
- 已修正自動備份的問題，其中排程器服務會意外進入已停用狀態。 

<!--2850083, IS ASDK -->
- 已從 Azure Stack 入口網站移除**重設閘道**，在按一下按鈕時會擲回錯誤。 此按鈕在 Azure Stack 中沒有任何功能，因為 Azure Stack 有多用戶閘道，而不是每個租用戶 VPN 閘道有專用 VM 執行個體，因此將其移除以避免混淆。 

<!-- 3209594, IS ASDK -->
- 已從 [網路屬性] 刀鋒視窗移除**有效安全性規則**連結，因為此功能在 Azure Stack 中不支援。 讓連結存在會給人此功能受支援但是無作用的印象。 為了減少混淆，我們已移除連結。

<!-- 3139614 | IS -->
- 已修正以下問題：在更新從 OEM 套用到 Azure Stack 之後，**更新可用**通知未顯示在 Azure Stack 系統管理員入口網站中。

## <a name="changes"></a>變更

<!-- 3083238 IS -->
- 此更新中的安全性增強功能導致目錄服務角色的備份大小增加。 如需已更新的外部儲存體位置大小調整指引，請參閱[基礎結構備份文件](azure-stack-backup-reference.md#storage-location-sizing)。 這項變更導致需要更多時間才能完成備份，因為資料傳輸大小變大。 這項變更會影響整合式系統。 

- 從 2019 年 1 月開始，您可以在已註冊的 Active Directory 同盟服務 (AD FS) 上部署 Kubernetes 叢集，連線 Azure Stack 戳記 (需要網際網路存取)。 請遵循[這裡](azure-stack-solution-template-kubernetes-cluster-add.md)的指示以下載新的 Kubernetes Marketplace 項目。 請遵循[這裡](user/azure-stack-solution-template-kubernetes-adfs.md)的指示以部署 Kubernetes 叢集。 請注意新參數指出目標系統為已註冊的 ADD 或 AD FS。 如果是 AD FS，有新欄位可用於輸入 Key Vault 參數，在其中儲存部署憑證。

   請注意，即使有 AD FS 支援，部署 Kubernetes 叢集還是需要網際網路存取。

- 在安裝 Azure Stack 的更新或 Hotfix 之後，可能會導入新功能，需要將新權限授與一或多個身分識別應用程式。 授與這些權限需要主目錄的系統管理存取權，因此它無法自動完成。 例如︰

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- 準確規劃 Azure Stack 容量方面有新的考量。 使用 1901 更新時，現在可建立的虛擬機器總數有限制。  此限制為暫時，以避免影響解決方案不穩定的情況。 我們已解決大量 VM 的穩定性問題來源，但尚未決定補救措施的特定時間表。 使用 1901 更新時，現在每部伺服器有 60 個 VM 的限制，且總解決方案限制為 700 個。  例如，8 伺服器 Azure Stack VM 的限制會是 480 (8 * 60)。  針對 12 到 16 伺服器 Azure Stack 解決方案，限制將會是 700。 決定此限制時已考慮所有計算容量，例如營運商想要維持的備援能力保留與 CPU 虛擬對實體比率。 如需詳細資訊，請參閱新版本的容量規劃工具。  
萬一達到 VM 規模調整限制，將會傳回下列錯誤碼做為結果：VMsPerScaleUnitLimitExceeded，VMsPerScaleUnitNodeLimitExceeded。 
 

- 計算 API 版本已增加到 2017-12-01。

- 基礎結構備份現在需要僅具有公開金鑰的憑證 (.CER)，來進行備份資料的加密。 對稱加密金鑰支援從 1901 開始已被取代。 如果基礎結構備份在更新至 1901 之前已進行設定，則加密金鑰維持不變。 您有至少 2 個以上的更新具有更新備份設定的回溯相容性支援。 如需詳細資訊，請參閱 [Azure Stack 基礎結構備份最佳做法](azure-stack-backup-best-practices.md)。

## <a name="common-vulnerabilities-and-exposures"></a>常見的弱點與漏洞

此更新會安裝下列安全性更新：  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


如需關於這些弱點的詳細資訊，請按一下上述連結，或參閱 Microsoft 知識庫文章 [4480977](https://support.microsoft.com/en-us/help/4480977)。

## <a name="known-issues-with-the-update-process"></a>關於更新程序的已知問題

- 執行 [Test-AzureStack](azure-stack-diagnostic-test.md) 時，如果 **AzsInfraRoleSummary** 或 **AzsPortalApiSummary** 測試失敗，系統會提示您搭配 `-Repair` 旗標來執行 **Test-AzureStack**。  如果您執行此命令，它會失敗並顯示下列錯誤訊息：`Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

- 當您執行 [Test-AzureStack](azure-stack-diagnostic-test.md) 時，會顯示來自「基礎板管理控制器」(BMC) 的警告訊息。 您可以放心地忽略此警告。

- <!-- 2468613 - IS --> 在安裝此更新的期間，您可能會看到具有下列標題的警示：`Error – Template for FaultType UserAccounts.New is missing.` 您可以放心地忽略這些警示。 在此更新安裝完成之後，這些警示會自動關閉。

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
- 套用 1901 更新之後，在部署具有「受控磁碟」的 VM 時，您可能會遇到下列問題：

   - 如果訂用帳戶是在 1808 更新之前建立的，部署具有受控磁碟的 VM 可能會失敗，且會有內部錯誤訊息。 若要解決此錯誤，請針對每個訂用帳戶遵循下列步驟：
      1. 在租用戶入口網站中，移至 [訂用帳戶] 並尋找訂用帳戶。 選取 [資源提供者]，然後選取 [Microsoft.Compute]，接著按一下 [重新註冊]。
      2. 在相同的訂用帳戶底下，移至 [存取控制 (IAM)]，並確認 [AzureStack-DiskRP-Client] 已列出。
   - 如果您已設定多租用戶環境，則在與來賓目錄相關聯的訂用帳戶中部署 VM 可能會失敗，且會有內部錯誤訊息。 若要解決此錯誤，請依照[這篇文章](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的步驟重新設定您的每個來賓目錄。

- 所建立的 Ubuntu 18.04 VM 如果已啟用 SSH 授權，將不會允許您使用 SSH 金鑰來登入。 因應措施是，在佈建後使用「適用於 Linux 的 VM 存取」延伸模組來實作 SSH 金鑰，或使用密碼型驗證。

### <a name="networking"></a>網路功能  

<!-- 3239127 - IS, ASDK -->
- 在 Azure Stack 入口網站中，針對已連結至 VM 執行個體的網路介面卡，當您變更與其繫結之 IP 設定的靜態 IP 位址時，將會看到內容如下的警告訊息： 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...` 。

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

您可以從[這裡](https://aka.ms/azurestackupdatedownload)下載 Azure Stack 1901 更新套件。 

Azure Stack 部署會定期檢查受保護的端點，並在有您雲端適用的更新時自動通知您，但僅限於已連線的情況下。 如需詳細資訊，請參閱[管理 Azure Stack 的更新](azure-stack-updates.md#using-the-update-tile-to-manage-updates)。

## <a name="next-steps"></a>後續步驟

- 如需 Azure Stack 中的更新管理概觀，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md)。  
- 如需如何使用 Azure Stack 套用更新的相關資訊，請參閱[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)。
- 若要檢閱 Azure Stack 整合式系統的服務原則，以及必須如何做才能將狀態保持在支援的狀態，請參閱 [Azure Stack 服務原則](azure-stack-servicing-policy.md)。  
- 若要使用具有特殊權限的端點 (PEP) 來監視和繼續更新，請參閱[使用具有特殊權限的端點來監視 Azure Stack 中的更新](azure-stack-monitor-update.md)。  
