---
title: 備份 Azure Stack 上的 Azure App Service | Microsoft Docs
description: Azure Stack App Service 備份的詳細指導方針。
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
ms.openlocfilehash: 66f1f1389a7e4ceebc38544f2eb9836fad2bd96a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339742"
---
# <a name="back-up-app-service-on-azure-stack"></a>備份 Azure Stack 上的 App Service

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*  

此文件將說明如何備份 Azure Stack 上的 App Service。

> [!IMPORTANT]
> Azure Stack 上的 App Service 備份並不屬於 [Azure Stack 基礎結構備份](azure-stack-backup-infrastructure-backup.md)的一部份。 身為 Azure Stack 操作員，您必須採取一些步驟，以確保 App Service 可以在必要時成功復原。

在為 Azure Stack 上的 Azure App Service 規劃災害復原時，有四個要考慮的主要元件：
1. 資源提供者基礎結構；伺服器角色、背景工作角色層等。 
2. App Service 祕密
3. 裝載與測量資料庫的 App Service SQL Server
4. App Service 使用者的工作負載內容，儲存於 App Service 檔案共用   

## <a name="back-up-app-service-secrets"></a>備份 App Service 祕密
從備份復原 App Service 時，您需要提供初次部署使用的 App Service 金鑰。 當 App Service 成功部署並儲存在安全的位置時，應儲存此資訊。 資源提供者基礎結構組態會在使用 App Service 復原 PowerShell Cmdlet 進行復原的期間，從備份中重建。

請依照下列步驟，使用系統管理入口網站來備份應用程式服務的祕密： 

1. 以服務管理員身分登入 Azure Stack 管理入口網站。

2. 瀏覽至 [App Service] -> [祕密]。 

3. 選取 [下載祕密]。

   ![下載祕密](./media/app-service-back-up/download-secrets.png)

4. 當祕密可下載時，按一下 [儲存]，並將 App Service 祕密 (**SystemSecrets.JSON**) 檔案儲存在安全的位置。 

   ![儲存祕密](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> 請在每次替換 App Service 祕密時重複這些步驟。

## <a name="back-up-the-app-service-databases"></a>備份 App Service 資料庫
若要還原 App Service，您需要 **Appservice_hosting** 和 **Appservice_metering** 資料庫備份。 我們建議您使用 SQL Server 維護方案或 Azure 備份伺服器，以確保這些資料庫會定期且安全地進行備份及儲存。 不過，任何確定會定期建立 SQL 備份的方式皆可使用。

若要在登入 SQL Server 時手動備份這些資料庫，您可以使用下列 PowerShell 命令：

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> 如果您必須備份 SQL AlwaysOn 資料庫，請遵循[這些指示](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017)。 

成功備份所有資料庫之後，請將 .bak 檔案和 App Service 祕密資訊複製到安全的位置。

## <a name="back-up-the-app-service-file-share"></a>備份 App Service 檔案共用
App Service 會將租用戶應用程式資訊儲存在檔案共用中。 這必須與 App Service 資料庫一起定期備份，才能在需要還原時盡可能減少資料遺失。 

若要備份 App Service 檔案共用內容，您可以使用 Azure 備份伺服器或其他方法，定期地將檔案共用內容複製到儲存上述所有復原資訊的位置。 

例如，您可以使用這些步驟，從 Windows PowerShell (不是 PowerShell ISE) 主控台工作階段使用 Robocopy：

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>後續步驟
[還原 Azure Stack 上的 App Service](app-service-recover.md)