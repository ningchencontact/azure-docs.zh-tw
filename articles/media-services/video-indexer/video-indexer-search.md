---
title: 尋找影片中的確切時間 - 影片索引器
titlesuffix: Azure Media Services
description: 本主題將示範如何使用影片索引器搜尋影片中的確切時間。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: d2c2e87b3dade68dc311aa38743c11a60a69f68b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004537"
---
# <a name="find-exact-moments-within-videos"></a>尋找影片中的確切時間

本主題將說明可讓您尋找影片中確切時間的搜尋選項。

1. 瀏覽至[影片索引器](https://www.videoindexer.ai/)網站並登入。
2. 搜尋您帳戶中的所有影片。

    在下列範例中，我們會搜尋所有由 Channel9 與 Scott Hanselman 建立的影片。

    ![Search](./media/video-indexer-search/video-indexer-search01.png)
3. 搜尋影片的深入解析摘要。

    接著，藉由在影片上按一下 [播放]，您就可以在影片中進行搜尋。 然後，您可以選取 [搜尋] 索引標籤並在影片中進行搜尋。例如，我們已搜尋所有使用「身分識別保護」文字的位置。 

    ![Search](./media/video-indexer-search/video-indexer-search02.png)

    如果您按一下其中一個結果，播放程式就會將您帶往影片中的該時間點。 您可以在應用程式中實現播放程式/深入解析檢視和同步處理。 如需詳細資訊，請參閱[將索引器介面控件內嵌到應用程式](video-indexer-embed-widgets.md)。 
4. 搜尋影片的明細。

    如果您想要根據所找到的影片建立自己的明細，請按下 [編輯] 按鈕。 此頁面會顯示影片的完整明細。 您可以在明細中搜尋，讓其只顯示您感興趣的時段。 如需詳細資訊，請參閱[檢視和編輯影片索引器的深入解析](video-indexer-view-edit.md)。

    在此範例中，我們已搜尋「身分識別保護」文字。 我們也套用了其他篩選，如下列畫面所示。

    ![Search](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>後續步驟 

一旦您找到想使用的影片後，您可以繼續處理該影片，如以下其中一個主題中所述： 

- [根據現有影片建立新的影片深入解析](video-indexer-create-new.md)
- [使用影片索引器 REST API 處理內容](video-indexer-use-apis.md)
- [將視覺介面控件內嵌到應用程式](video-indexer-embed-widgets.md)

## <a name="see-also"></a>另請參閱

[影片索引子概觀](video-indexer-overview.md)
