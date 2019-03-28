---
title: 針對 Azure SQL 資料倉儲問題進行疑難排解 | Microsoft Docs
description: 針對 Azure SQL 資料倉儲問題進行疑難排解。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 03/27/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 5115ffbc3568c87c37bae4a3e65c37f8504f1fb8
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541123"
---
# <a name="troubleshooting-connectivity-issues"></a>對連線問題進行疑難排解

本文章列出常見的疑難排解技巧，解決連接到您的 SQL 資料倉儲。
- [檢查服務可用性](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [檢查有已暫停或調整作業](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [檢查防火牆設定](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [檢查您的 VNet/服務端點設定](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [檢查有最新的驅動程式](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [請檢查您的連接字串](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [間歇性連線問題](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [常見的錯誤訊息](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>檢查服務可用性

請檢查服務是否可用。 在 Azure 入口網站中，移至您想要連接的 SQL 資料倉儲。 在左側的目錄窗格中，按一下**診斷並解決問題**。

![選取 資源健康狀態](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

您的 SQL 資料倉儲的狀態將會如下所示。 如果服務未顯示為**可用**，查看進一步的步驟。

![可用的服務](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

如果您的資源健康狀態會顯示您的資料倉儲已暫停或調整，請依照下列指導方針，繼續您的資料倉儲。

![服務暫停](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)可以在這裡找到資源健康狀態的其他資訊。

## <a name="check-for-paused-or-scaling-operation"></a>檢查有已暫停或調整作業

請檢查入口網站，請參閱 < 是否您的 SQL 資料倉儲已暫停或調整。

![暫停服務](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

如果您看到您的服務已暫停或調整，請檢查它不在您的維護排程期間。 入口網站的 SQL 資料倉儲*概觀*，您會看到選定的維護排程。

![概觀維護排程](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

否則，請檢查您的 IT 系統管理員，以確認此次維護作業沒有排程的事件。 若要繼續 SQL 資料倉儲，請依照下列所述的步驟[此處](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute)。

## <a name="check-your-firewall-settings"></a>檢查防火牆設定

SQL 資料倉儲會透過連接埠 1433 通訊。   如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 在此情況下，您無法連線到 Azure SQL Database 伺服器除非您的 IT 部門開啟連接埠 1433年。 可以找到上的防火牆組態的其他資訊[此處](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json#manage-ip-firewall-rules-using-the-azure-portal)。

## <a name="check-your-vnetservice-endpoint-settings"></a>檢查您的 VNet/服務端點設定

如果您收到錯誤 40914 與 40615，請參閱[錯誤描述和解析這裡](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)。

## <a name="check-for-the-latest-drivers"></a>檢查有最新的驅動程式

### <a name="software"></a>軟體

請檢查以確定您用來連線到 SQL 資料倉儲的最新工具：

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>驅動程式

請檢查以確定您使用最新的驅動程式版本。  使用較舊版本的驅動程式可能導致非預期的行為，因為舊版的驅動程式可能不支援新功能。

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>請檢查您的連接字串

請檢查並確定已正確設定您的連接字串。  以下是一些範例。  您可以找到其他相關資訊[連接字串這裡](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings)。

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

## <a name="intermittent-connection-issues"></a>間歇性連線問題

檢查看看是否您遇到大量的要求排入佇列的伺服器上的負載過重。 您可能需要相應增加您的資料倉儲，如需其他資源。

## <a name="common-error-messages"></a>常見的錯誤訊息

錯誤 40914 與 40615，請參閱[錯誤描述和解析這裡](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)。

## <a name="still-having-connectivity-issues"></a>仍然有連線問題嗎？
建立[支援票證](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket)讓工程小組可支援。
