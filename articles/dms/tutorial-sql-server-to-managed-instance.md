---
title: 使用 DMS 遷移至 Azure SQL Database 受控執行個體 | Microsoft Docs
description: 了解如何使用 Azure 資料庫移轉服務，從內部部署 SQL Server 遷移至 Azure SQL Database 受控執行個體。
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/07/2018
ms.openlocfilehash: 86af0101d84fe9cd44211a931567a85d7b5166e0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261605"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>使用 DMS 將 SQL Server 遷移至 Azure SQL Database 受控執行個體
您可以使用 Azure 資料庫移轉服務，將內部部署 SQL Server 執行個體的資料庫遷移至 [Azure SQL Database 受控執行個體](../sql-database/sql-database-managed-instance.md)。 如需其他可能需要手動操作的方法，請參閱[將 SQL Server 執行個體遷移至 Azure SQL Database 受控執行個體](../sql-database/sql-database-managed-instance-migrate.md)一文。

> [!IMPORTANT]
> 將專案從 SQL Server 移轉至 Azure SQL Database 受控執行個體目前為預覽狀態，而且必須遵循 [Microsoft Azure 預覽補充使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在本教學課程中，您要使用 Azure 資料庫移轉服務，將 **Adventureworks2012** 資料庫從內部部署 SQL Server 執行個體遷移至 Azure SQL Database 受控執行個體。

在本教學課程中，您了解如何：
> [!div class="checklist"]
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 使用 Azure 資料庫移轉服務來建立移轉專案。
> * 執行移轉。
> * 監視移轉。
> * 下載移轉報告。

## <a name="prerequisites"></a>先決條件
若要完成本教學課程，您需要：

- 使用 Azure Resource Manager 部署模型來建立 Azure 資料庫移轉服務的 VNET，其使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 提供站對站連線能力給您的內部部署來源伺服器。 [了解使用 Azure 資料庫移轉服務進行 Azure SQL DB 受控執行個體移轉的網路拓樸](https://aka.ms/dmsnetworkformi)。
- 確定您的 Azure 虛擬網路 (VNET) 網路安全性群組規則不會封鎖下列通訊埠 443、53、9354、445、12000。 如需 Azure VNET NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)。
- 設定[用於來源資料庫引擎存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 SQL Server，其預設會通過 TCP 連接埠 1433。
- 如果您使用動態連接埠執行多個具名 SQL Server 執行個體，也許會想要啟用 SQL Browser 服務並允許通過防火牆存取 UDP 連接埠 1434，讓 Azure 資料庫移轉服務連線來源伺服器上的具名執行個體。
- 如果您是在來源資料庫前面使用防火牆設備，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取用於進行移轉的來源資料庫，以及透過 SMB 連接埠 445 存取檔案。
- 依照[在 Azure 入口網站中建立 Azure SQL Database 受控執行個體](https://aka.ms/sqldbmi) \(英文\) 一文中的詳細資料，建立 Azure SQL Database 受控執行個體。
- 確定用來連線來源 SQL Server 和目標受控執行個體的登入是 sysadmin 伺服器角色的成員。
- 建立 Azure 資料庫移轉服務可用來備份來源資料庫的網路共用。
- 確認執行來源 SQL Server 執行個體的服務帳戶在您所建立的網路共用上具有寫入權限，而且來源伺服器的電腦帳戶對相同的共用具備讀取/寫入存取權。
- 記下在您先前建立的網路共用上具有完整控制權限的 Windows 使用者 (和密碼)。 Azure 資料庫移轉服務會模擬該使用者認證，以便將備份檔案上傳至 Azure 儲存體容器以進行還原作業。
- 使用[使用儲存體總管來管理 Azure Blob 儲存體資源](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) 一文中的步驟來建立 blob 容器及取出其 SAS URI；在建立 SAS URI 時，請務必選取原則視窗上的所有權限 (讀取、寫入、刪除、列出)。 此詳細資料會向 Azure 資料庫移轉服務提供您儲存體帳戶容器的存取權，以供上傳用於將資料庫移轉至 Azure SQL Database 受控執行個體的備份檔案

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]，然後選取 [訂用帳戶]。

    ![顯示入口網站訂用帳戶](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]。

    ![顯示資源提供者](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. 搜尋移轉，然後在 [Microsoft.DataMigration] 的右邊，選取 [註冊]。

    ![註冊資源提供者](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>建立 Azure 資料庫移轉服務執行個體

1. 在 Azure 入口網站中，選取 [+ 建立資源]，搜尋 [Azure 資料庫移轉服務]，然後從下拉式清單選取 [Azure 資料庫移轉服務]。

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務] 畫面上，選取 [建立]。

    ![建立 Azure 資料庫移轉服務執行個體](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. 在 [建立移轉服務] 畫面上，指定服務的名稱、訂用帳戶，以及新的或現有的資源群組。

4. 選取現有的虛擬網路 (VNET) 或建立虛擬網路。
 
    VNET 會為 Azure 資料庫移轉服務提供來源 SQL Server 和目標 Azure SQL Database 受控執行個體的存取權。

    如需有關如何在 Azure 入口網站中建立 VNET 的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](https://aka.ms/DMSVnet)一文。

    如需其他詳細資訊，請參閱[了解使用 Azure 資料庫移轉服務進行 Azure SQL DB 受控執行個體移轉的網路拓樸](https://aka.ms/dmsnetworkformi)一文。

5. 選取定價層。

    如需成本和定價層的詳細資訊，請參閱[定價分頁](https://aka.ms/dms-pricing)。
   
    ![建立 DMS 服務](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  選取 [建立] 以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]。

    ![找出 Azure 資料庫移轉服務的所有執行個體](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. 在 [Azure 資料庫移轉服務] 畫面上，搜尋您建立的執行個體名稱，然後選取該執行個體。
 
3. 選取 [+ 新增移轉專案]。

4. 在 [新增移轉專案] 畫面上，於 [來源伺服器類型] 文字方塊中指定專案名稱、選取 [SQL Server]，然後在 [目標伺服器類型]文字方塊中，選取 [Azure SQL Database 受控執行個體]。

   ![建立 DMS 專案](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. 選取 [Create] \(建立\) 以建立專案。

## <a name="specify-source-details"></a>指定來源詳細資料

1. 在 [Source details] \(來源詳細資料\) 畫面上，指定來源 SQL Server 的連線詳細資料。

2. 如果您尚未在伺服器上安裝信任的憑證，選取 [信任伺服器憑證] 核取方塊。

    未安裝信任的憑證時，SQL Server 會在執行個體啟動時，產生自我簽署憑證。 此憑證用來加密用戶端連線的認證。

    > [!CAUTION]
    > 使用自我簽署憑證加密的 SSL 連線不會提供增強式安全性。 這種連線容易受到攔截式攻擊。 在生產環境中，或在連線到網際網路的伺服器上，您不應該仰賴使用自我簽署憑證的 SSL。

   ![來源詳細資料](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. 選取 [ **儲存**]。

4. 在 [選取來源資料庫] 畫面上，選取 [Adventureworks2012] 資料庫進行移轉。

   ![選取來源資料庫](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. 選取 [ **儲存**]。

## <a name="specify-target-details"></a>指定目標詳細資料

1.  在 [目標詳細資料] 畫面上，指定目標的連線詳細資料，這個目標是 [AdventureWorks2012] 資料庫所要遷移前往的預先佈建 Azure SQL Database 受控執行個體。

    如果您尚未佈建 Azure SQL Database 受控執行個體，請選取 [否]，以取得可協助您佈建執行個體的連結。 您仍然可以繼續建立專案，然後在 Azure SQL Database 受控執行個體準備就緒時，返回此特定專案來執行移轉。   
 
       ![選取目標](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  選取 [ **儲存**]。

3.  在 [專案摘要] 畫面上，檢閱並確認與移轉專案相關聯的詳細資料。
 
    ![移轉專案摘要](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  選取 [ **儲存**]。   

## <a name="run-the-migration"></a>執行移轉

1.  依序選取最近儲存的專案、[+ 新增活動] 以及 [執行移轉]。

    ![新建活動](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  出現提示時，請輸入來源伺服器與目標伺服器的認證，然後選取 [儲存]。

3.  在 [選取來源資料庫] 畫面上，選取您要遷移的來源資料庫。

    ![選取來源資料庫](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases1.png)

4.  選取 [儲存]，然後在 [設定移轉設定] 畫面上，提供下列詳細資料：

    | | |
    |--------|---------|
    |**網路位置共用** | Azure 資料庫移轉服務可將來源資料庫備份移入的本機網路共用。 執行來源 SQL Server 執行個體的服務帳戶在此網路共用上必須具有寫入權限。 在網路共用中提供伺服器的 FQDN 或 IP 位址，例如，'\\\servername.domainname.com\backupfolder' 或 '\\\IP address\backupfolder'。|
    |**使用者名稱** | 可供 Azure 資料庫移轉服務模擬並將備份檔案上傳至 Azure 儲存體容器以進行還原作業的 Windows 使用者名稱。 |
    |**密碼** | 使用者的密碼。 |
    |**儲存體帳戶設定** | 此 SAS URI 會向 Azure 資料庫移轉服務提供您儲存體帳戶容器的存取權，此容器會作為服務上傳備份檔案時的目的地，且會用於將資料庫遷移至 Azure SQL Database 受控執行個體。 [了解如何取得 Blob 容器的 SAS URI](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)。|
    
    ![設定移轉設定](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings1.png)

5.  選取 [儲存]，在 [移轉摘要] 畫面的 [活動名稱] 文字方塊中，指定移轉活動的名稱。

6. 展開 [驗證選項] 區段以顯示 [選擇驗證選項] 畫面，指定是否驗證已遷移資料庫的查詢正確性，然後選取 [儲存]。  

    ![移轉摘要](media\tutorial-sql-server-to-managed-instance\dms-migration-summary1.png)

7. 選取 [執行移轉]。

    [移轉活動] 視窗隨即出現，而且活動的狀態為 [擱置]。

   ![移轉活動擱置](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-pending.png)

## <a name="monitor-the-migration"></a>監視移轉

1. 在移轉活動畫面上，選取 [重新整理] 以更新顯示，直到您看到移轉的狀態顯示為 [已完成] 為止。
 
   ![移轉活動已完成](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-finished.png)

2. 移轉完成之後，請選取 [下載報告] 以取得報告，其中會列出與移轉程序相關聯的詳細資料。
 
3. 確認目標 Azure SQL Database 受控執行個體環境上的目標資料庫。

## <a name="next-steps"></a>後續步驟

- 如需如何使用 T-SQL 還原命令將資料庫遷移至受控執行個體的教學課程，請參閱[使用還原命令將備份還原到受控執行個體](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)。
- 如需受控執行個體的詳細資訊，請參閱[受控執行個體是什麼](../sql-database/sql-database-managed-instance.md)。
- 如需將應用程式連線至受控執行個體的相關資訊，請參閱[應用程式連線](../sql-database/sql-database-managed-instance-connect-app.md)。
