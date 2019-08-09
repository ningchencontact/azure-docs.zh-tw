---
title: 針對 Azure Data Factory 進行疑難排解 |Microsoft Docs
description: 瞭解如何在 Azure Data Factory 中針對外部控制活動進行疑難排解。
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: c76242c176ba4f4c9ffc0d6934f6b645743d77f4
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234572"
---
# <a name="troubleshoot-azure-data-factory"></a>疑難排解 Azure Data Factory

本文探討 Azure Data Factory 中外部控制活動的常見疑難排解方法。

## <a name="azure-databricks"></a>Azure Databricks

| 錯誤碼 | 錯誤訊息                                          | 描述                             | 建議                             |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | 錯誤403。                                                    | Databricks 存取權杖已過期。                         | 根據預設, Databricks 存取權杖的有效時間為90天。  建立新的權杖, 並更新連結的服務。 |
| 3201           | 遺漏必要欄位: settings. notebook_task. notebook_path | 錯誤的撰寫:未正確指定筆記本路徑。 | 在 Databricks 活動中指定筆記本路徑。 |
| 3201           | 叢集   ... 不存在。                                 | 撰寫錯誤:Databricks 叢集不存在或已被刪除。 | 確認 Databricks 叢集存在。 |
| 3201           | Python 檔案 URI 無效 ...請造訪 Databricks 使用者指南以取得支援的 URI 配置。 | 錯誤的撰寫。                                                | 針對工作區定址配置或`dbfs:/folder/subfolder/foo.py`儲存在 Databricks 檔案系統中的檔案, 指定絕對路徑。 |
| 3201           | {0}LinkedService 應具有 domain 和 accessToken 做為必要屬性。 | 錯誤的撰寫。                                                | 確認已[連結的服務定義](compute-linked-services.md#azure-databricks-linked-service)。 |
| 3201           | {0}LinkedService 應該指定現有的叢集識別碼或新的叢集資訊來建立。 | 錯誤的撰寫。                                                | 確認已[連結的服務定義](compute-linked-services.md#azure-databricks-linked-service)。 |
| 3201           | 不支援節點類型 Standard_D16S_v3。 支援的節點類型: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2,Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s,Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2. | 錯誤的撰寫。                                                | 請參閱錯誤訊息。                                          |
| 3201           | 不正確 notebook_path: ...目前僅支援絕對路徑。 路徑必須以 '/' 開頭。 | 錯誤的撰寫。                                                | 請參閱錯誤訊息。                                          |
| 3202           | 過去3600秒內已建立1000個工作, 超過速率限制: 1000每3600秒的作業建立。 | 一小時內執行太多 Databricks。                         | 檢查使用此 Databricks 工作區的所有管線, 以取得其作業建立速率。  如果管線啟動太多 Databricks 在匯總中執行, 請將一些管線遷移至新的工作區。 |
| 3202           | 無法剖析要求物件:必須為 JSON 對應欄位 base_parameters 設定「金鑰」和「值」, 但取得 ' key: "..." ' 只. | 撰寫錯誤:未提供參數的值。         | 檢查管線 JSON, 並確保 baseParameters 筆記本中的所有參數都指定非空白值。 |
| 3202           | 使用者: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}`未獲授權, 無法存取叢集。 | 產生存取權杖的使用者不允許存取連結服務中指定的 Databricks 叢集。 | 請確定使用者具有工作區中的必要許可權。   |
| 3203           | 叢集處於終止狀態, 無法供接收作業使用。 請修正叢集, 或稍後重試。 | 叢集已終止。    若為互動式叢集, 這可能是競爭條件。 | 若要避免此錯誤, 最好的方法是使用作業叢集。             |
| 3204           | 作業執行失敗。  | 錯誤訊息指出各種問題, 例如未預期的叢集狀態或特定的活動。 最常不會出現任何錯誤訊息。                                                          | N/A                                                          |



## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

下表適用于 U-SQL。

| 錯誤碼         | 錯誤訊息                                                | 描述                                          | 建議                            |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | 存取權杖來自錯誤的租使用者。                    | 不正確的 Azure Active Directory (Azure AD) 租使用者。                                         | 用來存取 Azure Data Lake Analytics 屬於另一個 Azure AD 租使用者的服務主體。 在與 Data Lake Analytics 帳戶相同的租使用者中建立新的服務主體。 |
| 2711、2705、2704 | 禁止. ACL 驗證失敗。 可能是資源不存在, 或使用者未獲授權執行要求的作業。<br/><br/>使用者無法存取 Data Lake Store。  <br/><br/>使用者未獲授權, 無法使用 Data Lake Analytics。 | 服務主體或憑證無法存取儲存體中的檔案。 | 請確定使用者為 Data Lake Analytics 作業提供的服務主體或憑證, 可以從根資料夾存取 Data Lake Analytics 帳戶和預設 Data Lake Storage 實例。 |
| 2711                 | 找不到 ' Azure Data Lake Store ' 檔案或資料夾。       | U-SQL 檔案的路徑錯誤, 或連結的服務認證沒有存取權。 | 請確認連結服務中提供的路徑和認證。 |
| 2707                 | 無法解析 AzureDataLakeAnalytics 的帳戶。 請檢查「AccountName」和「DataLakeAnalyticsUri」。 | 連結服務中的 Data Lake Analytics 帳戶錯誤。                  | 確認已提供正確的帳戶。             |
| 2703                 | 錯誤識別碼:E_CQO_SYSTEM_INTERNAL_ERROR (或任何以「錯誤識別碼:」開頭的錯誤)。 | 錯誤來自 Data Lake Analytics。                                    | 如範例的錯誤表示工作已提交至 Data Lake Analytics, 而腳本失敗。 在 Data Lake Analytics 中進行調查。 在入口網站中, 移至 [Data Lake Analytics] 帳戶, 然後使用 Data Factory 活動執行識別碼 (而非管線執行識別碼) 尋找作業。 作業會提供有關錯誤的詳細資訊, 並協助您進行疑難排解。 如果解決方式不清楚, 請洽詢 Data Lake Analytics 支援小組, 並提供工作 URL, 其中包括您的帳戶名稱和作業識別碼。 |
| 2709                 | 我們目前無法接受您的作業。 您帳戶的已佇列工作數目上限為200。 | 此錯誤是因為 Data Lake Analytics 上的節流所造成。                                           | 藉由變更活動的 Data Factory 觸發程式和並行設定, 減少提交的作業數以 Data Lake Analytics。 或增加 Data Lake Analytics 的限制。 |
| 2709                 | 此作業已遭到拒絕, 因為它需要24個 au。 此帳戶的系統管理員定義原則會防止作業使用5個以上的 au。 | 此錯誤是因為 Data Lake Analytics 上的節流所造成。                                           | 藉由變更活動的 Data Factory 觸發程式和並行設定, 減少提交的作業數以 Data Lake Analytics。 或增加 Data Lake Analytics 的限制。 |



## <a name="azure-functions"></a>Azure Functions

| 錯誤碼 | 錯誤訊息                           | 描述                                                  | 建議                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | 回應內容不是有效的 JObject。 | 呼叫的 Azure 函式未傳迴響應中的 JSON 承載。 Data Factory 中的 Azure 函式活動僅支援 JSON 回應內容。 | 更新 Azure 函數以傳回有效的 JSON 承載。 例如, C#函式可以`(ActionResult)new<OkObjectResult("{` \"傳回識別碼\":\"123。\" `}");` |
| 3600         | 不正確 HttpMethod: ' ... '。               | Azure function 活動不支援活動裝載中指定的 HTTP 方法。 | 使用支援的 HTTP 方法, 例如 PUT、POST、GET、DELETE、OPTIONS、HEAD 或 TRACE。 |



## <a name="custom"></a>自訂

下表適用于 Azure Batch。

| 錯誤碼 | 錯誤訊息                                                | 描述                                                  | 建議                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | 遇到未預期的例外狀況, 執行失敗。             | 無法啟動命令, 或程式傳回錯誤碼。 | 請確認可執行檔存在。 如果程式已啟動, 請確定*stdout*和*stderr*已上傳至儲存體帳戶。 在您的程式碼中發出膨脹佈滿記錄以進行調試是個很好的作法。 |
| 2501         | 無法存取使用者 batch 帳戶;請檢查 batch 帳戶設定。 | 不正確的批次存取金鑰或集區名稱。            | 請確認連結服務中的集區名稱和批次存取金鑰。 |
| 2502         | 無法存取使用者儲存體帳戶;請檢查儲存體帳戶設定。 | 儲存體帳戶名稱或存取金鑰不正確。       | 驗證連結服務中的儲存體帳戶名稱和存取金鑰。 |
| 2504         | 作業傳回不正確狀態碼 ' BadRequest '。     | 自訂活動的 folderPath 中有太多檔案。 ResourceFiles 的總大小不能超過32768個字元。 | 移除不必要的檔案。 或將其壓縮, 並新增解壓縮命令以將其解壓縮。 例如, 使用`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe` |
| 2505         | 除非使用帳戶金鑰認證, 否則無法建立共用存取簽章。 | 自訂活動僅支援使用存取金鑰的儲存體帳戶。 | 請參閱錯誤描述。                                            |
| 2507         | 資料夾路徑不存在或是空的: ...            | 位於指定路徑的儲存體帳戶中沒有任何檔案。       | 資料夾路徑必須包含您想要執行的可執行檔。 |
| 2508         | 資源資料夾中有重複的檔案。               | 相同名稱的多個檔案位於 folderPath 的不同子資料夾中。 | 自訂活動會在 folderPath 下壓平合併資料夾結構。  如果您需要保留資料夾結構, 請壓縮檔案, 並使用解壓縮命令在 Azure Batch 中將檔案解壓縮。 例如, 使用`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe` |
| 2509         | Batch url ... 無效;它必須是 Uri 格式。         | 批次 Url 必須類似于`https://mybatchaccount.eastus.batch.azure.com` | 請參閱錯誤描述。                                            |
| 2510         | 傳送要求時發生錯誤。               | 批次 URL 無效。                                         | 確認批次 URL。                                            |

## <a name="hdinsight"></a>HDInsight

下表適用于 Spark、Hive、MapReduce、Pig 和 Hadoop 串流。

| 錯誤碼 | 錯誤訊息                                                | 描述                                                  | 建議                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300、2310 | Hadoop 作業提交失敗。 Error:The remote name could not be resolved」的問題。 <br/><br/>找不到叢集。 | 提供的叢集 URI 無效。                              | 請確定叢集尚未刪除, 而且提供的 URI 正確。 當您在瀏覽器中開啟 URI 時, 您應該會看到 Ambari UI。 如果叢集位於虛擬網路中, 則 URI 應該是私用 URI。 若要開啟它, 請使用屬於相同虛擬網路的 VM。 如需詳細資訊, 請參閱[直接連接到 Apache Hadoop 服務](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)。 |
| 2300         | Hadoop 作業提交失敗。 作業: ...、Cluster: .../。 Error:工作已取消。 | 提交的作業超時。                         | 此問題可能是一般 HDInsight 連線能力或網路連線能力。 請先確認可以從任何瀏覽器取得 HDInsight Ambari UI。 確認您的認證仍然有效。 如果您使用的是自我裝載整合執行時間 (IR), 請務必從已安裝自我裝載 IR 的 VM 或電腦執行此動作。 然後再次嘗試從 Data Factory 提交工作。 如果仍然失敗, 請洽詢 Data Factory 小組以取得支援。 |
| 2300         | 未經授權 Ambari 的使用者名稱或密碼不正確  <br/><br/>未經授權 使用者系統管理員在 Ambari 中遭到鎖定。   <br/><br/>403-禁止:存取遭到拒絕。 | HDInsight 的認證不正確或已過期。 | 更正認證並重新部署已連結的服務。 首先, 請在任何瀏覽器上開啟叢集 URI 並嘗試登入, 以確定認證可在 HDInsight 上工作。 如果認證無法使用, 您可以從 Azure 入口網站重設它們。 |
| 2300、2310 | 502 - 網頁伺服器作為閘道或 Proxy 伺服器時收到無效的回應。       <br/>閘道錯誤。 | 這是來自 HDInsight 的錯誤。                               | 此錯誤來自 HDInsight 叢集。 如需詳細資訊, 請參閱[AMBARI UI 502 錯誤](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)、[連接到 spark Thrift 伺服器的502錯誤](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)、 [502 連線到 spark Thrift 伺服器的錯誤](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)和[疑難排解應用程式閘道中的閘道錯誤](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)。 |
| 2300         | Hadoop 作業提交失敗。 作業: ..., 叢集: ...錯誤: {\"error\":\"無法服務提交作業要求, 因為 templeton 服務忙碌中, 有太多提交作業要求。 請稍候片刻, 再重試此操作。 請參閱 config templeton. parallellism。提交以設定並行要求。  <br/><br/>Hadoop 作業提交失敗。 任務161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, Cluster: `https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/`。   錯誤: {\"error\":\"IOException: yarn。例外狀況。 YarnException:無法將 application_1561147195099_3730 提交至 YARN: org. AccessControlException:佇列根目錄。 joblauncher 已經有500應用程式, 無法接受提交應用程式: application_1561147195099_3730 \ | 同時將太多作業提交給 HDInsight。 | 請考慮限制提交至 HDInsight 的並行作業數目。 如果作業是由相同的活動所提交, 請參閱 Data Factory 活動並行。 變更觸發程式, 讓並行管線執行會隨著時間分散。 如錯誤所建議, 請`templeton.parallellism.job.submit`參閱 HDInsight 檔以進行調整。 |
| 2303、2347 | Hadoop 工作失敗, 結束代碼為 ' 5 '。 如需wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr詳細資訊, 請參閱 ' '。  <br/><br/>Hive 執行失敗, 錯誤碼為 ' UserErrorHiveOdbcCommandExecutionFailure '。   如需wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out詳細資訊, 請參閱 ' '。 | 作業已提交至 HDInsight, 但在 HDInsight 上失敗。 | 已成功將作業提交至 HDInsight。 在叢集上失敗。 在 HDInsight Ambari UI 中開啟作業和記錄, 或從儲存體開啟檔案, 如錯誤訊息所建議。 檔案會顯示錯誤詳細資料。 |
| 2328         | 處理要求時發生內部伺服器錯誤。 請重試要求或聯絡支援。 | 此錯誤會隨選在 HDInsight 中發生。                              | 當 HDInsight 布建失敗時, 此錯誤來自 HDInsight 服務。 請洽詢 HDInsight 小組, 並提供隨選叢集名稱。 |
| 2310         | java.lang.NullPointerException                               | 當作業提交至 Spark 叢集時, 就會發生此錯誤。      | 此例外狀況來自 HDInsight。 它會隱藏實際的問題。 請洽詢 HDInsight 小組以取得支援。 提供叢集名稱和活動運行時間範圍。 |
|              | 所有其他錯誤                                             |                                                              | 請參閱[使用 hdinsight 和 Hdinsight 進行疑難排解](../hdinsight/hdinsight-troubleshoot-guide.md)[常見問題](https://hdinsight.github.io/)。 |



## <a name="web-activity"></a>網路活動

| 錯誤碼 | 錯誤訊息                                                | 描述                                                  | 建議                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | 不正確 HttpMethod: ' ... '。                                    | Web 活動不支援活動裝載中指定的 HTTP 方法。 | 支援的 HTTP 方法包括 PUT、POST、GET 和 DELETE。 |
| 2108         | 不正確伺服器錯誤500。                                     | 端點發生內部錯誤。                               | 使用 Fiddler 或 Postman 來檢查 URL 上的功能。 |
| 2108         | 未經授權401。                                             | 要求上缺少有效的驗證。                      | Token 可能已過期。 請提供有效的驗證方法。 使用 Fiddler 或 Postman 來檢查 URL 上的功能。 |
| 2108         | 禁止403。                                                | 缺少必要的許可權。                                 | 檢查存取資源的使用者權限。 使用 Fiddler 或 Postman 來檢查 URL 上的功能。  |
| 2108         | 不正確的要求400。                                              | 不正確 HTTP 要求。                                         | 檢查要求的 URL、動詞和主體。 使用 Fiddler 或 Postman 來驗證要求。  |
| 2108         | 找不到404。                                                | 找不到資源。                                       | 使用 Fiddler 或 Postman 來驗證要求。  |
| 2108         | 服務無法使用。                                          | 服務無法使用。                                       | 使用 Fiddler 或 Postman 來驗證要求。  |
| 2108         | 不支援的媒體類型。                                       | 內容類型與 Web 活動主體不相符。           | 指定符合承載格式的內容類型。 使用 Fiddler 或 Postman 來驗證要求。 |
| 2108         | 您要尋找的資源已移除、其名稱已變更, 或暫時無法使用。 | 資源無法使用。                                | 使用 Fiddler 或 Postman 來檢查端點。 |
| 2108         | 無法顯示您要尋找的網頁, 因為使用了不正確方法 (HTTP 指令動詞)。 | 在要求中指定了不正確的 Web 活動方法。   | 使用 Fiddler 或 Postman 來檢查端點。 |
| 2108         | invalid_payload                                              | Web 活動主體不正確。                       | 使用 Fiddler 或 Postman 來檢查端點。 |

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



