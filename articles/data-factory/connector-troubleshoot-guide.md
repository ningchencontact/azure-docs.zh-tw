---
title: 針對 Azure Data Factory 連接器進行疑難排解
description: 瞭解如何在 Azure Data Factory 中針對連接器問題進行疑難排解。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 218031830a7516dfd539e1c0b9b665807822f38d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533155"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>針對 Azure Data Factory 連接器進行疑難排解

本文探討 Azure Data Factory 中連接器的常見疑難排解方法。

## <a name="azure-data-lake-storage"></a>Azure Data Lake 儲存體

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>錯誤訊息：遠端伺服器傳回錯誤：（403）禁止

- **徵兆**：複製活動因下列錯誤而失敗： 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **原因**：其中一個可能的原因是您使用的服務主體或受控識別沒有存取特定資料夾/檔案的許可權。

- **解決**方式：授與您需要複製之所有資料夾和子資料夾的對應許可權。 請參閱[此](connector-azure-data-lake-store.md#linked-service-properties)檔。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>錯誤訊息：無法使用服務主體取得存取權杖。 ADAL 錯誤： service_unavailable

- **徵兆**：複製活動因下列錯誤而失敗：

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **原因**：當 Azure Active Directory 所擁有的服務權杖伺服器（STS）無法使用時（亦即，過於忙碌而無法處理要求），它會傳回 HTTP 錯誤503。 

- **解決**方式：請在數分鐘後重新執行複製活動。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>錯誤訊息：要求大小太大

- **徵兆**：您會將資料複製到具有預設寫入批次大小的 Azure Cosmos DB，並遇到「 ***要求大小太大*** 」錯誤。

- **原因**： Cosmos DB 將一個單一要求的大小限制為 2 MB。 公式為，要求大小 = 單一檔案大小 * 寫入批次大小。 如果您的檔案大小很大，則預設行為會導致太大的要求大小。 您可以調整寫入批次大小。

- **解決**方式：在複製活動接收中，減少 [寫入批次大小] 值（預設值為10000）。

### <a name="error-message-unique-index-constraint-violation"></a>錯誤訊息：唯一索引條件約束違規

- **徵兆**：將資料複製到 Cosmos DB 時，您遇到下列錯誤：

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **原因**：有兩個可能的原因：

    - 如果您使用**Insert** as write 行為，此錯誤表示您的來源資料具有具有相同識別碼的資料列/物件。

    - 如果您使用**Upsert**做為寫入行為，並將另一個唯一索引鍵設定為容器，此錯誤表示您的來源資料具有具有不同識別碼但已定義之唯一索引鍵具有相同值的資料列/物件。

- **解決方式**： 

    - 針對 cause1，將**Upsert**設定為寫入行為。
    - 針對原因2，請確定每份檔的定義唯一索引鍵都有不同的值。

### <a name="error-message-request-rate-is-large"></a>錯誤訊息：要求速率很大

- **徵兆**：將資料複製到 Cosmos DB 時，您遇到下列錯誤：

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **原因**：所使用的要求單位大於 Cosmos DB 中設定的可用 RU。 從[這裡](../cosmos-db/request-units.md#request-unit-considerations)瞭解 Cosmos DB 如何計算 RU。

- **解決**方式：以下是兩個解決方案：

    1. 將**容器 RU 增加**到 Cosmos DB 中較大的值，這將可改善複製活動的效能，不過在 Cosmos DB 中會產生更多成本。 

    2. 將**writeBatchSize**減少為較小的值（例如1000），並將**parallelCopies**設定為較小的值（例如1），使複製執行效能比目前更糟，但不會在 Cosmos DB 中產生更多成本。

### <a name="column-missing-in-column-mapping"></a>資料行對應中遺漏資料行

- **徵兆**：當您匯入資料行對應 Cosmos DB 的架構時，部分資料行遺失。 

- **原因**： ADF 會從前10個 Cosmos DB 檔推斷架構。 如果某些資料行/屬性在這些檔中沒有值，ADF 就不會偵測到它們，因此不會顯示。

- **解決**方式：您可以微調下列查詢，以強制資料行顯示在結果集的空白值中：（假設前10個檔中遺漏「不可能」資料行）。 或者，您可以手動新增用於對應的資料行。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>錯誤訊息：讀取器的 GuidRepresentation 是 CSharpLegacy

- **徵兆**：使用 UUID 欄位從 Cosmos DB MongoAPI/MongoDB 複製資料時，遇到下列錯誤：

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **原因**：有兩種方式可代表 BSON 中的 UUID-UuidStardard 和 UuidLegacy。 根據預設，UuidLegacy 會用來讀取資料。 如果 MongoDB 中的 UUID 資料是 UuidStandard，您將會遇到錯誤。

- **解決**方式：在 MongoDB 連接字串中，新增選項 "**uuidRepresentation = standard**"。 如需詳細資訊，請參閱[MongoDB 連接字串](connector-mongodb.md#linked-service-properties)。

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>錯誤訊息：為 ' SshPublicKey ' 驗證類型提供的 SFTP 認證無效

- **徵兆**：您使用 `SshPublicKey` 驗證，並遇到下列錯誤：

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **原因**：有3個可能原因：

    1. 如果您使用 ADF 撰寫 UI 來撰寫 SFTP 連結服務，此錯誤表示您選擇使用的私密金鑰格式錯誤。 您可以使用 PKCS # 8 的 SSH 私密金鑰格式，但請注意，ADF 僅支援傳統的 SSH 金鑰格式。 更具體來說，PKCS # 8 格式和傳統金鑰格式的差異在於 PKCS # 8 金鑰內容是以「 *-----開始加密的私密金鑰-----* 」開頭，而傳統金鑰格式是以「 *-----開始 RSA 私密金鑰-----* 」開頭。
    2. 如果您使用 Azure Key Vault 來儲存私密金鑰內容，或使用 programmatical 方式來撰寫 SFTP 連結服務，此錯誤表示私密金鑰內容不正確，可能不是以 base64 編碼。
    3. 不正確認證或私用金鑰內容。

- **解決方式**： 

    - 針對 [原因 #1]，請執行下列命令，將金鑰轉換成傳統金鑰格式，然後在 ADF 撰寫 UI 中使用它。

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - 為了產生這類字串的原因 #2，客戶可以使用以下2種方式：
    - 使用協力廠商 base64 轉換工具：將整個私密金鑰內容貼入[Base64 編碼和](https://www.base64encode.org/)解碼之類的工具，並將其編碼為 base64 格式字串，然後將此字串貼入 key vault，或使用此值以程式設計方式撰寫 SFTP 連結服務。
    - 使用C#程式碼：

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - 針對 [原因 #3]，再次檢查金鑰檔案或密碼是否正確，並使用其他工具來驗證是否可以使用它來適當地存取 SFTP 伺服器。
  

## <a name="azure-sql-data-warehouse--azure-sql-database--sql-server"></a>Azure SQL 資料倉儲 \ Azure SQL Database \ SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>錯誤碼： SqlFailedToConnect

- **訊息**： `Cannot connect to SQL database: '%server;', Database: '%database;', User: '%user;'. Please check the linked service configuration is correct, and make sure the SQL database firewall allows the integration runtime to access.`

- **原因**：如果錯誤訊息包含 "SqlException"，SQL database 會擲回錯誤，指出某些特定的作業失敗。

- **建議**：如需詳細資訊，請依本參考檔中的 SQL 錯誤碼進行搜尋： https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors。 如果您需要進一步的協助，請洽詢 Azure SQL 支援。

- **原因**：如果錯誤訊息包含「具有 IP 位址的用戶端 ...」不允許存取伺服器」，而且您正嘗試連接到 Azure SQL database，這通常是由 Azure SQL database 防火牆問題所造成。

- **建議**：在 Azure SQL Server 防火牆設定中，啟用 [允許 Azure 服務和資源存取此伺服器] 選項。 參考檔： https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure。


### <a name="error-code--sqloperationfailed"></a>錯誤碼： SqlOperationFailed

- **訊息**： `A database operation failed. Please search error to get more details.`

- **原因**：如果錯誤訊息包含 "SqlException"，SQL database 會擲回錯誤，指出某些特定的作業失敗。

- **建議**：如需詳細資訊，請依本參考檔中的 SQL 錯誤碼進行搜尋： https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors。 如果您需要進一步的協助，請洽詢 Azure SQL 支援。

- **原因**：如果錯誤訊息包含 "PdwManagedToNativeInteropException"，通常是因為來源與接收資料行大小不相符所造成。

- **建議**：請檢查來源和接收資料行的大小。 如果您需要進一步的協助，請洽詢 Azure SQL 支援。

- **原因**：如果錯誤訊息包含 "InvalidOperationException"，通常是因為不正確輸入資料所造成。

- **建議**：若要找出發生問題的資料列，請在複製活動上啟用容錯功能，這可將有問題的資料列重新導向至儲存體，以供進一步調查。 參考檔： https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance。


### <a name="error-code--sqlunauthorizedaccess"></a>錯誤碼： SqlUnauthorizedAccess

- **訊息**： `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因**：認證不正確，或登入帳戶無法存取 SQL database。

- **建議**：請檢查登入帳戶是否有足夠的許可權可以存取 SQL 資料庫。


### <a name="error-code--sqlopenconnectiontimeout"></a>錯誤碼： SqlOpenConnectionTimeout

- **訊息**： `Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因**：可能是 SQL database 暫時性失敗。

- **建議**：請重試以較大的連接逾時值更新已連結的服務連接字串。


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>錯誤碼： SqlAutoCreateTableTypeMapFailed

- **訊息**： `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(colunm name:'%colunmName;') in auto-create table.`

- **原因**：自動建立資料表無法符合來源需求。

- **建議**：請更新 [對應] 中的資料行類型，或以手動方式在目標伺服器中建立接收資料表。


### <a name="error-code--sqldatatypenotsupported"></a>錯誤碼： SqlDataTypeNotSupported

- **訊息**： `A database operation failed. Please check the SQL errors.`

- **原因**：如果問題發生在 SQL 來源上，而且錯誤與 SqlDateTime 溢位相關，則資料值會超過邏輯類型範圍（1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM）。

- **建議**：請將類型轉換為來源 SQL 查詢中的字串，或在複製活動資料行對應中，將資料行類型變更為 ' string '。

- **原因**：如果問題發生在 SQL 接收器上，而且錯誤與 SqlDateTime 溢位相關，則資料值會超過接收資料表中允許的範圍。

- **建議**：請將對應的資料行類型更新為接收資料表中的 ' datetime2 ' 類型。


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>錯誤碼： SqlInvalidDbStoredProcedure

- **訊息**： `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因**：指定的預存程式無效。 這可能是因為預存程式不會傳回任何資料所造成。

- **建議**：請依 SQL 工具驗證預存程式。 請確定預存程式可以傳回資料。


### <a name="error-code--sqlinvaliddbquerystring"></a>錯誤碼： SqlInvalidDbQueryString

- **訊息**： `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因**：指定的 SQL 查詢無效。 這可能是因為查詢未傳回任何資料所造成

- **建議**：請依 sql 工具驗證 SQL 查詢。 請確定查詢可以傳回資料。


### <a name="error-code--sqlinvalidcolumnname"></a>錯誤碼： SqlInvalidColumnName

- **訊息**： `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因**：找不到資料行。 可能的設定錯誤。

- **建議**：請確認查詢中的資料行、dataset 中的 ' structure '，以及活動中的 ' 對應 '。


### <a name="error-code--sqlbatchwritetimeout"></a>錯誤碼： SqlBatchWriteTimeout

- **訊息**： `Timeout in SQL write opertaion.`

- **原因**：可能是 SQL database 暫時性失敗。

- **建議**：如果問題重現，請洽詢 Azure SQL 支援。


### <a name="error-code--sqlbatchwriterollbackfailed"></a>錯誤碼： SqlBatchWriteRollbackFailed

- **訊息**： `Timeout in SQL write operation and rollback also fail.`

- **原因**：可能是 SQL database 暫時性失敗。

- **建議**：請重試以較大的連接逾時值更新已連結的服務連接字串。


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>錯誤碼： SqlBulkCopyInvalidColumnLength

- **訊息**： `SQL Bulk Copy failed due to received an invalid column length from the bcp client.`

- **原因**： SQL 大量複製失敗，因為從 bcp 用戶端收到不正確資料行長度。

- **建議**：若要找出發生問題的資料列，請在複製活動上啟用容錯功能，這可將有問題的資料列重新導向至儲存體，以供進一步調查。 參考檔： https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance。


### <a name="error-code--sqlconnectionisclosed"></a>錯誤碼： SqlConnectionIsClosed

- **訊息**： `The connection is closed by SQL database.`

- **原因**：當高並存執行和伺服器終止連接時，sql database 會關閉 sql 連接。

- **建議**：遠端伺服器關閉 SQL 連接。 請再試一次。 如果問題重現，請洽詢 Azure SQL 支援。

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>錯誤訊息：從字元字串轉換為 uniqueidentifier 時，轉換失敗

- **徵兆**：當您使用分段複製和 PolyBase 將資料從表格式資料來源（例如 SQL Server）複製到 Azure SQL 資料倉儲時，會遇到下列錯誤：

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **原因**： Azure SQL 資料倉儲 PolyBase 無法將空字串轉換成 GUID。

- **解決方案**：在 [複製活動接收] 的 [Polybase 設定] 底下，將 [**使用類型預設值**] 選項設定為 false。

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>錯誤訊息：預期的資料類型： DECIMAL （x，x），違規值

- **徵兆**：當您使用分段複製和 PolyBase 將資料從表格式資料來源（例如 SQL Server）複製到 SQL DW 時，會遇到下列錯誤：

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **原因**： Azure SQL 資料倉儲 Polybase 無法將空字串（null 值）插入 decimal 資料行。

- **解決方案**：在 [複製活動接收] 的 [Polybase 設定] 底下，將 [**使用類型預設值**] 選項設定為 false。

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>錯誤訊息： JAVA 例外狀況訊息： HdfsBridge：： CreateRecordReader

- **徵兆**：您會使用 PolyBase 將資料複製到 Azure SQL 資料倉儲，並遇到下列錯誤：

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **原因**：可能的原因是架構（總數據行寬度）太大（大於 1 MB）。 藉由新增所有資料行的大小，檢查目標 SQL DW 資料表的架構：

    - Int-> 4 個位元組
    - Bigint-> 8 個位元組
    - Varchar （n）、char （n）、binary （n）、Varbinary （n）-> n 位元組
    - NVarchar （n）、Nchar （n）-> n * 2 位元組
    - 日期-> 6 個位元組
    - Datetime/（2）、Smalldatetime-> 16 個位元組
    - Datetimeoffset-> 20 個位元組
    - 十進位-> 19 個位元組
    - Float-> 8 個位元組
    - Money-> 8 個位元組
    - Smallmoney-> 4 個位元組
    - Real > 4 個位元組
    - Smallint-> 2 個位元組
    - 時間 > 12 個位元組
    - Tinyint-> 1 位元組

- **解決**方式：將資料行寬度縮減為小於 1 MB

- 或藉由停用 Polybase 來使用大量插入方法

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>錯誤訊息：不符合使用 HTTP 條件式標頭指定的條件

- **徵兆**：您可以使用 SQL 查詢從 Azure SQL 資料倉儲提取資料，並遇到下列錯誤：

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **原因**：查詢 Azure 儲存體中的外部資料表時，Azure SQL 資料倉儲遇到問題。

- **解決**方式：在 SSMS 中執行相同的查詢，並檢查您是否看到相同的結果。 若是如此，請開啟 Azure SQL 資料倉儲的支援票證，並提供您的 SQL DW 伺服器和資料庫名稱，進一步進行疑難排解。
            

## <a name="azure-blob-storage"></a>Azure Blob 儲存體

### <a name="error-code--azurebloboperationfailed"></a>錯誤碼： AzureBlobOperationFailed

- **訊息**： `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **原因**： Blob 儲存體作業遇到問題。

- **建議**：請在詳細資料中檢查錯誤。 請參閱 blob 說明文件： https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes。 如需協助，請聯絡儲存小組。



## <a name="azure-data-lake-gen2"></a>Azure Data Lake Gen2

### <a name="error-code--adlsgen2operationfailed"></a>錯誤碼： AdlsGen2OperationFailed

- **訊息**： `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因**： ADLS Gen2 擲回錯誤，指出操作失敗。

- **建議**：請檢查 ADLS Gen2 擲回的詳細錯誤訊息。 如果是暫時性失敗所造成，請重試。 如果您需要進一步協助，請洽詢 Azure 儲存體支援服務，並在錯誤訊息中提供要求識別碼。

- **原因**：當錯誤訊息包含「禁止」時，您所使用的服務主體或受控識別可能沒有足夠的許可權可以存取 ADLS Gen2。

- **建議**：請參閱說明文件： https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication。

- **原因**：當錯誤訊息包含 ' InternalServerError ' 時，ADLS Gen2 傳回錯誤。

- **建議**：可能是因為暫時性失敗所造成，請重試。 如果問題持續發生，請洽詢 Azure 儲存體支援服務，並在錯誤訊息中提供要求識別碼。


## <a name="next-steps"></a>後續步驟

如需更多疑難排解協助，請嘗試下列資源：

*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
            
