---
title: 將您的巨量資料分析解決方案從 Azure Data Lake Storage Gen1 升級為 Azure Data Lake Storage Gen2
description: 將解決方式升級為使用 Azure Data Lake Storage Gen2
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: 27d752b8ff7eafbb92930b19e17890ace8a90b85
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750440"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>將您的巨量資料分析解決方案從 Azure Data Lake Storage Gen1 升級為 Azure Data Lake Storage Gen2

如果您在巨量資料分析解決方案中使用 Azure Data Lake Storage Gen1，本指南可協助您將這些解決方案升級為使用 Azure Data Lake Storage Gen2。 您可以使用這份文件，評估您的解決方案對 Data Lake Storage Gen1 的相依性。 本指南也說明如何規劃及執行升級。

我們將協助您完成下列工作：

： heavy_check_mark：評估您的升級準備就緒

： heavy_check_mark：規劃升級

： heavy_check_mark：執行升級

## <a name="assess-your-upgrade-readiness"></a>評估您的升級整備程度

我們的目標是出現在 Data Lake Storage Gen1 的所有功能都可以在 Data Lake Storage Gen2 中使用。 在 Data Lake Storage Gen1 與 Data Lake Storage Gen2 中，這些功能的公開方式 (例如在 SDK、CLI 中等) 可能有所不同。 搭配 Data Lake Storage Gen1 運作的應用程式和服務，必須能夠以類似方式搭配 Data Lake Storage Gen2 運作。 最後，有些功能無法立即在 Data Lake Storage Gen2 中使用。 當這些功能可用時，我們將在這份文件中宣佈。

以下後續章節將協助您決定升級至 Data Lake Storage Gen2 的最適方式，以及何時這麼做最有意義。

### <a name="data-lake-storage-gen1-solution-components"></a>Data Lake Storage Gen1 解決方案元件

最有可能是，當您在分析解決方案或管線中使用 Data Lake Storage Gen1 時，您可運用許多其他技術來達到整體端對端功能。 這篇[文章](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios)描述資料流程的各種元件，包括內嵌、處理和取用資料。

此外，還有跨領域元件可佈建、管理及監視這些元件。 每個元件都可使用最合適的介面來搭配 Data Lake Storage Gen1 運作。 當您打算將解決方案升級至 Data Lake Storage Gen2 時，您必須留意所使用的介面。 您必須同時升級管理介面及資料介面，因為每個介面都有不同的需求。

![Data Lake Storage 解決方案元件](./media/data-lake-storage-upgrade/solution-components.png)

以上的**圖 1** 顯示您會在大多數分析解決方案中看到的功能元件。

**圖 2** 顯示如何使用特定技術實作這些元件的範例。

**圖 1** 中的「儲存」功能是由 Data Lake Storage Gen1 提供 (**圖 2**)。 請注意資料流程中的各種元件如何使用 REST API 或 Java SDK 與 Data Lake Storage Gen1 互動。 也請注意，跨領域功能元件與 Data Lake Storage Gen1 互動的方式。 布建元件會使用 Azure 資源範本，而使用 Azure 監視器記錄的監視元件會利用來自 Data Lake Storage Gen1 的運算元據。

若要將解決方案從使用 Data Lake Storage Gen1 升級為 Data Lake Storage Gen2 方案，您需要複製資料和中繼資料，重新串連資料流程，然後所有元件都必須能夠使用 Data Lake Storage Gen2。

下列各節提供資訊來協助您做出更好的決策：

： heavy_check_mark：平臺功能

： heavy_check_mark：程式設計介面

： heavy_check_mark： Azure 生態系統

： heavy_check_mark：合作夥伴生態系統

： heavy_check_mark：操作資訊

在每一節中，您將能夠判斷您升級的「必備條件」。 在您確定各項功能可用之後，或確定有合理的因應措施之後，請繼續本指南的進行[規劃升級](#planning-for-an-upgrade)一節。

### <a name="platform-capabilities"></a>平台功能

本節說明 Data Lake Storage Gen2 中目前有哪些 Data Lake Storage Gen1 功能可以使用。

| | Data Lake Storage Gen1 | Data Lake Storage Gen2 - 目標 | Data Lake Storage Gen2 - 可用性狀態  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| 資料組織| 支援作為資料夾和檔案儲存的資料 | 支援作為物件/Blob 以及資料夾和檔案儲存的資料[連結](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | 支援作為資料夾和檔案儲存的資料 – *現已推出* <br><br> 支援儲存成為物件/blob 的資料 - *尚無法使用* |
| 命名空間| 階層式 | 階層式 |  *現已推出*  |
| API  | 透過 HTTPS 的 REST API | 透過 HTTP/HTTPS 的 REST API| *現已推出* |
| 伺服器端 API| [WebHDFS 相容的 REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Azure Blob 服務 REST API [Data Lake Storage Gen2 REST API](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST API – *現已推出* <br><br> Azure Blob Service REST API – *尚無法使用*       |
| Hadoop 檔案系統用戶端 | 是 ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | 是 ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *現已推出*  |  
| 資料作業 - 授權  | 以 Azure Active Directory 身分識別為基礎的檔案和資料夾層級 POSIX 存取控制清單 (ACL)  | 以 Azure Active Directory 身分識別為基礎的檔案和資料夾層級 POSIX 存取控制清單 (ACL)。[共用金鑰](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)可供帳戶層級授權角色型存取控制 ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) 來存取容器 | *現已推出* |
| 資料作業 - 記錄  | 是 | 使用支援票證的特定期間記錄的一次性要求、Azure 監視整合 | 使用支援票證的特定期間記錄的一次性要求 – *現已推出*<br><br> Azure 的監視整合 – *尚無法使用* |
| 待用資料加密 | 透明的伺服器端，在 Azure Key Vault 中使用服務管理的金鑰與客戶管理的金鑰 | 透明的伺服器端，在 Azure Key Vault 中使用服務管理的金鑰與客戶管理的金鑰 | 服務管理的金鑰 – *現已推出*<br><br> 客戶管理的金鑰 – *現已推出*  |
| 管理作業 (例如帳戶建立) | [角色型存取控制](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) | [角色型存取控制](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) | *現已推出*|
| 開發人員 SDK | .NET、Java、Python、Node.js  | .NET、JAVA、Python、Node.js、C++、Ruby、PHP、Go、Android、iOS| *尚未提供* |
| |平行分析工作負載的效能最佳化。 高輸送量和 IOPS。 | 平行分析工作負載的效能最佳化。 高輸送量和 IOPS。 | *現已推出* |
| 虛擬網路 (VNet) 支援  | [使用虛擬網路整合](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [使用 Azure 儲存體的服務端點](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *現已推出* |
| 大小限制 | 帳戶大小、檔案大小或檔案數目沒有限制 | 帳戶大小或檔案數目沒有限制。 檔案大小限制為 5TB。 | *現已推出*|
| 異地備援| 本地備援 (LRS) | 本機多餘（LRS）區域冗余（ZRS）異地冗余（GRS）讀取權限異地冗余（RA-GRS）如需詳細資訊，請參閱[這裡](https://docs.microsoft.com/azure/storage/common/storage-redundancy)| *現已推出* |
| 區域可用性 | 請參閱 [這裡](https://azure.microsoft.com/regions/) | 所有 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *現已推出*                                                                                                                           |
| 價格                                       | 請參閱 [價格](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | 請參閱 [價格](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| 可用性 SLA                            | [請參閱 SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [請參閱 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *現已推出*                                                                                                                           |
| 資料管理                             | 檔案到期                                                                                                                                        | 生命週期原則                                                                                                                                                                                                                                                                                                                                                                                                          | *尚未提供*                                                                                                                       |

### <a name="programming-interfaces"></a>程式設計介面

下表說明有哪些 API 集合可供您的自訂應用程式使用。 為了讓事情更清楚，我們將這些 API 集合分成2種類型：管理 Api 和檔案系統 Api。

管理 Api 可協助您管理帳戶，而檔案系統 Api 則可協助您操作檔案和資料夾。

|  API 集合                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Data Lake Storage Gen2 的可用性 - 採用共用金鑰驗證 | Data Lake Storage Gen2 的可用性 - 採用 OAuth 驗證                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK - 管理                  | [連結](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *不支援*                                                      | *現已推出 –* [連結](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| .NET SDK –檔案系統                  | [連結](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *尚未提供*                                                | *尚未提供*                                                                                                                                             |
| Java SDK - 管理                  | [連結](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *不支援*                                                      | *現已推出 –* [連結](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| JAVA SDK –檔案系統                  | [連結](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                         | *尚未提供*                                                | *尚未提供*                                                                                                                                             |
| Node.js - 管理                   | [連結](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | 不支援                                                      | *現已推出 –* [連結](https://azure.github.io/azure-storage-node/)                                                                                            |
| Node.js-檔案系統                   | [連結](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *尚未提供*                                                | *尚未提供*                                                                                                                                             |
| Python - 管理                    | [連結](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *不支援*                                                      | *現已推出 –* [連結](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python-檔案系統                    | [連結](https://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *尚未提供*                                                | *尚未提供*                                                                                                                                             |
| REST API - 管理                  | [連結](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *不支援*                                                      | *現已推出 -*                                                                                                                                               |
| REST API 檔案系統                  | [連結](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *現已推出*                                                    | *現已推出 –* [連結](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell-管理和檔案系統 | [連結](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | 管理–不支援的檔案系統-尚未*提供*        | 管理 – *現已推出* [連結](https://docs.microsoft.com/powershell/module/az.storage) <br><br>檔案系統-尚未*提供* |
| CLI – 管理                       | [連結](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *不支援*                                                      | *現已推出 –* [連結](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI-檔案系統                       | [連結](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *尚未提供*                                                | *尚未提供*                                                                                                                                             |
| Azure Resource Manager 範本 - 管理             | [範本1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [範本2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [範本3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *不支援*                                                      | *現已推出 –* [連結](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Azure 生態系統

使用 Data Lake Storage Gen1 時，您可以在端對端管線中使用各種不同的 Microsoft 服務和產品。 這些服務和產品可直接或間接使用 Data Lake Storage Gen1。 下表顯示已修改成使用 Data Lake Storage Gen1 的服務清單，以及顯示哪些服務目前與 Data Lake Storage Gen2 相容。

| **領域**             | **Data Lake Storage Gen1 的可用性**                                                                                                                                    | **Data Lake Storage Gen2 的可用性 – 採用共用金鑰驗證**                                                                                                           | **Data Lake Storage Gen2 的可用性 – 採用 OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| 分析架構  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *現已推出*                                                                                                                                                              | *現已推出*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 - *現已推出* HDInsight 4.0 - *尚未提供*      | HDInsight 3.6 ESP – *現已推出* <br><br>  HDInsight 4.0 ESP - *尚無法使用*                                                                 |
|                      | Databricks Runtime 3.1 和更新版本                                                                                                                                               | [Databricks Runtime 5.1 和更新版本](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *– 現已推出* | [Databricks Runtime 5.1 和更新版本](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *現已推出*                                                                                              |
|                      | [SQL 資料倉儲](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *不支援*                                                                                                                                                              | *現已推出* [連結](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| 資料整合     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [版本 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *現已推出* 版本 1 – *不支援*                               | [版本 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *現已推出* <br><br> 版本 1 – *不支援*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *不支援*                                                                                                                                                              | *不支援*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *現已推出*                                                                                                                                                          | *現已推出*                                                                                                                             |
|                      | [資料目錄](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *尚未提供*                                                                                                                                                          | *尚未提供*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *現已推出*                                                                                                                                                          | *現已推出*                                                                                                                             |
| IoT                  | [事件中樞 – 擷取](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *現已推出*                                                                                                                                                          | *現已推出*                                                                                                                             |
|                      | [串流分析](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *現已推出*                                                                                                                                                          | *現已推出*                                                                                                                             |
| 消費          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *現已推出*                                                                                                                                                          | *現已推出*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *尚未提供*                                                                                                                                                          | *尚未提供*                                                                                                                             |
|                      | [分析服務](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *尚未提供*                                                                                                                                                          | *尚未提供*                                                                                                                             |
| 生產力         | [Azure 入口網站](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *不支援*                                                                                                                                                              | 帳戶管理 *– 現已推出* <br><br>資料作業 *–*  *尚無法使用*                                                                   |
|                      | [Visual Studio 適用的 Data Lake 工具](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *尚未提供*                                                                                                                                                          | *尚未提供*                                                                                                                             |
|                      | [Azure 儲存體總管](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *現已推出*                                                                                                                                                              | *現已推出*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *尚未提供*                                                                                                                                                          | *尚未提供*                                                                                                                             |

### <a name="partner-ecosystem"></a>合作夥伴生態系統

下表顯示已修改成使用 Data Lake Storage Gen1 的第三方服務和產品清單。 其中也顯示哪些項目目前與 Data Lake Storage Gen2 相容。

| 區域            | Partner  | 產品/服務  | Data Lake Storage Gen1 的可用性                                                                                                                                               | Data Lake Storage Gen2 的可用性 – 採用共用金鑰驗證                                                   | Data Lake Storage Gen2 的可用性 – 採用 OAuth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| 分析架構 | Cloudera     | CDH                  | [連結](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *尚未提供*                                                                                                  | [連結](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [連結](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *不支援*                                                                                                                  | *尚未提供*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [連結](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *尚未提供*                                                                                                  | *尚未提供*                                                                                                  |
|                     | Qubole       |                      | [連結](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *尚未提供*                                                                                                  | *尚未提供*                                                                                                  |
| ETL                 | StreamSets   |                      | [連結](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *尚未提供*                                                                                                  | *尚未提供*                                                                                                  |
|                     | Informatica  |                      | [連結](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *尚未提供*                                                                                                  | *尚未提供*                                                                                                  |
|                     | Attunity     |                      | [連結](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *尚未提供*                                                                                                  | *尚未提供*                                                                                                  |
|                     | Alteryx      |                      | [連結](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *尚未提供*                                                                                                  | *尚未提供*                                                                                                  |
|                     | ImanisData   |                      | [連結](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *尚未提供*                                                                                                  | *尚未提供*                                                                                                  |
|                     | WANdisco     |                      | [連結](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [連結](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [連結](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>操作資訊

Data Lake Storage Gen1 會將特定資訊和資料推送給其他可協助您運作管線的服務。 下表顯示 Data Lake Storage Gen2 中對應支援的可用性。

| 資料類型                                                                                       | Data Lake Storage Gen1 的可用性                                                                 | Data Lake Storage Gen2 的可用性                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| 計費資料 - 傳送給商務小組進行計費，而後提供給客戶的計量  | *現已推出*                                                                                             | *現已推出*                                                                                                                           |
| 活動記錄                                                                                          | [連結](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | 使用支援票證的特定期間記錄的一次性要求 – *現已可用* Azure 監視整合 – *尚未提供* |
| 診斷記錄                                                                                        | [連結](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | 使用支援票證的特定期間記錄的一次性要求 – *現已可用* Azure 監視整合 – *尚未提供* |
| 計量                                                                                                | *不支援*                                                                                               | *現已推出 –* [連結](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>規劃升級

本節假設您已檢閱本指南的[評估您的升級整備程度](#assess-your-upgrade-readiness)一節，而且您所有的相依性都符合。 如果有 Data Lake Storage Gen2 中仍然無法使用的功能，唯有知道對應的因應措施才能繼續執行。 下列各節提供如何規劃您的管線升級的相關指引。 本指南的[執行升級](#performing-the-upgrade)一節會說明如何執行實際升級。

### <a name="upgrade-strategy"></a>升級策略

升級的最重要部分就是決定策略。 這項決策會決定您可用的選擇。

下表列出一些已用來遷移資料庫、Hadoop 叢集等的知名策略。我們將在我們的指引中採用類似的策略，並將其調整為我們的內容。

| 策略                   | 優點                                                                                  | 缺點                                                           | 使用時機                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 隨即轉移                 | 最簡單。                                                                                 | 需要停機，以便複製資料、移動作業及移動輸入和輸出                                             | 適用於較簡單的解決方案，其中沒有多個解決方案存取相同的 Gen1 帳戶，因此可以快速受控的方式一起移動。                                                  |
| 一次性複製和累加式複製 | 在背景中執行複製以縮短停機時間，而來源系統仍在作用中。 | 需要停機，以便移動輸入和輸出。                   | 要複製的資料量很大，且無法接受與隨即轉移相關聯的停機時間。 轉換前，可能需要在目標系統上使用大量生產資料進行測試。 |
| 平行採用              | 停機時間最短：允許應用程式自行遷移的時間。           | 最精心規劃，因為兩個系統之間需要雙向處理同步。 | 適用於複雜案例，其中在 Data Lake Storage Gen1 上建置的應用程式不能一次完全移轉，而且必須以累加方式移動。                                                      |

以下是每個策略的相關步驟詳細資料。 這些步驟會列出您要對升級相關元件所執行的作業。 這包括整體來源系統、整體目標系統、來源系統的輸入來源，來源系統的輸出目的地，以及在來源系統上執行的作業。

這些步驟都不是規定的步驟。 主要用來設定關於每個策略的想法架構。 我們將提供每個策略在實作時的個案研究。

#### <a name="lift-and-shift"></a>隨即轉移

1. 暫停來源系統 – 輸入來源、作業、輸出目的地。
2. 將所有資料從來源系統複製到目標系統。
3. 將所有輸入來源指向目標系統。 從目標系統指向輸出目的地。
4. 將所有作業移到目標系統並加以修改、執行。
5. 關閉來源系統。

#### <a name="copy-once-and-copy-incremental"></a>一次性複製和累加式複製

1. 將資料從來源系統複製到目標系統。
2. 定期將累加資料從來源系統複製到目標系統。
3. 從目標系統指向輸出目的地。
4. 在目標系統上移動、修改、執行所有作業。
5. 在方便的情況下，以累加方式將輸入來源指向目標系統。
6. 一旦所有輸入來源都指向目標系統。
    1. 關閉累加式複製。
    2. 關閉來源系統。

#### <a name="parallel-adoption"></a>平行採用

1. 設定目標系統。
2. 設定來源系統與目標系統之間的雙向複寫。
3. 以累加方式將輸入來源指向目標系統。
4. 以累加方式將作業移到目標系統並加以修改、執行。
5. 以累加方式從目標系統指向輸出目的地。
6. 在所有原始輸入來源、作業和輸出目的地都使用目標系統之後，請關閉來源系統。

### <a name="data-upgrade"></a>資料升級

您用來執行升級的整體策略 (如[升級策略](#upgrade-strategy)一節所述)，將會決定您可用於資料升級的工具。 下面所列的工具是以目前資訊為基礎的建議工具。 

#### <a name="tools-guidance"></a>工具指引

| 策略                       | 工具                                                                                                             | 優點                                                                                                                             | 考量                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **隨即轉移**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | 受控雲端服務                                                                                                                | 目前可以複製資料和 Acl。                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | 知名 Hadoop 提供的工具權限，亦即可利用此工具複製 ACL                                                   | 需要可同時連線到 Data Lake Storage Gen1 和 Gen2 的叢集。                                                                                                                                                                                   |
| **一次性複製和累加式複製** | Azure Data Factory                                                                                                    | 受控雲端服務                                                                                                                | 目前可以複製資料和 Acl。 若要在 ADF 中支援累加式複製，必須以時間序列的方式組織資料。 累加式複製之間的最短間隔為 [15 分鐘](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger)。 |
| **平行採用**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | 支援一致複寫：如果使用已連線到 Azure Data Lake Storage 的純 Hadoop 環境，則支援雙向複寫 | 如果未使用純 Hadoop 環境，則複寫可能延遲。                                                                                                                                                                                                                                                  |

請注意，有協力廠商可以處理 Data Lake Storage Gen1 以 Data Lake Storage Gen2 升級，而不需涉及上述資料/中繼資料複製工具（例如： [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)）。 他們提供執行資料移轉及工作負載移轉的「一站式」體驗。 您可能必須針對其生態系統以外的任何工具，執行頻外升級。

#### <a name="considerations"></a>考量

* 在開始任何升級部分之前，您必須先手動建立 Data Lake Storage Gen2 帳戶，因為目前的指引不包含任何以您的 Gen1 帳戶資訊為基礎的自動 Gen2 帳戶程序。 請務必比較 [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) 和 [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account) 的帳戶建立程序。

* Data Lake Storage Gen2，僅支援最多 5 TB 大小的檔案。 若要升級至 Data Lake Storage Gen2，您可能需要在 Data Lake Storage Gen1 中調整檔案大小，使其小於 5 TB 的大小。

* 如果您使用不會複製 ACL 的工具，或您不想複製 ACL，則需要在適當的最上層手動設定目的地的 ACL。 您可以使用儲存體總管來執行此作業。 確保這些 ACL 是預設 ACL，您複製的檔案和資料夾才能繼承它們。

* 在 Data Lake Storage Gen1 中，您可以設定 ACL 的最高層級位於帳戶的根目錄。 不過，在 Data Lake Storage Gen2 中，您可以設定 Acl 的最高層級位於容器中的根資料夾，而不是整個帳戶。 因此，如果您想要在帳戶層級設定預設 ACL，則必須在 Data Lake Storage Gen2 帳戶中的所有檔案系統間複製這些 ACL。

* 兩個儲存系統之間的檔案命名限制不同。 從 Data Lake Storage Gen2 複製到 Data Lake Storage Gen1 時，這些差異格外令人關切，因為後者有更多受限的條件約束。

### <a name="application-upgrade"></a>應用程式升級

當您需要在 Data Lake Storage Gen1 或 Data Lake Storage Gen2 上建置應用程式時，您必須先選擇適當的程式設計介面。 在該介面上呼叫 API 時，您必須提供適當的 URI 和適當的認證。 Data Lake Storage Gen1 與 Data Lake Storage Gen2 之間的這三個元素 (API、URI 和認證提供方式) 有所不同。所以，在應用程式升級過程中，您必須適當地對應這三個概念。

#### <a name="uri-changes"></a>URI 變更

此處的主要工作是將具有 `abfss://` 前置詞 `adl://` 的 URI 轉譯為 URI。

Data Lake Storage Gen1 的 URI 配置會在[這裡](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store)詳述，但大致上說，其為 *adl://mydatalakestore.azuredatalakestore.net/\<file_path\>。*

[這裡](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)會詳細說明用來存取 Data Lake Storage Gen2 檔案的 URI 配置，但大致而言，這是 `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`。

您必須瀏覽現有的應用程式，並確保已將 URI 適度變更為指向 Data Lake Storage Gen2 URI。 此外，您必須新增適當的認證。 最後，您淘汰原始應用程式並取代為新應用程式的方式必須密切符合您的整體升級策略。

#### <a name="custom-applications"></a>自訂應用程式

視您的應用程式搭配 Data Lake Storage Gen1 使用的介面而定，您需要加以修改使其適應 Data Lake Storage Gen2。

##### <a name="rest-apis"></a>REST API

如果您的應用程式使用 Data Lake Storage REST API，您必須將應用程式修改為使用 Data Lake Storage Gen2 REST API。 「程式設計介面」一節會提供連結。

##### <a name="sdks"></a>SDK

如*評估您的升級整備程度*一節所提到，目前無法使用 SDK。 如果您想要透過連接埠將您的應用程式連到 Data Lake Storage Gen2，我們建議您等到有支援的 SDK 可用。

##### <a name="powershell"></a>PowerShell

如「評估您的升級整備程度」一節所提出，PowerShell 支援目前不適用於資料平面。

您可以使用 Data Lake Storage Gen2 中的適當 Cmdlet 取代管理平面 Cmdlet。 「程式設計介面」一節會提供連結。

##### <a name="cli"></a>CLI

如*評估您的升級整備程度*一節所提出，CLI 支援目前不適用於資料平面。

您可以使用 Data Lake Storage Gen2 中的適當命令取代管理平面命令。 「程式設計介面」一節會提供連結。

### <a name="analytics-frameworks-upgrade"></a>分析架構升級

如果您的應用程式在存放區 (例如明確檔案和資料夾路徑) 中建立資訊相關中繼資料，則必須在存放區資料/中繼資料升級後執行其他動作。 對於通常會在存放區資料之上建立目錄資料的分析架構 (例如 Azure HDInsight、Databricks 等) 而言，這特別真實。

分析架構適用於遠端存放區 (例如 Data Lake Storage Gen1 和 Gen2) 中儲存的資料和中繼資料。 因此，理論上，引擎可以是暫時的，只有在需要針對所儲存的資料執行作業時才會啟動。

不過，為了最佳化效能，分析架構可建立遠端存放區中所儲存檔案和資料夾的明確參考，然後建立快取來保存這些參考。 萬一遠端資料的 URI 變更 (例如，先前在 Data Lake Storage Gen1 中儲存資料的叢集，而現在想要儲存在 Data Lake Storage Gen2 中)，則所複製相同內容的 URI 會不同。 因此，在資料和中繼資料升級之後，也需要更新或重新初始化這些引擎的快取

在規劃過程中，您必須找出您的應用程式並指出如何重新初始化中繼資料資訊，以指向現在儲存在 Data Lake Storage Gen2 中的資料。 以下是常用分析架構的指引，可協助您進行其升級步驟。

#### <a name="azure-databricks"></a>Azure Databricks

視您選擇的升級策略而定，這些步驟會有所不同。 本節假設您已選擇「隨即轉移」策略。 此外，用來存取 Data Lake Storage Gen1 帳戶中資料的現有 Databricks 工作區，應該處理已複製到 Data Lake Storage Gen2 帳戶的資料。

首先，確定您已建立 Gen2 帳戶，接著使用適當工具將資料和中繼資料從 Gen1 複製到 Gen2。 這些工具已在[資料升級](#data-upgrade)一節中提出。

然後，[升級](https://docs.azuredatabricks.net/user-guide/clusters/index.html)現有的 Databricks 叢集以開始使用 Databricks 執行階段 5.1 或更新版本，其應該支援 Data Lake Storage Gen2。

之後的步驟會以現有 Databricks 工作區存取 Data Lake Storage Gen1 帳戶中資料的方式為基礎。 [直接](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly)從 Notebook，或透過[掛接點](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs)呼叫 adl:// URIs，即可達成。

如果您藉由提供完整的 adl:// URI，直接從 Notebook 存取，則必須瀏覽每個 Notebook 及變更組態，才能存取對應的 Data Lake Storage Gen2 URI。

接下來，您需要將它重新設定為指向 Data Lake Storage Gen2 帳戶。 不再需要變更，而且 Notebook 應該能夠如往常一樣運作。

如果您使用任何其他升級策略，則可以建立上述步驟的變化，以符合您需求。

### <a name="azure-ecosystem-upgrade"></a>Azure 生態系統升級

本指南的 [Azure 生態系統](#azure-ecosystem)一節中提出的每項工具和服務，必須設定為使用 Data Lake Storage Gen2。

首先，確定可以與 Data Lake Storage Gen2 整合。

然後，就必須變更上面所呼叫的元素（例如： URI 和認證）。 您可以修改使用 Data Lake Storage Gen1 的現有執行個體，或者可以建立會使用 Data Lake Storage Gen2 的新執行個體。

### <a name="partner-ecosystem-upgrade"></a>夥伴生態系統升級

請與提供元件和工具的夥伴合作，確保他們可以使用 Data Lake Storage Gen2。 

## <a name="performing-the-upgrade"></a>執行升級

### <a name="pre-upgrade"></a>升級前

在此階段，您已瀏覽本指南的*評估您的升級整備程度*一節和[規劃升級](#planning-for-an-upgrade)一節，您已收到所有必要資訊，而且已建立符合您需求的計劃。 您在這個階段可能會有測試工作。

### <a name="in-upgrade"></a>升級中

視您選擇的策略與您解決方案的複雜度而定，這可能是短期階段，也可能是長期階段，其中有多個工作負載等待以累加方式移至 Data Lake Storage Gen2。 這是升級的最重要部分。

### <a name="post-upgrade"></a>升級後

轉換作業完成之後，最終步驟會涉及徹底的驗證。 這包括但不限於確認資料已可靠地複製、驗證 Acl 是否已正確設定、驗證端對端管線是否正常運作等。完成驗證之後，您現在可以關閉舊的管線、刪除來源 Data Lake Storage Gen1 帳戶，並在 Data Lake Storage Gen2 架構的解決方案上獲得完整的速度。

## <a name="conclusion"></a>結論

本文件中所提供的指引應該已協助您將解決方案升級為使用 Data Lake Storage Gen2。 

如果您有其他問題或有任何意見反應，請在下面提供相關意見，或在 [Azure 意見反應論壇](https://feedback.azure.com/forums/327234-data-lake)中提供意見反應。
