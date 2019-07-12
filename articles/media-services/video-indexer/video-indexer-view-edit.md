---
title: 檢視及編輯影片索引器深入解析
titlesuffix: Azure Media Services
description: 本主題會示範如何檢視和編輯影片索引器的深入解析。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 24a723bd32cb40807da1757b3fffb925d4ba1fef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799167"
---
# <a name="view-and-edit-video-indexer-insights"></a>檢視及編輯影片索引器深入解析

本主題會說明如何檢視和編輯影片的影片索引器深入解析。

1. 瀏覽至[影片索引器](https://www.videoindexer.ai/)網站並登入。
2. 尋找您要從中建立影片所影器深入解析的影片。 如需詳細資訊，請參閱[尋找影片中的確切時間](video-indexer-search.md)。
3. 按下 [播放]  。

    頁面會顯示影片的深入解析摘要。 

    ![深入解析](./media/video-indexer-view-edit/video-indexer-summarized-insights.png)

4. 檢視影片的深入解析摘要。 

    深入解析摘要會顯示以下資料的彙總檢視：臉部、關鍵字、情緒。 例如，您可以看到人臉和每張臉出現的時間範圍，以及人臉出現的時間百分比。

    播放程式和深入解析會同步處理。 例如，如果您按一下關鍵字或文字記錄時段，播放程式就會將您帶往影片中的該時間點。 您可以在應用程式中實現播放程式/深入解析檢視和同步處理。 如需詳細資訊，請參閱[將 Azure 索引器介面控件內嵌到應用程式](video-indexer-embed-widgets.md)。 

## <a name="next-steps"></a>後續步驟

[使用您的影片深入解析](use-editor-create-project.md)

## <a name="see-also"></a>另請參閱

[影片索引子概觀](video-indexer-overview.md)

