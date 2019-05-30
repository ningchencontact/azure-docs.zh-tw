---
title: 比較使用 Azure 資訊安全中心中的檔案完整性監視的基準 |Microsoft Docs
description: 了解如何比較基準及 Azure 資訊安全中心中的檔案完整性監視。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e403a9bd4d3f8668544dab1d81e9052b37839bef
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358434"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>比較使用檔案完整性監視 (FIM) 的基準

檔案完整性監視 (FIM) 會通知您發生變更時機密區域中您的資源，因此您可以調查並解決未經授權的活動。 FIM 會監視 Windows 檔案、 Windows 登錄，以及 Linux 檔案。

本主題說明如何啟用 FIM 上的檔案和登錄。 如需 FIM 的詳細資訊，請參閱[在 Azure 資訊安全中心中的檔案完整性監視](security-center-file-integrity-monitoring.md)。

## <a name="why-use-fim"></a>為何要使用 FIM？

作業系統、 應用程式和相關聯的設定來控制您資源的行為和安全性狀態。 因此，攻擊者會以控制您的資源，以超越資源的作業系統和/或執行活動，而不會被偵測到的檔案。

事實上，許多的法規的合規性標準，例如 PCI DSS 及 ISO 17799 需要實作 FIM 控制項。  

## <a name="enable-built-in-recursive-registry-checks"></a>啟用內建的遞迴登錄檢查

FIM 登錄 hive 的預設值會提供便利的方式來監視常見的安全性區域內的遞迴變更。  比方說，攻擊者可能會設定 LOCAL_SYSTEM 內容中執行，藉由設定在啟動或關機的程式執行的指令碼。  若要監視此類型的變更，請啟用內建的檢查。  

![登錄](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> 遞迴檢查僅適用於建議的安全區而不是以自訂的登錄路徑。  

## <a name="adding-a-custom-registry-check"></a>新增自訂的登錄檢查

FIM 基準開始所識別的作業系統的已知的良好狀態的特性和支援的應用程式。  此範例中，我們將著重在 Windows Server 2008 和更新版本的密碼原則設定。


|原則名稱                 | 登錄設定|
|---------------------------------------|-------------|
|網域控制站：拒絕電腦帳戶密碼變更| MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RefusePasswordChange|
|網域成員：數位加密或簽章 （自動） 安全通道資料|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireSignOrSeal|
|網域成員：（如果可能） 的安全通道資料加以數位加密|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|網域成員：安全通道資料 （如果可能） 數位簽名|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|網域成員：停用電腦帳戶密碼變更|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|網域成員：最大的電腦帳戶密碼有效期|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\MaximumPasswordAge|
|網域成員：需要強式 (Windows 2000 或更新版本) 工作階段金鑰|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireStrongKey|
|網路安全性：限制 NTLM:在這個網域中的 NTLM 驗證|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RestrictNTLMInDomain|
|網路安全性：限制 NTLM:在這個網域中新增伺服器例外狀況|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|網路安全性：限制 NTLM:稽核此網域中的 NTLM 驗證|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> 若要深入了解各種作業系統版本所支援的登錄設定，請參閱[群組原則設定參考的試算表](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250)。

*若要設定 FIM 來監視登錄基準：*

1. 在 **為變更追蹤新增 Windows 登錄**視窗，請在**Windows 登錄機碼**文字中，輸入登錄機碼。

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![啟用 FIM 上的登錄](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Windows 檔案追蹤變更

1. 在 [**為變更追蹤新增 Windows 檔案**] 視窗，請在**Enter 路徑**文字方塊中，輸入您想要追蹤的檔案所在的資料夾。在範例中，在下圖中， **Contoso Web 應用程式**位於 D:\在磁碟機**ContosWebApp**資料夾結構。  
1. 建立自訂的 Windows 檔案項目所提供的設定類別的名稱、 啟用遞迴功能，以及指定以萬用字元 （*） 後置字元的最上層資料夾。

    ![啟用 FIM 上的檔案](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>正在擷取變更資料

檔案完整性監視 Azure Log Analytics 中的資料位於 / ConfigurationChange 資料表集。  

 1. 設定的時間範圍擷取變更的摘要的資源。
在下列範例中，我們會擷取類別中的登錄和檔案在過去 14 天內的所有變更：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. 若要檢視登錄變更的詳細資料：

    1. 移除**檔案**從**其中**子句， 
    1. 移除在摘要列，並取代的排序子句：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

可以匯出至 CSV，封存和 （或) 進行傳送至 Power BI 報表的報表。  

![FIM 資料](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)