---
title: 復原 Azure Stack 上的 Azure App Service | Microsoft Docs
description: Azure Stack App Service 災害復原的詳細指導方針
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: b034259dde817c863d976384b08da17983ed9de7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339730"
---
# <a name="recovery-of-app-service-on-azure-stack"></a>Azure Stack 上的 App Service 復原

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*  

本文件提供 App Service 災害復原所需動作的相關指示。

若要從備份復原 Azure Stack 上的 App Service，必須採取下列動作：
1.  還原 App Service 資料庫
2.  還原檔案伺服器共用內容
3.  還原 App Service 角色和服務

如果您使用 Azure Stack 儲存體來儲存函式應用程式，則也必須採取還原函式應用程式的步驟。

## <a name="restore-the-app-service-databases"></a>還原 App Service 資料庫
App Service SQL Server 資料庫應在可用於生產的 SQL Server 執行個體上還原。 

[準備好 SQL Server 執行個體](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance)來裝載 App Service 資料庫之後，請使用下列步驟從備份還原資料庫：

1. 以系統管理員權限登入要裝載已復原 App Service 資料庫的 SQL Server。
2. 在以系統管理員權限執行的命令提示字元中，使用下列命令還原 App Service 資料庫：
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. 請確認兩個 App Service 皆已成功還原，然後結束 SQL Server Management Studio。

> [!NOTE]
> 若要從容錯移轉叢集執行個體的錯誤中復原，請遵循[這些指示](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017)。 

## <a name="restore-the-app-service-file-share-content"></a>還原 App Service 檔案共用內容
[準備好檔案伺服器](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server)來裝載 App Service 檔案共用後，您需要從備份還原租用戶檔案共用內容。 您可以透過任何可用方法將檔案複製到新建立的 App Service 檔案共用位置。 在檔案伺服器上執行此範例將會使用 PowerShell 和 Robocopy 來連線到遠端共用，並將檔案複製到共用：

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

除了複製檔案共用內容之外，您也必須重設檔案共用本身的權限。 若要這樣做，請在檔案伺服器電腦上開啟系統管理命令提示字元，並執行 **ReACL.cmd** 檔案。 **ReACL.cmd** 檔案位於 **BCDR** 目錄下的 App Service 安裝檔案中。

## <a name="restore-app-service-roles-and-services"></a>還原 App Service 角色和服務
在還原 App Service 資料庫和檔案共用內容之後，接下來需要使用 PowerShell 還原 App Service 角色和服務。 以下步驟會還原 App Service 祕密和服務組態。  

1. 使用您在 App Service 安裝期間提供的密碼，以 **roleadmin** 身分登入 App Service 控制器 **CN0-VM** 虛擬機器。 
    > [!TIP]
    > 您將需要修改 VM 的網路安全性群組，以允許透過 RDP 連線。 
2. 將本機上的 **SystemSecrets.JSON** 檔案複製到控制站 VM。 在下一個步驟中，您必須提供此檔案的路徑來作為 `$pathToExportedSecretFile` 參數。 
3. 若要還原 App Service 角色和服務，請在提高權限的 PowerShell 主控台視窗中使用下列命令：

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> 強烈建議您在命令完成時關閉此 PowerShell 工作階段。

## <a name="restore-function-apps"></a>還原函式應用程式 
適用於 Azure Stack 的 App Service 不支援還原租用戶使用者應用程式，或是檔案共用內容以外的資料。 因此，這些項目必須從 App Service 備份和還原作業外部進行備份與復原。 如果使用 Azure Stack 儲存體來儲存函式應用程式，則應採取下列步驟來復原遺失的資料：

1. 建立新儲存體帳戶以供函式應用程式使用。 此儲存體可以是 Azure Stack 儲存體、Azure 儲存體或任何相容的儲存體。
2. 擷取儲存體的連接字串。
3. 開啟函式入口網站，並瀏覽至函式應用程式。
4. 瀏覽至 [平台功能] 索引標籤，然後按一下 [應用程式設定]。
5. 將 **AzureWebJobsDashboard** 和 **AzureWebJobsStorage** 變更為新的連接字串，然後按一下 [儲存]。
6. 切換至 [概觀]。
7. 重新啟動應用程式。 可能需要嘗多試幾次，才能清除所有錯誤。

## <a name="next-steps"></a>後續步驟
[Azure Stack 概觀上的 App Service](azure-stack-app-service-overview.md)