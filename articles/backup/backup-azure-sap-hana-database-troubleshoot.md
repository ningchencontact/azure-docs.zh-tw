---
title: 使用 Azure 備份來針對備份 SAP Hana 資料庫時的錯誤進行疑難排解 |Microsoft Docs
description: 說明如何針對使用 Azure 備份備份 SAP Hana 資料庫時可能發生的常見錯誤進行疑難排解。
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2019
ms.author: dacurwin
ms.openlocfilehash: 0512facbdf5f2222aee1e9bb5d2be64e22bf1a69
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774642"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>針對 Azure 上 SAP Hana 資料庫的備份進行疑難排解

本文提供在 Azure 虛擬機器上備份 SAP Hana 資料庫的疑難排解資訊。 下一節涵蓋診斷 SAP Hana 備份中常見錯誤所需的重要概念資料。

## <a name="prerequisites"></a>先決條件

作為[必要條件](backup-azure-sap-hana-database.md#prerequisites)的一部分, 請確定已在安裝 HANA 的虛擬機器上執行 preregistration 腳本。

### <a name="setting-up-permissions"></a>設定許可權

Preregistration 腳本的作用:

1. 在 HANA 系統中建立 AZUREWLBACKUPHANAUSER, 並新增下列必要的角色和許可權:
    - 資料庫管理員: 在還原期間建立新 Db。
    - 目錄讀取: 讀取備份類別目錄。
    - SAP_INTERNAL_HANA_SUPPORT: 存取幾個私用資料表。
2. 將金鑰新增至 Hdbuserstore, 以供 HANA 外掛程式處理所有作業 (資料庫查詢、還原作業、設定和執行備份)。
   
   若要確認金鑰建立, 請使用 SIDADM 認證在 HANA 電腦上執行到 HDBSQL 命令:

    ``` hdbsql
    hdbuserstore list
    ```
    
    命令輸出應該會顯示 {SID} {DBNAME} 金鑰, 且使用者會顯示為 AZUREWLBACKUPHANAUSER。

> [!NOTE]
> 請確定您在 **/usr/sap/{SID}/home/.hdb/** 下有一組唯一的 SSFS 檔案。 此路徑中應該只有一個資料夾。

### <a name="setting-up-backint-parameters"></a>設定 BackInt 參數

選擇資料庫進行備份之後, Azure 備份服務會在資料庫層級設定 backInt 參數:

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> 請確定這些參數*不*存在於主機層級。 主機層級的參數將會覆寫這些參數, 而且可能會導致非預期的行為。

## <a name="restore-checks"></a>還原檢查

### <a name="single-container-database-sdc-restore"></a>單一容器資料庫 (SDC) 還原

將 HANA 的單一容器資料庫 (SDC) 還原至另一部 SDC 機器時, 請注意輸入。 資料庫名稱應該以小寫形式提供, 並在括弧中附加 "sdc"。 HANA 實例會以大寫顯示。

假設已備份 SDC HANA 實例 "H21"。 [備份專案] 頁面會將備份專案名稱顯示為 **"h21 (sdc)"** 。 如果您嘗試將此資料庫還原到另一個目標 SDC (例如 H11), 則必須提供下列輸入。

![SDC 還原輸入](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

請注意下列事項
- 根據預設, 還原的資料庫名稱會填入備份專案名稱, 例如 h21 (sdc)
- 選取目標為 H11 時, 不會自動變更已還原的資料庫名稱。 **它應該會編輯為 h11 (sdc)** 。 在 SDC 的情況下, 還原的資料庫名稱會是小寫字母的目標實例識別碼, 而 ' SDC ' 會附加在方括弧中。
- 由於 SDC 只能有單一資料庫, 因此您也需要按一下核取方塊, 以允許以復原點資料覆寫現有的資料庫資料。
- Linux 會區分大小寫, 因此請務必保留大小寫。

### <a name="multiple-container-database-mdc-restore"></a>多個容器資料庫 (MDC) 還原

在 HANA 的多個容器資料庫中, 標準設定為 SYSTEMDB + 1 或更多租使用者 Db。 還原整個 SAP Hana 實例表示還原 SYSTEMDB 和租使用者 Db。 其中一個還原會先 SYSTEMDB, 然後再繼續進行租使用者資料庫。 系統資料庫基本上是指覆寫所選目標上的系統資訊。 這也會覆寫目標實例中的 BackInt 相關資訊。 因此, 在系統資料庫還原到目標實例之後, 必須再次執行預先註冊腳本。 只有之後, 後續的租使用者資料庫還原才會成功。

## <a name="common-user-errors"></a>一般使用者錯誤

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

資料| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 無法連接到 HANA 系統。 確認您的系統已啟動且正在執行。| Azure 備份服務無法連接到 HANA, 因為 HANA 資料庫已關閉。 或 HANA 正在執行, 但不允許 Azure 備份服務連接。 | 檢查 HANA 資料庫或服務是否已關閉。 如果 HANA 資料庫或服務已啟動且正在執行, 請檢查是否[已設定擁有權限](#setting-up-permissions)。 如果金鑰遺失, 請重新執行 preregistration 腳本以建立新的金鑰。 |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 偵測到不正確 Backint 設定。 停止保護並重新設定資料庫。| 未正確地指定 Azure 備份的 backInt 參數。 | 檢查是否[已設定參數](#setting-up-backint-parameters)。 如果主機中有以 backInt 為基礎的參數, 請將它們移除。 如果主機層級沒有參數, 但已在資料庫層級手動修改, 請將它們還原為適當的值, 如先前所述。 或者, 執行 [**停止保護] 並保留**Azure 入口網站中的備份資料, 然後選取 [**繼續備份**]。|