---
title: 連接到 Azure 的資料總管，使用 ODBC
description: 在此操作說明，您會學習如何設定 ODBC 連接到 Azure 資料總管，然後使用該連接要使用 Tableau 將資料視覺化。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 0ec1c2f4fc412ca6c81e179d0ad22f781b896357
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757616"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>連接到 Azure 的資料總管，使用 ODBC

開放式資料庫連接 ([ODBC](/sql/odbc/reference/odbc-overview)) 是資料庫存取權的廣為接受的應用程式開發介面 (API)。 使用 ODBC 應用程式不需要專用的連接器，從連線到 Azure 資料總管。

在幕後，應用程式呼叫 ODBC 介面，該屬性在呼叫的特定資料庫的模組實作函式*驅動程式*。 Azure 資料總管支援 SQL Server 通訊協定的子集 ([MS TDS](/azure/kusto/api/tds/))，因此它可以使用 ODBC driver for SQL Server。

在本文中，您會學習如何使用 SQL Server ODBC 驅動程式，因此您可以從任何支援 ODBC 應用程式連接到 Azure 資料總管。 您可以選擇性地從 Tableau，連線到 Azure 資料總管並匯入資料，從範例叢集。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明，您需要下列項目：

* [Microsoft ODBC Driver for SQL Server 版本 17.2.0.1 或更新版本](/sql/connect/odbc/download-odbc-driver-for-sql-server)適用於您作業系統。

* 如果您想要遵循我們的 Tableau 範例中，您也需要：

  * 完整的 tableau Desktop 或[試用版](https://www.tableau.com/products/desktop/download)版本。

  * 包含 StormEvents 範例資料的叢集。 如需詳細資訊，請參閱[快速入門：建立 Azure 資料總管叢集與資料庫](create-cluster-database-portal.md)及[將範例資料內嵌至 Azure 資料總管](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-odbc-data-source"></a>設定 ODBC 資料來源

請遵循下列步驟來設定 ODBC 資料來源使用 ODBC driver for SQL Server。

1. 在 Windows 中，搜尋*ODBC 資料來源*，並開啟 ODBC 資料來源的桌面應用程式。

1. 選取 [新增] 。

    ![新增資料來源](media/connect-odbc/add-data-source.png)

1. 選取  **ODBC Driver 17 for SQL Server**再**完成**。

    ![選取的驅動程式](media/connect-odbc/select-driver.png)

1. 輸入的名稱和描述，然後選取連線，以及您想要連接的叢集**下一步**。 URL 應在表單中的叢集 *\<ClusterName\>。\<區域\>。 kusto.windows.net*。

    ![選取伺服器](media/connect-odbc/select-server.png)

1. 選取 [**整合了 Active Directory**再**下一步]**。

    ![Active Directory (整合式)](media/connect-odbc/active-directory-integrated.png)

1. 然後選取範例資料的資料庫**下一步**。

    ![變更預設資料庫](media/connect-odbc/change-default-database.png)

1. 在下一個畫面上，將所有選項都保留成預設值 然後選取**完成**。

1. 選取 **測試資料來源**。

    ![測試資料來源](media/connect-odbc/test-data-source.png)

1. 確認測試成功，然後選取**確定**。 如果測試不成功，請檢查您在先前步驟中指定的值，並確定您有足夠的權限來連線到叢集。

    ![測試成功](media/connect-odbc/test-succeeded.png)

## <a name="visualize-data-in-tableau-optional"></a>在 Tableau （選擇性） 中的資料視覺化

現在您已完成設定 ODBC，您可以將範例資料到 Tableau。

1. 在 Tableau Desktop 中，在左側功能表中，選取**其他資料庫連接 (ODBC)**。

    ![與 ODBC 連線](media/connect-odbc/connect-odbc.png)

1. 針對**DSN**，選取您建立的 ODBC 資料來源，然後選取**登入**。

    ![ODBC 登入](media/connect-odbc/odbc-sign-in.png)

1. 針對**資料庫**，選取您的範例叢集上的資料庫，例如*TestDatabase*。 針對**結構描述**，選取*dbo*，並針對**資料表**，選取*StormEvents*範例資料表。

    ![選取資料庫和資料表](media/connect-odbc/select-database-table.png)

1. Tableau 現在會顯示範例資料的結構描述。 選取 **立即更新**來將資料帶入 Tableau。

    ![更新資料](media/connect-odbc/update-data.png)

    匯入資料時，Tableau 會顯示類似下圖中的資料列。

    ![結果集](media/connect-odbc/result-set.png)

1. 現在您可以建立視覺效果的 Tableau 根據您從 Azure 資料總管帶入的資料。 如需詳細資訊，請參閱 < [Tableau 學習](https://www.tableau.com/learn)。

## <a name="next-steps"></a>後續步驟

[撰寫 Azure 資料總管的查詢](write-queries.md)

[教學課程：在 Power BI 中從 Azure 資料總管將資料視覺化](visualize-power-bi.md)