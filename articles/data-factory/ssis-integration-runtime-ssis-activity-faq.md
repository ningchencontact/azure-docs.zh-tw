---
title: 疑難排解封裝執行中 SSIS 整合執行階段 |Microsoft Docs
description: 這篇文章提供 SSIS 整合執行階段中的 SSIS 封裝執行的疑難排解指引
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
ms.openlocfilehash: f17c364d258ef356a98180c9903603d92a6a9245
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078517"
---
# <a name="troubleshooting-package-execution-in-ssis-integration-runtime"></a>在 SSIS 整合執行階段疑難排解封裝執行

本文包含 SSIS 整合執行階段、 可能原因和動作來解決錯誤中執行 SSIS 套件時，您可能會達到最常見的錯誤。

## <a name="where-can-i-find-logs-for-troubleshoot"></a>哪裡可以找到記錄檔的疑難排解

* ADF 入口網站可用來檢查輸出的 SSIS 套件執行活動包括執行結果、 錯誤訊息和作業識別碼。 詳細資訊，請參閱[監視管線](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)

* SSIS 目錄 (SSISDB) 可用來檢查詳細資料記錄檔中的執行。 詳細資料，請參閱[監視封裝執行和其他作業](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)

## <a name="common-errors-causes-and-solution"></a>常見的錯誤、 原因和解決方案

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>錯誤訊息：`"Connection Timeout Expired."`或 `"The service has encountered an error processing your request. Please try again."`

* 可能的原因和建議的動作：
  * 資料來源/目的地會多載。 檢查在您的資料來源/目的地的負載，並查看是否有足夠的容量。 例如，如果使用 Azure SQL，則建議您考慮向上的小數位數，如果資料庫可能會逾時。
  * SSIS 整合執行階段和資料來源/目的地之間的網路不穩定，，尤其是當連接為跨區域或內部部署與 azure 之間。 建議要套用重試模式在 SSIS 封裝中的遵循下列步驟：
    * 請確定您的 SSIS 套件可以上重新執行失敗，沒有副作用 （例如。 遺失資料，資料 dup....）
    * 設定**重試**並**重試間隔**的 [一般] 索引標籤中執行 SSIS 套件活動![設定 [一般] 索引標籤上的屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * ADO.NET 和 OLEDB 來源/目的地元件，ConnectRetryCount 和 ConnectRetryInterval 可以設定在 SSIS 封裝中的連接管理員或 SSIS 活動

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>錯誤訊息： `"ADO NET Source has failed to acquire the connection '...' with the following error message: "A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible."`

* 可能的原因和建議的動作：
  * 此問題通常表示資料來源/目的地是從 SSIS 整合執行階段，這可能因不同原因而無法存取：
    * 請確定您正確地傳送資料的來源/目的地的名稱/IP
    * 請確定防火牆已正確設定
    * 請確定您的 vNet 已正確設定，如果您的資料來源/目的地位於內部部署。
      * 您可以確認問題是否來自 vNet 組態中，藉由佈建相同的 vNet 中的 Azure VM。 然後檢查是否可以從 Azure VM 中存取資料來源/目的地
      * 您可以找到更多詳細與 SSIS 整合執行階段在使用 vNet [Azure SSIS 整合執行階段加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-could-not-create-a-managed-connection-manager"></a>錯誤訊息: 「`ADO NET Source has failed to acquire the connection '...' with the following error message: "Could not create a managed connection manager.`"

* 可能的原因和建議的動作：
  * 封裝中使用的 ADO.NET 提供者未安裝 SSIS 整合執行階段中。 您可以使用 自訂安裝來安裝提供者。 自訂安裝程式的更多詳細篇[自訂 AZURE-SSIS 整合執行階段設定](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-the-connection--is-not-found"></a>錯誤訊息: 「`The connection '...' is not found`"

* 可能的原因和建議的動作：
  * 此錯誤可能是因為舊版 SSMS 中的已知的問題。 如果封裝包含自訂元件 （比方說，SSIS 的 Azure Feature Pack 或第 3 個合作對象元件），其中使用 SSMS 來進行部署的電腦上未安裝，元件就會將由 SSMS 所移除，並會導致錯誤。 升級[SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)已修正此問題的最新版本。

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>錯誤訊息：「 沒有足夠的空間磁碟上 」

* 可能的原因和建議的動作：
  * 此錯誤表示在 SSIS Integration Runtime 節點的本機磁碟用完。 請檢查您的套件或自訂安裝程式是否會耗用許多磁碟空間。
    * 如果磁碟由您的套件，它將會釋出了封裝執行完成之後。
    * 如果磁碟由您的自訂設定而定，您必須停止 SSIS 整合執行階段，修改您的指令碼，然後重新啟動 SSIS 整合執行階段。 您指定的自訂安裝程式的整個 Azure Blob 容器會複製到 SSIS 整合執行階段節點，因此請確認是否有任何不必要的內容，該容器下。

### <a name="error-message-cannot-open-file-"></a>錯誤訊息：「 無法開啟檔案 '...' 」

* 可能的原因和建議的動作：
  * 當封裝執行 SSIS 整合執行階段中的本機磁碟中找不到檔案時，就會發生此錯誤。
    * 它也不建議在 SSIS 整合執行階段中執行的封裝中使用絕對路徑。 使用目前的執行工作目錄 （.） 或暫存資料夾 （%TEMP%)改為。
    * 如有需要保存 SSIS 整合執行階段節點上的某些檔案，則建議準備透過檔案[來自訂安裝](how-to-configure-azure-ssis-ir-custom-setup.md)。 在完成執行之後，將會清除執行的工作目錄中的所有檔案。
    * 另一個選項是使用 Azure 檔案，而不是將檔案儲存在 SSIS Integration Runtime 節點。 更多詳細資料，請參閱[ https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares ](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)。

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>錯誤訊息：「 資料庫 'SSISDB' 已達到大小配額 」

* 可能的原因和建議的動作：
  * 建立 SSIS 整合執行階段時，Azure SQL 或受控執行個體中建立 SSISDB 已達到其配額。
    * 請考慮增加您的資料庫，以解決此問題的 DTU。 詳細資訊，請參閱 [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)
    * 請檢查您的套件是否會產生許多記錄。 如果是的話，就可以設定彈性工作來清除這些記錄檔。 請參閱[清除 使用 Azure 彈性資料庫工作的 SSISDB 記錄](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)如需詳細資料。

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>錯誤訊息：「 資料庫的要求限制是...且已達到。 」

* 可能的原因和建議的動作：
  * 如果在 SSIS 整合執行階段中平行執行許多套件，因為叫用 SSISDB 的要求限制時，就可能會發生這個錯誤。 請考慮增加您的 SSISDB，若要解決此問題的 DTC。 詳細資訊，請參閱 [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>錯誤訊息：「 SSIS 作業失敗，發生未預期的作業狀態:...」

* 可能的原因和建議的動作：
  * 錯誤暫時性的錯誤所造成，因此請嘗試重新執行該封裝的執行。 建議要套用重試模式在 SSIS 封裝中的遵循下列步驟：
    * 請確定您的 SSIS 套件可以重新執行失敗而無副作用 （例如，資料遺失，資料 dup....）
    * 設定**重試**並**重試間隔**的 [一般] 索引標籤中執行 SSIS 套件活動![設定 [一般] 索引標籤上的屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * ADO.NET 和 OLEDB 來源/目的地元件，ConnectRetryCount 和 ConnectRetryInterval 可以設定在 SSIS 封裝中的連接管理員或 SSIS 活動

### <a name="error-message-there-is-no-active-worker"></a>錯誤訊息：「 沒有任何作用中的背景工作角色。 」

* 可能的原因和建議的動作：
  * 此錯誤通常表示 SSIS 整合執行階段處於狀況不良的狀態。 檢查 Azure 入口網站的狀態和詳細錯誤： [https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>錯誤訊息：「 您的 integration runtime 無法升級的而且最後會停止運作，因為我們無法存取您提供自訂的安裝程式的 Azure Blob 容器 」。

* SSIS 整合執行階段無法存取自訂安裝程式設定的儲存體時，就會發生此錯誤。 請檢查您提供的 SAS Uri 是否有效，而且未過期。

### <a name="package-takes-unexpected-long-time-to-execute"></a>套件就會發生未預期的長時間執行

* 可能的原因和建議的動作：
  * 太多的封裝執行已排程 SSIS 整合執行階段。 在此情況下，所有的執行會轉而執行的佇列中等待。
    * 每個 IR 的最大平行執行計數 = 節點計數 * 每個節點的最大平行執行
    * 請參閱[Azure Data Factory 中建立 Azure SSIS Integration Runtime](create-azure-ssis-integration-runtime.md)如何設定的節點計數和每個節點的最大平行執行。
  * SSIS 整合執行階段已停止或處於狀況不良的狀態。 請檢查[AZURE-SSIS 整合執行階段](monitor-integration-runtime.md#azure-ssis-integration-runtime)如何查看 SSIS 整合執行階段狀態和錯誤。
  * 建議您是否確定封裝執行應該完成一段時間中，設定逾時：![在 [一般] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

### <a name="poor-performance-in-package-execution"></a>在封裝執行中的效能不佳

* 可能的原因和建議的動作：

  * 檢查 SSIS 整合執行階段是否在資料來源和目的地位於相同區域中。

  * 啟用 「 效能 」 記錄層級

      您可以設定封裝執行 「 效能 」 會收集每個元件在執行中的更多詳細資料持續時間資訊的記錄層級。 詳細資料位於： [https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

  * 檢查 Azure 入口網站中 IR [監視] 頁面中的 IR 節點效能。
    * 如何監視 SSIS 整合執行階段：[Azure SSIS 整合執行階段](monitor-integration-runtime.md#azure-ssis-integration-runtime)
    * 在 Azure 入口網站中將 SSIS 整合執行階段的記憶體使用量的歷程記錄會位於資料處理站的計量![監視計量的 SSIS 整合執行階段](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
