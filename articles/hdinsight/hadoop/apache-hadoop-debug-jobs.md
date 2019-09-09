---
title: 對 Apache Hadoop 進行偵錯：檢視記錄與解譯錯誤訊息 - Azure HDInsight
description: 了解您使用 PowerShell 來管理 HDInsight 時可能收到的錯誤訊息，以及可採取來回復的步驟。
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: e5ae05b2ad1dc03bad210b1f67834865afd49df3
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810887"
---
# <a name="analyze-apache-hadoop-logs"></a>分析 Apache Hadoop 記錄

Azure HDInsight 中的每個 Apache Hadoop 叢集都有一個 Azure 儲存體帳戶作為預設檔案系統。 這個儲存體帳戶稱為預設儲存體帳戶。 叢集使用預設儲存體帳戶上的 Azure 資料表儲存體和 Blob 儲存體來儲存其記錄。  若要找出叢集的預設儲存體帳戶，請參閱[在 HDInsight 中管理 Apache Hadoop 叢集](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts)。 即使在刪除叢集之後，記錄仍會保留在儲存體帳戶中。

## <a name="logs-written-to-azure-tables"></a>寫入 Azure 資料表的記錄

寫入 Azure 資料表的記錄可讓人更深入了解 HDInsight 叢集發生的情形。

當您建立 HDInsight 叢集時，系統會自動在預設的資料表儲存體中，為以 Linux 為基礎的叢集建立 6 個資料表：

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

資料表檔案名為**u\<ClusterName >\<DDMonYYYYatHHMMSSsss TableName >** 。

這些資料表包含下列欄位：

* ClusterDnsName
* ComponentName
* EventTimestamp
* 主機
* MALoggingHash
* Message
* N
* PreciseTimeStamp
* Role
* RowIndex
* 租用戶
* TIMESTAMP
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>用於存取記錄的工具
有許多工具可用來存取這些資料表中的資料：

* Visual Studio
* Azure 儲存體總管
* Power Query for Excel

#### <a name="use-power-query-for-excel"></a>使用 Power Query for Excel
您可以從 [Microsoft Power Query for Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379) 安裝 Power Query。 請參閱下載頁面以了解系統需求。

**使用 Power Query 來開啟和分析服務記錄**

1. 開啟 **Microsoft Excel**。
2. 從 [Power Query] 功能表中，按一下 [從 Azure]，然後按一下 [從 Microsoft Azure 資料表儲存體]。
   
    ![HDInsight Hadoop Excel PowerQuery 開啟 Azure 資料表儲存體](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. 輸入儲存體帳戶名稱 (簡短名稱或 FQDN)。
4. 輸入儲存體帳戶金鑰。 您應該會看到一份資料表清單：
   
    ![儲存在 Azure 資料表儲存體中的 HDInsight Hadoop 記錄](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. 在 [導覽器] 窗格中以滑鼠右鍵按一下 hadoopservicelog 資料表，然後選取 [編輯]。 您應該會看到 4 個資料行。 選擇性地選取 [分割區索引鍵]、[資料列索引鍵] 和 [時間戳記] 資料行，然後從功能區的選項中按一下 [移除資料行]，將它們刪除。
6. 按一下 [內容] 資料行上的展開圖示，選擇您要匯入至 Excel 試算表的資料行。 在此示範中，我選擇 TraceLevel 和 ComponentName：它可以提供一些基本資訊讓我知道哪些元件有問題。
   
    ![HDInsight Hadoop 記錄選擇資料行 excel](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png "HDInsight Hadoop 記錄選擇資料行 excel")
7. 按一下 [確定] 以匯入資料。
8. 選取 [TraceLevel]、[Role] 和 [ComponentName] 資料行，然後按一下功能區的 [群組依據] 控制項。
9. 按一下 [群組依據] 對話方塊中的 [確定]
10. 按一下 [套用並關閉]。

您現在可以視需要使用 Excel 來篩選和排序。 您可以包含其他資料行 (例如 Message)，以便在發生問題時深入探索，但對上述資料行進行選取和分組將有助於了解在 Hadoop 服務發生的狀況。 相同的概念也適用於 setuplog 和 hadoopinstalllog 資料表。

#### <a name="use-visual-studio"></a>使用 Visual Studio
**使用 Visual Studio**

1. 開啟 Visual Studio。
2. 從 [檢視] 功能表中，按一下 [Cloud Explorer]。 或直接按一下 **CTRL+\, CTRL+X**。
3. 從 [Cloud Explorer] 中，選取 [資源類型]。  另一個可用的選項是 [資源群組]。
4. 展開 [儲存體帳戶]、您叢集的預設儲存體帳戶，然後是 [資料表]。
5. 按兩下 [hadoopservicelog]。
6. 新增篩選器。 例如:
   
        TraceLevel eq 'ERROR'
   
    ![HDInsight Hadoop 記錄選擇資料行與](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png "HDInsight Hadoop 記錄選擇資料行與")
   
    如需建構篩選器的詳細資訊，請參閱[建構資料表設計工具的篩選字串](../../vs-azure-tools-table-designer-construct-filter-strings.md)。

## <a name="logs-written-to-azure-blob-storage"></a>寫入 Azure Blob 儲存體的記錄
寫入 Azure 資料表的記錄可讓人更深入了解 HDInsight 叢集發生的情形。 不過，這些資料表並不會提供工作層級記錄，這些記錄有助於發生問題時進一步深入探索。 為了提供這一層更深入的詳細資料，針對透過 Templeton 提交的任何作業，HDInsight 叢集設定成將工作記錄寫入 Blob 儲存體帳戶。 實際上，這代表使用 Microsoft Azure PowerShell Cmdlet 或 .NET Job Submission API 提交的作業，而不是透過叢集的 RDP/命令列存取所提交的作業。 

若要檢視記錄，請參閱[在 Linux 型 HDInsight 上存取 Apache Hadoop YARN 應用程式記錄](../hdinsight-hadoop-access-yarn-app-logs-linux.md)。


如需應用程式記錄的詳細資訊，請參閱 [簡化 Apache Hadoop YARN 的使用者記錄管理和存取](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/)。


## <a name="view-cluster-health-and-job-logs"></a>檢視叢集健康情況和工作記錄
### <a name="access-the-ambari-ui"></a>存取 Ambari UI
從 Azure 入口網站中，按一下 HDInsight 叢集名稱以開啟叢集窗格。 從叢集窗格中，按一下 [儀表板]。

![啟動叢集儀表板](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>存取 Yarn UI
從 Azure 入口網站中，按一下 HDInsight 叢集名稱以開啟叢集窗格。 從叢集窗格中，按一下 [儀表板]。 出現提示時，輸入叢集系統管理員認證。 在 Ambari 中，從左邊的服務清單中選取 [YARN]。 在出現的頁面上，選取 [Quick Links] \(快速連結\)，然後選取作用中的前端節點項目和 [Resource Manager] UI。

您可以使用 YARN UI 來執行下列動作：

* **取得叢集狀態**。 從左窗格中展開 [叢集]，然後按一下 [關於]。 這樣即會顯示叢集狀態詳細資料，例如配置的記憶體總計、使用的核心數目、叢集資源管理員的狀態、叢集版本等。
  
    ![啟動叢集儀表板 yarn](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png "啟動叢集儀表板 yarn")
* **取得節點狀態**。 從左窗格中展開 [叢集]，然後按一下 [節點]。 這樣會列出叢集中的所有節點、每個節點的 HTTP 位址、配置給每個節點的資源等資訊。
* **監視工作狀態**。 從左窗格展開 [叢集]，然後按一下 [應用程式] 以列出叢集中的所有工作。 如果您想要查看處於特定狀態 (例如，新增、已提交、執行中等狀態) 的工作，可按一下[應用程式]底下的適當連結。 您可以進一步按一下工作名稱來深入了解該工作，例如包含輸出、記錄等相關資訊。

### <a name="access-the-hbase-ui"></a>存取 HBase UI
從 Azure 入口網站中，按一下 HDInsight HBase 叢集名稱以開啟叢集窗格。 從叢集窗格中，按一下 [儀表板]。 出現提示時，輸入叢集系統管理員認證。 在 Ambari 中，從服務清單中選取 [HBase]。 選取頁面頂端的 [Quick links] \(快速連結\)，指向作用中的 Zookeeper 節點連結，然後按一下 [HBase Master] \(HBase 主節點\) UI。

## <a name="hdinsight-error-codes"></a>HDInsight 錯誤代碼
本節列舉的錯誤訊息可協助 Azure HDInsight 的 Hadoop 使用者了解他們在使用 Azure PowerShell 來管理服務時可能遭遇的錯誤狀況，並提供一些可採取的建議步驟，供他們從錯誤狀況中復原。

使用 Azure 入口網站管理 HDinsight 叢集時也可能會看到其中某些錯誤訊息。 但由於此情況下可能採取的補救動作有其限制，您可能看到的其他錯誤訊息比較不詳細。 在很顯然有緩和措施的情況下，則會提供其他錯誤訊息。 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **描述**：請至少提供一個元件的 Azure SQL 資料庫詳細資料，以便使用 Hive 和 Oozie metastore 的自訂設定。
* **降低風險**：使用者必須提供有效的 SQL Azure 中繼存放區，然後重試要求。  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **描述**：無法在區域 *nameOfYourRegion*中建立叢集。 請使用有效的 HDInsight 區域，然後重試要求。
* **降低風險**：客戶應該建立目前支援的叢集區域：東南亞、西歐、北歐、美國東部或美國西部。  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **描述**：伺服器找不到所要求的叢集記錄。  
* **降低風險**：重試作業。

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **描述**：叢集 DNS 名稱 *yourDnsName* 無效。 請確定名稱以英數字元開始和結束，且只包含 '-' 特殊字元  
* **降低風險**：請確定叢集的 DNS 名稱有效，開頭和結束是英數字元，且不含虛線 '-' 以外的特殊字元，然後重試作業。

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **描述**：叢集名稱 *yourClusterName* 無法使用。 請挑選其他名稱。  
* **降低風險**：使用者應該指定唯一且尚不存在的叢集名稱，然後重試。 如果使用者在使用入口網站，當建立步驟中已使用叢集名稱時，UI 會通知使用者。

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **描述**：叢集密碼無效。 密碼長度必須至少為 10 個字元，且必須至少包含一個數字、大寫字母、小寫字母和特殊字元，不可有空格，而且不應該包含使用者名稱。  
* **降低風險**：請提供有效的叢集密碼，然後重試作業。

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **描述**：叢集使用者名稱無效。 請確定使用者名稱不含特殊字元或空格。  
* **降低風險**：請提供有效的叢集使用者名稱，然後重試作業。

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **描述**：叢集 DNS 名稱 *yourDnsClusterName* 無效。 請確定名稱以英數字元開始和結束，且只包含 '-' 特殊字元  
* **降低風險**：請提供有效的 DNS 使用者名稱，然後重試作業。

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **描述**：URI *yourcontainerURI* 中的容器名稱和要求內文中的 DNS 名稱 *yourDnsName* 必須相同。  
* **降低風險**：請確定容器名稱和 DNS 名稱相同，然後重試作業。

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **描述**：叢集組態無效。 在節點大小中找不到任何資料節點定義。  
* **降低風險**：重試作業。

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **描述**：刪除叢集的部署失敗  
* **降低風險**：重試刪除作業。

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **描述**：服務組態錯誤。 找不到必要的 DNS 對應資訊。  
* **降低風險**：刪除叢集，然後建立新的叢集。

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **描述**：嘗試建立重複的叢集容器。 已存在 *nameOfYourContainer* 的記錄，但 Etag 不符。
* **降低風險**：請提供容器的唯一名稱，然後重試建立作業。

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **描述**：代管服務 *nameOfYourHostedService* 已包含叢集。 代管服務不可包含多個叢集  
* **降低風險**：將叢集裝載於其他代管服務中。

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **描述**：伺服器無法更新叢集部署的狀態。  
* **降低風險**：重試作業。 如果此情況發生多次，請連絡 CSS。

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **描述**：維護時已刪除叢集 *yourClusterName*。 請重新建立叢集。
* **降低風險**：重新建立叢集。

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **描述**：叢集組態無效。 在節點大小中找不到必要的前端節點組態。
* **降低風險**：重試作業。

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **描述**：無法建立代管服務 *nameOfYourHostedService*。 請重試要求。  
* **降低風險**：重試要求。

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **描述**：託管服務 *nameOfYourHostedService* 已有生產部署。 代管服務不可包含多個生產部署 請以不同的叢集名稱來重試要求。
* **降低風險**：請使用不同的叢集名稱，然後重試要求。

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **描述**：找不到叢集的代管服務 *nameOfYourHostedService*。  
* **降低風險**：如果叢集處於錯誤狀態，請刪除叢集，然後再試一次。

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **描述**：代管服務 *nameOfYourHostedService* 沒有相關聯的部署。  
* **降低風險**：如果叢集處於錯誤狀態，請刪除叢集，然後再試一次。

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **描述**：訂用帳戶識別碼 *yourSubscriptionId* 沒有剩餘的核心可建立叢集 *yourClusterName*。 必要：「resourcesRequired」、可用：「resourcesAvailable」。  
* **降低風險**：釋出訂用帳戶中的資源，或增加可供訂用帳戶的資源，然後重試建立叢集。

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **描述**：訂用帳戶識別碼 *yourSubscriptionId* 沒有配額可供新的 HostedService 建立叢集 *yourClusterName*。  
* **降低風險**：釋出訂用帳戶中的資源，或增加可供訂用帳戶的資源，然後重試建立叢集。

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **描述**：伺服器發生內部錯誤。 請重試要求。  
* **降低風險**：重試要求。

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **描述**：Azure 儲存體位置 *dataRegionName* 不是有效的位置。 請確定區域正確，然後重試要求。
* **降低風險**：請選取支援 HDInsight 的儲存體位置，檢查您的叢集已並存，然後重試作業。

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **描述**：資源節點的 VM 大小無效。 所有資料節點僅支援 'Large VM' 大小。  
* **降低風險**：請為資料節點指定支援的節點大小，然後重試作業。

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **描述**：前端節點的 VM 大小無效。 前端節點僅支援 'ExtraLarge VM' 大小。  
* **降低風險**：請為前端節點指定支援的節點大小，然後重試作業

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **描述**：使用的訂用帳戶識別碼 *yourSubscriptionId* 權限不足，無法對叢集 *yourClusterName* 執行刪除作業。  
* **降低風險**：如果叢集處於錯誤狀態，請卸除叢集，然後再試一次。  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **描述**：外部儲存帳戶 Blob 容器名稱 *yourContainerName* 無效。 請確定名稱以字母開頭，且只包含小寫字母、數字和虛線。  
* **降低風險**：請指定有效的儲存體帳戶 Blob 容器名稱，然後重試作業。

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **描述**：需要有密碼詳細資料，才能設定外部儲存體帳戶 *yourStorageAccountName* 的組態。  
* **降低風險**：請為儲存體帳戶指定有效的密碼，然後重試作業。

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **描述**：版本標頭 *yourVersionHeader* 不是有效格式 yyyy-mm-dd。  
* **降低風險**：請為版本標頭指定有效格式，然後重試要求。

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **描述**：叢集組態無效。 發現多個前端節點組態。  
* **降低風險**：請編輯組態，僅指定一個前端節點。

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **描述**：無法於允許的時間內完成作業，或已達到重試次數上限。 請重試要求。  
* **降低風險**：重試要求。

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **描述**：參數 *yourParameterName* 不可為 Null 或空白。  
* **降低風險**：請為參數指定有效值。

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **描述**：一或多個叢集建立要求輸入無效。 請確定輸入值正確，然後重試要求。  
* **降低風險**：請確定輸入值正確，然後重試要求。

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **描述**：區域 *yourRegionName* 和訂用帳戶識別碼 *yourSubscriptionId* 無法使用區域功能。  
* **降低風險**：請指定支援 HDInsight 叢集的區域。 公開支援的區域如下：東南亞、西歐、北歐、美國東部或美國西部。

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **描述**：儲存體帳戶 *yourStorageAccountName* 位於區域 *currentRegionName* 中。 必須與叢集區域 *yourClusterRegionName*相同。  
* **降低風險**：請指定您的叢集所在相同區域中的儲存體帳戶，或者，如果您的資料已在儲存帳戶中，請在現有儲存體帳戶所在的相同區域中建立新的叢集。 如果您在使用入口網站，UI 會事先通知此問題。

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **描述**：並未使用指定的訂用帳戶識別碼 *yourSubscriptionId*。  
* **降低風險**：請重新啟用訂用帳戶，或取得新的有效訂用帳戶。

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **描述**：找不到訂用帳戶識別碼 *yourSubscriptionId*。  
* **降低風險**：請檢查訂用帳戶識別碼有效，然後重試作業。

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **描述**：無法解析 DNS *yourDnsUrl*。 請確定您提供 Blob 端點的完整 URL。  
* **降低風險**：提供有效的 Blob URL。 URL「必須」完全有效，包括以*http://* 開頭，以 *.com*結尾。

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **描述**：無法驗證資源的 *yourDnsUrl* 的位置。 請確定您提供 Blob 端點的完整 URL。  
* **降低風險**：提供有效的 Blob URL。 URL「必須」完全有效，包括以*http://* 開頭，以 *.com*結尾。

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **描述**：版本 *specifiedVersion* 和訂用帳戶識別碼 *yourSubscriptionId* 無法使用版本功能。  
* **降低風險**：請選擇可用的版本，然後重試作業。

### <a id="VersionNotSupported"></a>VersionNotSupported
* **描述**：不支援版本 *specifiedVersion*。
* **降低風險**：請選擇支援的版本，然後重試作業。

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **描述**：Azure 區域 *specifiedRegion* 中無法使用版本 *specifiedVersion*。  
* **降低風險**：請選擇指定區域中支援的版本，然後重試作業。

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **描述**：叢集組態無效。 在外部帳戶中找不到必要的 WASB 帳戶組態。  
* **降低風險**：請驗證帳戶已存在且於組態中指定正確，然後重試作業。

## <a name="next-steps"></a>後續步驟

* [在以 Linux 為基礎的 HDInsight 上啟用 Apache Hadoop 服務的堆積傾印](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [使用 Apache Ambari Web UI 管理 HDInsight 叢集](../hdinsight-hadoop-manage-ambari.md)
