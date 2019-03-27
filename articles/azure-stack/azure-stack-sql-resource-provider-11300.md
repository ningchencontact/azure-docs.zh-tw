---
title: Azure Stack SQL 資源提供者 1.1.30.0 版本資訊 | Microsoft Docs
description: 了解最新的 Azure Stack SQL 資源提供者更新 (包括任何已知問題)，以及更新的下載位置。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 1/09/2019
ms.openlocfilehash: 176cf22957725927f9705985bf5471a1c5e4caad
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436318"
---
# <a name="sql-resource-provider-11300-release-notes"></a>SQL 資源提供者 1.1.30.0 版本資訊

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

這些版本資訊說明 SQL 資源提供者 1.1.30.0 版的改進和已知問題。

## <a name="build-reference"></a>建置參考
下載 SQL 資源提供者二進位檔，然後執行自我解壓縮程式，以將內容解壓縮到至暫存目錄。 資源提供者會有最低限度的相對應 Azure Stack 組建。 要安裝此版本的 SQL 資源提供者所需的最低 Azure Stack 發行版本如下所列：

> |最低 Azure Stack 版本|SQL 資源提供者版本|
> |-----|-----|
> |1808 版 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> 在部署最新版的 SQL 資源提供者之前，請先將支援的最低 Azure Stack 更新套用到您的 Azure Stack 整合式系統，或部署最新的 Azure Stack 開發套件 (ASDK)。

## <a name="new-features-and-fixes"></a>新功能和修正
此版本的 Azure Stack SQL 資源提供者包含下列改良功能與修正：

- **為 SQL 資源提供者部署啟用遙測**。 已為 SQL 資源提供者部署啟用遙測集合。 收集的遙測資料包括資源提供者部署、開始和停止時間、結束狀態、結束訊息和錯誤詳細資料 (如果適用)。

- **TLS 1.2 加密更新**。 已為資源提供者與內部 Azure Stack 元件的通訊啟用僅限 TLS 1.2 的支援。 

### <a name="fixes"></a>修正

- **SQL 資源提供者 Azure Stack PowerShell 的相容性**。 SQL 資源提供者已更新，可與 Azure Stack 2018-03-01-hybrid PowerShell 設定檔搭配運作，並提供 AzureRM 1.3.0 和更新版本的相容性。

- **SQL 登入變更密碼刀鋒視窗**。 已修正無法在 [變更密碼] 刀鋒視窗上變更密碼的問題。 已從密碼變更通知中移除連結。

- **SQL 主控伺服器的設定刀鋒視窗更新**。 已修正 [設定] 刀鋒視窗誤標示為「密碼」的問題。

## <a name="known-issues"></a>已知問題 

- **最多需要一小時才能在入口網站中看到 SQL SKU**。 在建立新的 SQL 資料庫時，新建立的 SKU 最多需要一小時才會顯示以供使用。 

    **因應措施**：無。

- **重複使用的 SQL 登入**。 嘗試使用與現有登入相同的使用者名稱在相同的訂用帳戶下建立新的 SQL 登入時，會導致重複使用相同登入和現有密碼的問題。 

    **因應措施**：在相同的訂用帳戶下，建立新登入時請使用不同的使用者名稱，或在不同訂用帳戶下，使用相同的使用者名稱建立登入。

- **共用的 SQL 登入導致資料不一致**。 如果在相同的訂用帳戶下讓多個 SQL 資料庫共用一個 SQL 登入，變更登入密碼將會導致資料不一致。

    **因應措施**：在相同的訂用帳戶下，一律讓不同的資料庫使用不同的登入。

- **TLS 1.2 支援需求**。 如果您自未啟用 TLS 1.2 的電腦，嘗試部署或更新 SQL 資源提供者時，作業可能會失敗。 用來部署或更新資源提供者，以確認 TLS 1.2，會傳回所支援電腦上執行的下列 PowerShell 命令：

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  如果 **Tls12** 未包含在命令的輸出中，則 TLS 1.2 無法在電腦上啟用。

    **因應措施**：執行下列 PowerShell 命令以啟用 TLS 1.2，然後啟動 資源提供者部署或從相同 PowerShell 工作階段的指令碼更新：

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
- **SQL 資源提供者無法新增 SQL Server Always On 接聽程式**。 在使用 SQL Server Always On 接聽程式的接聽程式 IP 位址時，SQL 資源提供者 VM 無法解析接聽程式的主機名稱。

    **因應措施**：請確定 DNS 正常運作，以便將接聽程式 IP 解析為接聽程式主機名稱。
    
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>雲端管理員操作 Azure Stack 時的已知問題
請參閱 [Azure Stack 版本資訊](azure-stack-servicing-policy.md)中的文件。

## <a name="next-steps"></a>後續步驟
[深入了解 SQL 資源提供者](azure-stack-sql-resource-provider.md)。

[準備部署 SQL 資源提供者](azure-stack-sql-resource-provider-deploy.md#prerequisites)。

[從舊版升級 SQL 資源提供者](azure-stack-sql-resource-provider-update.md)。 