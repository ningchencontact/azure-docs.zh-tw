---
title: 使用 Visual Studio 搭配 .NET 和 C# 查詢 Azure SQL Database | Microsoft Docs
description: 使用 Visual Studio 來建立連線到 Azure SQL Database 的 C# 應用程式，並使用 Transact-SQL 陳述式來查詢。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/11/2018
ms.openlocfilehash: 93249b7d274ce9d7928dfa46eb339da68c92b785
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163291"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>快速入門：在 Visual Studio 使用 .NET 和 C# 連線和查詢 Azure SQL Database

本快速入門示範如何在 Visual Studio 使用 [.NET Framework](https://www.microsoft.com/net/) 和 C# 程式碼，以 Transact-SQL 陳述式來查詢 Azure SQL Database。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您需要：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) Community、Professional 或 Enterprise 版。

## <a name="get-sql-server-connection-information"></a>取得 SQL Server 連線資訊

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-the-sql-database"></a>建立程式碼以查詢 SQL Database

1. 在 Visual Studio 中，選取 [檔案]  >  [新增]  >  [專案]。 
   
1. 在 [新增專案] 對話方塊中，選取 [Visual C#]，然後選取 [主控台應用程式 (.NET Framework)]。
   
1. 輸入 *sqltest* 作為專案名稱，然後選取 [確定]。 隨即建立新專案。 
   
1. 選取 [專案] > [管理 NuGet 套件]。 
   
1. 在 [NuGet 套件管理員] 中，選取 [瀏覽] 索引標籤，然後搜尋並選取 **System.Data.SqlClient**。
   
1. 在 **System.Data.SqlClient** 頁面上，選取 [安裝]。 
   - 出現提示時，選取 [確定] 以繼續安裝。 
   - 如果 [接受授權] 視窗出現時，選取 [我接受]。
   
1. 當安裝完成時，您可以關閉 [NuGet 套件管理員]。 
   
1. 在程式碼編輯器中，以下列程式碼取代 **Program.cs** 內容。 請將 `<server>`、`<username>`、`<password>` 及 `<database>` 替換為您的值。
   
   >[!IMPORTANT]
   >此範例中的程式碼使用範例 AdventureWorksLT 資料，您可以在建立資料庫時選擇這些範例資料作為來源。 如果您的資料庫中有不同的資料，請在 SELECT 查詢中使用來自您自己資料庫的資料表。 
   
   ```csharp
   using System;
   using System.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       connection.Open();       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>執行程式碼

1. 若要執行應用程式，請選取 [偵錯] > [開始偵錯]，或在工具列上選取 [開始]，或按下 **F5**。
1. 請確認會傳回資料庫的前 20 個「類別/產品」資料列，然後關閉應用程式視窗。

## <a name="next-steps"></a>後續步驟

- 了解如何在 Windows/Linux/macOS 中[使用 .NET Core 來連線及查詢 Azure SQL 資料庫](sql-database-connect-query-dotnet-core.md)。  
- 了解 [使用命令列以開始使用在 Windows/Linux/macOS 上的 .NET Core](/dotnet/core/tutorials/using-with-xplat-cli)。
- 深入了解如何[使用 SSMS 設計您的第一個 Azure SQL 資料庫](sql-database-design-first-database.md)或[使用 .NET 設計您的第一個 Azure SQL 資料庫](sql-database-design-first-database-csharp.md)。
- 如需 .NET 的詳細資訊，請參閱 [.NET 文件](https://docs.microsoft.com/dotnet/)。
- 重試邏輯範例：[使用 ADO.NET 復原連線 SQL][step-4-connect-resiliently-to-sql-with-ado-net-a78n]。


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

