---
title: 使用 Java 查詢 Azure SQL Database
description: 說明如何使用 Java 來建立可連線到 Azure SQL 資料庫的程式，並使用 T-SQL 陳述式加以查詢。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019
ms.openlocfilehash: 273f8ad080b80a58b63e70ab2aa311c9c498d491
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640074"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>快速入門：使用 JAVA 查詢 Azure SQL 資料庫

本文示範如何使用 [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) 連線至 Azure SQL 資料庫。 您可以接著使用 T-SQL 陳述式來查詢資料。

## <a name="prerequisites"></a>必要條件

若要完成此範例，請確定您具有下列必要條件：

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
  
  > [!IMPORTANT]
  > 本文中已撰寫的指令碼會使用 Adventure Works 資料庫。 對於受控執行個體，您必須將 Adventure Works 資料庫匯入執行個體資料庫中，或將本文中的指令碼修改為使用 Wide World Importers 資料庫。

- 針對您的作業系統安裝的 Java 相關軟體：

  - **MacOS**：安裝 Homebrew 和 JAVA，然後安裝 Maven。 請參閱[步驟 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/)。

  - **Ubuntu**：安裝 Java、Java Development Kit，然後安裝 Maven。 請參閱[步驟 1.2、1.3 和 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/)。

  - **Windows**：安裝 Java，然後安裝 Maven。 請參閱[步驟 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/)。

## <a name="get-sql-server-connection-information"></a>取得 SQL Server 連線資訊

取得連線到 Azure SQL 資料庫所需的連線資訊。 在後續程序中，您將需要完整的伺服器名稱或主機名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 瀏覽至 [SQL 資料庫]  或 [SQL 受控執行個體]  頁面。

3. 在 [概觀]  頁面上，針對單一資料庫檢閱 [伺服器名稱]  旁的完整伺服器名稱，若為受控執行個體，則檢閱 [主機]  旁的完整伺服器名稱。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製]  圖示。 

## <a name="create-the-project"></a>建立專案

1. 從命令提示字元，建立名為 sqltest  的新 Maven 專案。

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. 將資料夾切換到 *sqltest*，並使用您慣用的文字編輯器開啟 pom.xml  。 使用下列代碼將 **Microsoft JDBC Driver for SQL Server** 新增到專案的相依性。

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. 此外，在 pom.xml  中，將下列屬性新增至您的專案。 如果您沒有屬性區段，您可以將它新增在相依性後面。

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. 儲存並關閉 pom.xml  。

## <a name="add-code-to-query-database"></a>新增程式碼以查詢資料庫

1. 您的 Maven 專案中應該已經有名為 App.java  的檔案，位於：

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. 開啟檔案，並以下列程式碼取代其內容。 然後，為您的伺服器、資料庫、使用者和密碼新增適當的值。

    ```java
    package com.sqldbsamples;

    import java.sql.Connection;
    import java.sql.Statement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.DriverManager;

    public class App {

        public static void main(String[] args) {

            // Connect to database
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
            Connection connection = null;

            try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName "
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > 程式碼範例會使用適用於 Azure SQL 的 **AdventureWorksLT** 範例資料庫。

## <a name="run-the-code"></a>執行程式碼

1. 在命令提示字元中，執行應用程式。

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. 請確認前 20 個資料列已傳回，然後關閉應用程式視窗。

## <a name="next-steps"></a>後續步驟

- [設計您的第一個 Azure SQL 資料庫](sql-database-design-first-database.md)  

- [Microsoft JDBC Driver for SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [回報問題/發問](https://github.com/microsoft/mssql-jdbc/issues)  
