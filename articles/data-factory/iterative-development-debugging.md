---
title: Azure Data Factory 中的反覆式開發和偵測 | Microsoft Docs
description: 了解如何在 Azure 入口網站中反覆地開發和偵錯 Data Factory 管線。
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 05/14/2018
ms.topic: article
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: e403afa5c870b2d007a8c5e1d46162cd899ead29
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164817"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>透過 Azure Data Factory 的反覆式開發與偵錯

Azure Data Factory 可讓您反覆地開發和偵錯 Data Factory 管線。

如需此功能的簡介與示範，請觀看下列 8 分鐘長的影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>反覆式偵錯功能
使用管線畫布中的 [偵錯] 功能來建立管線和執行測試回合，而不需撰寫任何一行程式碼。

![管線畫布上的偵錯功能](media/iterative-development-debugging/iterative-development-image1.png)

在管線畫布的 [輸出] 視窗中檢視測試回合的結果。

![管線畫布的輸出視窗](media/iterative-development-debugging/iterative-development-image2.png)

測試回合成功之後，將更多活動新增至您的管線並繼續以反覆方式進行偵錯。 您也可以 [取消] 正在進行的測試回合。

![取消測試回合](media/iterative-development-debugging/iterative-development-image3.png)

當您執行測試回合時，您不必在選取 [偵錯] 前，先將變更發佈至資料處理站。 如果您想要在更新資料處理站工作流程之前，先確保變更如預期般運作，這很有幫助。

## <a name="more-info-about-debugging"></a>偵錯詳細資訊

1. [監視] 索引標籤上不會列出使用 [偵錯] 功能起始的測試回合。在 [監視] 索引標籤中，您只能看到使用 [立即觸發]、[排程] 或 [輪轉視窗] 觸發程序所觸發的回合。在管線畫布的 [輸出] 視窗中，您可以看到使用 [偵錯] 功能起始的最後一個測試回合。

2. 選取 [偵錯] 實際上會執行管線。 因此，如果管線包含複製活動，則測試回合將資料從來源複製到目的地。 如此一來，我們建議您在偵錯時，於複製活動或其他活動中使用測試資料夾。 完成管線偵錯之後，請切換到您要在正常作業中使用的實際資料夾。

## <a name="setting-breakpoints-for-debugging"></a>設定偵錯的中斷點

Data Factory 也可讓您進行偵錯，直到您到達管線畫布上的特定活動為止。 只要將中斷點放在您要測試時的活動，然後選取 [偵錯]。 Data Factory 可確保測試回合只會進行到管線畫布上的中斷點活動。 如果您不想測試整個管線，而只想測試管線內的部分活動，這項 [偵錯直到] 功能很實用。

![管線畫布上的中斷點](media/iterative-development-debugging/iterative-development-image4.png)

若要設定中斷點，請選取管線畫布上的元素。 [偵錯直到] 選項會在元素的右上角顯示為空心的紅色圓圈。

![對選取的元素設定中斷點之前](media/iterative-development-debugging/iterative-development-image5.png)

在您選取 [偵錯直到] 選項後，它會變更為實心的紅色圓圈，以指出中斷點已啟用。

![對選取的元素設定中斷點之後](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>後續步驟
[Azure Data Factory 中的持續整合和部署](continuous-integration-deployment.md)
