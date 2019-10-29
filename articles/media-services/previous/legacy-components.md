---
title: Azure 媒體服務舊版元件 |Microsoft Docs
description: 本主題討論 Azure 媒體服務的舊版元件。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2019
ms.author: juliako
ms.openlocfilehash: 30e8fa51df42d202d77ecdbc6a31fe3c7aaac6f4
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968614"
---
# <a name="azure-media-services-legacy-components"></a>Azure 媒體服務舊版元件

經過一段時間，媒體服務元件已有穩定的改良功能和增強功能。 因此，某些舊版元件已淘汰。 您可以在下列文章中找到如何將應用程式從舊版元件遷移至目前元件的指示。

## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>舊版元件和遷移指引的淘汰計畫

我們宣佈*Windows Azure 媒體編碼器*（WAME）和*Azure 媒體編碼器*（AME）媒體處理器已淘汰。 這些處理器將于2019年11月30日淘汰。

* [從 Windows Azure 媒體編碼器遷移至媒體編碼器標準](migrate-windows-azure-media-encoder.md)
* [從 Azure 媒體編碼器遷移至媒體編碼器標準](migrate-azure-media-encoder.md)

我們也宣佈淘汰下列媒體分析媒體處理器： 

|媒體處理器名稱|停用日期|其他注意事項|
|---|---|
|[Azure 媒體索引子2](media-services-process-content-with-indexer2.md)| 2020年1月1日|[Azure 媒體服務影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)會取代此媒體處理器。 如需詳細資訊，請參閱[從 Azure 媒體索引子2遷移至 Azure 媒體服務影片索引子](migrate-indexer-v1-v2.md)。|
|[Azure Media Indexer](media-services-index-content.md)|2020年10月1日|[Azure 媒體服務影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)會取代此媒體處理器。 如需詳細資訊，請參閱[從 Azure 媒體索引子遷移至 Azure 媒體服務影片索引子](migrate-indexer-v1-v2.md)
|[Azure 媒體臉部偵測器](media-services-face-and-emotion-detection.md)|2020年2月1日|此媒體分析的預覽處理器將會淘汰，而且不會移至正式推出。 我們將會評估其案例和使用案例，讓客戶進行未來的投資。|
|[Azure 媒體動態偵測器](media-services-motion-detection.md)|2020年2月1日|此媒體分析的預覽處理器將會淘汰，而且不會移至正式推出。 我們將會評估其案例和使用案例，讓客戶進行未來的投資。|
|[Azure 媒體 OCR](media-services-video-optical-character-recognition.md)|2020年2月1日|此媒體處理器會由[Azure 媒體服務影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)和[Azure 媒體服務 V3 API Video Analyzer 預設值](../latest/analyzing-video-audio-files-concept.md)取代。|
|[Azure 媒體影片縮圖](media-services-video-summarization.md)|2020年2月1日|此媒體處理器會由[Azure 媒體服務影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)和[Azure 媒體服務 V3 API Video Analyzer 預設值](../latest/analyzing-video-audio-files-concept.md)取代。|

## <a name="next-steps"></a>後續步驟

[從媒體服務 v2 移動至 v3 的移轉指導](../latest/migrate-from-v2-to-v3.md)
