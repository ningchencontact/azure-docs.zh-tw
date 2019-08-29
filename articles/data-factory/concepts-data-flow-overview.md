---
title: Azure Data Factory 對應資料流程概觀
description: Azure Data Factory 中對應資料流程的概觀說明
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 6f4c124c59584c8538d85ac61650661ae559a77b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123974"
---
# <a name="what-are-mapping-data-flows"></a>什麼是對應的資料流程？

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

對應資料流程是 Azure Data Factory 中以視覺化方式設計的資料轉換。 資料流程可讓資料工程師開發圖形化資料轉換邏輯, 而不需要撰寫程式碼。 結果的資料流程會以活動的形式在 Azure Data Factory [管線] 內執行，並使用擴充的 Azure Databricks 叢集。

Azure Data Factory 資料流程的目的，是為了在無須撰寫程式碼的情況下，提供完全視覺化的體驗。 您的資料流程將會在您自己的執行叢集上執行，以取得擴充的資料處理。 Azure Data Factory 會處理所有的程式碼轉譯、路徑最佳化，以及您資料流程作業的執行。

您可以先在偵錯模式中建立資料流程，以透過互動方式驗證您的轉換邏輯。 接下來，將資料流程活動新增到您的管線以在管線偵錯中執行並測試您的資料流程，或是在管線中使用 [立即觸發] 來從管線活動測試您的資料流程。

您接著便可以使用會執行資料流程活動的 Azure Data Factory 管線，對該資料流程活動進行排程和監視。

偵錯模式開關能開啟資料流程設計介面，來允許以互動方式建置資料轉換。 「偵錯工具模式」提供資料流程結構的資料準備和資料預覽環境。

## <a name="begin-building-your-data-flow-logical-graph"></a>開始建立資料流程邏輯圖形

使用 Factory 資源下的 + 符號, 開始建立資料流程, 以建立新的資料流程。

![新增資料流程](media/data-flow/newdataflow2.png "新增資料流程")

首先, 設定您的來源轉換, 然後使用 + 符號將資料轉換新增至每個後續步驟。 當您建立邏輯圖形時, 您可以使用 [顯示圖形] 和 [隱藏圖形] 按鈕, 在圖表和設定模式之間切換。

![顯示圖形](media/data-flow/showg.png "顯示圖形")

## <a name="configure-transformation-logic"></a>設定轉換邏輯

![隱藏圖形](media/data-flow/hideg.png "隱藏圖形")

隱藏您的圖形可讓您橫向流覽轉換節點。

![導覽](media/data-flow/showhide.png "導覽")

## <a name="next-steps"></a>後續步驟

* [從來源轉換開始](data-flow-source.md)
