---
title: 使用單鍵內嵌將資料內嵌至 Azure 資料總管
description: 瞭解如何只使用單鍵內嵌, 將資料內嵌 (載入) 至 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: b1ce2d9efe44021b4e3191739bd2f922e34c44cb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520045"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>使用單鍵內嵌將資料內嵌至 Azure 資料總管

本文說明如何使用單鍵內嵌, 將 json 或 csv 格式的新資料表快速內嵌到 Azure 資料總管。 內嵌資料之後, 您就可以編輯資料表, 並使用 Web UI 執行查詢。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 登入[應用程式](https://dataexplorer.azure.com/)。
* 建立[Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)
* Azure 儲存體中的資料來源。

## <a name="ingest-new-data"></a>內嵌新資料

1. 以滑鼠右鍵按一下*資料庫名稱*, 然後選取 [內嵌**新資料 (預覽)** ]

    ![在 web UI 中選取一個點擊內嵌](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. 在 [**資料內嵌 (預覽)** ] 視窗的 [**來源**] 索引標籤中, 完成**專案詳細資料**:

    * 輸入新的**資料表名稱**。 
    * **從 [儲存體**] 選取 [內嵌**類型** > ]。
    * 輸入**儲存體的連結**將 url 新增至儲存體。 使用私人儲存體帳戶的 Blob SAS URL。 
    * 選取 [**編輯架構**]
 
    ![一次按一下內嵌來源詳細資料](media/ingest-data-one-click/one-click-ingestion-source.png) 

1. 在 [**架構**] 索引標籤中, 從下拉式 > **JSON**或**CSV**選取 [**資料格式**]。 
   
   如果選取**CSV**:
    * 選取 [**略過標題**以忽略 csv 檔案的標題資料列] 核取方塊。    
    * **對應名稱**會自動設定, 但可供編輯。

    ![按一下 [內嵌 csv 格式架構 .png]](media/ingest-data-one-click/one-click-csv-format.png)

   如果選取**JSON**:
    * 選取**JSON 層級**:1-10 從下拉式。 Json 檔案中的層級會顯示在右下方的表格中。 
    * **對應名稱**會自動設定, 但可供編輯。

    ![按一次內嵌 json 格式架構](media/ingest-data-one-click/one-click-json-format.png)  

1. 在**編輯器**中, 選取 [ **V** on right] 以開啟編輯器。 在編輯器中, 您可以查看並複製從輸入產生的自動查詢。 

1.  在右下方的資料表中: 
    * 選取 [資料行右側的**V** ] 來**重新命名資料行**、**刪除資料行**、**昇冪**或**遞減排序**
    * 按兩下 [資料行名稱] 進行編輯。
    * 選取資料行名稱左邊的圖示以變更資料類型。 

1. 選取 [**開始**內嵌] 以建立資料表、建立對應, 以及資料內嵌。
 
## <a name="query-data"></a>查詢資料

1. 在 [**資料內嵌已完成**] 視窗中, 如果已順利完成資料內嵌, 則所有三個步驟都會標示綠色核取記號。 
 
    ![按一次資料內嵌完成](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. 選取 [ **V** ] 以開啟 [查詢]。 複製到 Web UI 以編輯查詢。

1. 右邊的功能表包含**快速查詢**和**工具**。 

    * **快速查詢**包含具有範例查詢之 Web UI 的連結。
    * **工具**包含含有**Drop 命令**的 Web UI 連結, 可讓您執行相關`.drop`的命令來針對問題進行疑難排解。

    > [!TIP]
    > 使用`.drop`命令可能會遺失資料。 請小心使用它們。

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管 Web UI 中查詢資料](web-query-data.md)
* [使用 Kusto 查詢語言撰寫 Azure 資料總管的查詢](write-queries.md)
