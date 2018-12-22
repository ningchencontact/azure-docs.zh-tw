---
title: 使用 Python 查詢 Azure SQL Database | Microsoft Docs
description: 本主題說明如何使用 Python 來建立連線到 Azure SQL Database 的程式，並使用 Transact-SQL 陳述式查詢。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: b9c33da4f002504a55802e4253d648ff87847d92
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271822"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>快速入門：使用 Python 查詢 Azure SQL 資料庫

 此快速入門示範如何使用 [Python](https://python.org) 來連線至 Azure SQL 資料庫，並使用 Transact-SQL 陳述式來查詢資料。 如需 SDK 的詳細資訊，請查看我們的[參考](https://docs.microsoft.com/python/api/overview/azure/sql)文件、[pyodbc GitHub 存放庫](https://github.com/mkleehammer/pyodbc/wiki/)，及 [pyodbc 範例](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，請確定您具備下列項目︰

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- 在此快速入門中，您所使用電腦的公用 IP 位址[伺服器層級防火牆規則](sql-database-get-started-portal-firewall.md)。
  
- 您作業系統的 Python 與相關軟體：
  
  - **MacOS**：安裝 Homebrew 和 Python，安裝 ODBC 驅動程式和 SQLCMD，然後再安裝 Python Driver for SQL Server。 請參閱[在 macOS 上使用 SQL Server 建立 Python 應用程式](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)中的步驟 1.2、1.3 和 2.1。 如需詳細資訊，請參閱[在 Linux 和 macOS 上安裝 Microsoft ODBC 驅動程式](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)。
    
  - **Ubuntu**：安裝 Python 和其他必要套件與 `sudo apt-get install python python-pip gcc g++ build-essential`。 下載並安裝 ODBC 驅動程式、SQLCMD 和 SQL Server 的 Python 驅動程式。 如需相關指示，請參閱[設定 pyodbc Python 開發的開發環境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)。
    
  - **Windows**：安裝 Python、ODBC 驅動程式、SQLCMD 和 SQL Server 的 Python 驅動程式。 如需相關指示，請參閱[設定 pyodbc Python 開發的開發環境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)。

## <a name="get-sql-server-connection-information"></a>取得 SQL Server 連線資訊

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="create-code-to-query-your-sql-database"></a>建立程式碼以查詢您的 SQL Database 

1. 在文字編輯器中，建立名為 *sqltest.py* 的新檔案。  
   
1. 新增下列程式碼。 用您自己的值取代 \<server>、\<database>、\<username>，以及 \<password>。
   
   >[!IMPORTANT]
   >此範例中的程式碼使用範例 AdventureWorksLT 資料，您可以在建立資料庫時選擇這些範例資料作為來源。 如果您的資料庫中有不同的資料，請在 SELECT 查詢中使用來自您自己資料庫的資料表。 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>執行程式碼

1. 在命令提示字元中，執行下列命令：

   ```cmd
   python sqltest.py
   ```

1. 請確認傳回前 20 個的類別/產品資料列，然後關閉命令視窗。

## <a name="next-steps"></a>後續步驟

- [設計您的第一個 Azure SQL Database](sql-database-design-first-database.md)
- [Microsoft Python Drivers for SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python 開發人員中心](https://azure.microsoft.com/develop/python/?v=17.23h)

