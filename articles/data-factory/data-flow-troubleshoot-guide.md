---
title: 針對 Azure Data Factory 資料流程進行疑難排解 |Microsoft Docs
description: 瞭解如何針對 Azure Data Factory 中的資料流程問題進行疑難排解。
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: 5eafa0cc6284df5c969f63e2ab3fac4113fab417
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178614"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>針對 Azure Data Factory 資料流程進行疑難排解

本文探討 Azure Data Factory 中資料流程的常見疑難排解方法。

## <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>錯誤訊息：DF-SYS-01： databricks. AzureException： StorageException 中的 azure.：/..：指定的容器不存在。

- **徵兆**：資料預覽、debug 和管線資料流程執行失敗，因為容器不存在

- **原因**︰當資料集包含不存在於儲存體中的容器時

- **解決方案**︰請確定您在資料集中參考的容器存在

## <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>錯誤訊息：DF-SYS-01： AssertionError：判斷提示失敗：偵測到衝突的目錄結構。 可疑的路徑

- **徵兆**：在來源轉換中搭配 Parquet 檔案使用萬用字元時

- **原因**︰萬用字元語法不正確或無效

- **解決方案**︰檢查您在來源轉換選項中所使用的萬用字元語法

## <a name="error-message-df-src-002-container-container-name-is-required"></a>錯誤訊息：DF-SRC-002：必須有 ' container ' （容器名稱）

- **徵兆**：資料預覽、debug 和管線資料流程執行失敗，因為容器不存在

- **原因**︰當資料集包含不存在於儲存體中的容器時

- **解決方案**︰請確定您在資料集中參考的容器存在

## <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>錯誤訊息：DF-一對一-001：PrimaryKeyValue 具有不相容的類型 IntegerType 和 StringType

- **徵兆**：資料預覽、debug 和管線資料流程執行失敗，因為容器不存在

- **原因**︰嘗試在資料庫接收中插入不正確的 primary key 類型時發生

- **解決方案**︰使用衍生的資料行，將您在資料流程中用來作為主要金鑰的資料行轉換成符合目標資料庫的資料類型

## <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>錯誤訊息：DF-SYS-01： .com. SQLServerException：與主機 xxxxx.database.windows.net 通訊埠1433的 TCP/IP 連線失敗。 錯誤：「xxxx.database.windows.net。 驗證連接屬性。 請確定 SQL Server 的實例正在主機上執行，並接受埠上的 TCP/IP 連接。 請確定埠的 TCP 連線不會遭到防火牆封鎖。」

- **徵兆**：無法使用資料庫來源或接收來預覽資料或執行管線

- **原因**︰資料庫受到防火牆保護

- **解決方案**︰開啟對資料庫的防火牆存取

## <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>錯誤訊息：DF-SYS-01： .com. SQLServerException：資料庫中已經有名為 ' xxxxxx ' 的物件。

- **徵兆**：接收無法建立資料表

- **原因**︰目標資料庫中已有現有的資料表名稱，其名稱定義在您的來源或資料集中

- **解決方案**︰變更您嘗試建立之資料表的名稱



## <a name="next-steps"></a>後續步驟

如需更多疑難排解協助，請嘗試下列資源：

*  [Data Factory 的 blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
