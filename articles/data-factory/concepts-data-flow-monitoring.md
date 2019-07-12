---
title: Azure Data Factory 對應資料流程視覺化監視
description: 如何以視覺化方式監視 Azure Data Factory 資料流程
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 90aa6261aebb9d1f7da89c101854bad8061dd6ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61268966"
---
# <a name="monitor-data-flows"></a>監視資料流程

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

在您完成建置和偵錯資料流程之後，您可將資料流程排定為在管線內容中依照排程執行。 您可以使用觸發程從 Azure Data Factory 進行管線排程。 或者，可以從 Azure Data Factory Pipeline Builder 使用 [立即觸發] 選項來執行單程執行，以在管線內容中測試您的資料流程。

當您執行您的管線時，您可以監視管線和管線內含的所有活動，包括「資料流程」活動。 按一下左側 Azure Data Factory UI 面板中的監視器圖示。 您會看到類似以下的畫面。 醒目提示的圖示可讓您向下鑽研到管線中的活動，包括「資料流程」活動。

<img src="media/data-flow/mon001.png" width="800">

您會看到此層級的統計資料，包括執行時間和狀態。 活動層級的執行識別碼與管線層級的執行識別碼不同。 前一層級的執行識別碼適用於管線。 按一下眼鏡可讓您深入了解資料流程執行的詳細資料。

<img src="media/data-flow/mon002.png" width="800">

當您在圖形化節點監視檢視時，您會看到經過簡化的僅供檢視資料流程圖版本。

<img src="media/data-flow/mon003.png" width="800">

## <a name="view-data-flow-execution-plans"></a>檢視資料流程執行計劃

在 Databricks 中執行資料流程時，Azure Data Factory 會根據資料流程的整體性決定最佳的程式碼路徑。 此外，執行路徑可能會發生在不同的相應放大節點和資料分割區上。 因此，監視圖表示您的流程設計，並將轉換的執行路徑納入考量。 當您按一下個別節點時，您會看到「群組 」，其代表在叢集上一起執行的程式碼。 您看見的時間和計數代表這些群組，而不是您設計中的個別步驟。

<img src="media/data-flow/mon004.png" width="800"> 

* 當您按一下監視視窗中的空白處時，底端窗格中的統計資料會顯示每個接收的時間和資料列計數，以及導向轉換歷程接收資料的轉換。

* 當您選取個別的轉換時，您會在右側面板上收到其他意見反應，其中顯示分割區統計資料、資料行計數、偏態 (資料平均分佈於分割區的程度) 和峰態 (資料的上升程度)。

* 當您按一下節點檢視中的 [接收] 時，您會看到資料行歷程。 有三種不同方法可讓資料行累積於資料流程以置入接收中。 其中包括：

  * 計算：您使用資料行進行條件式處理，或使用於資料流程中的運算式內，但請勿將它置入接收中
  * 衍生：資料行是您在流程中產生的新資料行，也就是不存在於來源
  * 對應：資料行源自來源，而您會將它對應至接收欄位
  
## <a name="monitor-icons"></a>監視器圖示

此圖示表示已在叢集上快取轉換資料，所以時間和執行路徑已將該點納入考量：

<img src="media/data-flow/mon005.png" width="800"> 

您也會在轉換中看到綠色圓圈圖示。 這些圖示代表資料流入的接收數目。
