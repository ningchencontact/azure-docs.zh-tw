---
title: Azure Stack 1903 更新 | Microsoft Docs
description: 了解 Azure Stack 整合式系統 1903 更新，包括新功能、已知問題及下載更新的位置。
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
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: f07f81562c604913e633a8d93fa9c7db28a7bf55
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471472"
---
# <a name="azure-stack-1903-update"></a>Azure Stack 1903 更新

*適用於：Azure Stack 整合系統*

此文章說明 1903 更新套件的內容。 此更新包括此版 Azure Stack 的改良功能、修正及新功能。 此文章也說明此版本的已知問題，並包含下載更新的連結。 已知問題分為與更新程序直接相關的問題，以及與組建 (安裝後) 相關的問題。

> [!IMPORTANT]  
> 這個更新程式封裝僅適用於 Azure Stack 整合系統。 請勿將此更新套件套用至 Azure Stack 開發套件。

## <a name="build-reference"></a>建置參考

Azure Stack 1903 更新組建編號為 **1.1903.0.35**。

> [!IMPORTANT]
> 1903 承載不包含 ASDK 版本。

## <a name="hotfixes"></a>Hotfix

Azure Stack 會定期發行 Hotfix。 將 Azure Stack 更新成 1903 之前，請務必先安裝 1902 的[最新 Azure Stack Hotfix](#azure-stack-hotfixes)。

Azure Stack Hotfix 僅適用於 Azure Stack 整合系統，請勿嘗試在 ASDK 上安裝 Hotfix。

> [!TIP]  
> 請訂閱下列 *RSS* 或 *Atom* 摘要，以掌握最新的 Azure Stack Hotfix：
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack Hotfix

- **1902**：[KB 4481548 – Azure Stack Hotfix 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="improvements"></a>改進

- 1903 更新承載包含的 Azure Stack 元件更新，並不包含裝載 Azure Stack 的底層作業系統。 這可將特定更新列入範圍。 因此，完成 1903 更新所需的預期時間較短 (約為 16 個小時，但確切的時間可能有所不同)。 執行時間縮短的情況僅限於 1903 更新，而後續更新可能包含作業系統更新，意味著所需的執行時間會不同。 未來的更新將根據包含的承載，對完成更新所需的預期時間，提供類似的指導方針。

- 已修正網路中導致 [公用 IP 位址] 的 [閒置逾時 (分鐘)] 值變更無法生效的錯誤 (Bug)。 先前會忽略對此值的變更，無論您做出任何變更，該值都會預設為 4 分鐘。 此設定可控制不需依賴用戶端傳送保持連線訊息，讓 TCP 連線保持開啟的分鐘數。 請注意，這個錯誤 (Bug) 只會影響執行個體層級的公用 IP，而不會影響指派給負載平衡器的公用 IP。

- 改善更新引擎可靠性，包括自動修復常見的問題，才能套用更新，而不會中斷。

- 改善偵測和補救磁碟空間不足的情況。

### <a name="secret-management"></a>祕密管理

- Azure Stack 現在支援輪替根憑證 (由外部祕密輪替的憑證所使用)。 如需詳細資訊，[請參閱此文章](azure-stack-rotate-secrets.md)。

- 1903 包含祕密輪替的效能改進，減少了執行內部祕密輪替所花費的時間。

## <a name="prerequisites"></a>先決條件

> [!IMPORTANT]
> 在更新為 1903 之前，請先安裝 1902 的[最新 Azure Stack Hotfix](#azure-stack-hotfixes) (如果有的話)。

- 請務必使用最新版的 [Azure Stack 容量規劃工具](https://aka.ms/azstackcapacityplanner)來規劃您的工作負載並調整其大小。 最新版本包含錯誤 (Bug) 修正，並提供隨每個 Azure Stack 更新發行的新功能。

- 開始安裝此更新之前，請先搭配下列參數執行 [Test-AzureStack](azure-stack-diagnostic-test.md) 來驗證 Azure Stack 的狀態，並解決所發現的一切運作問題，包括所有警告與失敗。 也請檢閱作用中警示，並將所有需要採取動作的警示解決：

    ```PowerShell
   Test-AzureStack -Group UpdateReadiness  
    ```

- 當 Azure Stack 由 System Center Operations Manager 管理時，請務必先將[適用於 Microsoft Azure Stack 的管理組件](https://www.microsoft.com/download/details.aspx?id=55184)更新為 1.0.3.11 版，再套用 1903。

- 從 1902 版本開始，Azure Stack 更新的套件格式已從 **.bin/.exe/.xml** 變更為 **.zip/.xml**。 具有已連線 Azure Stack 縮放單位的客戶會在入口網站中看到**有可用更新**的訊息。 未連線的客戶現在可以直接下載並匯入具有對應 .xml 的 .zip 檔案。

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>關於更新程序的已知問題

- 當您執行 [Test-AzureStack](azure-stack-diagnostic-test.md) 時，會顯示來自「基礎板管理控制器」(BMC) 的警告訊息。 您可以放心地忽略此警告。

<!-- 2468613 - IS -->
- 在安裝更新期間，您可能會看到警示，標題為「錯誤 - FaultType UserAccounts.New 的範本已遺失。」 您可以放心地忽略這些警示。 在此更新安裝完成之後，這些警示會自動關閉。

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
- 您無法使用 Azure Stack 入口網站來檢視對您訂用帳戶的權限。 因應措施是，使用 [PowerShell 來確認權限](/powershell/module/azurerm.resources/get-azurermroleassignment)。

<!-- Daniel 3/28 -->
- 在使用者入口網站中，當您瀏覽至儲存體帳戶內的 Blob，並嘗試開啟瀏覽樹狀目錄中的 [存取原則] 時，後續的視窗會無法載入。 若要解決此問題，下列 PowerShell Cmdlet 分別允許建立、擷取、設定及刪除存取原則：

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

<!-- Daniel 3/28 -->
- 在使用者入口網站中，當您嘗試使用 [OAuth (預覽)] 選項上傳 Blob 時，工作會失敗並出現錯誤訊息。 若要解決此問題，請使用 [SAS] 選項上傳 Blob。

- 登入 Azure Stack 入口網站時，您可能會看到有關公用 Azure 入口網站的通知。 這些通知目前不適用於 Azure Stack，因此您可以放心地忽略 (例如，「1 個新的更新 - 現已提供下列更新：Azure 入口網站 2019 4 月更新」)。

<!-- ### Health and monitoring -->

### <a name="compute"></a>計算

- 在建立新的 Windows 虛擬機器 (VM) 時，可能會顯示下列錯誤：

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   如果您啟用 VM 上的開機診斷，但刪除您的開機診斷儲存體帳戶，就會發生此錯誤。 若要解決此問題，請重新建立儲存體帳戶，該帳戶的名稱與您先前使用的名稱相同。

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- 虛擬機器擴展集建立體驗提供 CentOS 架構 7.2 作為部署選項。 因為該映像在 Azure Stack 上無法使用，所以請為您的部署選取其他作業系統，或使用指定另一個 CentOS 映像 (已由操作員在部署前從市集下載) 的 Azure Resource Manager 範本。  

<!-- TBD - IS ASDK --> 
- 套用 1903 更新之後，在部署具有「受控磁碟」的 VM 時，您可能會遇到下列問題：

   - 如果訂用帳戶是在 1808 更新之前建立的，部署具有受控磁碟的 VM 可能會失敗，且會有內部錯誤訊息。 若要解決此錯誤，請針對每個訂用帳戶遵循下列步驟：
      1. 在租用戶入口網站中，移至 [訂用帳戶] 並尋找訂用帳戶。 選取 [資源提供者]，然後選取 [Microsoft.Compute]，接著按一下 [重新註冊]。
      2. 在相同的訂用帳戶底下，移至 [存取控制 (IAM)]，並確認 [Azure Stack - 受控磁碟] 已列出。
   - 如果您已設定多租用戶環境，則在與來賓目錄相關聯的訂用帳戶中部署 VM 可能會失敗，且會有內部錯誤訊息。 若要解決此錯誤，請依照[這篇文章](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的步驟重新設定您的每個來賓目錄。

- 所建立的 Ubuntu 18.04 VM 如果已啟用 SSH 授權，將不會允許您使用 SSH 金鑰來登入。 因應措施是，在佈建後使用「適用於 Linux 的 VM 存取」延伸模組來實作 SSH 金鑰，或使用密碼型驗證。

- Azure Stack 現在支援大於 2.2.20 版的 Windows Azure Linux 代理程式。 此支援是 1901 和 1902 Hotfix 的一部分，允許客戶在 Azure 和 Azure Stack 之間維護一致的 Linux 映像。

- 如果您沒有硬體生命週期主機 (HLH)：在組建 1902 之前，您必須將群組原則**電腦設定\Windows 設定\安全性設定\本機原則\安全性選項**設定為 [傳送 LM 和 NTLM - 如有交涉，使用 NTLMv2 工作階段安全性]。 自組建 1902 開始，則必須讓此原則保持 [未定義] 或將其設定為 [只傳送 NTLMv2 回應] (此為預設值)。 否則，您將無法建立 PowerShell 遠端工作階段，且會看到「存取遭拒」錯誤：

   ```powershell
   PS C:\Users\Administrator> $session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint  -Credential $cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the 
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $Session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

### <a name="networking"></a>網路功能  

<!-- 3239127 - IS, ASDK -->
- 在 Azure Stack 入口網站中，針對已連結至 VM 執行個體的網路介面卡，當您變更與其繫結之 IP 設定的靜態 IP 位址時，將會看到內容如下的警告訊息： 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

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

### <a name="syslog"></a>syslog

- syslog 設定不會在更新循環中保存，造成 syslog 用戶端會遺失其設定，並停止正在轉送的 syslog 訊息。 此問題適用於自用戶端 (1809) 公開推出後的所有 Azure Stack 版本。 若要解決此問題，請在套用 Azure Stack 更新後重新設定 syslog 用戶端。

## <a name="download-the-update"></a>下載更新

您可以從[這裡](https://aka.ms/azurestackupdatedownload)下載 Azure Stack 1903 更新套件。 

Azure Stack 部署會定期檢查受保護的端點，並在有您雲端適用的更新時自動通知您，但僅限於已連線的情況下。 如需詳細資訊，請參閱[管理 Azure Stack 的更新](azure-stack-updates.md#using-the-update-tile-to-manage-updates)。

## <a name="next-steps"></a>後續步驟

- 如需 Azure Stack 中的更新管理概觀，請參閱[在 Azure Stack 中管理更新概觀](azure-stack-updates.md)。  
- 如需如何使用 Azure Stack 套用更新的相關資訊，請參閱[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)。
- 若要檢閱 Azure Stack 整合式系統的服務原則，以及必須如何做才能將狀態保持在支援的狀態，請參閱 [Azure Stack 服務原則](azure-stack-servicing-policy.md)。  
- 若要使用具有特殊權限的端點 (PEP) 來監視和繼續更新，請參閱[使用具有特殊權限的端點來監視 Azure Stack 中的更新](azure-stack-monitor-update.md)。  
