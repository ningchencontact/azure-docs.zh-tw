---
title: 使用 Azure 資料總管的開放式資料庫連接 (ODBC) 連線來使用 Tableau 將資料視覺化
description: 在本文中，您了解如何使用的開放式資料庫連接 (ODBC) 連接到 Azure 資料總管使用 Tableau 將資料視覺化。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: a0948ae35a5c23768df117979db819861ac64529
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499080"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>將資料從 Azure 中的資料總管 Tableau 視覺化

 [Tableau](https://www.tableau.com/)是商業智慧的視覺效果分析平台。 若要從連線到 Azure 資料總管 Tableau 並匯入資料，從範例的叢集中，使用 SQL Server 開放式資料庫連接 (ODBC) 驅動程式。 

## <a name="prerequisites"></a>先決條件

您需要下列項目完成這篇文章：

* [連接到 Azure 資料總管，使用 ODBC](connect-odbc.md) Tableau 從連線到 Azure 資料總管中使用 SQL Server ODBC 驅動程式。 

* Tableau Desktop 中，完整，或是[試用版](https://www.tableau.com/products/desktop/download)版本。

* 包含 StormEvents 範例資料的叢集。 如需詳細資訊，請參閱 <<c0> [ 建立 Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)並[範例資料內嵌至 Azure 資料總管](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>在 Tableau 中的資料視覺化 

一旦設定 ODBC 完成之後，您可以將範例資料到 Tableau。

1. 在 Tableau Desktop 中，在左側功能表中，選取**其他資料庫連接 (ODBC)** 。

    ![與 ODBC 連線](media/tableau/connect-odbc.png)

1. 針對**DSN**，選取您建立的 ODBC 資料來源，然後選取**登入**。

    ![ODBC 登入](media/tableau/odbc-sign-in.png)

1. 針對**資料庫**，選取您的範例叢集上的資料庫，例如*TestDatabase*。 針對**結構描述**，選取*dbo*，並針對**資料表**，選取*StormEvents*範例資料表。

    ![選取資料庫和資料表](media/tableau/select-database-table.png)

1. Tableau 現在會顯示範例資料的結構描述。 選取 **立即更新**來將資料帶入 Tableau。

    ![更新資料](media/tableau/update-data.png)

    匯入資料時，Tableau 會顯示類似下圖中的資料列。

    ![結果集](media/tableau/result-set.png)

1. 現在您可以建立視覺效果的 Tableau 根據您從 Azure 資料總管帶入的資料。 如需詳細資訊，請參閱 < [Tableau 學習](https://www.tableau.com/learn)。

## <a name="next-steps"></a>後續步驟

* [撰寫 Azure 資料總管的查詢](write-queries.md)