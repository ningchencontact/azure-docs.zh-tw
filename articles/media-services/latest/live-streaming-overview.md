---
title: 使用 Azure 媒體服務之即時串流的概觀 | Microsoft Docs
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
ms.date: 02/01/2019
ms.author: juliako
ms.openlocfilehash: e90dd052f6a4af83d2dd794dd405a4700da75bde
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656329"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>使用 Azure 媒體服務 v3 進行即時串流

Azure 媒體服務可讓您在 Azure 雲端上將實況活動傳遞給客戶。 若要使用媒體服務串流實況活動，您需要下列項目：  

- 拍攝實況活動用的相機。<br/>如需了解裝備，請參閱[簡單的可攜式活動視訊器材裝備]( https://link.medium.com/KNTtiN6IeT) \(英文\)。
- 即時視訊編碼器，可將來自相機 (或另一部裝置，例如膝上型電腦) 的訊號轉換成發佈摘要以傳送給「媒體服務」。 發佈摘要可以包含廣告相關訊號，例如 SCTE-35 標記。<br/>如需建議的即時串流編碼器清單，請參閱[即時串流編碼器](recommended-on-premises-live-encoders.md)。 此外，也請參閱這個部落格：[使用 OBS 來產生即時串流](https://link.medium.com/ttuwHpaJeT) \(英文\)。
- 媒體服務中的元件可讓您內嵌、預覽、封裝、記錄、加密實況活動，並向客戶廣播這些活動，或是向 CDN 廣播以進一步發佈。

有了「媒體服務」，您便可以利用**動態封裝**，從您要傳送給該服務的發佈摘要來預覽和廣播 [MPEG DASH、HLS 及 Smooth Streaming 格式](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)的即時資料流。 您的檢視者可以搭配支援 HLS、DASH 或 Smooth Streaming 的播放器播放即時串流。 您可以在網路或行動應用程式中使用 [Azure 媒體播放器](http://amp.azure.net/libs/amp/latest/docs/index.html)，透過這些通訊協定中的任何一個傳遞串流。

媒體服務可讓您傳遞利用進階加密標準 (AES-128) 或下列三個主要數位版權管理 (DRM) 系統中任一個所動態加密 (**動態加密**) 的內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，傳遞 AES 金鑰和 DRM 授權給授權用戶端。 如需如何使用媒體服務加密內容的詳細資訊，請參閱[保護內容概觀](content-protection-overview.md)

您也可以套用「動態篩選」，這可用來控制傳送給播放器的曲目編號、格式、位元速率及呈現時間範圍。 如需詳細資訊，請參閱 [篩選器與動態資訊清單](filters-dynamic-manifest-overview.md)。

本文提供使用「媒體服務」進行即時串流的概觀和指引。

## <a name="prerequisites"></a>必要條件

若要了解「媒體服務 v3」中的即時串流工作流程，您必須檢閱及了解下列概念： 

- [串流端點](streaming-endpoint-concept.md)
- [實況活動與實況輸出](live-events-outputs-concept.md)

## <a name="live-streaming-workflow"></a>即時串流工作流程

即時串流工作流程的步驟如下：

1. 在您的媒體服務帳戶中，確認**串流端點**正在執行。 
2. 建立 [即時事件](live-events-outputs-concept.md)。 <br/>在建立事件時，您可以指定要自動啟動它。 或者，您可以在準備好開始進行串流處理時啟動事件。<br/> 當自動啟動設定為 true 時，即時事件將會在建立後隨即啟動。 只要即時事件一執行，就會立即開始計費。 您必須對「實況活動」資源明確呼叫「停止」，才能終止進一步計費。 如需詳細資訊，請參閱[實況活動狀態和計費](live-event-states-billing.md)。
3. 取得內嵌 URL 並設定您的內部部署編碼器，以使用該 URL 來傳送貢獻摘要。<br/>請參閱[建議的即時編碼器](recommended-on-premises-live-encoders.md)。
4. 取得預覽 URL 並使用它來確認實際上已收到來自編碼器的輸入。
5. 建立新的 [資產] 物件。
6. 建立 [即時輸出] 並使用您建立的資產名稱。<br/>**實況輸出**會將資料流封存到**資產**中。
7. 使用內建的**串流原則**類型來建立**串流定位器**。<br/>若您想要將內容加密，請檢閱[內容保護概觀](content-protection-overview.md)。
8. 列出 [串流定位器] 上的路徑，以取得要使用的 URL (這些具有決定性)。
9. 取得您想要串流的來源**串流端點**主機名稱。
10. 結合步驟 8 的 URL 和步驟 9 的主機名稱，即可取得完整的 URL。
11. 如果想要停止讓**實況活動**可供檢視，您必須將該活動停止串流並刪除**串流定位器**。

## <a name="other-important-articles"></a>其他重要文章

- [建議的即時編碼器](recommended-on-premises-live-encoders.md)
- [使用雲端 DVR](live-event-cloud-dvr.md)
- [即時事件類型功能比較](live-event-types-comparison.md)
- [狀態和計費](live-event-states-billing.md)
- [延遲](live-event-latency.md)

## <a name="next-steps"></a>後續步驟

* [即時串流教學課程](stream-live-tutorial-with-api.md)
* [從媒體服務 v2 移動至 v3 的移轉指導](migrate-from-v2-to-v3.md)
