---
title: Azure 媒體服務影片索引器的版本資訊 |Microsoft Docs
description: 若要隨時掌握最新發展，這篇文章提供 Azure 媒體服務影片索引器的最新更新。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: f1c5f43316292f17547b84d970a0f03a1a2c366f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454018"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure 媒體服務影片索引器的版本資訊

為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

* 最新版本
* 已知問題
* 錯誤修正
* 已被取代的功能

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-indexer-deployed-to-japan-east"></a>影片索引器部署至日本東部

您現在可以建立付費帳戶，在日本東部地區的影片索引器。

### <a name="create-and-repair-account-api-preview"></a>建立並修復帳戶 API （預覽）

加入新的 API，可讓您[更新金鑰的 Azure 媒體服務連線端點](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)。

### <a name="improve-error-handling-on-upload"></a>改善錯誤處理上傳 

基礎的 Azure 媒體服務帳戶的設定不正確時，會傳回描述性訊息。

### <a name="player-timeline-keyframes-preview"></a>播放程式時間軸的主要畫面格預覽 

您現在可以看到每次影像預覽玩家的動態時報上。

### <a name="editor-semi-select"></a>編輯器以選取

您現在可以看到所有的結果在編輯器中，選擇特定的深入解析的時間範圍內選取的深入解析的預覽。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>更新自訂語言模型，從隱藏式輔助字幕檔案

[建立自訂語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag)並[更新自訂語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag)Api 現在支援 VTT，SRT，和 TTML 檔案格式做為輸入語言模型。

當呼叫[更新影片文字記錄 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)，文字記錄會自動新增。 影片與相關聯的定型模型會自動一併更新。 如需如何自訂和定型您的語言模型資訊，請參閱[自訂語言模型，使用影片索引器](customize-language-model-overview.md)。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新下載文字記錄格式 – TXT 和 CSV

關閉隱藏式輔助字幕的格式已支援 （SRT、 VTT 和 TTML），除了影片索引器現在支援下載 TXT 和 CSV 格式的文字記錄。

## <a name="next-steps"></a>後續步驟

[概觀](video-indexer-overview.md)
