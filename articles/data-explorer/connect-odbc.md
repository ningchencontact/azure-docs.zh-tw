---
title: 使用 ODBC 連接到 Azure 資料總管
description: 在本文中，您將瞭解如何設定 Azure 資料總管的開放式資料庫連接（ODBC）連線。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034023"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>使用 ODBC 連接到 Azure 資料總管

開放式資料庫連接（[ODBC](/sql/odbc/reference/odbc-overview)）是廣為接受的應用程式開發介面（API），可用於資料庫存取。 使用 ODBC 從沒有專用連線器的應用程式連接到 Azure 資料總管。

在幕後，應用程式會呼叫 ODBC 介面中的函式，這些函式會在稱為*驅動程式*的資料庫特定模組中執行。 Azure 資料總管支援 SQL Server 通訊協定的子集（[MS TDS](/azure/kusto/api/tds/)），因此可以使用 ODBC driver for SQL Server。

您可以使用下列影片來瞭解如何建立 ODBC 連接。 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

或者，您可以[設定 ODBC 資料來源](#configure-the-odbc-data-source)，如下所述。 

在本文中，您會瞭解如何使用 SQL Server ODBC 驅動程式，因此您可以從任何支援 ODBC 的應用程式連線到 Azure 資料總管。 

## <a name="prerequisites"></a>先決條件

您需要下列項目：

* 適用于您作業系統的[Microsoft ODBC Driver for SQL Server 版本17.2.0.1 或更新版本](/sql/connect/odbc/download-odbc-driver-for-sql-server)。

## <a name="configure-the-odbc-data-source"></a>設定 ODBC 資料來源

請遵循下列步驟，使用 ODBC driver for SQL Server 來設定 ODBC 資料來源。

1. 在 Windows 中，搜尋 [ *Odbc 資料來源*]，然後開啟 [Odbc 資料來源] 桌面應用程式。

1. 選取 [新增]。

    ![新增資料來源](media/connect-odbc/add-data-source.png)

1. 選取 [ **ODBC Driver 17 for SQL Server]，** 然後按一下 **[完成]** 。

    ![選取驅動程式](media/connect-odbc/select-driver.png)

1. 輸入連接的名稱和描述，以及您要連線的叢集，然後選取 **[下一步]** 。 叢集 URL 的格式應為 *\<ClusterName\>。\<區域\>. kusto.windows.net*。

    ![選取伺服器](media/connect-odbc/select-server.png)

1. 選取 [ **Active Directory 整合** **] [下一步]** 。

    ![Active Directory 整合](media/connect-odbc/active-directory-integrated.png)

1. 選取包含範例資料的資料庫，然後按 [**下一步]** 。

    ![變更預設資料庫](media/connect-odbc/change-default-database.png)

1. 在下一個畫面中，將所有選項保留為預設值，然後選取 **[完成]** 。

1. 選取 [**測試資料來源**]。

    ![測試資料來源](media/connect-odbc/test-data-source.png)

1. 確認測試成功，然後選取 **[確定]** 。 如果測試不成功，請檢查您在先前步驟中指定的值，並確定您有足夠的許可權可以連接到叢集。

    ![測試成功](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>後續步驟

* [從 Tableau 連接到 Azure 資料總管](tableau.md)