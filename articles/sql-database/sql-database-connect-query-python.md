---
title: 使用 Python 查詢 Azure SQL Database | Microsoft Docs
description: 本主題說明如何使用 Python 來建立連線到 Azure SQL 資料庫的程式，並使用 Transact-SQL 陳述式查詢。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 89e67fd58f6cfc54e21406e9385e7ca5e5bc1d17
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569147"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>快速入門：使用 Python 查詢 Azure SQL 資料庫

 此快速入門示範如何使用 [Python](https://python.org) 來連線至 Azure SQL 資料庫，並使用 Transact-SQL 陳述式來查詢資料。 如需 SDK 的詳細資訊，請查看我們的[參考](https://docs.microsoft.com/python/api/overview/azure/sql)文件、[pyodbc GitHub 存放庫](https://github.com/mkleehammer/pyodbc/wiki/)，及 [pyodbc 範例](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)。

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
  |載入資料|每個快速入門載入的 Adventure Works|[還原 Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||從 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 中的 [BACPAC](sql-database-import.md) 檔案還原或匯入 Adventure Works|
  |||

  > [!IMPORTANT]
  > 本文中已撰寫的指令碼會使用 Adventure Works 資料庫。 對於受控執行個體，您必須將 Adventure Works 資料庫匯入執行個體資料庫中，或將本文中的指令碼修改為使用 Wide World Importers 資料庫。
  
- 您作業系統的 Python 與相關軟體：
  
  - **MacOS**：安裝 Homebrew 和 Python，安裝 ODBC 驅動程式和 SQLCMD，然後再安裝 Python Driver for SQL Server。 請參閱[在 macOS 上使用 SQL Server 建立 Python 應用程式](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)中的步驟 1.2、1.3 和 2.1。 如需詳細資訊，請參閱[在 Linux 和 macOS 上安裝 Microsoft ODBC 驅動程式](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)。

  - **Ubuntu**：安裝 Python 和其他必要套件與 `sudo apt-get install python python-pip gcc g++ build-essential`。 下載並安裝 ODBC 驅動程式、SQLCMD 和 SQL Server 的 Python 驅動程式。 如需相關指示，請參閱[設定 pyodbc Python 開發的開發環境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)。

  - **Windows**：安裝 Python、ODBC 驅動程式、SQLCMD 和 SQL Server 的 Python 驅動程式。 如需相關指示，請參閱[設定 pyodbc Python 開發的開發環境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)。

## <a name="get-sql-server-connection-information"></a>取得 SQL Server 連線資訊

取得連線到 Azure SQL 資料庫所需的連線資訊。 在後續程序中，您將需要完整的伺服器名稱或主機名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 瀏覽至 [SQL 資料庫]  或 [SQL 受控執行個體]  頁面。

3. 在 [概觀]  頁面上，針對單一資料庫檢閱 [伺服器名稱]  旁的完整伺服器名稱，若為受控執行個體，則檢閱 [主機]  旁的完整伺服器名稱。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製]  圖示。

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

- [設計您的第一個 Azure SQL 資料庫](sql-database-design-first-database.md)
- [Microsoft Python Drivers for SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python 開發人員中心](https://azure.microsoft.com/develop/python/?v=17.23h)

