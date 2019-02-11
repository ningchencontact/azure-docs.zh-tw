---
title: 從現有影片建立影片深入解析 - Azure
titlesuffix: Azure Media Services
description: 本主題將說明如何根據現有影片檔案建立及發佈影片深入解析。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 2f6ceeebd18a91472ee12f04c0ac8e602b05f269
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197546"
---
# <a name="create-highlights-from-existing-videos"></a>從現有影片建立醒目提示

本主題將說明如何根據一些其他影片建立及發佈影片深入解析。

1. 瀏覽至[影片索引器](https://www.videoindexer.ai/)網站並登入。
2. 尋找您要從中建立影片深入解析的影片。
3. 按下 [播放]。

    頁面會顯示影片的深入解析摘要。 

    ![深入解析](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. 按下 [編輯] 按鈕。

    此頁面會顯示影片的完整明細。 明細會分成多個區塊。 這裡的區塊是為了讓您能更輕鬆地瀏覽資料。 例如，區塊的分割點可能會在說話者變更或長時間暫停時。 您可以建立您自己的播放清單，其中只包含您想要的時段。 若只要顯示來源影片的特定部分，您可以篩選主題/關鍵字、情緒、人員、說話者。 您可以選擇僅檢視影片文字記錄或 OCR。    

    ![深入解析](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. 建立您的播放清單。

    若要從播放清單中新增或移除時段，請按 **+**/**-**。

5. 預覽您的播放清單。

    完成播放清單的建立後，請按 [預覽]。
6. 發佈播放清單。

    預覽播放清單之後，您就可以發佈該清單。

    播放清單發佈後，系統會將其新增至您的影片深入解析清單。


## <a name="next-steps"></a>後續步驟 

一旦建立新的播放清單後，您可以繼續處理此清單，如下列其中一個主題所述： 

- [使用影片索引器 REST API 處理內容](video-indexer-use-apis.md)
- [將視覺介面控件內嵌到應用程式](video-indexer-embed-widgets.md)

## <a name="see-also"></a>另請參閱

[影片索引子概觀](video-indexer-overview.md) 
