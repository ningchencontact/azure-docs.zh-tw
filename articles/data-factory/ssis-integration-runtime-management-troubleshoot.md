---
title: 針對 Azure Data Factory 中的 SSIS Integration Runtime 管理進行疑難排解 |Microsoft Docs
description: 本文提供 SSIS Integration Runtime 管理問題的疑難排解指引 (SSIS IR)
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 8abffdf443e26c03c38c12a3947a47a94157c9da
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609615"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>針對 Azure Data Factory 中的 SSIS Integration Runtime 管理進行疑難排解

本文提供 Azure SQL Server Integration Services (SSIS) Integration Runtime (IR) (也稱為 SSIS IR) 中管理問題的疑難排解指引。

## <a name="overview"></a>總覽

如果您在布建或取消布建 SSIS IR 時遇到任何問題, 您會在 Microsoft Azure Data Factory 入口網站中看到錯誤訊息, 或從 PowerShell Cmdlet 傳回錯誤。 錯誤一律會以錯誤碼的格式出現, 並顯示詳細的錯誤訊息。

如果錯誤碼為 InternalServerError, 則服務會發生暫時性問題, 您應該稍後再重試此操作。 如果重試沒有説明, 請洽詢 Azure Data Factory 支援小組。

否則, 三個主要的外部相依性可能會導致錯誤: Azure SQL Database 伺服器或受控實例、自訂安裝腳本, 以及虛擬網路設定。

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL Database server 或受控實例問題

如果您要使用 SSIS 目錄資料庫布建 SSIS IR, 則需要 Azure SQL Database 伺服器或受控實例。 SSIS IR 必須能夠存取 Azure SQL Database 伺服器或受控實例。 此外, Azure SQL Database 伺服器或受控實例的帳戶, 應該具有建立 SSIS 目錄資料庫 (SSISDB) 的許可權。 如果發生錯誤, Data Factory 入口網站中將會顯示含有詳細 SQL 例外狀況訊息的錯誤碼。 使用下列清單中的資訊來針對錯誤碼進行疑難排解。

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

當您在布建新的 SSIS IR 或 IR 正在執行時, 您可能會看到此問題。 如果您在 IR 布建期間遇到此錯誤, 您可能會在錯誤訊息中取得詳細的 SqlException 訊息, 指出下列其中一個問題:

* 網路連接問題。 檢查 SQL Server 或受控實例主機名稱是否可存取。 也請確認沒有防火牆或網路安全性群組 (NSG) 封鎖對伺服器的 SSIS IR 存取。
* SQL 驗證期間登入失敗。 提供的帳戶無法登入 SQL Server 資料庫。 請確定您提供正確的使用者帳戶。
* Microsoft Azure Active Directory (Azure AD) 驗證期間登入失敗 (受控識別)。 將 factory 的受控識別新增至 AAD 群組, 並確定受控識別具有您目錄資料庫伺服器的存取權限。
* 連接逾時。 此錯誤一律是由安全性相關的設定所造成。 建議您：
  1. 建立新的 VM。
  1. 如果 IR 位於虛擬網路中, 請將 VM 加入相同的 IR Microsoft Azure 虛擬網路。
  1. 安裝 SSMS, 並檢查 Azure SQL Database 伺服器或受控實例狀態。

如有其他問題, 請修正詳細 SQL 例外狀況錯誤訊息中顯示的問題。 如果您仍然遇到問題, 請洽詢 Azure SQL Database server 或受控實例支援小組。

如果您在 IR 執行時看到錯誤, 則網路安全性群組或防火牆變更可能會導致 SSIS IR 背景工作角色節點無法存取 Azure SQL Database 伺服器或受控實例。 解除封鎖 SSIS IR 背景工作節點, 使其可以存取 Azure SQL Database 伺服器或受控實例。

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

這種錯誤訊息看起來可能像這樣:「資料庫的 SSISDB」已達到其大小配額。 分割或刪除資料、卸載索引, 或參閱檔以取得可能的解決方法。」 

可能的解決方案包括:
* 增加 SSISDB 的配額大小。
* 變更 SSISDB 的設定, 藉此減少大小:
   * 減少保留週期和專案版本數目。
   * 減少記錄的保留期間。
   * 變更記錄檔的預設層級。

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

此錯誤表示 Azure SQL Database 伺服器或受控實例已經有 SSISDB, 而且另一個 IR 正在使用它。 您必須提供不同的 Azure SQL Database 伺服器或受控實例, 否則請刪除現有的 SSISDB, 然後重新開機新的 IR。

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

這個錯誤可能是下列其中一個原因所造成:

* 針對 SSIS IR 設定的使用者帳戶沒有建立資料庫的許可權。 您可以授與使用者建立資料庫的許可權。
* 在資料庫建立期間發生超時, 例如執行超時或 DB 作業超時。 您應該稍後重試操作。 如果重試無法解決問題, 請洽詢 Azure SQL Database server 或受控執行個體支援小組。

如有其他問題, 請查看 SQL 例外狀況錯誤訊息, 並修正錯誤詳細資料中所述的問題。 如果您仍然遇到問題, 請洽詢 Azure SQL Database server 或受控實例支援小組。

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

這種錯誤訊息看起來像這樣:「不正確物件名稱」目錄。 catalog_properties '。」在此情況下, 您可能已經有名為 SSISDB 的資料庫, 但它不是由 SSIS IR 所建立, 或者資料庫處於不正確狀態, 因為最後一個 SSIS IR 布建發生錯誤。 您可以使用名稱 SSISDB 來卸載現有的資料庫, 也可以為 IR 設定新的 Azure SQL Database 伺服器或受控實例。

## <a name="custom-setup-issues"></a>自訂安裝問題

自訂安裝程式會在布建或重新設定 SSIS IR 時, 提供介面來新增您自己的設定步驟。 如需詳細資訊, 請參閱[自訂 AZURE SSIS Integration Runtime 的安裝程式](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

請確定您的容器只包含必要的自訂安裝檔案;容器中的所有檔案都會下載到 SSIS IR 背景工作角色節點。 我們建議您在本機電腦上測試自訂安裝腳本, 以修正任何腳本執行問題, 然後再于 SSIS IR 中執行腳本。

當 IR 正在執行時, 將會檢查自訂安裝腳本容器, 因為 SSIS IR 會定期更新。 這種更新需要有容器的存取權, 才能下載自訂安裝腳本並再次安裝。 此程式也會檢查容器是否可存取, 以及主要 .cmd 檔案是否存在。

對於牽涉到自訂設定的任何錯誤, 您會看到 CustomSetupScriptFailure 錯誤碼, 其中包含 CustomSetupScriptBlobContainerInaccessible 或 CustomSetupScriptNotFound 之類的副程式代碼。

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

此錯誤表示 SSIS IR 無法存取您的 Azure blob 容器以進行自訂設定。 請確定容器的 SAS URI 可供連線且未過期。

如果 IR 正在執行, 請將其停止, 以新的自訂安裝容器 SAS URI 重新設定 IR, 然後重新開機 IR。

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

此錯誤表示 SSIS IR 在您的 blob 容器中找不到自訂安裝腳本 (main .cmd)。 請確定主要 .cmd 存在於容器中, 這是自訂安裝程式的進入點。

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

此錯誤表示執行自訂安裝腳本 (main .cmd) 失敗。 請先在您的本機電腦上嘗試腳本, 或檢查 blob 容器上的自訂安裝執行記錄。

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

此錯誤表示執行自訂安裝腳本超時。 請確定您的 blob 容器只包含必要的自訂安裝檔。 您也應該檢查 blob 容器中的自訂安裝程式執行記錄。 自訂安裝程式的最大期限為45分鐘後才會超時, 而最長期間包含從容器下載所有檔案, 並將它們安裝在 SSIS IR 上的時間。 如果您需要較長的時間, 請提出支援票證。

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

此錯誤表示嘗試將自訂安裝程式執行記錄上傳至您的 blob 容器失敗。 此問題發生的原因可能是 SSIS IR 沒有 blob 容器的寫入權限, 或因為儲存體或網路問題所致。 如果自訂安裝成功, 此錯誤不會影響任何 SSIS 函數, 但記錄檔將會遺失。 如果自訂安裝失敗併發生另一個錯誤, 而且記錄檔未上傳, 我們會先回報此錯誤, 以便將記錄上傳以供分析。 此外, 解決此問題之後, 我們會報告任何更具體的問題。 如果在重試後未解決此問題, 請洽詢 Azure Data Factory 支援小組。

## <a name="virtual-network-configuration"></a>虛擬網路設定

當您將 SSIS IR 加入 Azure 虛擬網路時, SSIS IR 會使用使用者訂用帳戶下的虛擬網路。 如需詳細資訊, 請參閱將[AZURE SSIS Integration Runtime 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

當發生虛擬網路相關的問題時, 您會看到下列其中一個錯誤。

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

發生此錯誤的原因有很多種。 若要進行疑難排解, 請參閱[禁止](#forbidden)、 [InvalidPropertyValue](#invalidpropertyvalue)和[MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings)章節。

### <a name="forbidden"></a>禁止

這種錯誤可能類似于:「目前的帳戶未啟用 SubnetId。 Microsoft。 Batch 資源提供者未在 VNet 的相同訂用帳戶下註冊。」

這些詳細資料表示 Azure Batch 無法存取您的虛擬網路。 在與虛擬網路相同的訂用帳戶下註冊 Microsoft Batch 資源提供者。

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

這種錯誤可能與下列其中一項類似: 

- 「指定的 VNet 不存在, 或 Batch 服務沒有其存取權。」
- 「指定的子網 xxx 不存在」。

這些錯誤表示虛擬網路不存在、Azure Batch 服務無法存取它, 或提供的子網不存在。 請確定虛擬網路和子網存在, 而且 Azure Batch 可以存取它們。

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

這種錯誤訊息看起來可能像這樣:「無法在 VNet 中布建 Integration Runtime。 如果已設定 DNS 伺服器或 NSG 設定, 請確定 DNS 伺服器可供存取, 且 NSG 已正確設定。」

在此情況下, 您可能會有 DNS 伺服器或 NSG 設定的自訂設定, 以防止 SSIS IR 所需的 Azure 伺服器名稱解析或存取。 如需詳細資訊, 請參閱[SSIS IR 虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)設定。 如果您仍然遇到問題, 請洽詢 Azure Data Factory 支援小組。

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR 會定期自動更新。 升級期間會建立新的 Azure Batch 集區, 並刪除舊的 Azure Batch 集區。 此外, 系統會刪除舊集區的虛擬網路相關資源, 並在您的訂用帳戶下建立新的虛擬網路相關資源。 此錯誤表示刪除舊集區虛擬網路相關資源失敗, 因為訂用帳戶或資源群組層級的刪除鎖定。 因為客戶會控制和設定刪除鎖定, 所以必須在這種情況下移除刪除鎖定。

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

如果 SSIS IR 布建失敗, 則會刪除所有已建立的資源。 不過, 如果訂用帳戶或資源群組層級有資源刪除鎖定, 則不會如預期般刪除虛擬網路資源。 若要修正此錯誤, 請移除刪除鎖定, 然後重新開機 IR。

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

當您停止 SSIS IR 時, 會刪除與虛擬網路相關的所有資源。 但如果訂用帳戶或資源群組層級有資源刪除鎖定, 則刪除作業可能會失敗。 在此, 客戶也會控制和設定刪除鎖定。 因此, 它們必須移除刪除鎖定, 然後再次停止 SSIS IR。

### <a name="nodeunavailable"></a>NodeUnavailable

當 IR 正在執行時, 就會發生此錯誤, 這表示 IR 已變得狀況不良。 此錯誤一律是由 DNS 伺服器或 NSG 設定中的變更所造成, 封鎖 SSIS IR 無法連接到必要的服務。 由於 DNS 伺服器和 NSG 的設定是由客戶所控制, 因此客戶必須在其端修正封鎖問題。 如需詳細資訊, 請參閱[SSIS IR 虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)設定。 如果您仍然遇到問題, 請洽詢 Azure Data Factory 支援小組。
