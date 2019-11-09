---
title: 尋找影片中的確切時間 - 影片索引器
titleSuffix: Azure Media Services
description: 本主題將示範如何使用影片索引器搜尋影片中的確切時間。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 8ebf7606d1a0932cc2254e14a40a2fff550fd35f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833800"
---
# <a name="find-exact-moments-within-videos"></a>尋找影片中的確切時間

本主題將說明可讓您尋找影片中確切時間的搜尋選項。

1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。
2. 搜尋您帳戶中的所有影片。

    在下列範例中，我們會搜尋所有與安全性相關的影片，並在其中顯示 Satya。

    ![搜尋](./media/video-indexer-search/video-indexer-search01.png)
3. 搜尋影片的深入解析摘要。

    接著，藉由在影片上按一下 [播放]，您就可以在影片中進行搜尋。 然後，您可以選取 [搜尋] 索引標籤並在影片中進行搜尋。 

    在下列範例中，我們會搜尋所選影片內的「安全」。

    ![搜尋](./media/video-indexer-search/video-indexer-search02.png)

    如果您按一下其中一個結果，播放程式就會將您帶往影片中的該時間點。 您可以在應用程式中實現播放程式/深入解析檢視和同步處理。 如需詳細資訊，請參閱[將索引器介面控件內嵌到應用程式](video-indexer-embed-widgets.md)。 
4. 搜尋影片的明細。
    
    如果您想要根據找到的影片建立自己的剪輯，請按下 [**編輯**] 按鈕。 此頁面會顯示影片及其深入解析做為篩選準則。 如需詳細資訊，請參閱[檢視和編輯影片索引器的深入解析](video-indexer-view-edit.md)。 

    您可以在影片內搜尋，只顯示您感興趣的線條，並使用側邊深入解析來篩選您想要查看的元件。 當您完成時，您可以預覽剪輯並按 [**發佈**]，以建立出現在圖庫中的新剪輯。
    
    在下列範例中，我們會搜尋「mixed reality」文字。 我們也套用了其他篩選，如下列畫面所示。
    
    ![搜尋](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>後續步驟 

一旦您找到想使用的影片後，您可以繼續處理該影片，如以下其中一個主題中所述： 

- [使用您的影片深度深入解析](use-editor-create-project.md)
- [使用影片索引器 REST API 處理內容](video-indexer-use-apis.md)
- [將視覺介面控件內嵌到應用程式](video-indexer-embed-widgets.md)

## <a name="see-also"></a>另請參閱

[影片索引子概觀](video-indexer-overview.md)
