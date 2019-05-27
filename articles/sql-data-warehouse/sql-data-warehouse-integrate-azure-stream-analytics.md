---
title: 搭配使用 Azure 串流分析與 SQL 資料倉儲 | Microsoft Docs
description: 搭配使用 Azure 串流分析與 SQL 資料倉儲以便開發解決方案的秘訣。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 94646c41d9894dd00018ff5ca44d76534d35e8c5
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873273"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>搭配使用 Azure 串流分析與 SQL 資料倉儲
Azure 串流分析是完全受控的服務，可用來對雲端中的串流資料進行低延遲、高可用性、可延展的複雜事件處理。 如需基本概念，請參閱 [Azure 串流分析簡介][Introduction to Azure Stream Analytics]。 您可以接著依照[開始使用 Azure 串流分析][Get started using Azure Stream Analytics]教學課程，了解如何使用串流分析建立端對端解決方案。

在本文中，您將了解如何使用「Azure SQL 資料倉儲」資料庫作為「串流分析」工作的輸出接收器。

## <a name="prerequisites"></a>必要條件
首先，執行[開始使用 Azure 串流分析][Get started using Azure Stream Analytics]教學課程中的下列步驟。  

1. 建立事件中樞輸入
2. 設定並啟動事件產生器應用程式
3. 佈建資料流分析工作
4. 指定工作輸入和查詢

接著，建立 Azure SQL 資料倉儲資料庫

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>指定工作輸出：Azure SQL 資料倉儲資料庫
### <a name="step-1"></a>步驟 1
在您的 Stream Analytics 作業中按一下**輸出**從上方的頁面上，然後按一下**新增**。

### <a name="step-2"></a>步驟 2
選取 SQL Database。

### <a name="step-3"></a>步驟 3
在下一頁輸入下列值：

* *輸出別名*：為這個工作輸出設定一個容易記的名稱。
* 訂用帳戶：
  * 如果 SQL 資料倉儲資料庫是在與此資料流分析工作相同的訂用帳戶中，則請選取 [使用目前訂用帳戶的 SQL Database]。
  * 如果您的資料庫是在不同的訂用帳戶中，請選取 [使用其他訂用帳戶的 SQL Database]。
* *資料庫：* 指定目的地資料庫的名稱。
* *伺服器名稱*：為您剛指定的資料庫指定伺服器名稱。 您可以使用 Azure 入口網站進行搜尋。

![][server-name]

* *使用者名稱*：指定具有資料庫寫入權限的帳戶的使用者名稱。
* *密碼*：提供指定之使用者帳戶的密碼。
* *資料表*：指定資料庫中目標資料表的名稱。

![][add-database]

### <a name="step-4"></a>步驟 4
按一下核取按鈕以新增此工作輸出，並確認串流分析可成功連接到資料庫。

當資料庫的連接成功時，您會看到入口網站中的通知。 您可以按一下測試，以測試資料庫的連接。

## <a name="next-steps"></a>後續步驟
如需整合概觀，請參閱 [SQL 資料倉儲整合概觀][SQL Data Warehouse integration overview]。

如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀][SQL Data Warehouse development overview]。

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
