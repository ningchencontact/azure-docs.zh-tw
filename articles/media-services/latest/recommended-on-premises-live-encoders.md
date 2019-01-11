---
title: 深入了解 Azure 媒體服務所建議的即時串流處理內部部署編碼器 |Microsoft Docs
description: 深入了解 Azure 媒體服務所建議的即時串流處理內部部署編碼器
services: media-services
keywords: encoding;encoders;media;編碼;編碼器;媒體
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 12/14/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d1110669bd0ca8c0ba0caf34ef41861c500bdd33
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790023"
---
# <a name="recommended-live-streaming-encoders"></a>建議使用即時串流編碼器

在媒體服務中，[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) (通道) 代表處理即時串流內容的管線。 LiveEvent 會以兩種方式之一收到即時輸入串流：

* 內部部署即時編碼器會將多位元速率 RTMP 或 Smooth Streaming (分散式 MP4) 串流傳送到未啟用執行媒體服務即時編碼的 LiveEvent。 內嵌的串流會通過 LiveEvent，而不需任何進一步處理。 此方法稱為 **傳遞**。 即時編碼器可傳送單一位元速率資料流至傳遞通道，但不建議使用此設定，因為它不允序針對用戶端使用彈性位元速率串流。

  > [!NOTE]
  > 使用傳遞方法是進行即時串流的最經濟實惠方式。

* 內部部署即時編碼器會將單一位元速率串流傳送至 LiveEvent，可以使用下列格式之一，以媒體服務執行即時編碼：RTMP 或 Smooth Streaming (分散的 MP4)。 LiveEvent 接著會執行即時編碼，將內送單一位元速率資料流編碼成多位元速率 (自適性) 視訊資料流。

如需媒體服務即時編碼的詳細資訊，請參閱[使用媒體服務 v3 進行即時串流](live-streaming-overview.md)。

## <a name="live-encoders-that-output-rtmp"></a>輸出 RTMP 的即時編碼器

媒體服務建議使用下列其中一種具有 RTMP 作為輸出的即時編碼器。 支援的 URL 配置是 `rtmp://` 或 `rtmps://`。

> [!NOTE]
 > 透過 RTMP 串流處理時，請檢查防火牆和/或 Proxy 設定，確認輸出 TCP 連接埠 1935 和 1936 已開啟。<br/>
 透過 RTMPS 串流處理時，請檢查防火牆和/或 Proxy 設定，確認輸出 TCP 連接埠 2935 和 2936 已開啟。

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>輸出分散式 MP4 的即時編碼器

媒體服務建議使用下列其中一種具有多位元速率 Smooth Streaming (分散式 MP4) 做為輸出的即時編碼器。 支援的 URL 配置是 `rtmp://` 或 `rtmps://`。

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Media Excel Hero Live 和 Hero 4K (UHD/HEVC)

## <a name="how-to-become-an-on-premises-encoder-partner"></a>如何成為內部部署編碼器合作夥伴

身為 Azure 媒體服務內部部署編碼器合作夥伴，媒體服務將會向企業客戶建議您的編碼器，以協助推廣您的產品。 若要成為內部部署編碼器合作夥伴，您必須向媒體服務驗證您內部部署編碼器的相容性。 若要這樣做，請完成下列驗證：

### <a name="pass-through-liveevent-verification"></a>即時通行 LiveEvent 驗證

1. 建立或造訪您的 Azure 媒體服務帳戶。
2. 建立並啟動**即時通行** LiveEvent。
3. 設定您的編碼器以推送多位元速率即時資料流。
4. 建立已發行的即時事件。
5. 將您的即時編碼器執行約 10 分鐘的時間。
6. 停止即時事件。
7. 建立、啟動串流端點，使用 [Azure 媒體播放器](https://ampdemo.azureedge.net/azuremediaplayer.html)等播放程式來觀看已封存的資產，以確保所有品質層級的播放沒有可見問題 (或者，在步驟 6 之前的即時工作階段期間透過預覽 URL 觀看和驗證)。
8. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本。
9. 於建立每個範例之後，重設 LiveEvent 狀態。
10. 針對您編碼器所支援的所有設定 (包含及不包含廣告訊號/字幕/不同的編碼速度) 重複步驟 3 到步驟 9。

### <a name="live-encoding-liveevent-verification"></a>即時編碼 LiveEvent 驗證

1. 建立或造訪您的 Azure 媒體服務帳戶。
2. 建立及啟動**即時編碼** LiveEvent。
3. 設定您的編碼器以推送單一位元速率即時資料流。
4. 建立已發行的即時事件。
5. 將您的即時編碼器執行約 10 分鐘的時間。
6. 停止即時事件。
7. 建立、啟動串流端點，使用 [Azure 媒體播放器](https://ampdemo.azureedge.net/azuremediaplayer.html)等播放程式來觀看已封存的資產，以確保所有品質層級的播放沒有可見問題 (或者，在步驟 6 之前的即時工作階段期間透過預覽 URL 觀看和驗證)。
8. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本。
9. 於建立每個範例之後，重設 LiveEvent 狀態。
10. 針對您編碼器所支援的所有設定 (包含及不包含廣告訊號/字幕/各種編碼速度) 重複步驟 3 到步驟 9。

### <a name="longevity-verification"></a>使用壽命驗證

1. 建立或造訪您的 Azure 媒體服務帳戶。
2. 建立並啟動**傳遞**通道。
3. 設定您的編碼器以推送多位元速率即時資料流。
4. 建立已發行的即時事件。
5. 將您的即時編碼器執行一週或更久的時間。
6. 使用 [Azure 媒體播放器](https://ampdemo.azureedge.net/azuremediaplayer.html)等播放程式不時觀看即時串流 (或已封存的資產)，以確保播放沒有可見問題。
7. 停止即時事件。
8. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本。

最後，以電子郵件將記錄的設定和即時封存參數傳送到 amsstreaming@microsoft.com 的 Azure 媒體服務，做為所有自我驗證檢查已通過的通知。 此外，包含任何後續動作的連絡資訊。 若有任何關於此程序的問題，請連絡 Azure 媒體服務團隊。

## <a name="next-steps"></a>後續步驟

[使用媒體服務 v3 進行即時串流](live-streaming-overview.md)
