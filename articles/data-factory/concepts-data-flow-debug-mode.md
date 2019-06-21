---
title: Azure Data Factory 對應資料流程偵錯模式
description: 在建置資料流程時啟動互動式偵錯工作階段
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d86725718217caf7fd1d9dd6d5d67362e5de7270
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147381"
---
# <a name="mapping-data-flow-debug-mode"></a>對應資料流程偵錯模式

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory 對應資料流程的偵錯模式可以與 「 資料流程偵錯 」 按鈕上方的設計介面切換。 當設計資料流程、 開啟偵錯模式可讓您以互動方式觀看資料圖形轉換，而您建置和偵錯資料流。 在 資料流程設計研討會，以及資料流程管線偵錯執行期間，可以使用偵錯工作階段。

![偵錯 按鈕](media/data-flow/debugbutton.png "偵錯 按鈕")

## <a name="overview"></a>概觀
開啟偵錯模式時，您會以互動方式建立您與使用中的 Spark 叢集的資料流程。 在 Azure Data Factory 中關閉偵錯後，將會關閉此工作階段。 您應該留意在您已開啟偵錯工作階段的期間內，由 Azure Databricks 所產生的每小時費用。

在大部分情況下，最好建置您的資料流入偵錯模式，讓您可以驗證您的商務邏輯和 Azure Data Factory 中發佈您的工作之前先檢視您的資料轉換。 使用 [管線] 面板上的 [偵錯] 按鈕，來測試您在管線內的資料流程。

> [!NOTE]
> 在 Data Factory 工具列上的綠色偵錯模式燈時，您將資料流程偵錯費率收費 8 核心/小時的一般計算的 60 分鐘與存留時間 

> [!NOTE]
>在資料流程的偵錯模式中執行時，您的資料不會寫入至接收轉換。 偵錯工作階段被要做為測試控管您的轉換。 偵錯期間不需要接收，而且會在資料流程中忽略。 如果您想要測試寫入資料接收器中，執行從 Azure Data Factory 管線的資料流程，並使用偵錯執行管線中。

## <a name="debug-settings"></a>偵錯設定
偵錯可以在資料流程的 畫布 工具列上依序按一下 偵錯設定 「 編輯設定。 您可以選取的限制及/或檔案來源，以便用於每個您的來源轉換。 此設定中的資料列限制為僅針對目前的偵錯工作階段。 您也可以選取要用於 SQL DW 來源暫存已連結的服務。 

![偵錯設定](media/data-flow/debug-settings.png "偵錯設定")

## <a name="cluster-status"></a>叢集狀態
沒有叢集狀態指標會變成綠色，當叢集已準備好進行偵錯在設計介面的頂端。 如果您的叢集已經暖機，則綠色指示器幾乎會立即出現。 如果您的叢集不已在執行進入偵錯模式時，則您必須等候 5-7 分鐘才能啟動叢集。 指標會旋轉直到其就緒。

當您完成偵錯，關閉偵錯參數，讓您的 Azure Databricks 叢集可以終止，並在您將無法再支付偵錯活動。

## <a name="data-preview"></a>資料預覽
若開啟偵錯，[資料預覽] 索引標籤在底部面板上亮起。 若未開啟偵錯模式，資料流程會顯示只有目前中繼資料進出 [檢查] 索引標籤中的每個轉換。資料預覽只會查詢您已設定為您的限制，在偵錯設定中的資料列數目。 您可能需要按一下 [擷取資料] 來重新整理資料預覽。

![資料預覽](media/data-flow/datapreview.png "資料預覽")

## <a name="data-profiles"></a>資料設定檔
您資料預覽 索引標籤中選取個別的資料行，就會出現在右側的資料方格中，有關每個欄位的詳細統計資料的圖表。 Azure Data Factory 會根據要顯示圖表類型的資料取樣近進行判斷。 高基數欄位會預設為 /NOT NULL 的圖表中，而具有較低的基數的類別和數值資料會顯示橫條圖顯示資料值的頻率。 您也會看到最大/len 長度的字串欄位中數值的欄位、 標準的開發人員、 百分位數、 計數和平均的最小/最大值。 

![資料行統計資料](media/data-flow/stats.png "資料行統計資料")

## <a name="next-steps"></a>後續步驟

一旦您完成建置和偵錯您的資料流程，[從管線中執行它。](control-flow-execute-data-flow-activity.md)

測試時的資料流程管線，使用管線[偵錯執行 [執行] 選項。](iterative-development-debugging.md)
