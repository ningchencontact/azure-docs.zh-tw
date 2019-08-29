---
title: 針對 Azure Data Factory 連接器進行疑難排解 |Microsoft Docs
description: 瞭解如何在 Azure Data Factory 中針對連接器問題進行疑難排解。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: bea5191063cf673f6b1395d46a15536e80b0aa30
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143501"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>針對 Azure Data Factory 連接器進行疑難排解

本文探討 Azure Data Factory 中連接器的常見疑難排解方法。

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>錯誤訊息：遠端伺服器傳回錯誤：(403) 禁止

- **徵兆**：複製活動因下列錯誤而失敗: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **原因**︰其中一個可能的原因是您使用的服務主體或受控識別沒有存取特定資料夾/檔案的許可權。

- **解決方案**︰針對您需要複製的所有資料夾和子資料夾, 授與對應的許可權。 請參閱[此](connector-azure-data-lake-store.md#linked-service-properties)檔。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>錯誤訊息：無法使用服務主體取得存取權杖。 ADAL 錯誤: service_unavailable

- **徵兆**: 複製活動因下列錯誤而失敗:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **原因**︰當 Azure Active Directory 所擁有的服務權杖伺服器 (STS) 無法使用時 (亦即, 過於忙碌而無法處理要求), 它會傳回 HTTP 錯誤503。 

- **解決方案**︰請在數分鐘後重新執行複製活動。

## <a name="azure-sql-data-warehouse"></a>Azure SQL 資料倉儲

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>錯誤訊息：從字元字串轉換為 uniqueidentifier 時, 轉換失敗

- **徵兆**：當您使用分段複製和 PolyBase 將資料從表格式資料來源 (例如 SQL Server) 複製到 Azure SQL 資料倉儲時, 您會遇到下列錯誤:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **原因**︰Azure SQL 資料倉儲 PolyBase 無法將空字串轉換成 GUID。

- **解決方案**︰在複製活動接收的 [Polybase 設定] 底下, 將 [**使用類型預設值**] 選項設定為 false。

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>錯誤訊息：預期的資料類型:DECIMAL (x, x), 違規值

- **徵兆**：當您使用分段複製和 PolyBase 將資料從表格式資料來源 (例如 SQL Server) 複製到 SQL DW 時, 您會遇到下列錯誤:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **原因**︰Azure SQL 資料倉儲 Polybase 無法將空字串 (null 值) 插入 decimal 資料行。

- **解決方案**︰在複製活動接收的 [Polybase 設定] 底下, 將 [**使用類型預設值**] 選項設定為 false。

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>錯誤訊息：JAVA 例外狀況訊息: HdfsBridge:: CreateRecordReader

- **徵兆**：您可以使用 PolyBase 將資料複製到 Azure SQL 資料倉儲, 並遇到下列錯誤:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **原因**︰可能的原因是架構 (總數據行寬度) 太大 (大於 1 MB)。 藉由新增所有資料行的大小, 檢查目標 SQL DW 資料表的架構:

    - Int-> 4 個位元組
    - Bigint-> 8 個位元組
    - Varchar (n)、char (n)、binary (n)、Varbinary (n)-> n 位元組
    - NVarchar (n)、Nchar (n)-> n * 2 位元組
    - 日期-> 6 個位元組
    - Datetime/(2)、Smalldatetime-> 16 個位元組
    - Datetimeoffset-> 20 個位元組
    - 十進位-> 19 個位元組
    - Float-> 8 個位元組
    - Money-> 8 個位元組
    - Smallmoney-> 4 個位元組
    - Real > 4 個位元組
    - Smallint-> 2 個位元組
    - 時間 > 12 個位元組
    - Tinyint-> 1 位元組

- **解決方案**︰將資料行寬度減少為小於 1 MB

- 或藉由停用 Polybase 來使用大量插入方法

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>錯誤訊息：不符合使用 HTTP 條件式標頭指定的條件

- **徵兆**：您可以使用 SQL 查詢從 Azure SQL 資料倉儲提取資料, 並遇到下列錯誤:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **原因**︰Azure SQL 資料倉儲在 Azure 儲存體中查詢外部資料表時遇到問題。

- **解決方案**︰在 SSMS 中執行相同的查詢, 並檢查您是否看到相同的結果。 如果是, 請開啟支援票證以 Azure SQL 資料倉儲, 並提供您的 SQL DW 伺服器和資料庫名稱, 以進行進一步的疑難排解。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>錯誤訊息：要求大小太大

- **徵兆**：您可以使用預設的寫入批次大小將資料複製到 Azure Cosmos DB, 並遇到「 ***要求大小太大*** 」錯誤。

- **原因**︰Cosmos DB 會將一個單一要求的大小限制為 2 MB。 公式為, 要求大小 = 單一檔案大小 * 寫入批次大小。 如果您的檔案大小很大, 則預設行為會導致太大的要求大小。 您可以調整寫入批次大小。

- **解決方案**︰在複製活動接收中, 減少 [寫入批次大小] 值 (預設值為 10000)。

### <a name="error-message-unique-index-constraint-violation"></a>錯誤訊息：唯一索引條件約束違規

- **徵兆**：將資料複製到 Cosmos DB 時, 您遇到下列錯誤:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **原因**︰有兩個可能的原因：

    - 如果您使用**Insert** as write 行為, 此錯誤表示您的來源資料具有具有相同識別碼的資料列/物件。

    - 如果您使用**Upsert**做為寫入行為, 並將另一個唯一索引鍵設定為容器, 此錯誤表示您的來源資料具有具有不同識別碼但已定義之唯一索引鍵具有相同值的資料列/物件。

- **解決方案**︰ 

    - 針對 cause1, 將**Upsert**設定為寫入行為。
    - 針對原因 2, 請確定每份檔的定義唯一索引鍵都有不同的值。

### <a name="error-message-request-rate-is-large"></a>錯誤訊息：要求率非常大

- **徵兆**：將資料複製到 Cosmos DB 時, 您遇到下列錯誤:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **原因**︰所使用的要求單位大於 Cosmos DB 中設定的可用 RU。 從[這裡](../cosmos-db/request-units.md#request-unit-considerations)瞭解 Cosmos DB 如何計算 RU。

- **解決方案**︰以下是兩種解決方案:

    1. 將**容器 RU 增加**到 Cosmos DB 中較大的值, 這將可改善複製活動的效能, 不過在 Cosmos DB 中會產生更多成本。 

    2. 將**writeBatchSize**減少為較小的值 (例如 1000), 並將**parallelCopies**設定為較小的值 (例如 1), 使複製執行效能比目前更糟, 但不會在 Cosmos DB 中產生更多成本。

### <a name="column-missing-in-column-mapping"></a>資料行對應中遺漏資料行

- **徵兆**：當您匯入資料行對應 Cosmos DB 的架構時, 某些資料行會遺失。 

- **原因**︰ADF 會從前10個 Cosmos DB 檔推斷架構。 如果某些資料行/屬性在這些檔中沒有值, ADF 就不會偵測到它們, 因此不會顯示。

- **解決方案**︰您可以如下微調查詢, 以強制資料行顯示在結果集的空白值中: (假設前10個檔中遺漏「不可能」資料行)。 或者, 您可以手動新增用於對應的資料行。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>錯誤訊息：讀取器的 GuidRepresentation 是 CSharpLegacy

- **徵兆**：從具有 UUID 欄位 Cosmos DB MongoAPI/MongoDB 複製資料時, 遇到下列錯誤:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **原因**︰有兩種方式可代表 BSON 中的 UUID-UuidStardard 和 UuidLegacy。 根據預設, UuidLegacy 會用來讀取資料。 如果 MongoDB 中的 UUID 資料是 UuidStandard, 您將會遇到錯誤。

- **解決方案**︰在 MongoDB 連接字串中, 新增選項 "**uuidRepresentation = standard**"。 如需詳細資訊, 請參閱[MongoDB 連接字串](connector-mongodb.md#linked-service-properties)。

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>錯誤訊息：為 ' SshPublicKey ' 驗證類型提供的 SFTP 認證無效

- **徵兆**：您使用`SshPublicKey`驗證並遇到下列錯誤:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **原因**︰有3種可能的原因:

    1. 如果您使用 ADF 撰寫 UI 來撰寫 SFTP 連結服務, 此錯誤表示您選擇使用的私密金鑰格式錯誤。 您可以使用 PKCS # 8 的 SSH 私密金鑰格式, 但請注意, ADF 僅支援傳統的 SSH 金鑰格式。 更具體來說, PKCS # 8 格式和傳統金鑰格式的差異在於 PKCS # 8 金鑰內容是以「 *-----開始加密的私密金鑰-----* 」開頭, 而傳統金鑰格式是以「 *-----開始 RSA 私密金鑰-----* 」開頭。
    2. 如果您使用 Azure Key Vault 來儲存私密金鑰內容, 或使用 programmatical 方式來撰寫 SFTP 連結服務, 此錯誤表示私密金鑰內容不正確, 可能不是以 base64 編碼。
    3. 不正確認證或私用金鑰內容。

- **解決方案**︰ 

    - 針對 [原因 #1], 請執行下列命令, 將金鑰轉換成傳統金鑰格式, 然後在 ADF 撰寫 UI 中使用它。

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - 為了產生這類字串的原因 #2, 客戶可以使用以下2種方式:
    - 使用協力廠商 base64 轉換工具: 將整個私密金鑰內容貼入[Base64 編碼和](https://www.base64encode.org/)解碼之類的工具, 並將其編碼為 base64 格式字串, 然後將此字串貼入 key vault, 或使用此值以程式設計方式撰寫 SFTP 連結服務。
    - 使用C#程式碼:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - 針對 [原因 #3], 再次檢查金鑰檔案或密碼是否正確, 並使用其他工具來驗證是否可以使用它來適當地存取 SFTP 伺服器。

## <a name="next-steps"></a>後續步驟

如需更多疑難排解協助, 請嘗試下列資源:

*  [Data Factory 的 blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)



