---
title: 針對 Azure Data Factory 進行疑難排解 |Microsoft Docs
description: 瞭解如何在 Azure Data Factory 中針對外部控制活動進行疑難排解。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: ed466b072a771c3aa288a96fa4a0037c31b875f9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091982"
---
# <a name="troubleshoot-azure-data-factory"></a>疑難排解 Azure Data Factory

本文探討 Azure Data Factory 中外部控制活動的常見疑難排解方法。

## <a name="connector-and-copy-activity"></a>連接器和複製活動

如需連接器問題，例如使用複製活動時遇到錯誤，請參閱針對[Azure Data Factory 連接器進行疑難排解](connector-troubleshoot-guide.md)。

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>錯誤碼:3200

- **訊息**：錯誤403。

- **原因**：`The Databricks access token has expired.`

- **建議**：根據預設，Azure Databricks 存取權杖的有效時間為90天。 建立新的權杖, 並更新連結的服務。


### <a name="error-code--3201"></a>錯誤碼:3201

- **訊息**：`Missing required field: settings.task.notebook_task.notebook_path.`

- **原因**：`Bad authoring: Notebook path not specified correctly.`

- **建議**：在 Databricks 活動中指定筆記本路徑。

<br/>

- **訊息**：`Cluster   ... does not exist.`

- **原因**：`Authoring error: Databricks cluster does not exist or has been deleted.`

- **建議**：確認 Databricks 叢集存在。

<br/>

- **訊息**：`Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **原因**：`Bad authoring.`

- **建議**：針對工作區定址配置或`dbfs:/folder/subfolder/foo.py`儲存在 Databricks 檔案系統中的檔案，指定絕對路徑。

<br/>

- **訊息**：`{0} LinkedService should have domain and accessToken as required properties.`

- **原因**：`Bad authoring.`

- **建議**：確認已[連結的服務定義](compute-linked-services.md#azure-databricks-linked-service)。

<br/>

- **訊息**：`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **原因**：`Bad authoring.`

- **建議**：確認已[連結的服務定義](compute-linked-services.md#azure-databricks-linked-service)。

<br/>

- **訊息**：`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **原因**：`Bad authoring.`

- **建議**：請參閱錯誤訊息。 

<br/>

### <a name="error-code--3202"></a>錯誤碼:3202

- **訊息**：`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **原因**：`Too many Databricks runs in an hour.`

- **建議**：檢查使用此 Databricks 工作區的所有管線, 以取得其作業建立速率。  如果管線啟動太多 Databricks 在匯總中執行, 請將一些管線遷移至新的工作區。

<br/>

- **訊息**：`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **原因**：`Authoring error: No value provided for the parameter.`

- **建議**：檢查管線 JSON, 並確保 baseParameters 筆記本中的所有參數都指定非空白值。

<br/>

- **訊息**：`User: `SimpleUserCoNtext {userId = ...，name =user@company.com，orgId = ...}` is not   authorized to access cluster.`

- **原因**︰產生存取權杖的使用者不允許存取連結服務中指定的 Databricks 叢集。

- **建議**：請確定使用者具有工作區中的必要許可權。


### <a name="error-code--3203"></a>錯誤碼:3203

- **訊息**：`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **原因**︰叢集已終止。 若為互動式叢集, 這可能是競爭條件。

- **建議**：若要避免此錯誤, 最好的方法是使用作業叢集。


### <a name="error-code--3204"></a>錯誤碼:3204

- **訊息**：`Job execution failed.`

- **原因**︰錯誤訊息指出各種問題, 例如未預期的叢集狀態或特定的活動。 最常不會出現任何錯誤訊息。 

- **建議**：N/A


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

下表適用于 U-SQL。


### <a name="error-code--2709"></a>錯誤碼:2709

- **訊息**：`The access token is from the wrong tenant.`

- **原因**︰不正確的 Azure Active Directory (Azure AD) 租使用者。

- **建議**：不正確的 Azure Active Directory (Azure AD) 租使用者。

<br/>

- **訊息**：`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **原因**︰此錯誤是因為 Data Lake Analytics 上的節流所造成。

- **建議**：藉由變更活動的 Data Factory 觸發程式和並行設定, 減少提交的作業數以 Data Lake Analytics。 或增加 Data Lake Analytics 的限制。

<br/>

- **訊息**：`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **原因**︰此錯誤是因為 Data Lake Analytics 上的節流所造成。 

- **建議**：藉由變更活動的 Data Factory 觸發程式和並行設定, 減少提交的作業數以 Data Lake Analytics。 或增加 Data Lake Analytics 的限制。


### <a name="error-code--2705"></a>錯誤碼:2705

- **訊息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **原因**︰服務主體或憑證無法存取儲存體中的檔案。

- **建議**：請確定使用者為 Data Lake Analytics 作業提供的服務主體或憑證, 可以從根資料夾存取 Data Lake Analytics 帳戶和預設 Data Lake Storage 實例。


### <a name="error-code--2711"></a>錯誤碼:2711

- **訊息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **原因**︰服務主體或憑證無法存取儲存體中的檔案。

- **建議**：請確定使用者為 Data Lake Analytics 作業提供的服務主體或憑證, 可以從根資料夾存取 Data Lake Analytics 帳戶和預設 Data Lake Storage 實例。

<br/>

- **訊息**：`Cannot find the 'Azure Data Lake Store' file or folder.`

- **原因**︰U-SQL 檔案的路徑錯誤, 或連結的服務認證沒有存取權。

- **建議**：請確認連結服務中提供的路徑和認證。


### <a name="error-code--2704"></a>錯誤碼:2704

- **訊息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **原因**︰服務主體或憑證無法存取儲存體中的檔案。

- **建議**：請確定使用者為 Data Lake Analytics 作業提供的服務主體或憑證, 可以從根資料夾存取 Data Lake Analytics 帳戶和預設 Data Lake Storage 實例。


### <a name="error-code--2707"></a>錯誤碼:2707

- **訊息**：`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **原因**︰連結服務中的 Data Lake Analytics 帳戶錯誤。

- **建議**：確認已提供正確的帳戶。


### <a name="error-code--2703"></a>錯誤碼:2703

- **訊息**：`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **原因**︰錯誤來自 Data Lake Analytics。 

- **建議**：如範例的錯誤表示工作已提交至 Data Lake Analytics, 而腳本失敗。 在 Data Lake Analytics 中進行調查。 在入口網站中, 移至 [Data Lake Analytics] 帳戶, 然後使用 Data Factory 活動執行識別碼 (而非管線執行識別碼) 尋找作業。 作業會提供有關錯誤的詳細資訊, 並協助您進行疑難排解。 如果解決方式不清楚, 請洽詢 Data Lake Analytics 支援小組, 並提供工作 URL, 其中包括您的帳戶名稱和作業識別碼。



## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>錯誤碼:3602

- **訊息**：`Invalid HttpMethod: {method}.`

- **原因**︰Azure Function 活動不支援活動裝載中指定的 Http 方法。 

- **建議**：支援的 Http 方法包括 PUT、POST、GET、DELETE、OPTIONS、HEAD 和 TRACE。


### <a name="error-code--3603"></a>錯誤碼:3603

- **訊息**：`Response content is not a valid JObject.`

- **原因**︰呼叫的 Azure 函式未傳迴響應中的 JSON 承載。 Data Factory 中的 Azure 函式活動僅支援 JSON 回應內容。 

- **建議**：更新 Azure 函數以傳回有效的 JSON 承載。 例如, C#函式可以`(ActionResult)new<OkObjectResult("{` \"傳回識別碼\":\"123。\" `}");`


### <a name="error-code--3606"></a>錯誤碼:3606

- **訊息**：`Azure function activity missing function key.`

- **原因**︰Azure 函式活動定義不完整。 

- **建議**：請檢查輸入 AzureFunction 活動 JSON 定義具有名為 ' functionKey ' 的屬性。


### <a name="error-code--3607"></a>錯誤碼:3607

- **訊息**：`Azure function activity missing function name.`

- **原因**︰Azure 函式活動定義不完整。 

- **建議**：請檢查輸入 AzureFunction 活動 JSON 定義具有名為 ' functionName ' 的屬性。


### <a name="error-code--3608"></a>錯誤碼:3608

- **訊息**：`Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **原因**︰活動定義中的 Azure 函數詳細資料可能不正確。 

- **建議**：請修正 azure function 詳細資料，然後再試一次。


### <a name="error-code--3609"></a>錯誤碼:3609

- **訊息**：`Azure function activity missing functionAppUrl.` 

- **原因**︰Azure 函式活動定義不完整。 

- **建議**：請檢查輸入 AzureFunction 活動 JSON 定義具有名為 ' functionAppUrl ' 的屬性。


### <a name="error-code--3610"></a>錯誤碼:3610

- **訊息**：`There was an error while calling endpoint.`

- **原因**︰函數 URL 可能不正確。

- **建議**：請確認活動 JSON 中 ' functionAppUrl ' 的值正確，然後再試一次。


### <a name="error-code--3611"></a>錯誤碼:3611

- **訊息**：`Azure function activity missing Method in JSON.` 

- **原因**︰Azure 函式活動定義不完整。

- **建議**：檢查輸入 AzureFunction 活動 JSON 定義是否具有名為 ' method ' 的屬性。


### <a name="error-code--3612"></a>錯誤碼:3612

- **訊息**：`Azure function activity missing LinkedService definition in JSON.`

- **原因**︰Azure 函式活動定義可能不完整。

- **建議**：請檢查輸入 AzureFunction 活動 JSON 定義是否已連結服務的詳細資料。


## <a name="azure-machine-learning"></a>Azure Machine Learning


### <a name="error-code--4101"></a>錯誤碼:4101

- **訊息**：`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **原因**︰錯誤的格式或遺漏屬性的定義。

- **建議**：請檢查活動是否已定義正確的資料。


### <a name="error-code--4110"></a>錯誤碼:4110

- **訊息**：AzureMLExecutePipeline 活動缺少 JSON 中的 LinkedService 定義。

- **原因**︰AzureMLExecutePipeline 活動定義不完整。

- **建議**：請檢查輸入 AzureMLExecutePipeline 活動 JSON 定義是否有已連結的服務詳細資料。


### <a name="error-code--4111"></a>錯誤碼:4111

- **訊息**：`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **原因**︰活動定義不正確。

- **建議**：請檢查輸入 AzureMLExecutePipeline 活動 JSON 定義是否有正確的連結服務詳細資料。


### <a name="error-code--4112"></a>錯誤碼:4112

- **訊息**：`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **原因**︰錯誤的格式或遺漏屬性的定義。

- **建議**：請檢查連結的服務定義是否有正確的資料。


### <a name="error-code--4121"></a>錯誤碼:4121

- **訊息**：`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**︰用來存取 Azure ML 服務的認證已過期。

- **建議**：請確認認證有效，然後再試一次


### <a name="error-code--4122"></a>錯誤碼:4122

- **訊息**：`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**︰AzureML 服務連結服務中提供的認證無效，或沒有該作業的許可權。

- **建議**：請確認已連結服務中的認證有效，而且具有存取 AzureML 服務的許可權。


### <a name="error-code--4123"></a>錯誤碼:4123

- **訊息**：`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**：`Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **建議**：請檢查活動屬性的值，以符合連結服務中所指定已發佈 Azure ML 管線的預期裝載。


### <a name="error-code--4124"></a>錯誤碼:4124

- **訊息**：`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**︰發行的 Azure ML 管線端點不存在。

- **建議**：請確認 Azure ML 服務中已有已連結服務中所指定的已發佈 Azure ML 管線端點。


### <a name="error-code--4125"></a>錯誤碼:4125

- **訊息**：`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**︰Azure ML 服務上的伺服器錯誤。

- **建議**：請稍後重試。 如果問題仍然存在，請聯絡 Azure ML 服務小組以取得協助。


### <a name="error-code--4126"></a>錯誤碼:4126

- **訊息**：`AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **原因**︰AzureML 管線執行失敗。

- **建議**：請查看 AzureMLService 以取得更多錯誤記錄並修正 ML 管線


## <a name="custom"></a>自訂

下表適用于 Azure Batch。


### <a name="error-code--2500"></a>錯誤碼:2500

- **訊息**：`Hit unexpected exception and execution failed.`

- **原因**：`Can't launch command, or the program returned an error code.`

- **建議**：請確認可執行檔存在。 如果程式已啟動, 請確定*stdout*和*stderr*已上傳至儲存體帳戶。 在您的程式碼中發出膨脹佈滿記錄以進行調試是個很好的作法。


### <a name="error-code--2501"></a>錯誤碼:2501

- **訊息**：`Cannot access user batch account; please check batch account settings.`

- **原因**︰不正確的批次存取金鑰或集區名稱。

- **建議**：請確認連結服務中的集區名稱和批次存取金鑰。


### <a name="error-code--2502"></a>錯誤碼:2502

- **訊息**：`Cannot access user storage account; please check storage account settings.`

- **原因**︰儲存體帳戶名稱或存取金鑰不正確。

- **建議**：驗證連結服務中的儲存體帳戶名稱和存取金鑰。


### <a name="error-code--2504"></a>錯誤碼:2504

- **訊息**：`Operation returned an invalid status code 'BadRequest'.` 

- **原因**︰自訂活動的 folderPath 中有太多檔案。 ResourceFiles 的總大小不能超過32768個字元。

- **建議**：移除不必要的檔案。 或將其壓縮, 並新增解壓縮命令以將其解壓縮。 例如, 使用`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>錯誤碼:2505

- **訊息**：`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **原因**︰自訂活動僅支援使用存取金鑰的儲存體帳戶。

- **建議**：請參閱錯誤描述。


### <a name="error-code--2507"></a>錯誤碼:2507

- **訊息**：`The folder path does not exist or is empty: ....`

- **原因**︰位於指定路徑的儲存體帳戶中沒有任何檔案。

- **建議**：資料夾路徑必須包含您想要執行的可執行檔。


### <a name="error-code--2508"></a>錯誤碼:2508

- **訊息**：`There are duplicate files in the resource folder.`

- **原因**︰相同名稱的多個檔案位於 folderPath 的不同子資料夾中。

- **建議**：自訂活動會在 folderPath 下壓平合併資料夾結構。  如果您需要保留資料夾結構, 請壓縮檔案, 並使用解壓縮命令在 Azure Batch 中將檔案解壓縮。 例如, 使用`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>錯誤碼:2509

- **訊息**：`Batch   url ... is invalid; it must be in Uri format.` 

- **原因**︰批次 Url 必須類似于`https://mybatchaccount.eastus.batch.azure.com`

- **建議**：請參閱錯誤描述。


### <a name="error-code--2510"></a>錯誤碼:2510

- **訊息**：`An   error occurred while sending the request.`

- **原因**︰批次 URL 無效。 

- **建議**：確認批次 URL。


## <a name="hdinsight"></a>HDInsight

下表適用于 Spark、Hive、MapReduce、Pig 和 Hadoop 串流。


### <a name="error-code--2300"></a>錯誤碼:2300

- **訊息**：`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **原因**︰提供的叢集 URI 無效。 

- **建議**：請確定叢集尚未刪除, 而且提供的 URI 正確。 當您在瀏覽器中開啟 URI 時, 您應該會看到 Ambari UI。 如果叢集位於虛擬網路中, 則 URI 應該是私用 URI。 若要開啟它, 請使用屬於相同虛擬網路的 VM。 如需詳細資訊, 請參閱[直接連接到 Apache Hadoop 服務](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)。

<br/>

- **訊息**：`Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **原因**︰提交的作業超時。 

- **建議**：此問題可能是一般 HDInsight 連線能力或網路連線能力。 請先確認可以從任何瀏覽器取得 HDInsight Ambari UI。 確認您的認證仍然有效。 如果您使用的是自我裝載整合執行時間 (IR), 請務必從已安裝自我裝載 IR 的 VM 或電腦執行此動作。 然後再次嘗試從 Data Factory 提交工作。 如果仍然失敗, 請洽詢 Data Factory 小組以取得支援。


- **訊息**：`Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **原因**︰HDInsight 的認證不正確或已過期。

- **建議**：更正認證並重新部署已連結的服務。 首先, 請在任何瀏覽器上開啟叢集 URI 並嘗試登入, 以確定認證可在 HDInsight 上工作。 如果認證無法使用, 您可以從 Azure 入口網站重設它們。

<br/>

- **訊息**：`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **原因**︰這是來自 HDInsight 的錯誤。

- **建議**：此錯誤來自 HDInsight 叢集。 如需詳細資訊, 請參閱[AMBARI UI 502 錯誤](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)、[連接到 spark Thrift 伺服器的502錯誤](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)、 [502 連線到 spark Thrift 伺服器的錯誤](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)和[疑難排解應用程式閘道中的閘道錯誤](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)。

<br/>

- **訊息**：`Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **原因**︰同時將太多作業提交給 HDInsight。

- **建議**：請考慮限制提交至 HDInsight 的並行作業數目。 如果作業是由相同的活動所提交, 請參閱 Data Factory 活動並行。 變更觸發程式, 讓並行管線執行會隨著時間分散。 如錯誤所建議, 請`templeton.parallellism.job.submit`參閱 HDInsight 檔以進行調整。


### <a name="error-code--2303"></a>錯誤碼:2303

- **訊息**：`Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **原因**︰作業已提交至 HDInsight, 但在 HDInsight 上失敗。

- **建議**：已成功將作業提交至 HDInsight。 在叢集上失敗。 在 HDInsight Ambari UI 中開啟作業和記錄, 或從儲存體開啟檔案, 如錯誤訊息所建議。 檔案會顯示錯誤詳細資料。


### <a name="error-code--2310"></a>錯誤碼:2310

- **訊息**：`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **原因**︰提供的叢集 URI 無效。 

- **建議**：請確定叢集尚未刪除, 而且提供的 URI 正確。 當您在瀏覽器中開啟 URI 時, 您應該會看到 Ambari UI。 如果叢集位於虛擬網路中, 則 URI 應該是私用 URI。 若要開啟它, 請使用屬於相同虛擬網路的 VM。 如需詳細資訊, 請參閱[直接連接到 Apache Hadoop 服務](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)。

<br/>

- **訊息**：`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **原因**︰這是來自 HDInsight 的錯誤。

- **建議**：此錯誤來自 HDInsight 叢集。 如需詳細資訊, 請參閱[AMBARI UI 502 錯誤](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)、[連接到 spark Thrift 伺服器的502錯誤](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)、 [502 連線到 spark Thrift 伺服器的錯誤](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)和[疑難排解應用程式閘道中的閘道錯誤](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)。

<br/>

- **訊息**：`java.lang.NullPointerException`

- **原因**︰當作業提交至 Spark 叢集時, 就會發生此錯誤。 

- **建議**：此例外狀況來自 HDInsight。 它會隱藏實際的問題。 請洽詢 HDInsight 小組以取得支援。 提供叢集名稱和活動運行時間範圍。


### <a name="error-code--2347"></a>錯誤碼:2347

- **訊息**：`Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **原因**︰作業已提交至 HDInsight, 但在 HDInsight 上失敗。

- **建議**：已成功將作業提交至 HDInsight。 在叢集上失敗。 在 HDInsight Ambari UI 中開啟作業和記錄, 或從儲存體開啟檔案, 如錯誤訊息所建議。 檔案會顯示錯誤詳細資料。


### <a name="error-code--2328"></a>錯誤碼:2328

- **訊息**：`Internal server error occurred while processing the request. Please retry the request or contact support. `

- **原因**︰此錯誤會隨選在 HDInsight 中發生。

- **建議**：當 HDInsight 布建失敗時, 此錯誤來自 HDInsight 服務。 請洽詢 HDInsight 小組, 並提供隨選叢集名稱。



## <a name="web-activity"></a>網路活動

### <a name="error-code--2108"></a>錯誤碼:2108

- **訊息**：`Invalid HttpMethod: '...'.`

- **原因**︰Web 活動不支援活動裝載中指定的 HTTP 方法。

- **建議**：支援的 HTTP 方法包括 PUT、POST、GET 和 DELETE。

<br/>

- **訊息**：`Invalid Server Error 500.`

- **原因**︰端點發生內部錯誤。

- **建議**：使用 Fiddler 或 Postman 來檢查 URL 上的功能。

<br/>

- **訊息**：`Unauthorized 401.`

- **原因**︰要求上缺少有效的驗證。

- **建議**：Token 可能已過期。 請提供有效的驗證方法。 使用 Fiddler 或 Postman 來檢查 URL 上的功能。

<br/>

- **訊息**：`Forbidden 403.`

- **原因**︰缺少必要的許可權。

- **建議**：檢查存取資源的使用者權限。 使用 Fiddler 或 Postman 來檢查 URL 上的功能。

<br/>

- **訊息**：`Bad Request 400.`

- **原因**︰不正確 HTTP 要求。

- **建議**： 檢查要求的 URL、動詞和主體。 使用 Fiddler 或 Postman 來驗證要求。

<br/>

- **訊息**：`Not found 404.` 

- **原因**︰找不到資源。   

- **建議**：使用 Fiddler 或 Postman 來驗證要求。

<br/>

- **訊息**：`Service unavailable.`

- **原因**︰服務無法使用。

- **建議**：使用 Fiddler 或 Postman 來驗證要求。

<br/>

- **訊息**：`Unsupported Media Type.`

- **原因**︰內容類型與 Web 活動主體不相符。

- **建議**：指定符合承載格式的內容類型。 使用 Fiddler 或 Postman 來驗證要求。

<br/>

- **訊息**：`The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **原因**︰資源無法使用。 

- **建議**：使用 Fiddler 或 Postman 來檢查端點。

<br/>

- **訊息**：`The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **原因**︰在要求中指定了不正確的 Web 活動方法。

- **建議**：使用 Fiddler 或 Postman 來檢查端點。

<br/>

- **訊息**：`invalid_payload`

- **原因**︰Web 活動主體不正確。

- **建議**：使用 Fiddler 或 Postman 來檢查端點。


### <a name="error-code--2208"></a>錯誤碼:2208

- **訊息**：`Invoking Web Activity failed with HttpStatusCode - {0}.`

- **原因**︰目標服務傳回失敗狀態。

- **建議**：使用 Fiddler/Postman 來驗證要求。


### <a name="error-code--2308"></a>錯誤碼:2308

- **訊息**：`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**︰發生此錯誤的原因有很多，例如網路連線、DNS 失敗、伺服器憑證驗證或超時。

- **建議**：使用 Fiddler/Postman 來驗證要求。


若要使用 Fiddler 來建立受監視 web 應用程式的 HTTP 會話:

1. 下載、安裝及開啟[Fiddler](https://www.telerik.com/download/fiddler)。

1. 如果您的 web 應用程式使用 HTTPs, 請前往 [**工具** > ] [**Fiddler 選項** > ] [**HTTPs**]。 選取 [ **CAPTURE HTTPs** Connect AND**解密 HTTPs 流量**]。 
   
   ![Fiddler 選項](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. 如果您的應用程式使用 SSL 憑證, 請將 Fiddler 憑證新增至您的裝置。 前往 [**工具** > ] [**Fiddler 選項** > ] [**HTTPS**  > **動作** > ] [**將根憑證匯出到桌面**]。

1. 前往 [檔案] [Capture  > ] [**流量**] 來關閉捕捉。 或按**F12**。

1. 清除瀏覽器的快取, 以便移除所有快取的專案, 而且必須重新下載。

1. 建立要求: 

   a. 選取 [**編輯器**] 索引標籤。

   b. 設定 HTTP 方法和 URL。

   c. 如有需要, 請新增標頭和要求主體。

   d. 選取 [執行]。

9. 再次開啟流量捕捉, 並在您的頁面上完成有問題的交易。

10. 移至  > [檔案] [**儲存** > **所有會話**]。

如需詳細資訊, 請參閱[開始使用 Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)。

## <a name="next-steps"></a>後續步驟

如需更多疑難排解協助, 請嘗試下列資源:

*  [Data Factory 的 blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)



