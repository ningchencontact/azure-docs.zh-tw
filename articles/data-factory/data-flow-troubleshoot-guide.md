---
title: 針對 Azure Data Factory 資料流程進行疑難排解 |Microsoft Docs
description: 瞭解如何針對 Azure Data Factory 中的資料流程問題進行疑難排解。
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: 5cf4773ac781ae51a60ef7d987c3dc324c125d95
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387719"
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
