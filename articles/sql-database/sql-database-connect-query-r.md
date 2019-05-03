---
title: 使用 R 查詢 Azure SQL Database
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 本文將說明如何使用 R 指令碼來連線至 Azure SQL 資料庫，並使用 Transact-SQL 陳述式查詢。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 2b1206e3087b0573736174d4eed502653d76f7a5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001180"
---
# <a name="quickstart-use-r-to-query-an-azure-sql-database-preview"></a>快速入門：使用 R 查詢 Azure SQL 資料庫 (預覽)

 此快速入門會示範如何搭配使用 [R](https://www.r-project.org/) 及機器學習服務來連線至 Azure SQL 資料庫，並使用 Transact-SQL 陳述式來查詢資料。 機器學習服務是 Azure SQL Database 的功能，用來執行資料庫內 R 指令碼。 如需更詳細的資訊，請參閱[搭配使用 R 及 Azure SQL Database 機器學習服務 (預覽)](sql-database-machine-learning-services-overview.md)。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，請確定您具備下列項目︰

- Azure SQL 資料庫。 您可以使用其中一個快速入門，在 Azure SQL Database 中建立資料庫並加以設定：

  || 單一資料庫 | 受控執行個體 |
  |:--- |:--- |:---|
  | 建立| [入口網站](sql-database-single-database-get-started.md) | [入口網站](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | 設定 | [伺服器層級 IP 防火牆規則](sql-database-server-level-firewall-rule.md)| [VM 的連線能力](sql-database-managed-instance-configure-vm.md)|
  |||[現場的連線能力](sql-database-managed-instance-configure-p2s.md)
  |||

- 已啟用機器學習服務 (搭配 R)。 在公開預覽期間，Microsoft 會將您加入，並為您現有的或新的資料庫啟用機器學習服務。 請遵循[註冊預覽版](sql-database-machine-learning-services-overview.md#signup)中的步驟。

- 最新的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。 您可以使用其他資料庫管理或查詢工具來執行 R 指令碼，但是在本快速入門中，您將使用 SSMS。

## <a name="get-sql-server-connection-information"></a>取得 SQL Server 連線資訊

取得連線到 Azure SQL 資料庫所需的連線資訊。 在後續程序中，您將需要完整的伺服器名稱或主機名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 瀏覽至 [SQL 資料庫] 或 [SQL 受控執行個體] 頁面。

3. 在 [概觀] 頁面上，針對單一資料庫檢閱 [伺服器名稱] 旁的完整伺服器名稱，若為受控執行個體，則檢閱 [主機] 旁的完整伺服器名稱。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製] 圖示。

## <a name="create-code-to-query-your-sql-database"></a>建立程式碼以查詢您的 SQL Database

1. 開啟 **SQL Server Management Studio**，然後連線至 SQL 資料庫。

   如果您需要連線方面的協助，請參閱[快速入門：使用 SQL Server Management Studio 連線和查詢 Azure SQL Database](sql-database-connect-query-ssms.md)。

1. 將完整的 R 指令碼傳遞至 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 預存程序。

   此指令碼會透過 `@script` 引數傳遞。 `@script` 引數內的所有項目都必須是有效的 R 程式碼。

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > 如果發生任何錯誤，有可能是因為您的 SQL 資料庫未啟用公開預覽版的機器學習服務 (搭配 R)。 請參閱上述[必要條件](#prerequisites)。

## <a name="run-the-code"></a>執行程式碼

1. 執行 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 預存程序。

1. 確認 [訊息] 視窗中已傳回前 20 個類別/產品資料列。

## <a name="next-steps"></a>後續步驟

- [設計您的第一個 Azure SQL Database](sql-database-design-first-database.md)
- [Azure SQL Database 機器學習服務 (搭配 R)](sql-database-machine-learning-services-overview.md)
- [在 Azure SQL Database 機器學習服務中建立和執行簡單的 R 指令碼 (預覽)](sql-database-quickstart-r-create-script.md)
- [使用機器學習服務在 Azure SQL Database 中撰寫進階的 R 函式 (預覽)](sql-database-machine-learning-services-functions.md)
