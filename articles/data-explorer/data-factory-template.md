---
title: 使用 Azure Data Factory 範本從資料庫大量複製到 Azure 資料總管
description: 在本主題中，您將瞭解如何使用 Azure Data Factory 範本，從資料庫大量複製到 Azure 資料總管
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: acfcee6e8308bc508bd709c40bd1794beab3130f
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804726"
---
# <a name="use-azure-data-factory-template-for-bulk-copy-from-database-to-azure-data-explorer"></a>使用 Azure Data Factory 範本從資料庫大量複製到 Azure 資料總管

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自許多來源（例如應用程式、網站和 IoT 裝置）的大量資料串流。 Azure Data Factory 是完全受控的雲端式資料整合服務。 您可以使用 Azure Data Factory 服務，將現有系統的資料填入 Azure 資料總管資料庫，並在建立分析解決方案時節省時間。 

[Azure Data Factory 範本](/azure/data-factory/solution-templates-introduction)是預先定義的 Azure Data Factory 管線，可讓您快速開始使用 Data Factory 並縮短建立資料整合專案的開發時間。 **從資料庫大量複製到 Azure 資料總管**範本是使用**查閱**和**ForEach**活動所建立。 您可以使用此範本，針對每個資料庫或資料表建立許多管線，以便更快速地複製資料。 

> [!IMPORTANT]
> * 使用**從資料庫大量複製到 azure 資料總管**範本，將大量資料從 SQL Server 和 Google BigQuery 之類的資料庫複製到 azure 資料總管。 
> * 使用[複製工具](data-factory-load-data.md)，將包含少量或適中資料量的幾個資料表複製到 Azure 資料總管。 

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)
* [建立資料處理站](data-factory-load-data.md#create-a-data-factory)
* 資料庫中的資料來源

## <a name="create-controltabledataset"></a>建立 ControlTableDataset

**ControlTableDataset**會指出哪些資料會從來源複製到管線中的目的地。 資料列數目表示複製資料所需的管線總數。 **ControlTableDataset**應該定義為源資料庫的一部分。

SQL Server 來源資料表格式的範例：
    
    ```sql   
    CREATE TABLE control_table (
        PartitionId int,
        SourceQuery varchar(255),
        ADXTableName varchar(255)
    );
    ```
    
        |屬性  |描述  | 範例
        |---------|---------| ---------|
        |PartitionId   |   複製順序 | 1  |  
        |SourceQuery   |   指出管線執行時間期間將複製哪些資料的查詢 | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
        |ADXTableName  |  目的地資料表名稱 | MyAdxTable       |  

如果您的**ControlTableDataset**使用不同的格式，請為您的格式建立可比較的**ControlTableDataset** 。

## <a name="use-bulk-copy-from-database-to-azure-data-explorer-template"></a>使用從資料庫到 Azure 資料總管範本的大量複製

1. 在 [**現在就開始**吧] 頁面中，選取 [**從範本建立管線**] 圖格，或**從範本**選取右側 > **+**  > 管線的鉛筆圖示（[**作者**] 索引標籤），以開啟範本庫。

    ![Azure Data Factory 讓我們開始吧](media/data-factory-template/adf-get-started.png)

1. 選取 [**從資料庫將範本大量複製到 Azure 資料總管**]。
 
    ![從範本選取管線](media/data-factory-template/pipeline-from-template.png)

1.  在 [**從資料庫大量複製到 Azure 資料總管**] 視窗中，從下拉式選單選取 [現有資料集]。 

    * **ControlTableDataset** -用來控制資料表的連結服務，指出要從來源複製到目的地的資料，以及要將哪些資料放在目的地中。 
    * **SourceDataset** –連結服務到源資料庫。 
    * **AzureDataExplorerTable** -Azure 資料總管資料表。 如果資料集不存在，請[建立 Azure 資料總管連結服務](data-factory-load-data.md#create-the-azure-data-explorer-linked-service)以新增資料集。
    * 選取 [**使用此範本**]。

    ![設定大量複製 Azure 資料總管範本](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. 在 [活動] 外的畫布中選取一個區域，以存取範本管線。 選取 [**參數**] 以輸入資料表的參數，包括**名稱**（控制資料表名稱）和**預設值**（資料行名稱）。

    ![管線參數](media/data-factory-template/pipeline-parameters.png)

1.  選取 [查閱活動] **GetPartitionList**，以查看預設設定。 系統會自動建立查詢。
1.  選取命令活動**ForEachPartition**，選取**設定**
    * **批次計數**：選取1-50 中的 [數位]。 此選項會決定以平行方式執行的管線數目，直到達到**ControlTableDataset**的資料列數為止。 
    * 請勿選取 [**連續**] 核取方塊，讓管線批次以平行方式執行。

    ![ForEachPartition 設定](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > 最佳做法是以平行方式執行許多管線，以便更快速地複製資料。 分割來源資料表中的資料，並根據日期和資料表配置每個管線的分割區，以提高效率。

1. 選取 [**全部驗證**] 以驗證 ADF 管線。 請參閱**管線驗證輸出**。

    ![驗證範本管線](media/data-factory-template/validate-template-pipelines.png)

1. 視需要選取 [ **Debug**]，然後按一下 [**新增觸發**程式] 來執行管線。

    ![執行管線](media/data-factory-template/trigger-run-of-pipeline.png)    


您現在可以使用**從資料庫到 Azure 的大量複製資料總管**範本，從資料庫和資料表有效率地複製大量資料。

## <a name="next-steps"></a>後續步驟

* 瞭解[使用 Azure Data Factory 將資料複製到 Azure 資料總管](data-factory-load-data.md)的程式。

* 瞭解 Azure Data Factory 中的[Azure 資料總管連接器](/azure/data-factory/connector-azure-data-explorer)。

* 瞭解用於資料查詢的[Azure 資料總管查詢](/azure/data-explorer/web-query-data)。






