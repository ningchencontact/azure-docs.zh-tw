---
title: 使用 Azure Data Factory 中的一個控制資料表的資料庫中的差異複本 |Microsoft Docs
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
ms.openlocfilehash: c32592ce539eeb2dec71792e4a6eb31e7d904eff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312377"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>具有一個控制資料表的資料庫差異複本

本文說明可用於以累加方式新增或更新資料列從資料庫資料表載入至 Azure 使用的外部控制資料表用於儲存高水位線值的範本。

此範本需要來源資料庫結構描述包含時間戳記資料行或遞增索引鍵來識別新的或更新的資料列。

>[!NOTE]
> 如果時間戳記資料行中有您的來源資料庫，來識別新的或更新的資料列，但您不想要建立為差異複本所使用的外部控制資料表，您可以改為使用[Azure Data Factory 複製資料工具](copy-data-tool.md)取得管線。 該工具會從來源資料庫讀取新資料列，做為變數使用觸發程序排定的時間。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會先擷取舊的水位線值，並比較它與目前的水位線值。 在那之後，它會從來源資料庫，比較兩個水位線值之間複製只有變更。 最後，它會儲存至外部控制項資料表下一次載入差異資料的新高水位線值。

範本包含四個活動：
- **查閱**擷取舊的高水位線值儲存在外部控制資料表。
- 另一個**查閱**活動會從來源資料庫擷取目前的高水位線值。
- **複製**將所做的變更從來源資料庫複製到目的地存放區。 識別來源資料庫中的變更的查詢是類似於 ' 選取 * 從 Data_Source_Table 其中 TIMESTAMP_Column > 「 最後一個高水位線 」 和 TIMESTAMP_Column < ="目前高水位線"'。
- **SqlServerStoredProcedure**目前高水位線值寫入為差異複本的外部控制資料表下一次。

範本會定義五個參數：
- *Data_Source_Table_Name*是您想要將資料從來源資料庫中的資料表。
- *Data_Source_WaterMarkColumn*是用來辨識新的或更新資料列的來源資料表中的資料行的名稱。 此資料行的類型是通常*datetime*， *INT*，或類似。
- *Data_Destination_Folder_Path*或是*Data_Destination_Table_Name*是其中的資料會複製到目的地存放區中的位置。
- *Control_Table_Table_Name*是外部的控制資料表來儲存高水位線值。
- *Control_Table_Column_Name*是可儲存高水位線值的外部控制資料表中的資料行。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 瀏覽來源資料表您想来載入，並定義可用來識別新的或更新的資料列高水位線資料行。 此資料行的型別可能*datetime*， *INT*，或類似。 加入新的資料列時，都會增加此資料行的值。 下列範例來源資料表 (data_source_table)，我們可以使用*LastModifytime*為高水位線資料行的資料行。

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
    
2. 建立一個控制資料表在 SQL Server 或 Azure SQL Database 來儲存高水位線值載入差異資料。 在下列範例中，控制資料表的名稱是*watermarktable*。 下表中*WatermarkValue*是儲存高水位線的值，資料行，且其類型為*datetime*。

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. 您用來建立控制資料表位於相同 SQL Server 或 Azure SQL Database 執行個體中建立預存程序。 預存程序用來寫入下一次載入差異資料的外部控制資料表中的新高水位線值。

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. 移至**從資料庫的差異複本**範本。 建立**新增**連接到您想要複製資料從來源資料庫。

    ![建立與來源資料表的新連線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. 建立**新增**連接到您想要將資料複製到目的地資料存放區。

    ![建立與目的地資料表的新連線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 建立**新增**連接到外部控制資料表和您在步驟 2 和 3 中建立的預存程序。

    ![建立與控制資料表資料存放區的新連線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. 選取 **使用此範本**。

     ![使用此範本](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. 您會看到可用的管線，如下列範例所示：

     ![檢閱管線](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. 選取 **預存程序**。 針對**預存程序名稱**，選擇 **[update_watermark]**。 選取 **匯入參數**，然後選取**新增動態內容**。  

     ![將預存程序活動](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. 將內容寫入 **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}**，然後選取**完成**。  

     ![撰寫預存程序參數的內容](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. 選取 **偵錯**，輸入**參數**，然後選取**完成**。

    ![選取 [偵錯]](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. 結果類似下列範例會顯示：

    ![檢閱結果](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. 您可以在您的來源資料表中建立新的資料列。 以下是範例 SQL 語言來建立新的資料列：

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. 若要重新執行管線，請選取**偵錯**，輸入**參數**，然後選取**完成**。

    ![選取 [偵錯]](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    您會看到只有新的資料列已複製到目的地。

15. (選擇性：)如果您選取 SQL 資料倉儲做為資料目的地時，您也必須提供預備環境，SQL 資料倉儲 Polybase 所需的 Azure Blob 儲存體的連線。 請確定容器，已建立 Blob 儲存體中。
    
    ![設定 Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Factory 中的一個控制資料表的資料庫中的大量複製](solution-template-bulk-copy-with-control-table.md)
- [使用 Azure Data Factory 的多個容器中的檔案複製](solution-template-copy-files-multiple-containers.md)
