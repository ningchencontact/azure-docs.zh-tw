---
title: 使用 Azure Data Factory 從具有控制資料表的資料庫大量複製 | Microsoft Docs
description: 深入了解如何使用解決方案範本，利用 Azure Data Factory 從使用外部控制資料表的資料庫將資料大量完整複製到來源資料表的存放區資料分割清單。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: b267da18f2537e462ecda0ac265eac07a069c293
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966474"
---
# <a name="bulk-copy-from-database-with-control-table"></a>從具有控制資料表的資料庫大量複製

當您想要從資料倉儲 (例如 Oracle 伺服器、Netezza 伺服器、Teradata 伺服器或 SQL Server) 將資料複製到 Azure 時，您必須從資料來源中的多個資料表載入大量資料。 在大部分情況下，資料必須在每個資料表中進一步分割，讓您可以從單一資料表平行載入具有多個執行緒的資料列。 現存的範本是針對該案例而設計。 

如果您想要從具有少量資料的少量資料表複製資料，更有效率的做法是移至「複製資料工具」進行一個單一複製活動，或在管線中進行 foreach 活動 + 複製活動。 此範本能提供您比這個簡單的使用案例還多的項目。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會從外部控制資料表 (需要複製到目的地存放區) 擷取來源資料庫的資料分割清單，然後逐一查看來源資料庫中的每個資料分割，並且執行資料複製作業。

範本包含三個活動：
-   **查閱**活動，從外部控制資料表擷取來源資料庫的資料分割清單。
-   **ForEach**活動，從查閱活動取得資料分割清單，然後逐一查看每個活動進行複製活動。
-   **複製**活動，從來源資料庫存放區將每個資料分割複製到目的地存放區。

範本會定義五個參數：
-   參數 Control_Table_Name 是您外部控制資料表的名稱。 控制資料表是用來儲存來源資料庫的資料分割清單。
-   參數 Control_Table_Schema_PartitionID 是您的外部控制資料表中用來儲存每個資料分割識別碼的資料行名稱。 請確定來源資料庫中每個資料分割的資料分割識別碼是唯一的。
-   參數 Control_Table_Schema_SourceTableName 是您的外部控制資料表中用來儲存來源資料庫每個資料表名稱的資料行名稱。
-   參數 Control_Table_Schema_FilterQuery 是您的外部控制資料表中的資料行名稱，用來儲存篩選查詢以取得來源資料庫中每個資料分割的資料。 例如，如果您依據每年來分割資料，每個資料列中儲存的查詢會類似於 ‘select * from datasource where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''
-   參數 Data_Destination_Folder_Path 是資料夾路徑，該位置的資料複製到您的目的地存放區。  當您選擇的目的地是檔案型儲存體存放區時，才會顯示此參數。  如果您選擇 SQL 資料倉儲作為目的地存放區，就不需要在這裡輸入參數。 但是，SQL 資料倉儲中的資料表名稱和結構描述必須與來源資料庫中的項目相同。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 在 SQL Server 或 SQL Azure 中建立控制資料表，以儲存來源資料庫的資料分割清單進行大量複製。  您可以從下列範例看到在您的來源資料庫中有五個資料分割，其中三個資料分割適用於一個資料表：datasource_table，另外兩個資料分割適用於另一個資料表：project_table。 資料行 LastModifytime 是用來分割來源資料庫中資料表 datasource_table 的資料。 用來讀取第一個資料分割的查詢是 'select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''。  您也可以查看用來讀取其他資料分割資料的類似查詢。 

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. 移至範本**從資料庫大量複製**，然後建立與您的外部控制資料表的**新連線**。  這個連線會連線到資料庫，您在步驟 #1 中於該資料庫建立控制資料表。

    ![建立與控制資料表的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. 建立與來源資料庫 (資料複製來源) 的**新連線**。

     ![建立與來源資料庫的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. 建立與目的地資料存放區 (資料複製目標) 的**新連線**。

    ![建立與目的地存放區的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. 按一下 [使用此範本]。

    ![使用此範本](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. 您會看到在窗格中看到可用的管線，如下列範例所示：

    ![檢閱管線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. 按一下 [偵錯]、輸入參數，然後按一下 [完成]

    ![按一下 [偵錯]](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 您會看到在窗格中看到可用的結果，如下列範例所示：

    ![檢閱結果](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (選擇性) 如果您選取 SQL 資料倉儲作為資料目的地，您也需要輸入 Azure Blob 儲存體的連線作為暫存，SQL 資料倉儲 Polybase 有此需要。  請確定已在 Blob 儲存體中建立容器。  
    
    ![Polybase 設定](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)