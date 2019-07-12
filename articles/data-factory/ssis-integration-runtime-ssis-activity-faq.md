---
title: 疑難排解封裝執行中的 SSIS 整合執行階段 |Microsoft Docs
description: 這篇文章提供有關 SSIS 整合執行階段中的 SSIS 封裝執行的疑難排解指引
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 05723a90725992e6b955524a2d35c82d3378ee3d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621846"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>針對 SSIS 整合執行階段中的封裝執行進行疑難排解

本文會包含最常見的錯誤，您可能會發現當 SSIS 整合執行階段中執行 SQL Server Integration Services (SSIS) 套件。 它說明可能的原因和動作來解決錯誤。

## <a name="where-to-find-logs-for-troubleshooting"></a>哪裡可以找到記錄檔進行疑難排解

您可以使用 Azure Data Factory 入口網站來檢查 SSIS 封裝執行活動的輸出。 輸出會包含執行結果、 錯誤訊息和作業識別碼。 如需詳細資訊，請參閱 <<c0> [ 監視管線](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)。

若要查看執行詳細資料記錄檔中使用 SSIS 目錄 (SSISDB)。 如需詳細資訊，請參閱 <<c0> [ 監視封裝執行和其他作業](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)。

## <a name="common-errors-causes-and-solutions"></a>常見的錯誤、 原因和解決方案

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>錯誤訊息：「 連線逾時已過期 」 或者 「 服務發生處理您的要求時發生錯誤。 請再試一次。 」

以下是可能的原因及建議的動作：
* 資料來源或目的地是多載。 檢查您的資料來源或目的地的負載，並查看是否有足夠的容量。 例如，如果您使用 Azure SQL Database，請考慮相應增加，如果資料庫可能會逾時。
* SSIS 整合執行階段的資料來源或目的端之間的網路不穩定，，尤其是當連接為跨區域或內部部署與 Azure 之間。 適用於 SSIS 封裝中的重試模式，依照下列步驟：
  * 請確定您的 SSIS 套件可以重新執行失敗，沒有副作用 （例如，資料遺失或資料重複） 上。
  * 設定**重試**並**重試間隔**的**執行 SSIS 套件**活動上的**一般** 索引標籤。![在 [一般] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * ADO.NET 和 OLE DB 來源或目的地元件，請設定**ConnectRetryCount**並**ConnectRetryInterval**在 連接管理員中的 SSIS 套件或 SSIS 活動。

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>錯誤訊息：「 ADO NET 來源有 '...' 取得的連接失敗 」 使用 「 建立 SQL Server 的連接時發生網路相關或執行個體特有的錯誤。 伺服器找不到或無法存取。 」

此問題通常表示資料來源或目的地與 SSIS 整合執行階段無法存取。 原因可能會不同。 請嘗試下列動作：
* 請確定您所傳遞的資料來源或目的地名稱正確。
* 請確定防火牆已正確設定。
* 請確定您的虛擬網路已正確設定，如果您的資料來源或目的地是在內部部署環境：
  * 您可以確認問題是否來自虛擬網路組態中，藉由佈建 Azure VM 相同的虛擬網路中。 然後檢查資料來源或目的地是否可以從 Azure VM 存取。
  * 您可以找到與 SSIS 整合執行階段中使用虛擬網路的更多詳細[將 Azure SSIS 整合執行階段加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>錯誤訊息：「 ADO NET 來源有 '...' 取得的連接失敗 」 使用 「 無法建立 managed 的連接管理員。 」

可能的原因是封裝中使用的 ADO.NET 提供者未安裝 SSIS 整合執行階段中。 您可以使用自訂安裝程式來安裝提供者。 您可以找到自訂的安裝程式中的更多詳細[來自訂 AZURE-SSIS 整合執行階段的安裝程式](how-to-configure-azure-ssis-ir-custom-setup.md)。

### <a name="error-message-the-connection--is-not-found"></a>錯誤訊息：「 連線 [...]找不到 」

在舊版 SQL Server Management Studio (SSMS) 中的已知的問題會造成這個錯誤。 如果封裝包含在其中使用 SSMS 來進行部署的電腦未安裝自訂元件 （例如，SSIS 的 Azure Feature Pack 或協力廠商元件） 時，SSMS 將會移除元件，並會造成錯誤。 升級[SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)已修正此問題的最新版本。

### <a name="error-messagessis-executor-exit-code--1073741819"></a>錯誤訊息: 「 SSIS 執行程式結束代碼： 並顯示-1073741819。 」

* 可能的原因和建議的動作：
  * 此錯誤可能是因為 Excel 來源和目的地的限制中，當多個 Excel 來源或目的地會以平行方式執行多執行緒中。 您可以藉由這項限制變更您的 Excel 元件，依序執行，或將它們分成不同的套件和觸發程序，透過 「 執行封裝工作 」 ExecuteOutOfProcess 屬性設定為 [True] 的因應措施。

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>錯誤訊息：「 沒有足夠的空間磁碟上 」

此錯誤表示在 SSIS integration runtime 節點的本機磁碟用完。 檢查您的套件或自訂安裝程式是否正耗用大量磁碟空間：
* 如果磁碟由您的套件，它將會釋出了封裝執行完成之後。
* 如果磁碟由您的自訂安裝，將需要停止 SSIS 整合執行階段中，修改您的指令碼，並重新啟動 integration runtime。 整個 Azure blob 容器，您所指定的自訂安裝程式將會複製到 SSIS integration runtime 節點，因此請檢查是否有任何不必要的內容，在該容器。

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>錯誤訊息：「 無法從 master 擷取資源。 Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException:程式碼： 300004。 描述： 載入檔案 」 * * * 「 失敗。 」

* 可能的原因和建議的動作：
  * 如果專案、 封裝或組態檔不能存取您在 SSIS 活動中提供套件的存取認證，如果 SSIS 活動在執行封裝，從檔案系統 （封裝檔案或專案檔），會發生此錯誤
    * 如果您使用 Azure 檔案：
      * 檔案路徑的開頭應\\ \\\<儲存體帳戶名稱\>。 file.core.windows.net\\\<檔案共用路徑\>
      * 網域應與 「 Azure 」
      * 使用者名稱應該是\<儲存體帳戶名稱\>
      * 密碼應為\<儲存體存取金鑰\>
    * 如果您使用內部部署檔案，請檢查如果 VNet、 封裝的存取認證和權限已正確設定，讓您的 Azure SSIS 整合執行階段可以存取您的內部部署檔案共用

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>錯誤訊息：「 檔案名稱 '...'中所指定的連接無效。 」

* 可能的原因和建議的動作：
  * 指定無效的檔案名稱
  * 請確定您使用 FQDN （完整網域名稱） 而不短時間中您的連接管理員

### <a name="error-message-cannot-open-file-"></a>錯誤訊息：「 無法開啟檔案 '...' 」

當封裝執行 SSIS 整合執行階段中的本機磁碟中找不到檔案時，就會發生此錯誤。 請嘗試下列動作：
* 請勿使用 SSIS 整合執行階段中執行的封裝中的絕對路徑。 使用目前的執行工作目錄 （.） 或 [temp] 資料夾 （%TEMP%)改為。
* 如果您需要保存在 SSIS 整合執行階段節點上的某些檔案，準備好檔案中所述[來自訂安裝程式](how-to-configure-azure-ssis-ir-custom-setup.md)。 在完成執行之後，將會清除工作目錄中的所有檔案。
* 使用 Azure 檔案，而不是將檔案儲存在 SSIS integration runtime 節點。 如需詳細資訊，請參閱 <<c0> [ 使用 Azure 檔案共用](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)。

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>錯誤訊息：「 資料庫 'SSISDB' 已達到大小配額 」

可能的原因是當您建立的 SSIS 整合執行階段中，建立 Azure SQL database 或受控執行個體中的 SSISDB 資料庫已達到其配額。 請嘗試下列動作：
* 請考慮提高資料庫的 DTU。 詳細資訊，請在[Azure SQL Database 伺服器的 SQL Database 資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)。
* 請檢查您的套件是否會產生許多記錄。 如果是這樣，您可以設定彈性工作來清除這些記錄檔。 如需詳細資訊，請參閱 <<c0> [ 清除 使用 Azure 彈性資料庫工作的 SSISDB 記錄](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)。

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>錯誤訊息：「 資料庫的要求限制是...且已達到。 」

如果在 SSIS 整合執行階段中平行執行許多套件，因為 SSISDB 達到其要求限制，就可能會發生這個錯誤。 請考慮增加以解決此問題的 SSISDB DTC。 詳細資訊，請在[Azure SQL Database 伺服器的 SQL Database 資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)。

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>錯誤訊息：「 SSIS 作業失敗，發生未預期的作業狀態:...」

錯誤暫時性的問題所造成，因此請嘗試重新執行該封裝的執行。 適用於 SSIS 封裝中的重試模式，依照下列步驟：

* 請確定您的 SSIS 套件可以重新執行失敗，沒有副作用 （例如，資料遺失或資料重複） 上。
* 設定**重試**並**重試間隔**的**執行 SSIS 套件**活動上的**一般** 索引標籤。![在 [一般] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* ADO.NET 和 OLE DB 來源或目的地元件，請設定**ConnectRetryCount**並**ConnectRetryInterval**在 連接管理員中的 SSIS 套件或 SSIS 活動。

### <a name="error-message-there-is-no-active-worker"></a>錯誤訊息：「 沒有任何作用中的背景工作角色。 」

此錯誤通常表示 SSIS 整合執行階段具有狀況不良的狀態。 請檢查 Azure 入口網站的 狀態 和 詳細的錯誤。 如需詳細資訊，請參閱 < [AZURE-SSIS 整合執行階段](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)。

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>錯誤訊息：「 您的 integration runtime 無法升級的而且最後會停止運作，因為我們無法存取您提供自訂的安裝程式的 Azure Blob 容器 」。

SSIS 整合執行階段無法存取自訂的安裝程式設定的儲存體時，就會發生此錯誤。 檢查共用的存取簽章 (SAS) 所提供的 URI 是否有效，而且未過期。

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>錯誤訊息：「 Analysis services 的 Microsoft OLE DB 提供者。 ' Hresult:0x80004005 描述:'COM 錯誤：COM 錯誤： mscorlib;引動過程的目標已擲回例外狀況 」

一個可能的原因是使用者名稱或使用 Azure Multi-factor Authentication，並啟用密碼設定為 Azure Analysis Services 驗證。 SSIS 整合執行階段不支援此驗證。 嘗試使用服務主體進行 Azure Analysis Services 驗證：
1. 中所述，準備的服務主體[使用服務主體的自動化](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)。
2. 在 [連接管理員] 中，設定**使用特定的使用者名稱和密碼**： 設定**AppID**作為使用者名稱與**clientSecret**做為密碼。

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>錯誤訊息：「 ADONET 來源無法取得連接 {GUID} 並出現下列錯誤訊息：使用者 ' NT AUTHORITY\ANONYMOUS LOGON' 登入失敗 」 時使用受控身分識別

請確定您未設定的驗證方法的連接管理員做**Active Directory 密碼驗證**時的參數*ConnectUsingManagedIdentity*是 **，則為 True**. 您可以將其設定為**SQL 驗證**相反地，其中如果會忽略*ConnectUsingManagedIdentity*設定。

### <a name="package-execution-takes-too-long"></a>封裝執行的時間太長

以下是可能的原因及建議的動作：
* 太多的封裝執行已排程 SSIS 整合執行階段。 所有這些執行等候佇列中其開啟。
  * 使用下列公式判斷最大值： 
    
    每個 IR 的最大平行執行計數 = 節點計數 * 每個節點的最大平行執行
  * 若要了解如何設定的節點計數和每個節點的最大平行執行，請參閱[Azure Data Factory 中建立 Azure SSIS integration runtime](create-azure-ssis-integration-runtime.md)。
* SSIS 整合執行階段已停止，或具有狀況不良的狀態。 若要了解如何查看 SSIS 整合執行階段狀態和錯誤，請參閱[AZURE-SSIS 整合執行階段](monitor-integration-runtime.md#azure-ssis-integration-runtime)。

我們也建議您在上設定逾時**一般** 索引標籤：![在 [一般] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)。

### <a name="poor-performance-in-package-execution"></a>在封裝執行中的效能不佳

請嘗試下列動作：

* 請確定 SSIS 整合執行階段是在資料來源和目的地位於相同區域中。

* 設定封裝執行的記錄層次**效能**收集在執行中的每個元件的持續時間資訊。 如需詳細資訊，請參閱 < [Integration Services (SSIS) 記錄](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)。

* 檢查 Azure 入口網站中的 IR 節點效能：
  * 如需如何監視 SSIS 整合執行階段的詳細資訊，請參閱[AZURE-SSIS 整合執行階段](monitor-integration-runtime.md#azure-ssis-integration-runtime)。
  * 您可以在 Azure 入口網站中檢視 data factory 的度量，SSIS 整合執行階段找到 CPU/記憶體歷程記錄。
    ![SSIS 整合執行階段的監視度量](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
