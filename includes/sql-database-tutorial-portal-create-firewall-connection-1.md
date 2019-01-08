---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: ab31ee82e8035fe888fa70b5796aef2c2b2939b2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728526"
---
## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)

## <a name="create-a-blank-sql-database"></a>建立空白 SQL Database

Azure SQL Database 會存在於一組已定義的[計算和儲存體資源](../articles/sql-database/sql-database-service-tiers-dtu.md)內。 此資料庫會在 [Azure 資源群組](../articles/azure-resource-manager/resource-group-overview.md)和 [Azure SQL Database 邏輯伺服器](../articles/sql-database/sql-database-features.md)底下運作。

遵循以下步驟來建立空白 SQL 資料庫。

1. 按一下 Azure 入口網站左上角的 [建立資源]。

1. 在 [新增] 頁面上，選取 [資料庫] > [SQL Database]。

   ![建立空白資料庫](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

1. 在 [SQL Database] 窗格中，輸入或選取下列值：

   | 設定       | 建議的值 | 說明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **資料庫名稱** | *yourDatabase* | 如需有效的資料庫名稱，請參閱[資料庫識別碼](/sql/relational-databases/databases/database-identifiers)。 |
   | **訂用帳戶** | *yourSubscription*  | 如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。 |
   | **資源群組** | *yourResourceGroup* | 如需有效的資源群組名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/naming-conventions)。 |
   | **選取來源** | 空白資料庫 | 指定應建立空白資料庫。 |

   ![建立資料庫](../articles/sql-database/media/sql-database-design-first-database/create-database.png)

   1. 選取 [伺服器] 來為您的新資料庫設定伺服器。 接著，輸入或選取下列值：

      | 設定       | 建議的值 | 說明 |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **伺服器名稱** | 任何全域唯一名稱 | 如需有效的伺服器名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/naming-conventions)。 |
      | **伺服器管理員登入** | 任何有效名稱 | 如需有效的登入名稱，請參閱[資料庫識別碼](/sql/relational-databases/databases/database-identifiers)。|
      | **密碼** | 任何有效密碼 | 您的密碼至少要有 8 個字元，而且必須使用下列幾種字元的其中三種︰大寫字元、小寫字元、數字和非英數字元。 |
      | **位置** | 任何有效位置 | 如需有關區域的資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。 |

      選擇 [選取]。

      ![建立資料庫伺服器](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

   1. 選取 [定價層] 以指定服務層、DTU 數目和儲存體數量。 瀏覽 DTU 的選項，以及可供您每個服務層使用的儲存體。

      在選取伺服器層、DTU 數目和儲存體數量之後，請選取 [套用]。

   1. 為空白資料庫輸入 [定序] (在此教學課程中使用預設值)。 如需有關定序的詳細資訊，請參閱[定序](/sql/t-sql/statements/collations)。

1. 既然您已完成 **SQL Database** 表單，請選取 [建立] 來建立資料庫。 此步驟可能最多需要 1.5 分鐘的時間來完成。

1. 在工具列上，按一下 [通知] 以監視部署程序。

     ![通知](../articles/sql-database/media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>建立防火牆規則

SQL 資料庫服務會在伺服器層級建立防火牆，以防止外部應用程式和工具連線到伺服器或伺服器上的任何資料庫。 請依照下列步驟，為您的用戶端 IP 位址建立 [SQL 資料庫伺服器層級防火牆規則](../articles/sql-database/sql-database-firewall-configure.md)。 此程序將只允許透過您 IP 位址的 SQL 資料庫防火牆進行外部連線。

> [!NOTE]
> SQL 資料庫會透過連接埠 1433 通訊。 如果您嘗試從公司網路進行連線，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 若情況如此，除非系統管理員開啟連接埠 1433，否則您無法連線至 Azure SQL Database 伺服器。

1. 部署完成之後，請從左側功能表中選擇 [SQL 資料庫]，然後選取 [SQL 資料庫] 頁面上的 [yourDatabase]。 資料庫的 [概觀] 頁面隨即開啟，其中會顯示完整的伺服器名稱 (例如 *yourserver.database.windows.net*)，並提供進一步的設定選項。

1. 複製完整的伺服器名稱，以在稍後的步驟中用來連線到伺服器及其資料庫。

   ![伺服器名稱](../articles/sql-database/media/sql-database-design-first-database/server-name.png)

1. 在工具列上選取 [設定伺服器防火牆]。 SQL 資料庫伺服器的 [防火牆設定] 頁面隨即開啟。

   ![伺服器防火牆規則](../articles/sql-database/media/sql-database-design-first-database/server-firewall-rule.png)

   1. 選取工具列上的 [新增用戶端 IP]，以將目前的 IP 位址新增至新的防火牆規則。 防火牆規則可以針對單一 IP 位址或 IP 位址範圍開啟連接埠 1433。

   1. 選擇 [儲存]。 系統便會為目前的 IP 位址建立伺服器層級防火牆規則，以便在邏輯伺服器上開啟連接埠 1433。

   1. 選取 [確定]，然後關閉 [防火牆設定] 頁面。

您的 IP 位址現在已可通過防火牆，且現在可以使用 SSMS 或您所選的其他工具，來連線至 SQL 資料庫伺服器及其資料庫。 請務必使用您先前建立的伺服器系統管理員帳戶。

> [!IMPORTANT]
> 根據預設，已對所有 Azure 服務啟用透過 SQL 資料庫防火牆存取。 選取此頁面上的 [關閉] 即可對所有 Azure 服務停用。
