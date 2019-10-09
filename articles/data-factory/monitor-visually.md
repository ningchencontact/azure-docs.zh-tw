---
title: 以視覺化方式監視 Azure 資料處理站 | Microsoft Docs
description: 瞭解如何以視覺化方式監視 Azure data factory
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: f7c27bde3806684045bc43f8ff99eefb14c8d04a
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029151"
---
# <a name="visually-monitor-azure-data-factories"></a>以視覺化方式監視 Azure 資料處理站
Azure Data Factory 是以雲端為基礎的資料整合服務。 您可以使用它在雲端中建立資料驅動的工作流程，以協調和自動化資料移動和資料轉換。 藉由使用 Azure Data Factory，您可以：

- 建立並排程資料驅動的工作流程 (稱為管線)，它可以從不同的資料存放區擷取資料。
- 使用計算服務（例如 Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure Machine Learning）來處理/轉換資料。
- 將輸出資料發佈至資料存放區，例如 Azure SQL 資料倉儲，讓商業智慧 (BI) 應用程式取用。

在此快速入門中，您將了解如何在不用撰寫任何程式碼的情況下，以視覺方式監視 Data Factory 管線。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="monitor-data-factory-pipelines"></a>監視 Data Factory 管線

使用簡單的清單視圖介面來監視管線和活動執行。 所有執行都會顯示在瀏覽器的當地時區中。 如果您變更時區，所有日期/時間欄位都會貼齊您選取的區域。  

1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這兩個網頁瀏覽器支援 Data Factory UI。
2. 登入[Azure 入口網站](https://portal.azure.com/)。
3. 在 Azure 入口網站中，移至建立的資料處理站的分頁。 選取 [**監視] & [管理**] 磚，以啟動 Data Factory 視覺效果監視體驗。

## <a name="monitor-pipeline-runs"></a>監視管線回合
清單視圖會顯示 Data Factory 管線的每個管線執行。 其中包含下列資料行：

| **資料行名稱** | **描述** |
| --- | --- |
| 管線名稱 | 管線的名稱 |
| 動作 | 可供觀看活動執行的單一動作 |
| 回合開始 | 管線執行的開始日期和時間（MM/DD/YYYY、HH： MM： SS AM/PM） |
| Duration | 回合持續時間 (HH:MM:SS) |
| 觸發方式 | 手動觸發程式或排程觸發程式 |
| 狀態 | **失敗**、**成功**或**進行中** |
| 參數 | 管線執行的參數（名稱/值配對） |
| Error | 管線執行錯誤（如果有的話） |
| 回合識別碼 | 管線執行的識別碼 |

![監視管線執行的清單視圖](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>監視活動回合
清單視圖會顯示對應至每個管線執行的活動執行。 若要查看每個管線執行的活動執行，請選取 [**動作**] 資料行底下的 [**活動執行**] 圖示。 清單視圖包含下列資料行：

| **資料行名稱** | **描述** |
| --- | --- |
| 活動名稱 | 管線內的活動名稱 |
| 活動類型 | 活動的類型，例如**Copy**、 **HDInsightSpark**或**HDInsightHive** |
| 回合開始 | 活動執行的開始日期和時間（MM/DD/YYYY，HH： MM： SS AM/PM） |
| Duration | 回合持續時間 (HH:MM:SS) |
| 狀態 | **失敗**、**成功**或**進行中** |
| Input | 描述活動輸入的 JSON 陣列 |
| Output | 描述活動輸出的 JSON 陣列 |
| Error | 活動執行錯誤（如果有的話） |

![監視活動執行的清單視圖](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> 您必須選取頂端的 **[重新**整理] 按鈕，以重新整理管線和活動執行的清單。 目前不支援自動重新整理。

![重新整理按鈕](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>選取要監視的資料處理站
將滑鼠停留在左上方的**Data Factory**圖示上。 選取箭號圖示，以查看您可以監視的 azure 訂用帳戶和資料處理站清單。

![選取 data factory](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>設定清單檢視

### <a name="apply-rich-ordering-and-filtering"></a>套用豐富的排序和篩選功能

順序管線會根據執行開始時間，在 DESC/ASC 中執行。 使用下列資料行篩選管線執行：

| **資料行名稱** | **描述** |
| --- | --- |
| 管線名稱 | 管線的名稱。 選項包括**過去24小時**、**過去一周**和**過去30天**的快速篩選。 或選取自訂的日期和時間。 |
| 回合開始 | 管線執行的開始日期和時間。 |
| 回合狀態 | 依狀態篩選執行：**成功**、**失敗**或**進行中**。 |

![篩選選項](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>新增或移除資料行
以滑鼠右鍵按一下清單視圖標題，然後選擇您想要在清單視圖中顯示的資料行。

![資料行的選項](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>調整資料行寬度
將滑鼠停留在資料行標頭上方，以增加和減少清單視圖中的資料行寬度。

## <a name="promote-user-properties-to-monitor"></a>將使用者屬性升階以便監視

您可以將任何管線活動屬性升階為使用者屬性，讓它變成您可以監視的實體。 例如，您可以將管線中複製活動的 [**來源**] 和 [**目的地**] 屬性升階為使用者屬性。 您也可以選取 [**自動產生**] 來產生複製活動的**來源**和**目的地**使用者屬性。

![建立使用者屬性](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> 您最多隻能將五個管線活動屬性升階為使用者屬性。

建立使用者內容之後，您可以在 [監視] 清單視圖中監視這些屬性。 如果複製活動的來源是資料表名稱，您可以在活動執行的清單視圖中，監視來源資料表名稱的資料行。

![活動執行清單沒有使用者屬性](media/monitor-visually/monitor-user-properties-image2.png)

![將使用者屬性的資料行新增至活動執行清單](media/monitor-visually/monitor-user-properties-image3.png)

![活動執行清單具有使用者屬性的資料行](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>在管線中重新執行活動

您現在可以在管線中重新執行活動。 選取 [ **View activity 執行**]，然後選取您的管線中要重新執行管線的活動。

![檢視活動執行](media/monitor-visually/rerun-activities-image1.png)

![選取活動執行](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>檢視重新執行歷程記錄

您可以在清單檢視中檢視所有管線執行的重新執行歷程記錄。

![檢視歷程記錄](media/monitor-visually/rerun-history-image1.png)

您也可以檢視特定管線執行的重新執行歷程記錄。

![檢視管線執行的歷程記錄](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>甘特圖視圖

使用 [甘特圖] 視圖以快速視覺化您的管線和活動執行。 您可以查看每個管線的 [甘特圖] 或 [依您在管線上建立的批註/標記群組]。

![甘特圖的範例](media/monitor-visually/gantt1.png)

![甘特圖注釋](media/monitor-visually/gantt2.png)

橫條的長度會通知管線的持續時間。 您也可以選取橫條以查看更多詳細資料。

![甘特圖持續時間](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>導覽
選取左下方的**資訊**圖示。 然後選取 [**引導式導覽**]，以取得如何監視管線和活動執行的逐步指示。

![導覽](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>意見反應
選取 [**意見**反應] 圖示，為我們提供各種功能或您可能面臨的任何問題的意見反應。

![意見反應](media/monitor-visually/feedback.png)

## <a name="alerts"></a>警示

您可以針對 Data Factory 中支援的計量提出警示。 在 [Data Factory 監視] 頁面上選取 [**監視** >  個**警示 & 計量**]，即可開始使用。

![Data factory [監視] 頁面](media/monitor-visually/alerts01.png)

如需此功能的 7 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>建立警示

1.  選取 [**新增警示規則**] 以建立新的警示。

    ![[新增警示規則] 按鈕](media/monitor-visually/alerts02.png)

1.  指定規則名稱，然後選取警示嚴重性。

    ![規則名稱和嚴重性的方塊](media/monitor-visually/alerts03.png)

1.  選取警示準則。

    ![目標準則的 Box](media/monitor-visually/alerts04.png)

    ![準則清單](media/monitor-visually/alerts05.png)

1.  設定警示邏輯。 您可以針對所有管線和對應活動，建立所選計量的警示。 您也可以選取特定的 [活動類型]、[活動名稱]、[管線名稱] 或 [失敗類型]。

    ![設定警示邏輯的選項](media/monitor-visually/alerts06.png)

1.  設定警示的電子郵件、SMS、推播和語音通知。 為警示通知建立動作群組，或選擇現有的群組。

    ![設定通知的選項](media/monitor-visually/alerts07.png)

    ![新增通知的選項](media/monitor-visually/alerts08.png)

1.  建立警示規則。

    ![建立警示規則的選項](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>後續步驟

若要瞭解如何監視和管理管線，請參閱以程式設計[方式監視和管理管線](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)一文。
