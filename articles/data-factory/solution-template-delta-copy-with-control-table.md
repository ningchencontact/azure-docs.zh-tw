---
title: 使用 Azure Data Factory 從具有控制資料表的資料庫增量複製 | Microsoft Docs
description: 深入了解如何使用解決方案範本，使用 Azure Data Factory 以增量方式僅從資料庫複製新的或更新的資料列。
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
ms.date: 12/24/2018
ms.openlocfilehash: 23e1255013cd5e52166fe0e59a8931dd9ecd81a0
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966434"
---
# <a name="delta-copy-from-database-with-control-table"></a>從具有控制資料表的資料庫增量複製

當您想要以增量方式僅從資料庫中的資料表將變更 (新的或更新的資料列) 載入具有外部控制資料表 (儲存高水位線值) 的 Azure。  現存的範本是針對該案例而設計。 

此範本要求來源資料庫的結構描述必須包含時間戳記資料行或遞增索引鍵，來識別新的或更新的資料列。

如果您的來源資料庫有時間戳記資料行可用來識別新的或更新的資料列，但是不想建立外部控制資料表來啟用增量複製，您可以使用複製資料工具來取得管線，該工具使用觸發程序排程時間作為變數，來讀取僅來自來源資料庫的新資料列。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本一律會先擷取舊的水位線值，然後將它與目前的水位線值做比較。 然後，它只會根據 2 個水位線值之間的比較，從來源資料庫複製變更。  完成時，它會將新的高水位線值儲存至外部控制資料表，在下一次進行增量資料載入。

範本包含四個活動：
-   **查閱**活動，擷取儲存在外部控制資料表中的舊高水位線值。
-   **查閱**活動，從來源資料庫擷取目前的高水位線值。
-   **複製**活動，僅從來源資料庫將變更複製到目的地存放區。 用來識別複製活動中來源資料庫變更的查詢，類似於 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > “last high-watermark” and TIMESTAMP_Column <= “current high-watermark”'。
-   **SqlServerStoredProcedure**活動，將目前的高水位線值寫入到外部控制資料表，在下一次進行增量複製。

範本會定義五個參數：
-   參數 Data_Source_Table_Name 是您想要從中載入資料的來源資料庫資料表名稱。
-   參數 Data_Source_WaterMarkColumn 是來源資料表中的資料行名稱，可用來識別新的或更新的資料列。 一般來說，此資料行的類型可以是 datetime 或 INT 等。
-   參數 Data_Destination_Folder_Path 或 Data_Destination_Table_Name 是資料複製到您目的地存放區的位置。
-   參數 Control_Table_Table_Name 是用來儲存高水位線值的外部控制資料表名稱。
-   參數 Control_Table_Column_Name 是用來儲存高水位線值的外部控制資料表中的資料行名稱。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 探索您想要載入的來源資料表，並且定義可以用來對新的或更新的資料列進行配量的高水位線資料行。 一般來說，此資料行的類型可以是 datetime 或 INT 等，它的資料會在新增資料列時持續增加。  從以下的範例來源資料表 (資料表名稱：data_source_table)，我們可以使用資料行 LastModifytime 作為高水位線資料行。

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. 在 SQL Server 或 SQL Azure 中建立控制資料表，以儲存高水位線值來進行增量資料載入。 您可以在下列範例中看到控制資料表的名稱是 watermarktable。 其中，用來儲存高水位線值的資料行名稱是 WatermarkValue，類型是 datetime。

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. 在相同的 SQL Server 或 SQL Azure 中建立預存程序，用來建立控制資料表。 預存程序是用來將新的高水位線值寫入至外部控制資料表，在下一次進行增量資料載入。

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. 移至範本**從資料庫進行增量複製**，並且建立與來源資料庫 (從其中複製資料) 的**新連線**。

    ![建立與來源資料表的新連線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. 建立與目的地資料存放區 (資料複製目標) 的**新連線**。

    ![建立與目的地資料表的新連線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 建立與外部控制資料表和預存程序的**新連線**。  連線到資料庫，您在步驟 #2 和 #3 中於該資料庫建立控制資料表和預存程序。

    ![建立與控制資料表資料存放區的新連線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. 按一下 [使用此範本]。

     ![使用此範本](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. 您會看到在窗格中看到可用的管線，如下列範例所示：

     ![檢閱管線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. 按一下預存程序活動，選取 [預存程序名稱]、按一下 [匯入參數]，然後按一下 [新增動態內容]。  

     ![設定預存程序活動](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. 撰寫內容 **@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}**，然後按一下 [完成]。  

     ![撰寫預存程序參數的內容](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. 按一下 [偵錯]、輸入參數，然後按一下 [完成]。

    ![按一下 [偵錯]](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. 您會看到在窗格中看到可用的結果，如下列範例所示：

    ![檢閱結果](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. 您可以在您的來源資料表中建立新的資料列。  用來建立新資料列的範例 sql 如下所示：

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
13. 藉由按一下 [偵錯]、輸入參數，然後按一下 [完成] 以再次執行管線。

    ![按一下 [偵錯]](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

14. 您只會看到新的資料列複製到目的地。

15. (選擇性) 如果您選取 SQL 資料倉儲作為資料目的地，您也需要輸入 Azure Blob 儲存體的連線作為暫存，SQL 資料倉儲 Polybase 有此需要。  請確定已在 Blob 儲存體中建立容器。  
    
    ![設定 Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)