---
title: App Service on Azure Stack 更新 4 版本資訊 | Microsoft Docs
description: 了解適用於 App Service on Azure Stack 更新 4 的新功能、已知問題，以及可下載更新的位置。
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: c8900e639e6baba29067c50d4ac754d4dbda2dd6
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449230"
---
# <a name="app-service-on-azure-stack-update-4-release-notes"></a>App Service on Azure Stack 更新 4 版本資訊

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

這些版本資訊說明 Azure App Service on Azure Stack 更新 4 的增強功能和修正，以及任何已知問題。 已知問題分為直接與部署、更新程序相關的問題，以及組建 (安裝後) 的相關問題。

> [!IMPORTANT]
> 在部署 Azure App Service 1.4 之前，請先將 1809 更新套用到您的 Azure Stack 整合式系統，或部署最新的 Azure Stack 開發套件。
>
>

## <a name="build-reference"></a>建置參考

App Service on Azure Stack 更新 4 組建編號是 **78.0.13698.5**

### <a name="prerequisites"></a>必要條件

開始部署之前，請參閱[「在您開始之前」文件](azure-stack-app-service-before-you-get-started.md)。

開始將 Azure App Service on Azure Stack 升級至 1.4 之前：

- 確定 Azure Stack 管理入口網站的 Azure App Service 管理中所有角色都已就緒

- 備份 App Service 和 Master 資料庫：
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- 備份租用戶應用程式內容檔案共用

- 從 Marketplace 同步發佈自訂指令碼擴充功能 1.9 版

### <a name="new-features-and-fixes"></a>新功能和修正

Azure App Service on Azure Stack 更新 4 包含下列改良功能和修正：

- [CVE 2018-8600](https://aka.ms/CVE20188600) 跨網站指令碼弱點的解決方案。

- 已新增對 App Service 2018-02-01 API 版本的支援

- **App Service 租用戶、系統管理員、Functions 入口網站和 Kudu 工具**的更新。 與 Azure Stack 入口網站 SDK 版本保持一致。

- 將 **Azure Functions 執行階段**更新至 **v1.0.11959**。

- 用來改善可靠性和錯誤訊息以利進行常見問題診斷的核心服務更新。

- **下列應用程式架構和工具的更新**：
  - Added NodeJS 10.6.0
  - Added NPM 6.1.0
  - Added Zulu OpenJDK 8.31.0.2
  - 已新增 Tomcat 8.5.34 和 9.0.12
  - 新增 PHP 版本︰
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - Python 版本更新：
    - 2.7.15
    - 3.6.6
  - 將 Git for Windows 更新為 v 2.17.1.2
  - 將 Kudu 更新為 78.11022.3613
  
- **所有角色的基礎作業系統更新**：
  - [適用於 Windows Server 2016 (x64 型系統) 的 2018-10 累積更新 (KB4462928)](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- 已解決部署 Wordpress、DNN 和 Orchard CMS 資源庫項目時的範本驗證問題

- 已解決 Azure Stack 輪替 Azure Resource Manager 用戶端憑證時的組態問題

- 已還原 App Service 租用戶入口網站中跨來源資源共用設定中的功能

- 當資源提供者控制平面無法連線到所設定的 SQL Server 執行個體時，在 App Service 管理員入口網站體驗中顯示錯誤訊息

- 確定在新的函式應用程式中指定結束點時，也會在自訂儲存體連接字串中指定結束點

### <a name="post-deployment-steps"></a>部署後步驟

> [!IMPORTANT]  
> 如果您已對 App Service RP 提供 SQL Always On 執行個體，就必須[將 appservice_hosting 和 appservice_metering 資料庫新增至可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)並同步處理資料庫，以避免在資料庫進行容錯移轉時中斷服務。

### <a name="post-update-steps-optional"></a>更新後的步驟 (選擇性)

對於想要遷移至 Azure Stack 部署上現有 Azure App Service 自主資料庫的客戶，請在 Azure App Service on Azure Stack 1.4 更新完成之後，執行這些步驟：

> [!IMPORTANT]
> 移轉程序需要大約 5-10 分鐘才能完成。  此程序可能會終止現有的資料庫登入工作階段。  請規劃停機時間，以遷移 Azure App Service on Azure Stack，並在遷移後進行驗證。  如果在更新至 Azure App Service on Azure Stack 1.3 之後完成這些步驟，則不需要這些步驟。
>
>

1. 將 [AppService 資料庫 (appservice_hosting and appservice_metering) 新增至可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. 啟用自主資料庫
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. 將資料庫轉換為「部分自主」，此轉換會造成停機，因為所有作用中的工作階段都必須終止

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate Logins to Contained Database Users

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

驗證

1. 檢查 SQL Server 是否已啟用內含項目

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. 檢查現有的自主行為
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>已知問題 (安裝後)

- 將應用程式服務部署在現有的虛擬網路中且只能在私人網路上使用檔案伺服器時，背景工作角色無法連線到檔案伺服器，如 Azure App Service on Azure Stack 部署文件中所述。

如果您選擇要部署到現有的虛擬網路並以內部 IP 位址連線到檔案伺服器，便必須新增輸出安全性規則，以啟用背景工作角色子網路與檔案伺服器之間的 SMB 流量。 移至管理入口網站中的 WorkersNsg，然後使用下列屬性新增輸出安全性規則：
 * 來源：任意
 * 來源連接埠範圍：*
 * 目的地：IP 位址
 * 目的地 IP 位址範圍：檔案伺服器的 IP 範圍
 * 目的地連接埠範圍：445
 * 通訊協定：TCP
 * 動作：允許
 * 優先順序：700
 * 名稱：Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>雲端管理員操作 Azure App Service on Azure Stack 時的已知問題

請參閱 [Azure Stack 1809 版本資訊](azure-stack-update-1809.md)中的文件

## <a name="next-steps"></a>後續步驟

- 如需 Azure App Service 的概觀，請參閱 [Azure App Service on Azure Stack 概觀](azure-stack-app-service-overview.md)。
- 如需如何準備部署 App Service on Azure Stack 的詳細資訊，請參閱[開始使用 App Service on Azure Stack 之前](azure-stack-app-service-before-you-get-started.md)。
