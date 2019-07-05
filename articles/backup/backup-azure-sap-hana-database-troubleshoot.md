---
title: 針對錯誤進行疑難排解時使用 Azure 備份的 SAP HANA 資料庫的備份 |Microsoft Docs
description: 本指南說明如何疑難排解常見的錯誤時嘗試使用 Azure 備份的備份 SAP HANA 資料庫。
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 33f1b4674aad35d55ab014c45cd73753533931cb
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514179"
---
# <a name="troubleshoot-back-up-of-sap-hana-server-on-azure"></a>在 Azure 上的 SAP HANA 伺服器的備份進行疑難排解

本文章提供疑難排解資訊保護 Azure 虛擬機器上的 SAP HANA 資料庫。 疑難排解之前，讓我們了解權限和設定相關的一些重點。

## <a name="understanding-pre-requisites"></a>了解必要條件

做為一部分[必要條件](backup-azure-sap-hana-database.md#prerequisites)，預先註冊指令碼應該執行虛擬機器上安裝 HANA 來設定正確的權限。

### <a name="setting-up-permissions"></a>設定權限

預先註冊指令碼的用途：

1. 建立 AZUREWLBACKUPHANAUSER HANA 系統中，並加入必要的角色和權限，如下所示：
    - 資料庫管理員-建立新的資料庫在還原期間
    - 目錄讀取： 讀取備份類別目錄
    - SAP_INTERNAL_HANA_SUPPORT – 若要存取幾個私用的資料表
2. 將金鑰新增至 Hdbuserstore HANA 外掛程式來執行所有作業 （查詢的資料庫中設定的備份、 執行備份、 還原）
   
   - 若要確認建立金鑰，執行 HDBSQL 命令 HANA 機器內使用 SIDADM 認證：

    ``` hdbsql
    hdbuserstore list
    ```
    
    命令輸出應該顯示使用者的金鑰 {SID} {DBNAME} 為 'AZUREWLBACKUPHANAUSER'。

> [!NOTE]
> 請確定您有一組唯一 SSFS 下的檔案路徑"/ usr/sap/{SID}/home/.hdb/"。 應該要有此路徑下的只能有一個資料夾。

### <a name="setting-up-backint-parameters"></a>設定 BackInt 參數

一旦資料庫選擇的備份時，Azure 備份服務會在資料庫層級設定 backInt 參數。

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> 請確定這些參數都不是出現在主機層級。 主機層級的參數會覆寫這些參數，而且比預期可能會導致不同的行為。

## <a name="understanding-common-user-errors"></a>了解常見使用者錯誤

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 無法連線到您的系統已啟動並執行的 HANA system.check。| Azure 備份服務不能夠連線到 HANA，因為 HANA DB 已關閉。 或 HANA 正在執行，但不是允許連線的 Azure 備份服務 | 請檢查 HANA DB/服務是否按下。 如果 HANA DB/服務已啟動並執行，請檢查如果所有設定權限會如所述[此處](#setting-up-permissions)。 如果遺漏的索引鍵，重新執行的預先註冊指令碼，以建立新的金鑰。 |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 偵測到無效的 Backint 組態。 停止保護，然後重新設定資料庫。| 適用於 Azure 備份不正確地指定 backInt 參數。 | 檢查參數是否如所述[此處](#setting-up-backint-parameters)。 如果基礎 backInt 參數出現在主應用程式，然後移除它們。 如果參數不會出現在主機上，但已經過手動修改的資料庫層級，然後還原為適當的值如先前所述。 停止保護保留資料 從 Azure 入口網站和 繼續備份 或一次一次。|
