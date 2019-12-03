---
title: 使用單鍵內嵌將資料內嵌至 Azure 資料總管
description: 瞭解如何只使用單鍵內嵌，將資料內嵌（載入）至 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: c4ee4ed81cd4cc443a8f412462a5a7f204c91898
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688183"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>使用單鍵內嵌將資料內嵌至 Azure 資料總管

本文說明如何使用單鍵內嵌，從儲存體或本機檔案將 json 或 csv 格式的新資料表快速內嵌到現有的資料表或 Azure 資料總管中的新資料表。 使用直覺的 wizard 並在幾分鐘內就會內嵌您的資料、您可以編輯資料表，以及使用 Web UI 執行查詢。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 登入[應用程式](https://dataexplorer.azure.com/)。
* 建立[Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)
* 登入[WEB UI](https://dataexplorer.azure.com/)並新增連線[到您](/azure/data-explorer/web-query-data#add-clusters)的叢集
* Azure 儲存體中的資料來源。

## <a name="ingest-new-data"></a>內嵌新資料

1. 在 Web UI 的左側功能表中，以滑鼠右鍵按一下資料庫或*資料表* *資料*列，然後選取 [內嵌**新資料（預覽）** ]

    ![在 web UI 中選取一個點擊內嵌](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. 在 [內嵌**新資料（預覽）** ] 視窗的 [**來源**] 索引標籤中，完成**專案詳細資料**：

    * **資料表**：從下拉式清單中選取現有的資料表名稱，或選取 **[新建] 以建立**新的資料表。
    * **從 [儲存體**] 或 [**從**檔案] 選取 [內嵌**類型** > ]。
        * 如果您已選取 [**從儲存體**]，請輸入 [儲存體] 的**連結**，以將 url 新增至儲存體。 使用私人儲存體帳戶的[BLOB SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) 。 
        * 如果您選取 [**從**檔案]，請選取 **[流覽]** ，並將檔案拖曳至方塊中。
    * 選取 [**編輯架構**] 以查看並編輯您的資料表資料行設定。
 
    ![一次按一下內嵌來源詳細資料](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > 如果您在*資料表*資料列上選取 [內嵌**新資料（預覽）** ]，選取的資料表名稱會出現在 [**專案詳細**資料] 中。

1. 如果您選取現有的資料表，[**對應資料行**] 視窗就會開啟，以將來源資料行對應至目標資料表資料行。 
    * 使用 [**省略資料行**]，從資料表中移除目標資料行。 
    * 使用 [**新增資料行**]，將新的資料行加入至資料表。 

    ![對應資料行視窗](media/ingest-data-one-click/one-click-map-columns-window.png)

1. 在 [**架構**] 索引標籤中：

    * 從下拉式 > 的 [**壓縮類型**] 選取 [**未壓縮**] 或 [ **GZip**]。
    * 從下拉式 > **JSON**、 **CSV**、 **TSV**、 **SCSV**、 **SOHSV**、 **TSVE**或**PSV**中選取 [**資料格式**]。 
        * 當您選取 [ **json**格式] 時，請選取 [ **json 層級**： 1-10]。 這些層級會影響資料表資料行的資料描述。 
        * 如果您選取 JSON 以外的格式：選取 [包含資料行名稱以忽略檔案的標題資料**列**]。    
    * **對應名稱**會自動設定，但可供編輯。
    * 如果您選取現有的資料表，則可以選取 [**對應資料行**] 按鈕來開啟 [對應資料**行**] 視窗。

    ![按一下 [內嵌 csv 格式架構 .png]](media/ingest-data-one-click/one-click-csv-format.png)

1. 在**編輯器**中，選取右側的 [ **V** ] 以開啟編輯器。 在編輯器中，您可以查看並複製從輸入產生的自動查詢。 

1.  在資料表中： 
    * 以滑鼠右鍵按一下新的資料行標頭，以**變更資料類型**、**重新命名資料行**、**刪除資料行**、**昇冪**或**遞減**排序。 在現有的資料行上，只能使用資料排序。 
    * 按兩下要編輯的新資料行名稱。

1. 選取 [**開始**內嵌] 以建立資料表、建立對應，以及資料內嵌。

    ![按一次內嵌 json 格式架構](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>查詢資料

1. 在 [**資料內嵌已完成**] 視窗中，如果已順利完成資料內嵌，則所有三個步驟都會標示綠色核取記號。 
 
    ![按一次資料內嵌完成](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. 選取 [ **V** ] 以開啟 [查詢]。 複製到 Web UI 以編輯查詢。

1. 右邊的功能表包含**快速查詢**和**工具**。 

    * **快速查詢**包含具有範例查詢之 Web UI 的連結。
    * **工具**包含含有**Drop 命令**的 Web UI 連結，可讓您藉由執行相關的 `.drop` 命令來針對問題進行疑難排解。

    > [!TIP]
    > 資料可能會使用 `.drop` 命令而遺失。 請小心使用它們。

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管 Web UI 中查詢資料](web-query-data.md)
* [使用 Kusto 查詢語言撰寫 Azure 資料總管的查詢](write-queries.md)
