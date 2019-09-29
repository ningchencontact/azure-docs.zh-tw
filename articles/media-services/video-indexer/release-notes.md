---
title: Azure 媒體服務影片索引子版本資訊 |Microsoft Docs
description: 為了讓您隨時掌握最新的開發，本文提供 Azure 媒體服務影片索引子的最新更新。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 09/26/2019
ms.author: juliako
ms.openlocfilehash: 4953160a30e45b9be1ff249b6cd1f74e01bb14f3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672666"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure 媒體服務影片索引子版本資訊

為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

* 最新版本
* 已知問題
* 錯誤修正
* 已被取代的功能

## <a name="september-2019"></a>2019年9月
 
在 IBC 2019 宣佈多項改進：
 
* 動畫字元識別（公開預覽）

    能夠透過與自訂視覺整合，偵測動畫內容中的群組廣告辨識字元。 如需詳細資訊，請參閱[動畫字元偵測](animated-characters-recognition.md)。
* 多重語言識別（公開預覽）

    在音訊播放軌中偵測多種語言的區段，並根據它們建立多語系文字記錄。 初始支援：英文、西班牙文、德文和法文。 如需詳細資訊，請參閱[自動識別和轉譯多語言內容](multi-language-identification-transcription.md)。
* 人員和位置的命名實體解壓縮

    透過自然語言處理（NLP），從語音和視覺文字中解壓縮品牌、位置和人員。
* 編輯照片類型分類

    以編輯類型（如近條、中度、兩次、室內、戶外等等）標記拍照。如需詳細資訊，請參閱[編輯快照類型偵測](scenes-shots-keyframes.md#editorial-shot-type-detection)。
* 主題推斷增強功能-現在涵蓋層級2
    
    推斷模型的主題現在支援 IPTC 分類法更深入的資料細微性。 閱讀[Azure 媒體服務新的 AI 技術創新](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)的完整詳細資料。

## <a name="august-2019"></a>2019 年 8 月
 
### <a name="video-indexer-deployed-in-uk-south"></a>英國南部中部署的影片索引子

您現在可以在英國南部區域建立影片索引子付費帳戶。

### <a name="new-editorial-shot-type-insights-available"></a>提供新的編輯快照類型深入解析

新增至影片快照的新標籤會提供編輯「拍攝類型」，以使用內容建立工作流程中常用的編輯片語來識別它們，例如：極端特寫、特寫、寬、中、兩次拍照、戶外、室內、左側臉部和右方臉部（可在JSON）。

### <a name="new-people-and-locations-entities-extraction-available"></a>新的人員和位置實體可供提取

影片索引子會從影片的 OCR 和轉譯中，透過自然語言處理（NLP）識別命名位置和人員。 影片索引子會使用機器學習演算法來辨識特定位置（例如，Eiffel 塔式）或人員（例如，John Doe）在影片中被呼叫的時間。

### <a name="keyframes-extraction-in-native-resolution"></a>以原生解析度解壓縮的主要畫面格

影片索引子所解壓縮的主要畫面格可在影片的原始解析度中取得。
 
### <a name="ga-for-training-custom-face-models-from-images"></a>針對從影像定型自訂臉部模型的 GA

從預覽模式移至 GA （可透過 API 和在入口網站中取得）的影像定型臉部。

> [!NOTE]
> 「預覽至 GA」轉換沒有任何相關的定價影響。

### <a name="hide-gallery-toggle-option"></a>隱藏圖庫切換選項

使用者可以選擇隱藏入口網站中的 [圖庫] 索引標籤（類似于隱藏 [範例] 索引標籤）。
 
### <a name="maximum-url-size-increased"></a>增加的 URL 大小上限

在編制影片索引時，支援 URL 查詢字串4096（而不是2048）。
 
### <a name="support-for-multi-lingual-projects"></a>支援多語言專案

現在可以根據以不同語言編制索引的影片建立專案（僅限 API）。

## <a name="july-2019"></a>2019年7月

### <a name="editor-as-a-widget"></a>編輯器做為 widget

影片索引子 AI 編輯器現在以 widget 的形式提供，可內嵌在客戶應用程式中。

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>從入口網站更新隱藏式輔助字幕檔案中的自訂語言模型

客戶可以在入口網站的 [自訂] 頁面中，提供 VTT、SRT 和 TTML 檔案格式作為語言模型的輸入。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-indexer-deployed-to-japan-east"></a>已部署至日本東部的影片索引子

您現在可以在日本東部地區建立影片索引子付費帳戶。

### <a name="create-and-repair-account-api-preview"></a>建立和修復帳戶 API （預覽）

已加入新的 API，可讓您[更新 Azure 媒體服務連線端點或金鑰](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)。

### <a name="improve-error-handling-on-upload"></a>改善上傳時的錯誤處理 

當基礎 Azure 媒體服務帳戶設定錯誤時，會傳回描述性訊息。

### <a name="player-timeline-keyframes-preview"></a>播放玩家時程表主要畫面格預覽 

您現在可以在播放程式的時間軸上看到影像預覽。

### <a name="editor-semi-select"></a>編輯器半選取

您現在可以看到選取的所有深入解析的預覽，做為在編輯器中選擇特定的深入解析時間範圍的結果。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>從隱藏式輔助字幕檔案更新自訂語言模型

[建立自訂語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag)和[更新自訂語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag)api 現在支援 VTT、SRT 和 TTML 檔案格式，做為語言模型的輸入。

呼叫[更新影片文字記錄 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)時，會自動加入文字記錄。 與影片相關聯的定型模型也會自動更新。 如需如何自訂和定型語言模型的相關資訊，請參閱[使用影片索引子自訂語言模型](customize-language-model-overview.md)。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新的下載文字記錄格式– TXT 和 CSV

除了已支援的隱藏式字幕格式（SRT、VTT 和 TTML）之外，影片索引子現在支援以 TXT 和 CSV 格式下載文字記錄。

## <a name="next-steps"></a>後續步驟

[概觀](video-indexer-overview.md)
