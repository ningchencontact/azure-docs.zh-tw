---
title: 教學課程：將 Azure SQL Database 彈性集區新增至容錯移轉群組 |Microsoft Docs
description: 使用 Azure 入口網站、PowerShell 或 Azure CLI, 將 Azure SQL Database 彈性集區新增至容錯移轉群組。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 5dd241fed757669cf8bccd96a1de948e8d73a021
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033264"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>教學課程：將 Azure SQL Database 彈性集區新增至容錯移轉群組

使用 Azure 入口網站設定 Azure SQL Database 彈性集區的容錯移轉群組, 並測試容錯移轉。  在本教學課程中，您將了解如何：

> [!div class="checklist"]
> - 建立 Azure SQL Database 單一資料庫。
> - 將單一資料庫新增至彈性集區。 
> - 在兩個邏輯 SQL server 之間建立彈性集區的[容錯移轉群組](sql-database-auto-failover-group.md)。
> - 測試容錯移轉。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定您具有下列項目︰ 

- Azure 訂用帳戶。 如果您還沒有帳戶, 請[建立一個免費帳戶](https://azure.microsoft.com/free/)。


## <a name="1---create-a-single-database"></a>1-建立單一資料庫 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2-將單一資料庫新增至彈性集區

1. 選擇[Azure 入口網站](https://portal.azure.com)左上角的 [**建立資源**]。
1. 在`elastic pool`搜尋方塊中輸入, 按下 enter, 選取 [ **SQL 彈性資料庫集**區] 圖示, 然後選取 [**建立**]。 

    ![從 marketplace 選擇彈性集區](media/sql-database-elastic-pool-create-failover-group-tutorial/elastic-pool-market-place.png)

1. 使用下列值來設定您的彈性集區:
   - **名稱**：提供彈性集區的唯一名稱, 例如`myElasticPool`。 
   - 訂用帳戶：從下拉式清單中選取訂用帳戶。
   - **資源群組**：從`myResourceGroup`下拉式選單中, 選取您在第1節中建立的資源群組。 
   - **伺服器**：從下拉式選單中, 選取您在第1節中建立的伺服器。  

       ![為彈性集區建立新的伺服器](media/sql-database-elastic-pool-create-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **計算 + 儲存體**：選取 [**設定彈性集**區] 以設定您的計算、儲存體, 並將您的單一資料庫新增至彈性集區。 在 [**集區設定**] 索引標籤上, 保留預設值第5代, 其中包含2虛擬核心和32gb。 

1. 在 [**設定**] 頁面上, 選取 [**資料庫**] 索引標籤, 然後選擇 [**新增資料庫**]。 選擇您在第1節中建立的資料庫,然後選取 [套用], 將它新增至您的彈性集區。 再次選取 [套用] 以套用您的彈性集區設定, 然後關閉 [**設定**] 頁面。 

    ![將 SQL DB 新增至彈性集區](media/sql-database-elastic-pool-create-failover-group-tutorial/add-database-to-elastic-pool.png)

1. 選取 [**審查 + 建立**] 以審查您的彈性集區設定, 然後選取 [**建立**] 以建立彈性集區。 


## <a name="3---create-the-failover-group"></a>3-建立容錯移轉群組 
在此步驟中, 您將在現有的 Azure SQL server 與另一個區域中的新 Azure SQL server 之間建立[容錯移轉群組](sql-database-auto-failover-group.md)。 然後將彈性集區新增至容錯移轉群組。 


1. 選取[Azure 入口網站](https://portal.azure.com)左上角的 [**所有服務**]。 
1. 在`sql servers` [搜尋] 方塊中輸入。 
1. 選擇性選取 [SQL Server 到我的最愛**sql server** ] 旁的星號圖示, 然後將它新增至左側導覽窗格。 
    
    ![尋找 SQL Server](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. 選取 [ **SQL** server], 然後選擇您在第1節中建立的伺服器。
1. 在 [**設定**] 窗格下選取 [**容錯移轉群組**], 然後選取 [**新增群組**] 以建立新的容錯移轉群組。 

    ![加入新的容錯移轉群組](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. 在 [**容錯移轉群組**] 頁面上, 輸入或選取下列值, 然後選取 [**建立**]:
    - **容錯移轉組名**:輸入唯一的容錯移轉組名, 例如`failovergrouptutorial`。 
    - **次要伺服器**:選取 [*設定必要設定*] 選項, 然後選擇 [**建立新的伺服器**]。 或者, 您也可以選擇已經存在的伺服器做為次要伺服器。 輸入新次要伺服器的下列值後, 請選取 [**選取**]。 
        - **伺服器名稱**：輸入次要伺服器的唯一名稱, 例如`mysqlsecondary`。 
        - **伺服器管理員登入**：型`azureuser`
        - **密碼**：輸入複雜密碼以符合密碼需求。
        - **位置**：從下拉式選單中選擇 [位置], 例如 [美國東部 2]。 這個位置不能與您的主伺服器位於相同的位置。

       > [!NOTE]
       > 伺服器登入和防火牆設定必須符合您的主伺服器。 
    
       ![建立容錯移轉群組的次要伺服器](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

1. 選取**群組中**的 [資料庫], 然後選取您在第2節中建立的彈性集區。 應該會出現警告, 提示您在次要伺服器上建立彈性集區。 選取警告, 然後選取 **[確定]** 以在次要伺服器上建立彈性集區。 
        
    ![將彈性集區新增至容錯移轉群組](media/sql-database-elastic-pool-create-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. 選取 [**選取**] 將您的彈性集區設定套用到容錯移轉群組, 然後選取 [**建立**] 以建立容錯移轉群組。 將彈性集區新增至容錯移轉群組, 將會自動啟動異地複寫程式。 


## <a name="4---test-failover"></a>4-測試容錯移轉 
在此步驟中, 您會將容錯移轉群組容錯移轉到次要伺服器, 然後使用 Azure 入口網站進行容錯回復。 

1. 在[Azure 入口網站](https://portal.azure.com)中, 流覽至您的**SQL** server 伺服器。 
1. 在 [**設定**] 窗格下選取 [**容錯移轉群組**], 然後選擇您在第2節中建立的容錯移轉群組。 
  
   ![從入口網站選取容錯移轉群組](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. 檢查哪一台伺服器是主要的, 哪個伺服器是次要的。 
1. 從工作窗格中選取 [**容錯移轉**], 以容錯移轉包含彈性集區的容錯移轉群組。 
1. 在通知您將會中斷 TDS 會話的警告上, 選取 **[是]** 。 

   ![容錯移轉包含 SQL 資料庫的容錯移轉群組](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. 檢查哪一個伺服器是主要的, 哪一個是次要伺服器。 如果容錯移轉成功, 這兩部伺服器應該會有已交換的角色。 
1. 再次選取 [**容錯移轉**], 將容錯移轉群組容錯回復至原始設定。 

## <a name="clean-up-resources"></a>清除資源 
藉由刪除資源群組來清除資源。 

1. 在[Azure 入口網站](https://portal.azure.com)中, 流覽至您的資源群組。
1. 選取 [**刪除資源群組**] 以刪除群組中的所有資源, 以及資源群組本身。 
1. 在文字方塊中輸入資源群組`myResourceGroup`的名稱, 然後選取 [**刪除**] 以刪除資源群組。  


## <a name="next-steps"></a>後續步驟

在本教學課程中, 您已將 Azure SQL Database 單一資料庫新增至容錯移轉群組, 並測試容錯移轉。 您已了解如何︰

> [!div class="checklist"]
> - 建立 Azure SQL Database 單一資料庫。
> - 將單一資料庫新增至彈性集區。 
> - 在兩個邏輯 SQL server 之間建立彈性集區的[容錯移轉群組](sql-database-auto-failover-group.md)。
> - 測試容錯移轉。

請前進到下一個關於如何使用 DMS 進行移轉的教學課程。

> [!div class="nextstepaction"]
> [教學課程：使用 DMS 將 SQL Server 遷移至集區資料庫](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
