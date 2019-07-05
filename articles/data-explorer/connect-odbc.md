---
title: 連接到 Azure 的資料總管，使用 ODBC
description: 在本文中，您了解如何設定開放式資料庫連接 (ODBC) 連線到 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 65795b5b4dea8d2cdeecf5f78f9de751f275dac0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537588"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>連接到 Azure 的資料總管，使用 ODBC

開放式資料庫連接 ([ODBC](/sql/odbc/reference/odbc-overview)) 是資料庫存取權的廣為接受的應用程式開發介面 (API)。 使用 ODBC 應用程式不需要專用的連接器，從連線到 Azure 資料總管。

在幕後，應用程式呼叫 ODBC 介面，該屬性在呼叫的特定資料庫的模組實作函式*驅動程式*。 Azure 資料總管支援 SQL Server 通訊協定的子集 ([MS TDS](/azure/kusto/api/tds/))，因此它可以使用 ODBC driver for SQL Server。

使用下列影片，您可以了解如何建立 ODBC 連接。 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

或者，您可以[ODBC 資料來源設定](#configure-the-odbc-data-source)如下所述。 

在本文中，您將了解如何使用 SQL Server ODBC 驅動程式，因此您可以從任何支援 ODBC 應用程式連接到 Azure 資料總管。 

## <a name="prerequisites"></a>必要條件

您需要下列項目：

* [Microsoft ODBC Driver for SQL Server 版本 17.2.0.1 或更新版本](/sql/connect/odbc/download-odbc-driver-for-sql-server)適用於您作業系統。

## <a name="configure-the-odbc-data-source"></a>設定 ODBC 資料來源

請遵循下列步驟來設定 ODBC 資料來源使用 ODBC driver for SQL Server。

1. 在 Windows 中，搜尋*ODBC 資料來源*，並開啟 ODBC 資料來源的桌面應用程式。

1. 選取 [新增]  。

    ![新增資料來源](media/connect-odbc/add-data-source.png)

1. 選取  **ODBC Driver 17 for SQL Server**再**完成**。

    ![選取的驅動程式](media/connect-odbc/select-driver.png)

1. 輸入的名稱和描述，然後選取連線，以及您想要連接的叢集**下一步**。 URL 應在表單中的叢集 *\<ClusterName\>。\<區域\>。 kusto.windows.net*。

    ![選取伺服器](media/connect-odbc/select-server.png)

1. 選取 [**整合了 Active Directory**再**下一步]** 。

    ![Active Directory 整合](media/connect-odbc/active-directory-integrated.png)

1. 然後選取範例資料的資料庫**下一步**。

    ![變更預設資料庫](media/connect-odbc/change-default-database.png)

1. 在下一個畫面上，將所有選項都保留成預設值 然後選取**完成**。

1. 選取 **測試資料來源**。

    ![測試資料來源](media/connect-odbc/test-data-source.png)

1. 確認測試成功，然後選取**確定**。 如果測試不成功，請檢查您在先前步驟中指定的值，並確定您有足夠的權限來連線到叢集。

    ![測試成功](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>後續步驟

* [Tableau 從連線到 Azure 的資料總管](tableau.md)