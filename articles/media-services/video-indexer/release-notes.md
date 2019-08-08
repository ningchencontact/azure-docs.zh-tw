---
title: Azure 媒體服務影片索引子版本資訊 |Microsoft Docs
description: 為了讓您隨時掌握最新的開發, 本文提供 Azure 媒體服務影片索引子的最新更新。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 07/22/2019
ms.author: juliako
ms.openlocfilehash: b627a78edef1c0b0fe6b3ed011678145aea397ae
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845878"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure 媒體服務影片索引子版本資訊

為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

* 最新版本
* 已知問題
* 錯誤修正
* 已被取代的功能

## <a name="july-2019"></a>2019年7月

### <a name="editor-as-a-widget"></a>編輯器做為 widget

影片索引子 AI 編輯器現在以 widget 的形式提供, 可內嵌在客戶應用程式中。

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>從入口網站更新隱藏式輔助字幕檔案中的自訂語言模型

客戶可以在入口網站的 [自訂] 頁面中, 提供 VTT、SRT 和 TTML 檔案格式作為語言模型的輸入。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-indexer-deployed-to-japan-east"></a>已部署至日本東部的影片索引子

您現在可以在日本東部地區建立影片索引子付費帳戶。

### <a name="create-and-repair-account-api-preview"></a>建立和修復帳戶 API (預覽)

已加入新的 API, 可讓您[更新 Azure 媒體服務連線端點或金鑰](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)。

### <a name="improve-error-handling-on-upload"></a>改善上傳時的錯誤處理 

當基礎 Azure 媒體服務帳戶設定錯誤時, 會傳回描述性訊息。

### <a name="player-timeline-keyframes-preview"></a>播放玩家時程表主要畫面格預覽 

您現在可以在播放程式的時間軸上看到影像預覽。

### <a name="editor-semi-select"></a>編輯器半選取

您現在可以看到選取的所有深入解析的預覽, 做為在編輯器中選擇特定的深入解析時間範圍的結果。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>從隱藏式輔助字幕檔案更新自訂語言模型

[建立自訂語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag)和[更新自訂語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag)api 現在支援 VTT、SRT 和 TTML 檔案格式, 做為語言模型的輸入。

呼叫[更新影片文字記錄 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)時, 會自動加入文字記錄。 與影片相關聯的定型模型也會自動更新。 如需如何自訂和定型語言模型的相關資訊, 請參閱[使用影片索引子自訂語言模型](customize-language-model-overview.md)。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新的下載文字記錄格式– TXT 和 CSV

除了已支援的隱藏式字幕格式 (SRT、VTT 和 TTML) 之外, 影片索引子現在支援以 TXT 和 CSV 格式下載文字記錄。

## <a name="next-steps"></a>後續步驟

[概觀](video-indexer-overview.md)
