---
title: 尋找影片中的確切時間 - 影片索引器
titlesuffix: Azure Media Services
description: 本主題將示範如何使用影片索引器搜尋影片中的確切時間。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 93376059dac2a8336e581269d8289d893779b291
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799100"
---
# <a name="find-exact-moments-within-videos"></a>尋找影片中的確切時間

本主題將說明可讓您尋找影片中確切時間的搜尋選項。

1. 瀏覽至[影片索引器](https://www.videoindexer.ai/)網站並登入。
2. 搜尋您帳戶中的所有影片。

    在下列範例中，我們會搜尋所有的影片，了解安全性和 Satya 其中顯示，

    ![Search](./media/video-indexer-search/video-indexer-search01.png)
3. 搜尋影片的深入解析摘要。

    接著，藉由在影片上按一下 [播放]  ，您就可以在影片中進行搜尋。 然後，您可以選取 [搜尋]  索引標籤並在影片中進行搜尋。 

    在下列範例中，我們搜尋 「 安全 」 內選取之視訊。

    ![Search](./media/video-indexer-search/video-indexer-search02.png)

    如果您按一下其中一個結果，播放程式就會將您帶往影片中的該時間點。 您可以在應用程式中實現播放程式/深入解析檢視和同步處理。 如需詳細資訊，請參閱[將索引器介面控件內嵌到應用程式](video-indexer-embed-widgets.md)。 
4. 搜尋影片的明細。
    
    如果您想要建立您自己剪輯會根據您所找到的視訊，請按下**編輯** 按鈕。 此頁面會顯示其深入解析以及視訊做為篩選。 如需詳細資訊，請參閱[檢視和編輯影片索引器的深入解析](video-indexer-view-edit.md)。 

    您可以搜尋的影片，以便只顯示您感興趣，並使用戶端 insights 篩選您想要查看組的件的行。 當您完成時，您可以預覽您的剪輯，然後按**發佈**來建立新的項目出現在您的資源庫中。
    
    在下列範例中，我們會搜尋"mixed reality"文字。 我們也套用了其他篩選，如下列畫面所示。
    
    ![Search](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>後續步驟 

一旦您找到想使用的影片後，您可以繼續處理該影片，如以下其中一個主題中所述： 

- [使用您的影片深入解析](use-editor-create-project.md)
- [使用影片索引器 REST API 處理內容](video-indexer-use-apis.md)
- [將視覺介面控件內嵌到應用程式](video-indexer-embed-widgets.md)

## <a name="see-also"></a>另請參閱

[影片索引子概觀](video-indexer-overview.md)
