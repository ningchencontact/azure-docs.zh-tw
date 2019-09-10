---
title: 針對 Azure SQL 資料倉儲問題進行疑難排解 | Microsoft Docs
description: 針對 Azure SQL 資料倉儲問題進行疑難排解。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: ebdeaf21253e89a9a14e3a56ca7be0f6e8adceb0
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859223"
---
# <a name="troubleshooting-connectivity-issues"></a>對連線問題進行疑難排解

本文列出連接到您的 SQL 資料倉儲時常見的疑難排解技巧。
- [檢查服務可用性](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [檢查已暫停或調整作業](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [檢查您的防火牆設定](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [檢查您的 VNet/服務端點設定](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [檢查最新的驅動程式](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [檢查您的連接字串](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [間歇連接問題](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [常見的錯誤訊息](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>檢查服務可用性

查看服務是否可用。 在 Azure 入口網站中，移至您嘗試連接的 SQL 資料倉儲。 在左側目錄面板中，按一下 [**診斷並解決問題**]。

![選取資源健康狀態](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

SQL 資料倉儲的狀態會顯示在這裡。 如果服務未顯示為 [**可用**]，請查看進一步的步驟。

![可用的服務](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

如果您的資源健康狀態顯示您的資料倉儲已暫停或調整，請遵循指引來繼續資料倉儲。

![服務已](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)暫停有關資源健康狀態的其他資訊，可在這裡找到。

## <a name="check-for-paused-or-scaling-operation"></a>檢查已暫停或調整作業

檢查入口網站，查看您的 SQL 資料倉儲是否已暫停或調整。

![服務已暫停](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

如果您看到您的服務已暫停或調整，請檢查它是否不在維護排程期間。 在您的 SQL 資料倉儲*總覽*的入口網站上，您會看到已選擇的維護排程。

![總覽維護排程](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

否則，請洽詢您的 IT 系統管理員，確認此維護不是已排程的事件。 若要繼續 SQL 資料倉儲，請遵循[這裡](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute)所述的步驟。

## <a name="check-your-firewall-settings"></a>檢查您的防火牆設定

SQL 資料倉儲會透過連接埠 1433 通訊。   如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 在此情況下，除非您的 IT 部門開啟埠1433，否則您無法連線到您的 Azure SQL Database 伺服器。 如需防火牆設定的詳細資訊，請參閱[這裡](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)。

## <a name="check-your-vnetservice-endpoint-settings"></a>檢查您的 VNet/服務端點設定

如果您收到錯誤40914和40615，請參閱[這裡的錯誤描述和解決](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)方式。

## <a name="check-for-the-latest-drivers"></a>檢查最新的驅動程式

### <a name="software"></a>軟體

請檢查以確定您使用的是最新的工具來連接到您的 SQL 資料倉儲：

* SSMS
* Azure Data Studio
* SQL Server Data Tools （Visual Studio）

### <a name="drivers"></a>驅動程式

請檢查以確定您使用的是最新的驅動程式版本。  使用較舊版本的驅動程式可能會導致非預期的行為，因為較舊的驅動程式可能不支援新的功能。

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>檢查您的連接字串

請檢查並確定您的連接字串設定正確。  以下是一些範例。  您可以在這裡找到關於[連接字串](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings)的其他資訊。

ADO.NET 連接字串

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC 連接字串

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP 連接字串

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC 連接字串

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>間歇連接問題

檢查是否有大量的佇列要求在伺服器上遇到繁重的負載。 您可能需要相應增加您的資料倉儲，以取得其他資源。

## <a name="common-error-messages"></a>常見的錯誤訊息

錯誤40914和40615，請參閱[這裡的錯誤描述和解決方法](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)。

## <a name="still-having-connectivity-issues"></a>仍然有連線問題嗎？
建立[支援票證](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket)，讓工程小組能夠支援您。
