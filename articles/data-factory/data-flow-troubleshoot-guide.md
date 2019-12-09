---
title: 針對資料流程進行疑難排解
description: 瞭解如何針對 Azure Data Factory 中的資料流程問題進行疑難排解。
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.custom: seo-lt-2019
ms.date: 12/06/2019
ms.openlocfilehash: b972bbeac419d88afdd257a7fd19587dbaedf0d9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930163"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>針對 Azure Data Factory 資料流程進行疑難排解

本文探討 Azure Data Factory 中資料流程的常見疑難排解方法。

## <a name="common-errors-and-messages"></a>常見的錯誤和訊息

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>錯誤訊息： DF-SYS-01： databricks. AzureException： StorageException：指定的容器不存在，請執行此動作中的工作區。

- **徵兆**：資料預覽、debug 和管線資料流程執行失敗，因為容器不存在

- **原因**：資料集包含不存在於儲存體中的容器

- **解決方案**：請確定您在資料集中參考的容器存在

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>錯誤訊息： DF-SYS-01： AssertionError：判斷提示失敗：偵測到衝突的目錄結構。 可疑的路徑

- **徵兆**：在來源轉換中搭配 Parquet 檔案使用萬用字元時

- **原因**：萬用字元語法不正確或無效

- **解決**方式：在來源轉換選項中檢查您所使用的萬用字元語法

### <a name="error-message-df-src-002-container-container-name-is-required"></a>錯誤訊息： DF-SRC-002： ' container ' （容器名稱）是必要的

- **徵兆**：資料預覽、debug 和管線資料流程執行失敗，因為容器不存在

- **原因**：資料集包含不存在於儲存體中的容器

- **解決方案**：請確定您在資料集中參考的容器存在

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>錯誤訊息： DF-單一-001： PrimaryKeyValue 具有不相容的類型 IntegerType 和 StringType

- **徵兆**：資料預覽、debug 和管線資料流程執行失敗，因為容器不存在

- **原因**：嘗試在資料庫接收中插入不正確的 primary key 類型時發生

- **解決**方式：使用衍生的資料行，將您在資料流程中用來作為主要金鑰的資料行轉換為符合目標資料庫的資料類型

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>錯誤訊息： DF-SYS-01： SQLServerException：與主機 xxxxx.database.windows.net 通訊埠1433的 TCP/IP 連線失敗。 錯誤：「xxxx.database.windows.net。 驗證連接屬性。 請確定 SQL Server 的實例正在主機上執行，並接受埠上的 TCP/IP 連接。 請確定埠的 TCP 連線不會遭到防火牆封鎖。」

- **徵兆**：無法使用資料庫來源或接收來預覽資料或執行管線

- **原因**：資料庫受到防火牆保護

- **解決**方式：開啟對資料庫的防火牆存取

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>錯誤訊息： DF-SYS-01： .com. SQLServerException：資料庫中已經有名為 ' xxxxxx ' 的物件。

- **徵兆**：接收無法建立資料表

- **原因**：目標資料庫中已有現有的資料表名稱，但在來源或資料集中定義了相同的名稱

- **解決**方式：變更您嘗試建立之資料表的名稱

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-string-or-binary-data-would-be-truncated"></a>錯誤訊息： DF-SYS-01： SQLServerException：字串或二進位資料會被截斷。 

- **徵兆**：將資料寫入至 SQL 接收器時，您的資料流程會在管線執行時失敗，並出現可能的截斷錯誤。

- **原因**：資料流程中的欄位對應到 SQL 資料庫中的資料行不足以儲存值，導致 SQL 驅動程式擲回此錯誤

- **解決**方式：您可以使用衍生資料行中的 ```left()```，或實作為「錯誤資料列」模式，來減少字串資料行的資料長度[。](how-to-data-flow-error-rows.md)

### <a name="error-message-since-spark-23-the-queries-from-raw-jsoncsv-files-are-disallowed-when-the-referenced-columns-only-include-the-internal-corrupt-record-column"></a>錯誤訊息：自 Spark 2.3 起，當參考的資料行只包含內部損毀記錄資料行時，不允許來自原始 JSON/CSV 檔案的查詢。 

- **徵兆**：從 JSON 來源讀取失敗

- **原因**：從 JSON 來源讀取具有許多嵌套行、ADF、透過 Spark 的單一檔時，無法判斷新檔的開始位置，以及上一份檔結束的位置。

- **解決**方式：在使用 JSON 資料集的來源轉換中，展開 [JSON 設定]，然後開啟 [單一檔]。

### <a name="error-message-duplicate-columns-found-in-join"></a>錯誤訊息：在聯結中發現重複的資料行

- **徵兆**：聯結轉換導致左側和右側的資料行包含重複的資料行名稱

- **原因**：正在聯結的資料流程具有一般資料行名稱

- **解決**方式：在聯結後面新增選取 transforamtion，然後選取 [移除重複的資料行] 來輸入和輸出。


## <a name="general-troubleshooting-guidance"></a>一般疑難排解指引

1. 檢查資料集連接的狀態。 在每個「來源」和「接收」轉換中，流覽您所使用之每個資料集的連結服務，並測試連接。
2. 從 [資料流程設計師] 檢查檔案和資料表連接的狀態。 開啟 [偵錯工具]，然後按一下來源轉換上的 [資料預覽]，以確保您能夠存取您的資料。
3. 如果資料預覽中的一切都不錯，請進入「管線設計師」，並將您的資料流程放在管線活動中。 針對端對端測試進行管線的偵錯工具。

## <a name="next-steps"></a>後續步驟

如需更多疑難排解協助，請嘗試下列資源：

*  [Data Factory 的 blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
*  [ADF 對應資料流程效能指南](concepts-data-flow-performance.md)
