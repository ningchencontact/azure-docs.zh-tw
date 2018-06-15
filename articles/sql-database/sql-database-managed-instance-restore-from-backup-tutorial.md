---
title: 將備份還原至 Azure SQL Database 受控執行個體 | Microsoft Docs
description: 使用 SSMS 將資料庫備份還原至 Azure SQL Database 受控執行個體。
keywords: SQL Database 教學課程, SQL Database 受控執行個體, 還原備份
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 06/07/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: c6f397c2cfcfd9fbd4a5cc8f70a99f4abc86ba21
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "34850673"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>將資料庫備份還原至 Azure SQL Database 受控執行個體

本教學課程示範如何使用 Wide World Importers - 標準備份檔案，將 Azure Blob 儲存體中儲存的資料庫備份還原至受控執行個體。 此方法需要一些停機時間。 如需使用 Azure 資料庫移轉服務 (DMS) 進行移轉的教學課程，請參閱[使用 DMS 的受控執行個體移轉](../dms/tutorial-sql-server-to-managed-instance.md)。 如需各種移轉方法的討論，請參閱[將 SQL Server 移轉至 Azure SQL Database 受控執行個體](sql-database-managed-instance-migrate.md)。

> [!div class="checklist"]
> * 下載 Wide World Importers - 標準備份檔案
> * 建立 Azure 儲存體帳戶及上傳備份檔案
> * 從備份檔案還原 Wide World Importers 資料庫

## <a name="prerequisites"></a>先決條件

本教學課程可作為本教學課程中建立的資源起點：[建立 Azure SQL Database 受控執行個體](sql-database-managed-instance-create-tutorial-portal.md)。

## <a name="download-the-wide-world-importers---standard-backup-file"></a>下載 Wide World Importers - 標準備份檔案

使用下列步驟來下載 Wide World Importers - 標準備份檔案。

使用 Internet Explorer，在 URL 位址方塊中輸入 https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak，然後在出現提示時，按一下 [儲存] 將此檔案儲存在 [下載] 資料夾中。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/#create/Microsoft.SQLManagedInstance)。

## <a name="create-azure-storage-account-and-upload-backup-file"></a>建立 Azure 儲存體帳戶及上傳備份檔案

1. 按一下 Azure 入口網站左上角的 [建立資源]。
2. 找出 [儲存體]，然後按一下 [儲存體帳戶] 以開啟儲存體帳戶表單。

   ![storage account](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. 使用下表中的資訊，填妥儲存體帳戶表單中所要求的資訊：

   | 設定| 建議的值 | 說明 |
   | ------ | --------------- | ----------- |
   |**名稱**|任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
   |**部署模型**|資源模型||
   |**帳戶類型**|Blob 儲存體||
   |**效能**|標準或進階|磁碟機或 SSD|
   |**複寫**|本地備援儲存體||
   |**存取層 (預設值)|非經常性或經常性||
   |**需要安全傳輸**|已停用||
   |**訂用帳戶**|您的訂用帳戶|如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。|
   |**資源群組**|您先前建立的資源群組|| 
   |**位置**|您先前選取的位置||
   |**虛擬網路**|已停用||

4. 按一下頁面底部的 [新增] 。

   ![儲存體帳戶詳細資料](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. 在儲存體帳戶部署成功之後，開啟新的儲存體帳戶。
6. 按一下 [設定] 之下的 [共用存取簽章]，以開啟共用存取簽章 (SAS) 表單。

   ![sas 表單](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. 在 SAS 表單上，視需要修改預設值。 請注意，到期日期/時間預設只有 8 小時。
8. 按一下 [產生 SAS]。

   ![完成的 sas 表單](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. 複製並儲存 [SAS 權杖] 和 [Blob 伺服器 SAS URL]。
10. 按一下 [設定] 下方的 [容器]。

    ![containers](./media/sql-database-managed-instance-tutorial/containers.png)

11. 按一下 [+容器]，建立容器來保存備份檔案。
12. 使用下表中的資訊，填妥容器表單中所要求的資訊：

    | 設定| 建議的值 | 說明 |
   | ------ | --------------- | ----------- |
   |**名稱**|任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。|
   |**公用存取層級**|私人 (沒有匿名存取)||

    ![容器詳細資料](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. 按一下 [確定]。
14. 建立容器之後，請開啟該容器。

    ![container](./media/sql-database-managed-instance-tutorial/container.png)

15. 按一下 [容器屬性]，然後將 URL 複製到容器。

    ![容器 URL](./media/sql-database-managed-instance-tutorial/container-url.png)

16. 按一下 [上傳] 以開啟 [上傳 Blob] 表單。

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. 瀏覽至您的下載資料夾，然後選取 **WideWorldIimporters-Standard.bak** 檔案。

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. 按一下 [上傳] 。
19. 直到上傳完成，才能繼續執行。

    ![上傳完成](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>從備份檔案還原 Wide World Importers 資料庫

透過 SSMS，使用下列步驟將 Wide World Importers 資料庫從備份檔案還原到您的受控執行個體。

1. 在 SSMS 中，開啟新的查詢視窗。
2. 使用下列指令碼來建立 SAS 認證 - 依照指示提供儲存體帳戶容器的 URL 和 SAS 金鑰。

   ```sql
   CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
      WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
      , SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![認證](./media/sql-database-managed-instance-tutorial/credential.png)

3. 使用下列指令碼來檢查 SAS 認證和備份有效性 - 提供含有備份檔案之容器的 URL：

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![檔案清單](./media/sql-database-managed-instance-tutorial/file-list.png)

4. 使用下列指令碼從備份檔案還原 Adventure Works 2012 資料庫 - 提供含有備份檔案之容器的 URL：

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![還原執行中](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. 若要追蹤還原的狀態，請在新的查詢工作階段中執行下列查詢：

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![還原完成百分比](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. 當還原完成時，請在 [物件總管] 加以檢視。 

    ![還原完成](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Wide World Importers - 標準備份檔案，將 Azure Blob 儲存體中儲存的資料庫備份還原至受控執行個體。 您已了解如何︰ 

> [!div class="checklist"]
> * 下載 Wide World Importers - 標準備份檔案
> * 建立 Azure 儲存體帳戶及上傳備份檔案
> * 從備份檔案還原 Wide World Importers 資料庫

前進到下一個教學課程，以了解如何使用 DMS 將 SQL Server 移轉至 Azure SQL Database 受控執行個體。

> [!div class="nextstepaction"]
>[使用 DMS 將 SQL Server 移轉至 Azure SQL Database 受控執行個體](../dms/tutorial-sql-server-to-managed-instance.md)