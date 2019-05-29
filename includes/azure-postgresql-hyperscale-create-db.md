---
title: 包含檔案
description: 包含檔案
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 05/14/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: c07e352288d7dc1d0bf198fd74c8baaded3a2d23
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66154482"
---
如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>建立適用於 PostgreSQL 的 Azure 資料庫 – Hyperscale (Citus)

請依照下列步驟來建立「適用於 PostgreSQL 的 Azure 資料庫」伺服器：
1. 按一下 Azure 入口網站左上角的 [建立資源]  。
2. 從 [新增]  頁面中選取 [資料庫]  ，然後從 [資料庫]  頁面中選取 [適用於 PostgreSQL 的 Azure 資料庫]  。
3. 針對部署選項，按一下 [Hyperscale (Citus) 伺服器群組 - 預覽]  下方的 [建立]  按鈕。
4. 在新伺服器詳細資料表單中填寫下列資訊︰
   - 資源群組：按一下此欄位的文字方塊下方的 [新建]  連結。 輸入名稱，例如 **myresourcegroup**。
   - 伺服器群組名稱：輸入新伺服器群組的唯一名稱，該名稱也會用於伺服器子網域。
   - 系統管理員使用者名稱：其值目前必須是 **citus**，且無法變更。
   - 密碼：長度必須至少有八個字元，且包含來自下列三種類別的字元：英文大寫字母、英文小寫字母、數字 (0-9) 和非英數字元 (!、$、#、% 等等)。
   - 位置：使用最接近使用者的位置，讓他們能以最快速度存取資料。

   > [!IMPORTANT]
   > 必須有您在此處指定的伺服器系統管理員密碼，才能登入伺服器和其資料庫。 請記住或記錄此資訊，以供稍後使用。

5. 按一下 [設定伺服器群組]  。 讓該區段中的設定維持不變，然後按一下 [儲存]  。
6. 按一下 [檢閱 + 建立]  ，然後按一下 [建立]  以佈建伺服器。 佈建需要幾分鐘的時間。
7. 此頁面會重新導向以監視部署。 當即時狀態從 [您的部署正在進行中]  變更為 [您的部署已完成]  時，按一下頁面左側的 [輸出]  功能表項目。
8. [輸出] 頁面將包含協調器主機名稱，且旁邊會有按鈕可將此值複製到剪貼簿。 請記錄此資訊，以供後續使用。

## <a name="configure-a-server-level-firewall-rule"></a>設定伺服器層級防火牆規則

「適用於 PostgreSQL 的 Azure 資料庫 – Hyperscale (Citus) (預覽)」服務會使用伺服器層級的防火牆。 根據預設，防火牆會防止所有外部應用程式和工具連線到協調器節點及其中的任何資料庫。 我們必須新增規則，以針對特定 IP 位址範圍開啟防火牆。

1. 在您先前複製協調器節點主機名稱的 [輸出]  區段中，按一下 [上一頁] 返回 [概觀]  功能表項目。

2. 尋找部署的伺服器群組名稱，然後對其按一下。 (伺服器群組名稱將「不會」  有尾碼。 舉例來說，名稱結尾為 "-c"、"-w0" 或 "-w1" 的項目不是伺服器群組)。

3. 按一下左側功能表中 [安全性]  下方的 [防火牆]  。

4. 按一下[+ 為目前的用戶端 IP 位址新增防火牆規則]  連結。

5. 最後，按一下 [儲存]  按鈕。

   > [!NOTE]
   > Azure PostgreSQL 伺服器會透過連接埠 5432 進行通訊。 如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 5432 的輸出流量。 若情況如此，除非 IT 部門開啟連接埠 5432，否則您無法連線至 Azure SQL Database 伺服器。
   >

## <a name="connect-to-the-database-using-psql"></a>使用 psql 連線到資料庫

當您建立 Azure Database for PostgreSQL 伺服器時，系統會建立名為 **citus** 的預設資料庫。 若要連線到資料庫伺服器，您需要連接字串和系統管理員密碼。

1. 取得連接字串。 在 [伺服器群組] 頁面中，按一下 [連接字串]  功能表項目。 (其位於 [設定]  底下)。尋找標示為 **C++ (libpq)** 的字串。 其格式將會是：

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   複製字串。 您必須將 "{your\_password}" 取代為您先前選擇的系統管理密碼。 系統不會儲存您的純文字密碼，因此無法在連接字串中對您顯示。

2. 在本機電腦上開啟終端機視窗。

3. 在提示字元處，使用 [psql](https://www.postgresql.org/docs/current/app-psql.html) 公用程式來連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器。 以引號括住來傳遞連接字串，並確定其包含密碼：
   ```bash
   psql "{connection_string}"
   ```

   例如，下列命令會連線至伺服器群組 **mydemoserver** 的協調器節點：

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
