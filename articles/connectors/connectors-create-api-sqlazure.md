---
title: 連線至 SQL Server 或 Azure SQL Database - Azure Logic Apps | Microsoft Docs
description: 如何使用 Azure Logic Apps 自動化工作流程，以存取和管理內部部署或雲端的 SQL 資料庫
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 804a913d17c3151d07a1ecf229e2db148dc45558
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050764"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>從 Azure Logic Apps 連線至 SQL Server 或 Azure SQL Database

本文說明如何使用 SQL Server 連接器，從邏輯應用程式內部存取 SQL 資料庫中的資料。 如此您即可建立邏輯應用程式，以自動化用來管理 SQL 資料和資源的工作、流程和工作流程。 該連接器同時適用於[內部部署 SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) 和[雲端 Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)。 

您可以建立會由 SQL 資料庫或其他系統 (例如，Dynamics CRM Online) 中的事件所觸發而執行的邏輯應用程式。 邏輯應用程式也可以取得、插入及刪除資料，還可執行 SQL 查詢或已儲存的程序。 例如，您可以建置邏輯應用程式，以自動檢查 Dynamics CRM Online 中是否有新記錄、將新記錄的項目新增至 SQL 資料庫，然後傳送電子郵件警示。

如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 如需連接器專屬的技術資訊，請參閱 [SQL Server 連接器參考](https://docs.microsoft.com/connectors/sql/)。

## <a name="prerequisites"></a>必要條件

* 需要存取 SQL 資料庫的邏輯應用程式。 若要使用 SQL 觸發程序啟動邏輯應用程式，您需要[空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 

* [Azure SQL 資料庫](../sql-database/sql-database-get-started-portal.md)或 [SQL Server 資料庫](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  資料表內必須有資料，邏輯應用程式才能在呼叫作業時傳回結果。 如果您建立的是 Azure SQL Database，則可以使用隨附的資料庫範例。 

* 伺服器名稱名稱、資料庫名稱、使用者名稱和密碼。 需要有這些認證，才能授權邏輯存取 SQL 伺服器。 

  * 如果是 Azure SQL Database，可在連接字串中找到這些詳細資料，也可在 Azure 入口網站的 SQL Database 屬性下找到：

    "Server=tcp:<*yourServerName*>.database.windows.net,1433;Initial Catalog=<*yourDatabaseName*>;Persist Security Info=False;User ID=<*yourUserName*>;Password=<*yourPassword*>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

  * 如果是 SQL Server，可在連接字串中找到這些詳細資料： 

    "Server=<*yourServerAddress*>;Database=<*yourDatabaseName*>;User Id=<*yourUserName*>;Password=<*yourPassword*>;"

* 在將邏輯應用程式連線至內部部署系統 (例如，SQL Server) 之前，您必須先[設定內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)才行。 這樣一來，您就可以在建立邏輯應用程式的 SQL 連線時選取閘道。

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>新增 SQL 觸發程序

在 Azure Logic Apps 中，每個邏輯應用程式都必須使用[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程序時，Logic Apps 引擎都會建立邏輯應用程式執行個體，並開始執行應用程式的工作流程。

1. 在 Azure 入口網站或 Visual Studio 中，建立空白的邏輯應用程式，以開啟 Logic Apps 設計工具。 這個範例會使用 Azure 入口網站。

2. 在搜尋方塊中，輸入「sql 伺服器」作為篩選條件。 從觸發程序清單中，選取您想要的 SQL 觸發程序。 

   針對此範例, 請選取此觸發程式:**SQL Server-建立專案時**

   ![選取「SQL Server - 建立項目時」觸發程序](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. 如果系統提示您輸入連線詳細資料，請[立即建立 SQL 連線](#create-connection)。 
   或者，如果連線已存在，請從清單中選取您想要的**資料表名稱**。

   ![選取資料表](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. 設定 [間隔] 和 [頻率] 屬性，以指定邏輯應用程式檢查資料表的頻率。

   這個範例只會檢查選取的資料表而已。 
   若要進行更有趣的作業，請新增動作來執行您想要的工作。 
   
   例如，若要檢視資料表中的新項目，則可新增其他動作，例如，建立具有資料表欄位的檔案，然後傳送電子郵件警示。 
   若要了解此連接器或其他連接器的其他動作，請參閱 [Logic Apps 連接器](../connectors/apis-list.md)。

5. 當您完成時, 請在設計工具工具列上選取 [**儲存**]。 

   此步驟會自動在 Azure 中即時啟用並發佈邏輯應用程式。 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>新增 SQL 動作

在 Azure Logic Apps 中，[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是工作流程中跟隨在觸發程序或另一個動作之後的步驟。 在此範例中，邏輯應用程式會使用[循環觸發程序](../connectors/connectors-native-recurrence.md)來啟動，並且會呼叫動作以從 SQL 資料庫取得資料列。

1. 在 Azure 入口網站或 Visual Studio 的邏輯應用程式設計工具中，開啟邏輯應用程式。 這個範例會使用 Azure 入口網站。

2. 在邏輯應用程式設計工具的 [觸發程式] 或 [動作] 底下, 選取 [**新增步驟** > ] [新增**動作**]。

   ![選取 [新步驟]、[新增動作]](./media/connectors-create-api-sqlazure/add-action.png)
   
   若要在現有步驟之間新增動作，請將滑鼠放在連接箭頭上。 
   選取顯示的加號 ( **+** ), 然後選取 [**新增動作**]。

2. 在搜尋方塊中，輸入「sql 伺服器」作為篩選條件。 從動作清單中，選取您想要的任何 SQL 動作。 

   針對此範例, 請選取此動作, 這會取得單一記錄:**SQL Server-取得資料列**

   ![輸入「sql 伺服器」，選取 [SQL Server - 取得資料列]](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. 如果系統提示您輸入連線詳細資料，請[立即建立 SQL 連線](#create-connection)。 
   或者，如果連線已存在，請選取 [資料表名稱]，並輸入您所要記錄的 [資料列識別碼]。

   ![輸入資料表名稱和資料列識別碼](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   這個範例只會傳回所選資料表的一個資料列而已。 
   若要檢視此資料列中的資料，請新增其他動作，以使用資料列欄位建立檔案以供之後檢閱，並將該檔案儲存在雲端儲存體帳戶中。 若要了解此連接器或其他連接器的其他動作，請參閱 [Logic Apps 連接器](../connectors/apis-list.md)。

4. 當您完成時, 請在設計工具工具列上選取 [**儲存**]。 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>連接到您的資料庫

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>處理大量資料

有時候, 您可能必須使用結果集, 如此一來, 連接器不會同時傳回所有結果, 或者您想要更有效地控制結果集的大小和結構。 以下是一些您可以處理這類大型結果集的方法:

* 若要協助您以較小的集合來管理結果, 請開啟*分頁*功能。 如需詳細資訊, 請參閱[使用分頁取得大量資料、記錄和專案](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)。

* 建立預存程式, 以您想要的方式來組織結果。

  取得或插入多個資料列時, 您的邏輯應用程式可以在這些[限制](../logic-apps/logic-apps-limits-and-config.md)內使用[*until 迴圈*](../logic-apps/logic-apps-control-flow-loops.md#until-loop), 逐一查看這些資料列。 
  不過, 當您的邏輯應用程式必須處理的記錄集很大 (例如, 數千或數百萬個數據列) 時, 您會想要將對資料庫的呼叫所產生的成本降至最低。

  若要以您想要的方式組織結果, 您可以建立在 SQL 實例中執行的[*預存*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)程式, 並使用**SELECT-ORDER BY**語句。 
  此解決方案可讓您對結果的大小和結構擁有更好的控制能力。 
  邏輯應用程式會使用 SQL Server 連接器的**執行預存程序**動作來呼叫預存程序。

  如需解決方案的詳細資訊，請參閱下列文章：

  * [可供使用 Logic Apps 傳輸大量資料的 SQL 分頁](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - ORDER BY 子句](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>連接器特定的詳細資料

如需此連接器的觸發程序、動作和限制的技術資訊，請參閱[連接器的參考詳細資料](/connectors/sql/)。 

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)

