---
title: 即時串流與 Azure 媒體服務 v3 的概觀 |Microsoft Docs
description: 本文提供使用 Azure 媒體服務 v3 進行即時串流的概觀。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/03/2019
ms.author: juliako
ms.openlocfilehash: 0157cdc8062d7c53aaeb3ff01762e9562aa9c394
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866348"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>使用 Azure 媒體服務 v3 進行即時串流

Azure 媒體服務可讓您在 Azure 雲端上將實況活動傳遞給客戶。 若要使用媒體服務串流實況活動，您需要下列項目：  

- 拍攝實況活動用的相機。<br/>如需了解裝備，請參閱[簡單的可攜式活動視訊器材裝備]( https://link.medium.com/KNTtiN6IeT) \(英文\)。

    如果您沒有存取相機，之類的工具[Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm)可用視訊檔案從產生的即時摘要。
- 即時視訊編碼器，可將來自相機 (或另一部裝置，例如膝上型電腦) 的訊號轉換成發佈摘要以傳送給「媒體服務」。 發佈摘要可以包含廣告相關訊號，例如 SCTE-35 標記。<br/>如需建議的即時串流編碼器清單，請參閱[即時串流編碼器](recommended-on-premises-live-encoders.md)。 此外，也請參閱這個部落格：[使用 OBS 來產生即時串流](https://link.medium.com/ttuwHpaJeT) \(英文\)。
- 媒體服務中的元件可讓您內嵌、預覽、封裝、記錄、加密實況活動，並向客戶廣播這些活動，或是向 CDN 廣播以進一步發佈。

這篇文章提供概觀和即時資料流，與媒體服務和其他相關文章連結的指引。

> [!NOTE]
> 目前您無法使用 Azure 入口網站管理 v3 資源。 請使用 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref) 或其中一個支援的 [SDK](developers-guide.md)。

## <a name="dynamic-packaging"></a>動態封裝

使用媒體服務，您可以善用[動態封裝](dynamic-packaging-overview.md)，可讓您預覽和廣播即時資料流中的[MPEG DASH、 HLS 和 Smooth Streaming 格式](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)從比重摘要正在傳送至服務。 您的檢視者可以搭配支援 HLS、DASH 或 Smooth Streaming 的播放器播放即時串流。 您可以在網路或行動應用程式中使用 [Azure 媒體播放器](https://amp.azure.net/libs/amp/latest/docs/index.html)，透過這些通訊協定中的任何一個傳遞串流。

## <a name="dynamic-encryption"></a>動態加密

動態加密，可讓您動態加密您即時或點播的內容，使用 AES-128 或三個主要數位版權管理 (DRM) 系統中任一個：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 如需詳細資訊，請參閱 <<c0> [ 動態加密](content-protection-overview.md)。

## <a name="dynamic-manifest"></a>動態資訊清單

動態篩選用來控制播放軌、 格式、 位元速率，以及展示給玩家送出的時間範圍的數目。 如需詳細資訊，請參閱 <<c0> [ 篩選器與動態資訊清單](filters-dynamic-manifest-overview.md)。

## <a name="live-event-types"></a>實況活動類型

即時的事件可以是下列其中一種： 傳遞和即時編碼。 如需媒體服務 v3 中的即時資料流的詳細資訊，請參閱[即時事件和 Live 輸出](live-events-outputs-concept.md)。

### <a name="pass-through"></a>傳遞

![即時通行](./media/live-streaming/pass-through.svg)

使用傳遞**實況活動**時，您會依賴內部部署即時編碼器來產生多重位元速率視訊資料流，然後將其當作發佈摘要傳送給「實況活動」(使用 RTMP 或分散式 MP4 通訊協定)。 「實況活動」會接著完成傳入的視訊資料流，而不會再進一步處理。 這類傳遞即時事件最適合用於長時間執行的即時事件，或 24 x 365 線性即時資料流。 

### <a name="live-encoding"></a>即時編碼  

![即時編碼](./media/live-streaming/live-encoding.svg)

搭配「媒體服務」使用即時編碼時，您會設定讓內部部署即時編碼器將單一位元速率視訊當作發佈摘要，傳送給「實況活動」(使用 RTMP 或分散式 MP4 通訊協定)。 「實況活動」會將該項傳入的單一位元速率資料流編碼成[多重位元速率視訊資料流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，使其可供透過 MPEG-DASH、HLS 及 Smooth Streaming 等通訊協定，傳遞給播放裝置。 

## <a name="live-streaming-workflow"></a>即時串流工作流程

若要了解媒體服務 v3 中的即時串流工作流程，您必須先檢閱，並了解下列概念： 

- [串流端點](streaming-endpoint-concept.md)
- [實況活動與實況輸出](live-events-outputs-concept.md)
- [串流定位器](streaming-locators-concept.md)

### <a name="general-steps"></a>一般步驟

1. 在您的媒體服務帳戶，請確定**串流端點**(Origin) 正在執行。 
2. 建立 [即時事件](live-events-outputs-concept.md)。 <br/>在建立事件時，您可以指定要自動啟動它。 或者，您可以在準備好開始進行串流處理時啟動事件。<br/> 當自動啟動設定為 true 時，即時事件將會在建立後隨即啟動。 只要即時事件一執行，就會立即開始計費。 您必須對「實況活動」資源明確呼叫「停止」，才能終止進一步計費。 如需詳細資訊，請參閱[實況活動狀態和計費](live-event-states-billing.md)。
3. 取得內嵌 URL，並設定您的內部部署編碼器，以使用 URL 來傳送發佈摘要。<br/>請參閱[建議的即時編碼器](recommended-on-premises-live-encoders.md)。
4. 取得預覽 URL 並使用它來確認實際上已收到來自編碼器的輸入。
5. 建立新的 [資產] 物件。
6. 建立 [即時輸出] 並使用您建立的資產名稱。<br/>**實況輸出**會將資料流封存到**資產**中。
7. 使用內建的**串流原則**類型來建立**串流定位器**。<br/>若您想要將內容加密，請檢閱[內容保護概觀](content-protection-overview.md)。
8. 列出 [串流定位器] 上的路徑，以取得要使用的 URL (這些具有決定性)。
9. 取得的主機名稱**串流端點**您想要從資料流 （來源）。
10. 結合步驟 8 的 URL 和步驟 9 的主機名稱，即可取得完整的 URL。
11. 如果想要停止讓**實況活動**可供檢視，您必須將該活動停止串流並刪除**串流定位器**。

## <a name="other-important-articles"></a>其他重要文章

- [建議的即時編碼器](recommended-on-premises-live-encoders.md)
- [使用雲端 DVR](live-event-cloud-dvr.md)
- [即時事件類型功能比較](live-event-types-comparison.md)
- [狀態和計費](live-event-states-billing.md)
- [延遲](live-event-latency.md)

## <a name="provide-feedback"></a>提供意見反應

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* [即時串流教學課程](stream-live-tutorial-with-api.md)
* [從媒體服務 v2 移動至 v3 的移轉指導](migrate-from-v2-to-v3.md)
