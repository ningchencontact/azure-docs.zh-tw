---
title: 設定和在 Azure 入口網站中適用於 MariaDB 存取稽核記錄檔的 Azure 資料庫
description: 本文說明如何設定和存取適用於 MariaDB 的 Azure 資料庫中的稽核記錄，從 Azure 入口網站。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: c9ee106972cc78a08709d2ce55d2dfddc96edbf7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079232"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>設定和存取 Azure 入口網站中的稽核記錄

您可以設定[MariaDB 稽核記錄檔的 Azure 資料庫](concepts-audit-logs.md)和從 Azure 入口網站的診斷設定。

> [!IMPORTANT]
> 稽核記錄功能目前為預覽狀態。

## <a name="prerequisites"></a>必要條件

若要逐步執行本作法指南，您需要︰

- [適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>設定稽核記錄

啟用並設定稽核記錄。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取適用於 MariaDB 的 Azure 資料庫伺服器。

1. 底下**設定**提要欄位中，選取一節**伺服器參數**。
    ![伺服器參數](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. 更新**audit_log_enabled**參數設為 ON。
    ![啟用稽核記錄檔](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. 選取要藉由更新記錄的事件**audit_log_events**參數。
    ![稽核記錄檔事件](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. 任何 MariaDB 將使用者新增至排除藉由更新記錄**audit_log_exclude_users**參數。 指定使用者藉由提供 MariaDB 使用者名稱。
    ![稽核記錄檔排除使用者](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. 變更參數之後，您可以按一下 [儲存]  。 或者，也可以 [捨棄]  您的變更。
    ![儲存](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄

1. 底下**監視**提要欄位中，選取一節**診斷設定**。

1. 按一下 [+ 新增診斷設定]![新增診斷設定](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. 提供診斷設定的名稱。

1. 指定的資料接收要傳送的稽核記錄 （儲存體帳戶、 事件中樞和/或 Log Analytics 工作區）。

1. 選取 「 MySqlAuditLogs"做為記錄類型。
![設定診斷設定](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. 一旦您已設定的資料來源，以透過管線傳送至稽核記錄檔，您可以按一下**儲存**。
![儲存診斷設定](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. 探索這些檔案在您設定的資料來源來存取稽核記錄檔。 可能需要 10 分鐘的時間才會出現記錄檔。

## <a name="next-steps"></a>後續步驟

- 深入了解[稽核記錄檔](concepts-audit-logs.md)適用於 MariaDB 的 Azure 資料庫中。