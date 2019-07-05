---
title: 疑難排解 Azure Data Factory |Microsoft Docs
description: 疑難排解 Azure Data Factory。 所有外部，控制活動的一般文件。
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshoot
ms.subservice: troubleshoot
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 8d6ab565098e1ea40ede5c650f05e670a1edc7f6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452679"
---
# <a name="troubleshooting-azure-data-factory"></a>疑難排解 Azure Data Factory
本文章列出常見的疑難排解問題。

- [Azure Databricks notebook、 jar (python）](#azure-databricks)
- [Azure Data Lake Analytics (U-SQL)](#azure-data-lake-analytics-u-sql)
- [Azure Functions](#azure-functions)
- [自訂 （Azure 批次）](#custom-azure-batch)
- [HDInsight （Spark、 Hive、 MapReduce、 Pig、 Hadoop 資料流）](#hdinsight-spark-hive-mapreduce-pig-hadoop-streaming)
- [Web 活動](#web-activity)



## <a name="azure-databricks"></a>Azure Databricks
| 錯誤碼 | 錯誤訊息                                          | 問題說明                             | 可能的修正程式 / 建議的動作                            |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | 錯誤 403                                                    | Databricks 存取權杖已過期。                         | 根據預設，Databricks 存取權杖是有效的 90 天。  請建立新的權杖，並更新連結的服務。 |
| 3201           | 遺漏必要的欄位： settings.task.notebook_task.notebook_path | 撰寫不正確：未正確指定 notebook 路徑。 | 請指定 Databricks 活動 notebook 路徑。 |
| 3201           | 叢集...不存在                                 | 撰寫錯誤：Databricks 叢集不存在，或已刪除。 | 請確認在 Databricks 叢集存在。 |
| 3201           | 無效的 python 檔案的 URI:...請瀏覽 Databricks 使用者指南以取得支援的 URI 配置。 | 撰寫不正確                                                | 指定絕對路徑的工作區的定址配置或 「 dbfs:/folder/subfolder/foo.py"dbfs 的方法儲存的檔案。 |
| 3201           | {0}   連結服務應該有網域和 accessToken 作為必要的屬性 | 撰寫不正確                                                | 請確認[連結服務定義](compute-linked-services.md#azure-databricks-linked-service)。 |
| 3201           | {0}   連結服務應該指定現有的叢集識別碼或建立新的叢集資訊 | 撰寫不正確                                                | 請確認[連結服務定義](compute-linked-services.md#azure-databricks-linked-service)。 |
| 3201           | 不支援的節點型別 Standard_D16S_v3。 支援的節點類型： Standard_DS3_v2、 Standard_DS4_v2、 Standard_DS5_v2、 Standard_D8s_v3、 Standard_D16s_v3、 Standard_D32s_v3、 Standard_D64s_v3、 Standard_D3_v2、 Standard_D8_v3、 Standard_D16_v3、 Standard_D32_v3、 Standard_D64_v3、 Standard_D12_v2、 Standard_D13_v2，Standard_D14_v2、 Standard_D15_v2、 Standard_DS12_v2、 Standard_DS13_v2、 Standard_DS14_v2、 Standard_DS15_v2、 Standard_E8s_v3、 Standard_E16s_v3、 Standard_E32s_v3、 Standard_E64s_v3、 Standard_L4s、 Standard_L8s、 Standard_L16s、 Standard_L32s，Standard_F4s、 Standard_F8s、 Standard_F16s、 Standard_H16、 Standard_F4s_v2、 Standard_F8s_v2、 Standard_F16s_v2、 Standard_F32s_v2、 Standard_F64s_v2、 Standard_F72s_v2、 Standard_NC12、 Standard_NC24、 Standard_NC6s_v3、 Standard_NC12s_v3、 Standard_NC24s_v3、 Standard_L8s_v2、 Standard_L16s_v2、 Standard_L32s_v2、 Standard_L64s_v2、 Standard_L80s_v2 | 撰寫不正確                                                | 請參閱錯誤訊息                                          |
| 3201           | 無效的 notebook_path:...目前支援僅絕對路徑。 路徑必須以開頭 '/'。 | 撰寫不正確                                                | 請參閱錯誤訊息                                          |
| 3202           | 已在過去的 3600 秒，超過速率限制中建立的 1000年項作業： 1000 作業建立每 3600 秒。 | 在一小時內的 Databricks 執行太多。                         | 請檢查所有作業建立速率使用 Databricks 工作區的管線。  如果管線啟動太多的 Databricks 執行彙總，將部分管線移轉至新的工作區。 |
| 3202           | 無法剖析要求物件：預期的 'key' 和 'value' 為 JSON 的對應欄位 base_parameters，' 索引鍵:"..."' 改為。 | 撰寫錯誤：未提供參數值         | 檢查管線 json，並確定 Notebook baseParameters 中的所有參數都有指定的非空白值。 |
| 3202           | 使用者：SimpleUserContext {userId =...，名稱 =user@company.com，orgId =...} 未獲授權存取叢集 | 產生存取權杖的使用者不得存取的連結服務中指定的 Databricks 叢集。 | 請確定使用者具有必要的權限在工作區中。   |
| 3203           | 叢集處於終止狀態，不適用於接收作業。 請修正叢集中，或稍後再重試。 | 已終止叢集。    針對互動式叢集，這可能是競爭條件。 | 若要避免這個問題的最佳方式是使用作業叢集。             |
| 3204           | 作業執行失敗。 可以有任意數目的錯誤訊息，從活動特定訊息的未預期的叢集狀態。  最常見根本是沒有任何錯誤訊息。 | N/A                                                          | N/A                                                          |



## <a name="azure-data-lake-analytics-u-sql"></a>Azure Data Lake Analytics (U-SQL)

| 錯誤碼         | 錯誤訊息                                                | 問題說明                                          | 可能的修正程式 / 建議的動作                             |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | 存取權杖是從錯誤的租用戶                    | 不正確的 AAD 租用戶                                         | 用來存取 ADLA 服務主體屬於另一個 AAD 租用戶。 請在 ADLA 帳戶相同的租用戶中建立新的服務主體。 |
| 2711,   2705,   2704 | 禁止。 ACL 驗證失敗。 資源不存在，或使用者未獲授權執行要求的作業<br/><br/>使用者不能存取 datalake 存放區  <br/><br/>使用者未獲授權至 data lake analytics | 服務主體或提供的憑證不需要存取儲存體中的檔案 | 請確定服務主體，或它們的 ADLA 作業提供的憑證，有權存取 ADLA 帳戶和預設 ADLS 儲存體，根資料夾中。 |
| 2711                 | 找不到 ' Azure Data Lake Store' 檔案或資料夾       | USQL 檔案的路徑是錯誤或不具有存取認證的連結的服務 | 請確認路徑，並提供連結的服務中的認證 |
| 2707                 | 無法解析 AzureDataLakeAnalytics 的帳戶。 請檢查 'AccountName' 和 'DataLakeAnalyticsUri'。 | 連結服務中的 ADLA 帳戶有誤                  | 請確認提供的正確的帳戶             |
| 2703                 | 錯誤 Id:E_CQO_SYSTEM_INTERNAL_ERROR 或傳回任何錯誤，開頭 」 錯誤識別碼:" | 錯誤來自 ADLA                                    | 任何錯誤，看起來像是範例表示作業已提交給 ADLA 和指令碼發生失敗。 必須在 ADLA 上完成的調查。 如果您開啟入口網站，並瀏覽至 ADLA 帳戶，請使用 ADF 活動回合 Id （不管線執行識別碼） 尋找作業。 那里的作業會有錯誤的詳細資訊，並且有助於進行疑難排解。 如果解析不清楚，請連絡 ADLA 支援小組並提供作業 URL，其中包含您的帳戶名稱和作業識別碼。 |
| 2709                 | 此時，我們無法接受您的作業。 為您的帳戶已排入佇列工作數目上限為 200。 | 在 ADLA 上節流                                           | 提交的作業數目減少到 ADLA，藉由變更 ADF 觸發程序和並行處理設定，於活動，或增加在 ADLA 上的限制 |
| 2709                 | 這項作業被拒，因為它需要 24 個 au 數，且此帳戶具有系統管理員定義的原則，可防止工作使用超過 5 個 au 數。 | 在 ADLA 上節流                                           | 提交的作業數目減少到 ADLA，藉由變更 ADF 觸發程序和並行處理設定，於活動，或增加在 ADLA 上的限制 |



## <a name="azure-functions"></a>Azure Functions

| 錯誤碼 | 錯誤訊息                           | 描述                                                  | 可能的修正程式 / 建議的動作                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | 回應內容不是有效的 JObject | 這表示呼叫 Azure 函式未在回應中傳回 JSON 承載。 ADF Azure 函式活動僅支援 JSON 回應內容。 | 更新 Azure 函式來傳回有效的 JSON 承載，例如C#函式會傳回 (ActionResult) 新 < OkObjectResult ("{`\"Id\":\"123\"`}"); |
| 3600         | 無效的 HttpMethod: '..'。               | 這表示 Azure 函式活動不支援活動裝載中指定的 Http 方法。 | 支援的 Http 方法包括：  <br/>PUT、 POST、 取得、 DELETE、 選項、 HEAD、 追蹤 |



## <a name="custom-azure-batch"></a>自訂 （Azure 批次）
| 錯誤碼 | 錯誤訊息                                                | 描述                                                  | 可能的修正程式 / 建議的動作                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | 叫用非預期的例外狀況和失敗的執行。             | 無法啟動命令或傳回錯誤程式碼的程式。 | 請檢查可執行檔存在。 如果程式啟動時，請檢查 stdout.txt 和 stderr.txt 上傳至儲存體帳戶。 您最好將佈滿記錄進行偵錯的程式碼中發出。 |
| 2501         | 無法存取使用者的 batch 帳戶，請檢查批次帳戶設定。 | 提供不正確的批次會存取金鑰或集區的名稱。            | 需要確認集區名稱和批次中的連結服務的存取金鑰。 |
| 2502         | 可以不存取使用者儲存體帳戶，請檢查儲存體帳戶設定。 | 不正確的儲存體帳戶名稱或存取金鑰所提供。       | 需要確認儲存體帳戶名稱和存取金鑰連結服務中。 |
| 2504         | 作業傳回無效的狀態碼 '不正確的要求'     | 在 folderPath 中的太多檔案如果自訂的活動。  （resourceFiles 的大小總計不能超過 32768 個字元）。 | 移除不必要的檔案，或加以壓縮，並新增 unzip 命令，以擷取，例如： powershell.exe-nologo noprofile-命令"& {Add-type-A 'System.IO.Compression.FileSystem';  [IO.Compression.ZipFile]::ExtractToDirectory $zipFile ($folder）;}" ; $folder\yourProgram.exe |
| 2505         | 無法建立共用存取簽章，除非使用帳戶金鑰的憑證。 | 自訂活動僅支援使用存取金鑰的儲存體帳戶。 | 描述，請參閱                                            |
| 2507         | 資料夾路徑不存在，或為空白:...            | 位於指定路徑的儲存體帳戶中沒有任何檔案。       | FolderPath 必須包含您想要執行的可執行檔。 |
| 2508         | 有重複的檔案處於資源資料夾。               | 有相同名稱的 folderPath 的不同子資料夾中的多個檔案。 | 自訂活動壓平合併資料夾結構 folderPath。  如果資料夾結構必須保留，zip 檔案，並將它們解壓縮 Azure Batch 上使用 unzip 命令，例如： powershell.exe-nologo noprofile-命令"& {Add-type-A 'System.IO.Compression.FileSystem';  [IO.Compression.ZipFile]::ExtractToDirectory $zipFile ($folder）;}" ;  $folder\yourProgram.exe |
| 2509         | 批次 url...是無效的它必須是以 Uri 格式。         | 批次 Url 必須是類似於 https://mybatchaccount.eastus.batch.azure.com | 描述，請參閱                                            |
| 2510         | 傳送要求時發生錯誤。               | 批次 URL 無效                                         | 請確認批次的 URL。                                            |

## <a name="hdinsight-spark-hive-mapreduce-pig-hadoop-streaming"></a>HDInsight （Spark、 Hive、 MapReduce、 Pig、 Hadoop 資料流）

| 錯誤碼 | 錯誤訊息                                                | 描述                                                  | 可能的修正程式 / 建議的動作                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Hadoop 工作提交失敗。 Error:The remote name could not be resolved」的問題。 <br/><br/>找不到叢集。 | 提供叢集 URI 無效                              | 請確定尚未刪除叢集，並提供的 URI 正確。 您可以在任何瀏覽器中開啟的 URI，您應該會看到 Ambari UI。 如果叢集不在 vNet 中，然後 URI 應該是私用的 URI，並應該嘗試從 VM 屬於相同的 vNet 中開啟它。 如需詳細資訊[在 HDInsight 中的虛擬網路](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)。 |
| 2300         | Hadoop 工作提交失敗。 作業:...」，叢集:.../。 Error:工作已取消。 | 在提交作業的逾時。                         | 這可能是一般 HDInsight 連線問題或網路連線問題。 先確認 HDInsight Ambari UI，都可透過任何瀏覽器，而且您的認證仍然有效。 請務必執行這項操作的 VM/電腦使用自我裝載 ir。 如果要安裝自我裝載的 IR 的位置 然後再次嘗試重新提交 ADF 的作業。 如果仍然失敗，請連絡 ADF 團隊尋求支援。 |
| 2300         | 未經授權： Ambari 使用者名稱或密碼不正確  <br/><br/>未經授權： 使用者系統管理員在 Ambari 中，已遭鎖定   <br/><br/>403-禁止：存取遭拒 | 針對 HDInsight 提供的認證不正確，或已過期 | 請更正它們，然後重新部署連結的服務。 請確定認證作用在 HDInsight 上第一次開啟叢集 URI 在任何瀏覽器，並嘗試將登入。 如果它們沒有作用，您可以從 Azure 入口網站重設它們。 |
| 2300,   2310 | 502-網頁伺服器作為閘道或 proxy 伺服器時收到無效的回應       <br/>錯誤的閘道 | 錯誤來自 HDInsight                               | 此錯誤來自 HDInsight 叢集。 請參閱[HDInsight 疑難排解員](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)與常見的錯誤。    <br/>對於 Spark 叢集它也導致因[這](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)。 <br/><br/>[其他連結](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502) |
| 2300         | Hadoop 工作提交失敗。 作業:...」，叢集:...錯誤: {\"錯誤\":\"無法提交作業要求提供服務，如 templeton 服務正在提交作業要求過多。 請等候一段時間再重試此作業。 請參閱設定並行要求設定 templeton.parallellism.job.submit。 \  <br/><br/>Hadoop 工作提交失敗。 作業：161da5d4-6fa8-4ef4-a240-6b6428c5ae2f，叢集： https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/ 。   錯誤: {\"錯誤\":\"java.io.IOException: org.apache.hadoop.yarn.exceptions.YarnException:無法提交 YARN 的 application_1561147195099_3730: org.apache.hadoop.security.AccessControlException:佇列 root.joblauncher 已經有 500 的應用程式，無法接受提交的應用程式： application_1561147195099_3730\ | 在相同的時間太多工作提交給 HDInsight | 鐏芶淢提交至 HDI 的並行作業數目。 請如果由相同的活動所提交，參閱 ADF 活動的並行存取。 變更觸發程序，因此並行管線執行散佈經過一段時間。 也請參閱 HDInsight 文件以調整 「 templeton.parallellism.job.submit 」，正如其錯誤。 |
| 2303,   2347 | Hadoop 工作失敗，結束代碼為 '5'。 請參閱 'wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' 以取得詳細資料。  <br/><br/>Hive 執行失敗，錯誤碼 'UserErrorHiveOdbcCommandExecutionFailure'。   請參閱 'wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' 以取得詳細資料 | 作業提交至 HDInsight，並在 HDInsight 上失敗 | 作業已提交至 HDInsight 成功。 它無法在叢集上。 請開啟的工作對 HDInsight 的 Ambari UI，並開啟記錄檔，或從儲存體的檔案開啟為錯誤訊息指出。錯誤的詳細資料會在該檔案。 |
| 2328         | 處理要求時發生內部伺服器錯誤。 請重試要求，或連絡客戶支援 | 隨選 HDInsight 上發生的狀況。                              | HDInsight 佈建失敗時，此錯誤來自 HDInsight 服務。 請連絡 HDInsight 小組，並提供他們在隨選叢集名稱。 |
| 2310         | java.lang.NullPointerException                               | 提交至 Spark 叢集作業時發生錯誤      | 這個例外狀況來自 HDInsight，並會隱藏實際的問題。   請連絡 HDInsight 小組以取得支援，並提供叢集名稱和活動執行的時間範圍。 |
|              | 所有其他錯誤                                             |                                                              | 請參閱[HDInsight 疑難排解員](../hdinsight/hdinsight-troubleshoot-guide.md)和[HDInsight 常見問題集](https://hdinsight.github.io/) |



## <a name="web-activity"></a>網路活動

| 錯誤碼 | 錯誤訊息                                                | 描述                                                  | 可能的修正程式 / 建議的動作                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | 無效的 HttpMethod: '..'。                                    | 這表示 Web 活動不支援活動裝載中指定的 Http 方法。 | 支援的 Http 方法包括： <br/>PUT、 POST、 GET、 DELETE |
| 2108         | 無效的伺服器錯誤 500                                     | 在端點上的內部錯誤                               | 請檢查 URL （使用 Fiddler/Postman) 上的功能：[如何使用 Fiddler 建立 HTTP 工作階段](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 未授權的 401                                             | 遺漏有效的驗證要求                      | 提供有效的驗證方法 （語彙基元可能已過期）。   <br/><br/>請檢查 URL （使用 Fiddler/Postman) 上的功能：[如何使用 Fiddler 建立 HTTP 工作階段](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 禁止的 403                                                | 遺漏必要的權限                                 | 檢查使用者權限存取的資源。   <br/><br/>請檢查 URL （使用 Fiddler/Postman) 上的功能：[如何使用 Fiddler 建立 HTTP 工作階段](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 400 不正確的要求                                              | 無效的 Http 要求                                         | 請檢查 URL、 動詞和要求的主體。   <br/><br/>您可以使用 Fiddler/Postman 來驗證要求：[如何使用 Fiddler 建立 HTTP 工作階段](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 找不到 404                                                | 找不到資源                                       | 您可以使用 Fiddler/Postman 來驗證要求：[如何使用 Fiddler 建立 HTTP 工作階段](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 服務無法使用                                          | 服務停用                                       | 您可以使用 Fiddler/Postman 來驗證要求：[如何使用 Fiddler 建立 HTTP 工作階段](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 不支援的媒體類型                                       | 使用 Web 活動內容的不相符的內容類型           | 指定正確的內容-符合的型別裝載格式使用 Fiddler/Postman 驗證要求：[如何使用 Fiddler 建立 HTTP 工作階段](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 您要尋找的資源已移除，名稱已變更，或暫時無法使用。 | 無法使用的資源，                                | 您可以使用 Fiddler/Postman 來檢查端點：[如何使用 Fiddler 建立 HTTP 工作階段](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 無法顯示您要尋找的網頁，因為正在使用無效的方法 （HTTP 指令動詞）。 | 要求中所指定不正確的 Web 活動方法   | 您可以使用 Fiddler/Postman 來檢查端點：[如何使用 Fiddler 建立 HTTP 工作階段](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | invalid_payload                                              | Web 活動的主體不正確                       | 您可以使用 Fiddler/Postman 來檢查端點：[如何使用 Fiddler 建立 HTTP 工作階段](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |

#### <a name="how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application"></a>如何使用 Fiddler 建立 HTTP 工作階段的受監視的 web 應用程式

1. [下載並安裝 Fiddler](https://www.telerik.com/download/fiddler)

2. 如果您的 web 應用程式使用 HTTPS:

   1. 開啟 Fiddler

   2. 移至**工具 > Fiddler 選項**選取如以下螢幕擷取畫面。 

      ![fiddler-options](media/data-factory-troubleshoot-guide/fiddler-options.png)

3. 如果您的應用程式使用 SSL 憑證，則您也必須新增 Fiddler 憑證到您的裝置。

4. Fiddler 將憑證新增至您的裝置，請移至**工具** > **Fiddler 選項** > **HTTPS**  >  **動作** > **匯出到桌面的根憑證**取得 Fiddler 憑證。

5. 關閉擷取，以便可以清除瀏覽器的快取，才能開始新的追蹤。

6. 1. 移至**檔案** > **擷取流量**或按**F12**。
   2. 如此會移除所有的快取項目，且必須重新下載，請清除您的瀏覽器快取。

7. 建立要求： 

8. 1. 按一下 編輯器索引標籤
   2. 設定 Http 方法和 URL
   3. 新增標頭和要求本文，如有必要
   4. 按一下 [執行]

9. 開始再次擷取流量，並完成您的頁面上有問題的交易。

10. 完成後，請移至**檔案** > **儲存** > **所有工作階段**。

Fiddler 的更多有關[這裡](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)

## <a name="next-steps"></a>後續步驟

如需更多尋找問題解決方案的協助，以下是您可以嘗試的其他資源。

*  [部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter](https://twitter.com/hashtag/DataFactory)



