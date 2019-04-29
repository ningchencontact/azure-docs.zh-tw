---
title: 使用 Azure Data Factory 中的一個控制資料表大量複製來源資料庫 |Microsoft Docs
description: 了解如何使用解決方案範本來從資料庫中複製大量資料，藉由使用 Azure Data Factory，將來源資料表的資料分割清單中使用的外部控制資料表。
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
ms.openlocfilehash: c4224693642e8c9f76deedc0c8ad8586e122cc23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635342"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>控制資料表的資料庫中的大量複製

若要將資料從 Oracle Server、 Netezza、 Teradata 或 SQL Server 中的資料倉儲複製到 Azure SQL 資料倉儲，您有多個資料表載入大量資料。 通常，資料必須分割每個資料表中，以便您可以從單一資料表中載入多個執行緒平行的資料列。 本文說明在這些情況下使用的範本。

 >!請注意是否您想要從少量且相對較小的資料磁碟區的資料表複製資料到 SQL 資料倉儲，則使用更有效率[Azure Data Factory 複製資料工具](copy-data-tool.md)。 這篇文章所述的範本可以是多個您需要針對該案例。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會擷取外部控制資料表複製的來源資料庫資料分割的清單。 然後它會逐一查看來源資料庫中的每個分割區，並將資料複製到目的地。

範本包含三個活動：
- **查閱**從外部控制資料表中擷取確定資料庫資料分割的清單。
- **ForEach**查閱活動從取得資料分割清單，並逐一查看給複製活動的每個資料分割。
- **複製**將從來源資料庫存放區的每個分割區複製到目的地存放區。

範本會定義五個參數：
- *Control_Table_Name*是控制外部資料表時，它會儲存來源資料庫的資料分割清單。
- *Control_Table_Schema_PartitionID*名稱資料行名稱，在您的外部控制資料表，其中儲存每個分割區識別碼。 請確定分割區識別碼是唯一的來源資料庫中的每個資料分割。
- *Control_Table_Schema_SourceTableName*是您儲存每個資料表名稱，從來源資料庫的外部控制資料表。
- *Control_Table_Schema_FilterQuery*是您儲存篩選查詢，從來源資料庫中的每個分割區取得資料的外部控制資料表中的資料行的名稱。 例如，如果您是依年份來分割資料，會儲存在每個資料列的查詢可能會類似於 '選取 * 從資料來源其中 LastModifytime > =' 2015年-01-01 00:00:00 '和 LastModifytime < =' 2015年-12-31 23:59:59.999 ' '。
- *Data_Destination_Folder_Path*是將資料複製到您的目的地存放區的位置的路徑。 此參數才會顯示您所選擇的目的地是檔案型存放裝置。 如果您選擇做為目的地存放區的 SQL 資料倉儲時，就不需要此參數。 但是，資料表名稱和 SQL 資料倉儲中的結構描述必須與來源資料庫中的項目相同。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 在 SQL Server 或 Azure SQL Database 來儲存大量複製的來源資料庫資料分割清單中建立控制資料表。 在下列範例中，有五個資料分割之來源資料庫中。 三個資料分割對於*datasource_table*，另外兩個用於*project_table 中*。 資料行*LastModifytime*用來分割資料表中的資料*datasource_table*從來源資料庫。 用來讀取第一個資料分割的查詢是 '選取 * 從 datasource_table 其中 LastModifytime > =' 2015年-01-01 00:00:00 '和 LastModifytime < =' 2015年-12-31 23:59:59.999 ' '。 您可以使用類似的查詢，從其他分割區讀取資料。

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

2. 移至**資料庫中的大量複製**範本。 建立**新增**連接至您在步驟 1 中建立的外部控制資料表。

    ![建立與控制資料表的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. 建立**新增**連接到您在複製資料從來源資料庫。

     ![建立與來源資料庫的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. 建立**新增**連接到目的地資料存放區，您要複製的資料。

    ![建立與目的地存放區的新連線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. 選取 **使用此範本**。

    ![使用此範本](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. 您會看到管線中，如下列範例所示：

    ![檢閱管線](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. 選取 **偵錯**，輸入**參數**，然後選取**完成**。

    ![按一下 [偵錯]](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 您會看到類似下列範例的結果：

    ![檢閱結果](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. （選擇性）如果您選擇做為資料目的地的 SQL 資料倉儲，您必須輸入連接到 Azure Blob 儲存體暫存，所需的 SQL 資料倉儲 Polybase。 請確定已建立 Blob 儲存體中的容器。
    
    ![Polybase 設定](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)
