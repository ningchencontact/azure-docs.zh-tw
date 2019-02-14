---
title: 檢視及編輯影片索引器深入解析
titlesuffix: Azure Media Services
description: 本主題會示範如何檢視和編輯影片索引器的深入解析。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: c1b26b8a59ac0306fc06bf90f01642d4585a45be
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991562"
---
# <a name="view-and-edit-video-indexer-insights"></a>檢視及編輯影片索引器深入解析

本主題會說明如何檢視和編輯影片的影片索引器深入解析。

1. 瀏覽至[影片索引器](https://www.videoindexer.ai/)網站並登入。
2. 尋找您要從中建立影片所影器深入解析的影片。 如需詳細資訊，請參閱[尋找影片中的確切時間](video-indexer-search.md)。
3. 按下 [播放]。

    頁面會顯示影片的深入解析摘要。 

    ![深入解析](./media/video-indexer-view-edit/video-indexer-summarized-insights.png)

4. 檢視影片的深入解析摘要。 

    深入解析摘要會顯示以下資料的彙總檢視：臉部、關鍵字、情緒。 例如，您可以看到人臉和每張臉出現的時間範圍，以及人臉出現的時間百分比。

    播放程式和深入解析會同步處理。 例如，如果您按一下關鍵字或文字記錄時段，播放程式就會將您帶往影片中的該時間點。 您可以在應用程式中實現播放程式/深入解析檢視和同步處理。 如需詳細資訊，請參閱[將 Azure 索引器介面控件內嵌到應用程式](video-indexer-embed-widgets.md)。 

3. 編輯影片索引器深入解析。

    按下影片下方的 [編輯]。 顯示影片完整明細的頁面會隨即出現。 明細會分成多個區塊。 這裡的區塊是為了讓您能更輕鬆地瀏覽資料。 例如，區塊的分割點可能會在說話者變更或長時間暫停時。 您可以建立您自己的播放清單，其中只包含您想要的時段。 若只要顯示來源影片的特定部分，您可以篩選主題/關鍵字、情緒、人員、說話者。 您可以選擇僅檢視影片文字記錄或 OCR。  

    ![深入解析](./media/video-indexer-view-edit/video-indexer-create-new-playlist.png)

## <a name="next-steps"></a>後續步驟

[了解如何根據一些其他影片來建立您自己的影片索引器深入解析](video-indexer-create-new.md)。

## <a name="see-also"></a>另請參閱

[影片索引子概觀](video-indexer-overview.md)

