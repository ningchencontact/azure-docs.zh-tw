---
title: Azure 媒體服務術語和概念-Azure |Microsoft Docs
description: 本主題提供 Azure 媒體服務術語和概念的簡要總覽，並提供更多詳細資料的連結。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 47c7e35f71fd33cc53d498867ef015364252d5ea
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910310"
---
# <a name="media-services-concepts"></a>媒體服務概念

本主題提供 Azure 媒體服務術語和概念的簡要概述。 本文也會提供文章的連結，其中包含媒體服務 v3 概念和功能的深入說明。 

請先檢閱這些主題所述的基本概念再開始進行開發。

> [!NOTE]
> 目前您無法使用 Azure 入口網站管理 v3 資源。 請使用 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref) 或其中一個支援的 [SDK](media-services-apis-overview.md#sdks)。

## <a name="terminology"></a>術語

本節說明一些常見的產業術語如何對應至媒體服務 v3 API。

### <a name="live-event"></a>即時事件

**實況活動**代表內嵌、轉碼（選擇性），以及封裝影片、音訊和即時中繼資料的即時串流的管線。

針對從媒體服務 v2 Api 進行遷移的客戶，**即時事件**會取代 v2 中的**通道**實體。 如需詳細資訊，請參閱[從 V2 遷移至 v3](migrate-from-v2-to-v3.md)。

### <a name="streaming-endpoint-packaging-and-origin"></a>串流端點（封裝和來源）

**串流端點**代表動態（即時）封裝和原始服務，可以使用其中一種常見的串流處理媒體通訊協定（HLS 或破折號），將您的即時和隨選內容直接傳遞到用戶端播放機應用程式。 此外,**串流端點**也提供動態 (即時) 的加密給領先業界的 drm。

在媒體串流處理產業中，這項服務通常稱為封裝工具**或** **來源**。  此功能的產業中的其他常見詞彙包括 JITP （僅限時間封裝程式）或 JITE （及時加密）。 
 
## <a name="cloud-upload-and-storage"></a>雲端上傳和儲存體

若要開始在 Azure 中管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶，並將數位檔案上傳到**資產**。

- [雲端上傳和儲存體](storage-account-concept.md)
- [資產概念](assets-concept.md)

## <a name="encoding"></a>編碼

將高品質的數位媒體檔案上傳到資產之後，您就可以將它們編碼成可在各種不同的瀏覽器和裝置上播放的格式。 

若要使用媒體服務 v3 進行編碼，您需要建立**轉換**和**作業**。

![轉換](./media/encoding/transforms-jobs.png)

- [轉換與作業](transforms-jobs-concept.md)
- [使用媒體服務進行編碼](encoding-concept.md)

## <a name="media-analytics"></a>媒體分析

若要分析您的影片和音訊檔案，您也需要建立**轉換**和**作業**。

- [分析影片和音訊檔案](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>封裝、傳遞、保護

一旦您的內容經過編碼，您就可以利用**動態封裝**。 在媒體服務中，**串流端點**/Origin 是用來將媒體內容傳遞給用戶端播放者的動態封裝服務。 若要讓輸出資產中的影片可供用戶端播放，您必須建立**串流定位器**，然後建立串流 url。 

建立**串流定位器**時，除了資產的名稱之外，您還需要指定**串流原則**。 **串流原則**可讓您定義**串流定位器**的串流通訊協定和加密選項（如果有的話）。

無論您是即時或隨選串流內容，都可以使用動態封裝。 下圖顯示具有動態封裝工作流程的隨選串流處理。

![動態封裝](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

有了媒體服務，您就可以使用進階加密標準（AES-128）或/以及三個主要數位版權管理（DRM）系統中的任何一種，以動態方式傳遞您的即時和隨選內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。

如果您在串流上指定加密選項，請建立**內容金鑰原則**，並將它與您的**串流定位器**建立關聯。 **內容金鑰原則**可讓您設定如何將內容金鑰傳遞給終端用戶端。

下圖說明媒體服務內容保護工作流程： 

![保護內容](./media/content-protection/content-protection.svg)

&#42;「動態加密」支援 AES-128 「清除金鑰」、CBCS 和 CENC。 

您可以使用媒體服務**動態資訊清單**，只串流特定的轉譯或影片 subclips。 下列範例使用編碼器將夾層資產編碼成七個 ISO MP4 視訊轉譯 (從 180p 到 1080p)。 編碼資產可以動態地封裝至下列任何一個資料流通訊協定：HLS、MPEG DASH 和 Smooth。  圖表頂端顯示不含篩選器的資產其 HLS 資訊清單 (包含全部七個轉譯)。  左下角顯示名為 "ott" 的篩選器已套用到 HLS 資訊清單。 "ott" 篩選器指定要移除所有不到 1 Mbps 的位元速率，因此將最差的兩個品質等級從回應中去除。 右下角顯示名為 "mobile" 的篩選器已套用到 HLS 資訊清單中。 "mobile" 篩選器指定要移除的解析度大於 720p 的轉譯，因此去除兩個 1080p 的轉譯。

![轉譯篩選](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [動態封裝](dynamic-packaging-overview.md)
- [串流端點](streaming-endpoint-concept.md)
- [串流定位器](streaming-locators-concept.md)
- [串流原則](streaming-policy-concept.md)
- [內容金鑰原則](content-key-policy-concept.md)
- [內容保護](content-protection-overview.md)
- [動態資訊清單](filters-dynamic-manifest-overview.md)
- [篩選條件](filters-concept.md)

## <a name="live-streaming"></a>即時串流

Azure 媒體服務可讓您在 Azure 雲端上將實況活動傳遞給客戶。 **實況活動**負責內嵌和處理即時視訊摘要。 當您建立**即時事件**時，系統會建立輸入端點，讓您用來從遠端編碼器傳送即時信號。 當資料流程流入**即時事件**之後，您就可以藉由建立**資產**、**即時輸出**和**串流定位器**來開始串流事件。 **即時輸出**會將串流封存到**資產**中，並透過**串流端點**將它提供給檢視器。 **實況活動**可以是下列兩種類型的其中一種：**傳遞**和**即時編碼**。

下圖說明傳遞類型工作流程：

![即時通行](./media/live-streaming/pass-through.svg)

- [即時串流概觀](live-streaming-overview.md)
- [實況活動與實況輸出](live-events-outputs-concept.md)

## <a name="monitoring"></a>監視

### <a name="event-grid"></a>事件格線

若要查看作業的進度，您應該使用**事件方格**。 媒體服務也會發出即時事件種類。 透過事件方格，您的應用程式幾乎可以從所有 Azure 服務和自訂來源接聽及回應事件。 

- [處理事件方格事件](reacting-to-media-services-events.md)
- [架構](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure 監視器

監視計量和診斷記錄，以協助您瞭解應用程式如何使用 Azure 監視器來執行。

- [計量和診斷記錄](media-services-metrics-diagnostic-logs.md)
- [診斷記錄架構](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>播放程式用戶端

您可以使用 Azure 媒體播放機，在各種不同的瀏覽器和裝置上播放媒體服務串流的媒體內容。 Azure Media Player 採用業界標準，例如 HTML5、媒體來源延伸模組 (MSE) 和加密媒體擴充功能 (EME)，提供豐富的調適性串流體驗。 

- [Azure 媒體播放器概觀](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* [遠端檔案編碼和串流影片 - REST](stream-files-tutorial-with-rest.md)
* [已上傳檔案編碼和串流影片 - .NET](stream-files-tutorial-with-api.md)
* [即時串流 - .NET](stream-live-tutorial-with-api.md)
* [分析影片 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 動態加密 - .NET](protect-with-aes128.md)
* [透過多重 DRM 進行動態加密 - .NET](protect-with-drm.md) 
