---
title: 設定和在 Azure 入口網站中適用於 MariaDB 存取伺服器記錄檔的 Azure 資料庫
description: 本文說明如何設定和存取適用於 MariaDB 的 Azure 資料庫中的伺服器記錄，從 Azure 入口網站。
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 3dbf7064e409230916668e62ef861c0ce149fdbb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065629"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>在 Azure 入口網站中設定和存取伺服器記錄

您可以設定、 列出及下載[MariaDB 慢速查詢記錄的 Azure 資料庫](concepts-server-logs.md)從 Azure 入口網站。

## <a name="prerequisites"></a>先決條件
若要逐步執行本作法指南，您需要︰
- [適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>設定記錄
設定慢速查詢記錄的存取。 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取適用於 MariaDB 的 Azure 資料庫伺服器。

3. 在提要欄位的 [監視]  區段中，選取 [伺服器記錄]  。 
   ![選取伺服器記錄，按一下以設定](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. 選取 [按一下這裡可啟用記錄，並設定記錄參數]  標題，來查看伺服器參數。

5. 變更您需要調整的參數，包括將 "slow_query_log" 調整為 "ON"。 您在此工作階段中所做的所有變更都會以紫色顯示。 

   變更參數之後，您可以按一下 [儲存]  。 或者，也可以**捨棄**您的變更。

   ![按一下 [儲存] 或 [捨棄]](./media/howto-configure-server-logs-portal/3-save-discard.png)

6. 按一下 [伺服器參數]  頁面上的**關閉按鈕** (X 圖示)。

## <a name="view-list-and-download-logs"></a>檢視清單並下載記錄
開始記錄之後，您可以檢視可用的慢速查詢記錄檔的清單，並下載伺服器記錄 窗格上的個別記錄檔。 

1. 開啟 Azure 入口網站。

2. 選取適用於 MariaDB 的 Azure 資料庫伺服器。

3. 在提要欄位的 [監視]  區段中，選取 [伺服器記錄]  。 頁面會顯示記錄檔的清單，如下所示：

   ![記錄清單](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > 記錄的命名慣例為 **mysql-slow-< your server name>-yyyymmddhh.log**。 檔案名稱中使用的日期和時間是發出記錄的時間。 記錄會每隔 24 小時或 7.5 GB 旋轉一次，先到者先用。

4. 如有需要，請使用**搜尋方塊**，根據日期/時間快速縮小至特定記錄。 搜尋是根據記錄的名稱進行。

5. 使用資料表資料列中每個記錄檔旁邊的**下載**按鈕 (向下箭號圖示) 下載個別記錄檔，如下所示：

   ![按一下下載圖示](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="next-steps"></a>後續步驟
- 深入了解[緩慢查詢記錄](concepts-server-logs.md)適用於 MariaDB 的 Azure 資料庫中。
- 如需參數定義與 MariaDB 記錄的詳細資訊，請參閱有關[記錄](https://mariadb.com/kb/en/library/slow-query-log-overview/) MariaDB 文件。

<!--- See [Access Server Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download logs programmatically. -->
