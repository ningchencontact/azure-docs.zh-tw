---
title: Azure 媒體服務概念-Azure |Microsoft Docs
description: 本主題提供 Azure 媒體服務概念的簡短概觀，並提供連結，如需詳細資訊。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: e4a79d7528f2e814e6370a5ead7d77a19057ebbb
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311266"
---
# <a name="media-services-concepts"></a>媒體服務概念

本主題提供 Azure 媒體服務概念的簡短概觀，並提供深入的媒體服務 v3 概念和功能說明中的文章連結。 請先檢閱這些主題所述的基本概念再開始進行開發。

> [!NOTE]
> 目前您無法使用 Azure 入口網站管理 v3 資源。 使用[REST API](https://aka.ms/ams-v3-rest-ref)， [CLI](https://aka.ms/ams-v3-cli-ref)，或其中一個支援[Sdk](developers-guide.md)。

## <a name="cloud-upload-and-storage"></a>雲端上傳和儲存體

若要開始管理、 加密、 編碼、 分析，以及串流處理媒體內容，在 Azure 中的，您需要建立媒體服務帳戶並上傳到將數位檔案**資產**。

- [雲端上傳和儲存體](storage-account-concept.md)
- [資產概念](assets-concept.md)

## <a name="encoding"></a>編碼

一旦您將高品質的數位媒體檔案上傳到資產時，您可以將它們編碼成可播放的瀏覽器和裝置的各種不同的格式。 

若要編碼使用媒體服務 v3，您需要建立**轉換**並**作業**。

![轉換](./media/encoding/transforms-jobs.png)

- [轉換與作業](transforms-jobs-concept.md)
- [使用媒體服務編碼](encoding-concept.md)

## <a name="media-analytics"></a>媒體分析

若要分析您的視訊和音訊檔案，您也需要建立**轉換**並**作業**。

- [分析的視訊和音訊檔案](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>封裝、傳遞、保護

一旦您的內容編碼時，您可以善用**動態封裝**。 **串流端點**是用來傳遞媒體內容給用戶端播放程式媒體服務中的動態封裝服務。 若要讓影片在輸出資產中供用戶端播放，您必須建立**串流定位器**，然後再建置串流 Url。 

建立時**串流定位器**，您要指定資產的名稱，除了**串流原則**。 **串流處理原則**讓您定義串流通訊協定和加密選項 （如果有的話） 您**串流定位器**。

不論您串流處理實況或點播內容，使用動態封裝。 下圖顯示使用動態封裝工作流程上隨選資料流。

![動態封裝](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

使用媒體服務中，您可以傳遞利用進階加密標準 (AES-128) 動態加密的即時與隨內容或 / 和三個主要數位版權管理 (DRM) 系統中任一個：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。

如果指定的加密選項，在您的資料流上，建立**內容的索引鍵原則**和其關聯您**串流定位器**。 **內容金鑰原則**可讓您設定內容金鑰傳送給終端用戶端的方式。

下圖說明媒體服務內容保護工作流程： 

![保護內容](./media/content-protection/content-protection.svg)

&#42;動態加密支援 AES-128 「 清除金鑰 」、 CBCS 和 CENC。 

您可以使用媒體服務**動態資訊清單**只有特定的轉譯或子剪輯的視訊串流處理。 下列範例使用編碼器將夾層資產編碼成七個 ISO MP4 視訊轉譯 (從 180p 到 1080p)。 編碼資產可以動態地封裝至下列任何一個資料流通訊協定：HLS、MPEG DASH 和 Smooth。  圖表頂端顯示不含篩選器的資產其 HLS 資訊清單 (包含全部七個轉譯)。  左下角顯示名為 "ott" 的篩選器已套用到 HLS 資訊清單。 "ott" 篩選器指定要移除所有不到 1 Mbps 的位元速率，因此將最差的兩個品質等級從回應中去除。 右下角顯示名為 "mobile" 的篩選器已套用到 HLS 資訊清單中。 "mobile" 篩選器指定要移除的解析度大於 720p 的轉譯，因此去除兩個 1080p 的轉譯。

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

Azure 媒體服務可讓您在 Azure 雲端上將實況活動傳遞給客戶。 **實況活動**負責內嵌和處理即時視訊摘要。 當您建立**即時事件**，輸入的端點會建立可用來從遠端的編碼器傳送有效的訊號。 一旦您讓串流流入**即時事件**，您可以藉由建立開始串流事件**資產**， **Live 輸出**，和**串流定位器**. **Live 輸出**將封存資料流**Asset**並將它提供給檢視器，透過**串流端點**。 A**即時事件**可以是下列其中一種：**傳遞**並**即時編碼**。

下圖顯示傳遞類型工作流程：

![即時通行](./media/live-streaming/pass-through.svg)

- [即時串流概觀](live-streaming-overview.md)
- [實況活動與實況輸出](live-events-outputs-concept.md)

## <a name="monitoring"></a>監視

### <a name="event-grid"></a>Event Grid

若要查看工作的進度，您應該使用**Event Grid**。 媒體服務也會發出的即時事件類型。 透過事件方格，您的應用程式幾乎可以從所有 Azure 服務和自訂來源接聽及回應事件。 

- [Event Grid 事件處理](reacting-to-media-services-events.md)
- [結構描述](media-services-event-schemas.md)

## <a name="player-clients"></a>播放程式用戶端

您可以使用 Azure Media Player 播放在各種不同的瀏覽器和裝置上的媒體服務串流處理媒體內容。 Azure Media Player 採用業界標準，例如 HTML5、媒體來源延伸模組 (MSE) 和加密媒體擴充功能 (EME)，提供豐富的調適性串流體驗。 

- [Azure 媒體播放器概觀](use-azure-media-player.md)

## <a name="next-steps"></a>後續步驟

[使用媒體服務上傳、編碼和串流](stream-files-tutorial-with-api.md)
