---
title: 使用 Azure 資料庫移轉服務將 SQL Server 遷移至 Azure SQL Database 受控執行個體 | Microsoft Docs
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
ms.date: 03/29/2018
ms.openlocfilehash: 8abf3bae3a2274ed5514a5c621675b4c9ec27ae2
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance"></a>將 SQL Server 遷移至 Azure SQL Database 受控執行個體
您可以使用 Azure 資料庫移轉服務，將內部部署 SQL Server 執行個體的資料庫移轉到 Azure SQL Database。 在本教學課程中，您要使用 Azure 資料庫移轉服務，將 **Adventureworks2012** 資料庫從內部部署 SQL Server 執行個體遷移至 Azure SQL Database。

在本教學課程中，您了解如何：
> [!div class="checklist"]
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 使用 Azure 資料庫移轉服務來建立移轉專案。
> * 執行移轉。
> * 監視移轉。

## <a name="prerequisites"></a>先決條件
若要完成本教學課程，您需要：

- 使用 Azure Resource Manager 部署模型來建立 Azure 資料庫移轉服務的 VNET，其使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 提供站對站連線能力給您的內部部署來源伺服器。 [了解使用 Azure 資料庫移轉服務進行 Azure SQL DB 受控執行個體移轉的網路拓樸](https://aka.ms/dmsnetworkformi)。
- 確定您的 Azure 虛擬網路 (VNET) 網路安全性群組規則不會封鎖下列通訊埠 443、53、9354、445、12000。 如需 Azure VNET NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg)。
- 設定[用於來源資料庫引擎存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 SQL Server，其預設會通過 TCP 連接埠 1433。
- 如果您使用動態連接埠執行多個具名 SQL Server 執行個體，也許會想要啟用 SQL Browser 服務並允許通過防火牆存取 UDP 連接埠 1434，讓 Azure 資料庫移轉服務連線來源伺服器上的具名執行個體。
- 如果您是在來源資料庫前面使用防火牆設備，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取用於進行移轉的來源資料庫，以及透過 SMB 連接埠 445 存取檔案。
- 依照[在 Azure 入口網站中建立 Azure SQL Database 受控執行個體](https://aka.ms/sqldbmi)一文中的詳細資料來建立 Azure SQL Database 受控執行個體的執行個體。
- 確定用來連線來源 SQL Server 和目標受控執行個體的登入是 sysadmin 伺服器角色的成員。
- 建立 Azure 資料庫移轉服務可用來備份來源資料庫的網路共用。
- 確定執行來源 SQL Server 執行個體的服務帳戶在您所建立的網路共用上具有寫入權限。
- 記下在您上面所建立的網路共用上具有完整控制權限的 Windows 使用者 (和密碼)。 Azure 資料庫移轉服務會模擬該使用者認證，以便將備份檔案上傳至 Azure 儲存體容器以進行還原作業。
- 使用[使用儲存體總管來管理 Azure Blob 儲存體資源 (預覽)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) 一文中的步驟來建立 blob 容器及取出其 SAS URI；在建立 SAS URI 時，請務必選取原則視窗上的所有權限 (讀取、寫入、刪除、列出)。 這會向 Azure 資料庫移轉服務提供您儲存體帳戶容器的存取權，以供上傳用於將資料庫移轉至 Azure SQL Database 受控執行個體的備份檔案

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1.  登入 Azure 入口網站，選取 [所有服務]，然後選取 [訂用帳戶]。
![顯示入口網站訂用帳戶](media\tutorial-sql-server-to-managed-instance\portal-select-subscription.png)

1.  選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]。
![顯示資源提供者](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

1.  搜尋移轉，然後在 [Microsoft.DataMigration] 的右邊，選取 [註冊]。
![註冊資源提供者](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>建立執行個體

1.  在 Azure 入口網站中，選取 [+ 建立資源]，搜尋 [Azure 資料庫移轉服務]，然後從下拉式清單選取 [Azure 資料庫移轉服務]。

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

1.  在 [Azure 資料庫移轉服務 (預覽)] 畫面上，選取 [建立]。

    ![建立 Azure 資料庫移轉服務執行個體](media\tutorial-sql-server-to-managed-instance\dms-create.png)

1.  在 [資料庫移轉服務] 畫面上，指定服務的名稱、訂用帳戶、資源群組、虛擬網路和定價層。

    如需成本和定價層的詳細資訊，請參閱[定價分頁](https://aka.ms/dms-pricing)。 *Azure 資料庫移轉服務目前為預覽狀態，所以不會向您收費。*

    **網路：**選取現有 VNET 或建立新的 VNET，以向 Azure 資料庫移轉服務提供來源 SQL Server 和目標 Azure SQL Database 受控執行個體的存取權。 [了解使用 Azure 資料庫移轉服務進行 Azure SQL DB 受控執行個體移轉的網路拓樸](https://aka.ms/dmsnetworkformi)。

    如需有關如何在 Azure 入口網站中建立 VNET 的詳細資訊，請參閱[使用 Azure 入口網站建立具有多個子網路的虛擬網路](https://aka.ms/DMSVnet)。

    ![建立 DMS 服務](media\tutorial-sql-server-to-managed-instance\dms-create-service.png)

1.  選取 [建立] 以建立服務。


## <a name="create-a-migration-project"></a>建立移轉專案

在服務建立之後，請在 Azure 入口網站中找到它，然後將它開啟。

1.  選取 [+ New Migration Project] \(+ 新增移轉專案\)。

1.  在 [新增移轉專案] 畫面上，於 [來源伺服器類型] 文字方塊中指定專案名稱、選取 [SQL Server]，然後在 [目標伺服器類型]文字方塊中，選取 [Azure SQL Database 受控執行個體]。

    ![建立 DMS 專案](media\tutorial-sql-server-to-managed-instance\dms-create-project.png)

1.  選取 [Create] \(建立\) 以建立專案。

## <a name="specify-source-details"></a>指定來源詳細資料

1.  在 [Source details] \(來源詳細資料\) 畫面上，指定來源 SQL Server 的連線詳細資料。

    ![來源詳細資料](media\tutorial-sql-server-to-managed-instance\dms-source-details.png)

1.  選取 [儲存]，然後選取 [Adventureworks2012] 資料庫進行移轉。

    ![選取來源資料庫](media\tutorial-sql-server-to-managed-instance\dms-source-database.png)

## <a name="specify-target-details"></a>指定目標詳細資料

1.  選取 [儲存]，然後在 [目標詳細資料] 畫面上指定目標的連線詳細資料，這個目標是 [AdventureWorks2012] 資料庫所要遷移前往的預先佈建 Azure SQL Database 受控執行個體。

    ![選取目標](media\tutorial-sql-server-to-managed-instance\dms-target-details.png)

1.  選取 [ **儲存**]。

1.  在 [專案摘要] 畫面上，檢閱並確認與移轉專案相關聯的詳細資料。

## <a name="run-the-migration"></a>執行移轉

1.  依序選取最近儲存的專案、[+ 新增活動] 以及 [執行移轉]。

    ![新建活動](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity.png)

1.  出現提示時，請輸入來源伺服器與目標伺服器的認證，然後選取 [儲存]。

1.  在 [對應到目標資料庫] 畫面上，選取您要遷移的來源資料庫。

    ![選取來源資料庫](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases.png)

1.  選取 [儲存]，在 [設定移轉設定] 畫面上，提供下列詳細資料：

    | | |
    |--------|---------|
    |**伺服器備份位置** | Azure 資料庫移轉服務可將來源資料庫備份移入的本機網路共用。 執行來源 SQL Server 執行個體的服務帳戶在此網路共用上必須具有寫入權限。 |
    |**使用者名稱** | 可供 Azure 資料庫移轉服務模擬並將備份檔案上傳至 Azure 儲存體容器以進行還原作業的 Windows 使用者名稱。 |
    |**密碼** | 上述使用者的密碼。 |
    |**儲存體 SAS URI** | 此 SAS URI 會向 Azure 資料庫移轉服務提供您儲存體帳戶容器的存取權，此容器會作為服務上傳備份檔案時的目的地，且會用於將資料庫遷移至 Azure SQL Database 受控執行個體。 [了解如何取得 Blob 容器的 SAS URI](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)。|
    
    ![設定移轉設定](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings.png)

1.  在 [移轉摘要] 畫面上選取 [儲存]，在 [活動名稱] 文字方塊中，指定移轉活動的名稱。

    ![移轉摘要](media\tutorial-sql-server-to-managed-instance\dms-migration-summary.png)


## <a name="monitor-the-migration"></a>監視移轉

1.  選取移轉活動以檢閱活動的狀態。

1.  移轉完成後，確認目標 Azure SQL Database 受控執行個體上的目標資料庫。

    ![監視移轉](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration.png)

