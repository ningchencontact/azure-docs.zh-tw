---
title: 使用透過檢閱工具-Content Moderator 內容的評論
titlesuffix: Azure Cognitive Services
description: 了解審查工具如何讓人力審核者在入口網站審查影像。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: a482ecf4a0d321525ab7e392695d2c4c0eebeadc
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758493"
---
# <a name="create-human-reviews"></a>建立人工審核

本指南中，您將了解如何設定[檢閱](../review-api.md#reviews)檢閱工具網站上。 檢閱儲存，並且顯示人力仲裁者，以評估的內容。 仲裁者可以更改套用的標籤，並套用適當地自己自訂的標記。 當使用者完成檢閱時，結果會傳送至指定的回呼端點，和內容會從站台移除。

## <a name="prerequisites"></a>必要條件

- 登入或建立帳戶，在內容仲裁[審核工具](https://contentmoderator.cognitive.microsoft.com/)站台。

## <a name="image-reviews"></a>影像檢閱

1. 移至[審核工具](https://contentmoderator.cognitive.microsoft.com/)，選取**嘗試**索引標籤，然後上傳要檢閱一些映像。
1. 一旦上傳的映像已完成處理，請移至**檢閱**索引標籤，然後選取**映像**。

    ![顯示審核工具的 Chrome 瀏覽器，其中 [審核影像] 選項已反白顯示](images/review-images-1.png)

    自動審核程序已指派給任何標籤，顯示影像。 其他檢閱者看不到您已檢閱工具透過上傳的映像。

1. （選擇性） 移動**審核，以顯示**滑桿 (1) 若要調整的畫面顯示的影像數目。 按一下**標記**或是**未標記**按鈕 (2) 要據以排序的映像。 按一下以開啟或關閉標籤面板 (3)。

    ![顯示含已標記影像供審核之審核工具的 Chrome 瀏覽器](images/review-images-2.png)

1. 若要查看映像的詳細資訊，請按一下縮圖，然後選取省略符號**檢視詳細資料**。 您可以將映像指派給小組，以使用**移至**選項 (請參閱[小組](./configure.md#manage-team-and-subteams)一節，以深入了解子團隊)。

    ![[檢視詳細資料] 選項已反白顯示的影像](images/review-images-3.png)

1. 在詳細資料頁面上瀏覽影像審核資訊。

    ![仲裁詳細資料列在不同窗格的影像](images/review-images-4.png)

1. 在您視需要審查並更新標記指派後，請按 [下一步] 以提交您的審查。 在您提交之後，您大約有五秒的時間可按 [上一步] 按鈕，以返回上一個畫面並再次審查影像。 在這之後，影像不再存在於 [提交] 佇列中，而且無法再使用 [上一步] 按鈕。

## <a name="text-reviews"></a>文字檢閱

文字檢閱函式，類似於映像的評論。 而不是上傳的內容，您只要撰寫，或貼上文字 （最多 1,024 個字元）。 然後，Content Moderator 分析的文字，並套用標籤 （除了其他仲裁資訊，例如不雅內容和個人資料）。 文字檢閱中可以切換套用的標記試算表和 （或) 套用自訂標籤，再提交檢閱。

![審查工具的螢幕擷取畫面，其中顯示在 Chrome 瀏覽器視窗中加上旗標的文字](../images/reviewresults_text.png)

## <a name="next-steps"></a>後續步驟

本指南中，您已了解如何設定及使用來自內容仲裁評論[審核工具](https://contentmoderator.cognitive.microsoft.com)。 接下來，請參閱 < [REST API 指南](../try-review-api-review.md)或[.NET SDK 指南](../moderation-reviews-quickstart-dotnet.md)以了解如何以程式設計方式建立評論。