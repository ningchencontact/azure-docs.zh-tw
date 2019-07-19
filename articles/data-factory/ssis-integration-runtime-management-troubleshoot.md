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
ms.openlocfilehash: 17fe8d9ed02156b8fe9aafd7adca946531895883
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253023"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>針對 Azure Data Factory 中的 SSIS Integration Runtime 管理進行疑難排解

本檔提供 SSIS Integration Runtime (SSIS IR) 管理問題的疑難排解指南。

## <a name="overview"></a>總覽

如果布建或取消布建 SSIS IR 有任何問題, ADF 入口網站中將會出現錯誤訊息, 或從 PowerShell Cmdlet 傳回。 錯誤的格式一律為錯誤碼, 並顯示詳細的錯誤訊息。

這表示如果錯誤碼是 InternalServerError, 則服務會發生暫時性問題。 您可以稍後再重試此操作。 如果重試無法解決問題, 請聯絡 Azure Data Factory 支援小組。

有三個主要的外部相依性可能會造成錯誤:Azure SQL Database server 或受控執行個體、自訂安裝腳本, 以及未 InternalServerError 錯誤碼的虛擬網路設定。

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL Database server 或受控實例問題

如果使用 SSIS 目錄資料庫布建 SSIS IR, 則需要 Azure SQL Database 伺服器或受控執行個體。 Azure SQL Database 的伺服器或受控執行個體應該可供 SSIS IR 存取。 Azure SQL Database 伺服器或受控執行個體的帳戶應該具有建立 SSIS 目錄資料庫 (SSISDB) 的許可權。 如果發生任何錯誤, ADF 入口網站中將會顯示含有詳細 SQL 例外狀況訊息的錯誤碼。 請遵循下列步驟來針對錯誤碼進行疑難排解。

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

當您布建新的 SSIS IR 或在執行 IR 時, 您可能會看到此問題。

這可能是由於下列原因所造成: 如果您在 IR 布建期間看到錯誤, 而且您可以在錯誤訊息中取得詳細的 SqlException 訊息。

* 網路連接問題。 檢查 SQL Server 或受控執行個體主機名稱可供存取, 而且沒有防火牆或 NSG 封鎖 SSIS IR 來存取伺服器。
* 登入失敗, 並使用 SQL 驗證。 這表示提供的帳戶無法登入 SQL Server。 請確定已提供正確的使用者帳戶。
* 登入失敗, 並使用 AAD 驗證 (受控識別)。 將 factory 的受控識別新增至 AAD 群組, 並讓受控識別具有目錄資料庫伺服器的存取權限。
* 連接逾時, 一律是因為安全性相關的設定。 建議您建立新的 VM, 讓 VM 加入相同的 IR VNet (如果 IR 位於 VNet 中), 然後安裝 SSMS, 並檢查 Azure SQL Database 的伺服器或受控執行個體狀態。

如有其他問題, 請參閱詳細 SQL 例外狀況錯誤訊息, 並修正錯誤訊息中顯示的問題。 如果您仍然遇到問題, 請洽詢 Azure SQL Database server 或受控執行個體支援小組。

如果您在執行 IR 時看到錯誤, 這可能會有一些網路安全性群組或防火牆變更, 這會導致 SSIS IR 背景工作角色節點無法再存取 Azure SQL Database 伺服器或受控執行個體。 解除封鎖 SSIS IR 背景工作節點, 以存取 Azure SQL Database 伺服器或受控執行個體。

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

錯誤訊息就像「資料庫的 SSISDB」已達到其大小配額。 分割或刪除資料、卸載索引, 或參閱檔以取得可能的解決方法。」 可能的解決方案包括:
* 增加 SSISDB 的大小配額。
* 變更 SSISDB 的設定以減少大小, 如下所示:
   * 減少保留週期和專案版本數目。
   * 減少記錄的保留期間。
   * 變更記錄檔的預設層級等等。

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

此錯誤表示 Azure SQL Database 伺服器或受控執行個體已經有其他 IR 所建立和使用的 SSISDB。 您需要提供不同的 Azure SQL Database 伺服器或受控執行個體, 或刪除現有的 SSISDB 並重新啟動新的 IR。

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

此錯誤可能是由下列原因所造成:

* 針對 SSIS IR 設定的使用者帳戶沒有建立資料庫的許可權。 您可以授與使用者擁有建立資料庫的許可權。
* 建立資料庫超時, 例如執行超時、資料庫作業超時等等。 您可以稍後再試一次, 查看問題是否已解決。 如果重試無法使用, 請洽詢 Azure SQL Database server 或受控執行個體支援小組。

如有其他問題, 請查看 SQL 例外狀況錯誤訊息, 並修正錯誤訊息中所述的問題。 如果您仍然遇到問題, 請洽詢 Azure SQL Database server 或受控執行個體支援小組。

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

錯誤訊息就像是「不正確物件名稱」 catalog_properties '。」, 這表示您已經有一個名為 SSISDB 的資料庫, 但它不是由 SSIS IR 所建立, 或者資料庫處於上次 SSIS IR 布建錯誤所造成的無效狀態。 您可以使用名稱 SSISDB 來卸載現有的資料庫, 或為 IR 設定新的 Azure SQL Database 伺服器或受控執行個體。

## <a name="custom-setup"></a>自訂設定

自訂安裝程式會在布建或重新設定 SSIS IR 時, 提供介面來新增您自己的設定步驟。 如需詳細資訊, 請參閱[自訂 AZURE SSIS 整合運行](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)時間的設定。

請確定您的容器只包含必要的自訂安裝檔案, 因為容器中的所有檔案都會下載到 SSIS IR 背景工作角色節點。 建議您在本機電腦上測試自訂安裝腳本, 以修正任何腳本執行問題, 然後再于 SSIS IR 中執行腳本。

在 IR 執行期間, 將會檢查自訂安裝腳本容器, 因為 SSIS IR 定期更新, 需要再次存取容器以下載自訂安裝腳本並再次安裝。 檢查會包含容器是否可供存取, 以及主要 .cmd 檔案是否存在。

自訂安裝程式發生錯誤時, 您會看到程式碼 CustomSetupScriptFailure 的錯誤, 請檢查有子錯誤碼的錯誤訊息。  請遵循下列步驟來針對子錯誤碼進行疑難排解。  

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

這表示 SSIS IR 無法存取您的 Azure blob 容器以進行自訂設定。 檢查容器的 SAS URI 是否可連線且未過期。

如果 IR 處於執行中狀態, 請先停止 IR, 使用新的自訂安裝容器 SAS URI 重新設定 IR, 然後再次啟動 IR。

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

這表示 SSIS IR 在您的 blob 容器中找不到自訂安裝腳本 (main .cmd)。 請確定 main .cmd 存在於容器中, 這是自訂安裝程式的進入點。

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

這表示執行自訂安裝腳本 (main .cmd) 失敗, 您可以先在本機電腦上嘗試腳本, 或檢查 blob 容器中的自訂安裝程式執行記錄。

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

這表示執行自訂安裝腳本超時。 請確定您的 blob 容器只包含必要的自訂安裝檔。 您也可以檢查 blob 容器中的自訂安裝程式執行記錄。 自訂安裝程式的最大期限設定為45分鐘之前的時間, 而最長的期間則包含從容器下載所有檔案, 並將其安裝在 SSIS IR 上的時間上限。 如果需要較長的時間, 請提出支援票證。

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

這表示將自訂安裝執行記錄上傳至您的 blob 容器失敗, 這可能是因為 SSIS IR 沒有 blob 容器的寫入權限, 或是某些儲存體或網路問題。 如果自訂安裝成功, 此錯誤不會影響任何 SSIS 函式, 但遺失記錄。 如果自訂安裝程式因其他錯誤而失敗, 而且我們無法上傳記錄檔, 我們會先回報此錯誤, 以便將記錄檔上傳以進行分析, 而且在解決此問題之後, 我們會回報更多指定的問題。 如果在重試後未解決此問題, 請聯絡 Azure Data Factory 支援小組。

## <a name="virtual-network-configuration"></a>虛擬網路設定

將 SSIS IR 聯結至虛擬網路 (VNet) 時, 它會使用 [使用者訂用帳戶] 底下的 VNet。 如需詳細資訊, 請參閱將[AZURE SSIS 整合執行時間加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

當有 VNet 相關的問題時, 您會看到如下的錯誤訊息

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

這可能是因 variant 原因所造成。 請遵循下列步驟來針對子錯誤碼進行疑難排解。

### <a name="forbidden"></a>禁止

錯誤訊息類似「目前的帳戶未啟用 subnetId。 Microsoft。 Batch 資源提供者未在 VNet 的相同訂用帳戶下註冊。」

這表示 Azure Batch 無法存取您的 VNet。 在相同的 VNet 訂用帳戶下, 註冊 Microsoft Batch 資源提供者。

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

錯誤訊息類似「指定的 VNet 不存在, 或 Batch 服務無法存取它」或「指定的子網 xxx 不存在」。

這表示 VNet 不存在, 或 Azure Batch 服務無法存取它, 或提供的子網不存在。 請確定 VNet 和子網存在, 而且 Azure Batch 可以存取它們。

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

訊息就像是「無法在 Vnet 中布建 Integration Runtime。 如果已設定 DNS 伺服器或 NSG 設定, 請確定 DNS 伺服器可供存取, 且 NSG 已正確設定」

您可能有一些 DNS 伺服器或 NSG 設定的自訂設定, 這會導致 SSIS IR 所需的 Azure 伺服器名稱無法解析或無法存取。 如需詳細資訊, 請參閱[SSIS IR VNet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)設定檔。 如果您仍然遇到問題, 請洽詢 Azure Data Factory 支援小組。

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR 會定期自動更新, 並在升級期間建立新的 Azure Batch 集區, 而舊的 Azure Batch 集區將會刪除, 舊集區的 VNet 相關資源將會遭到刪除, 而新的 VNet 相關資源將會建立在您的預定. 此錯誤表示因為訂用帳戶或資源群組層級的刪除鎖定, 所以刪除舊集區的 VNet 相關資源失敗。 協助移除刪除鎖定。

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

SSIS IR 布建可能因為某種原因而失敗, 而如果發生失敗, 將會刪除所有建立的資源。 不過, 因為在訂用帳戶或資源群組層級上有資源刪除鎖定, 所以無法刪除 VNet 資源。 移除刪除鎖定, 然後重新開機 IR。

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

停止 SSIS IR 時, 會刪除與 VNet 相關的所有資源, 但因為在訂用帳戶或資源群組層級有資源刪除鎖定, 所以刪除失敗。 協助移除刪除鎖定, 然後再次嘗試停止。

### <a name="nodeunavailable"></a>NodeUnavailable

此錯誤發生在 IR 執行期間, 這表示 IR 在健康狀態之前都是健全狀況, 而且一律是因為 DNS 伺服器或 NSG 設定已變更, 而導致 SSIS IR 無法連線到依存服務, 協助修正 DNS 伺服器或 NSG 設定問題如需詳細資訊, 請參閱[SSIS IR VNet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)設定。 如果您仍然遇到問題, 請洽詢 Azure Data Factory 支援小組。
