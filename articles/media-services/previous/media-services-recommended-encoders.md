---
title: 了解 Azure 媒體服務所建議的編碼器 | Microsoft Docs
description: 了解媒體服務所建議的編碼器
services: media-services
keywords: encoding;encoders;media;編碼;編碼器;媒體
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 373ce1d10af87603b1bdd6339c94891187c35d8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332647"
---
# <a name="recommended-on-premises-encoders"></a>建議的內部部署編碼器
使用 Azure 媒體服務進行即時串流時，您可以指定想讓通道接收輸入資料流的方式。 如果选择将本地编码器与实时编码通道结合使用，则编码器应推送高质量单比特率流作为输出。 如果选择将本地编码器与直通通道结合使用，则编码器应推送具备所有所需输出质量的多比特率流作为输出。 有关详细信息，请参阅[使用本地编码器实时传送视频流](media-services-live-streaming-with-onprem-encoders.md)。

Azure 媒體服務建議使用下列其中一種具有 RTMP 作為輸出的即時編碼器：
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

Azure 媒體服務建議使用下列其中一種具有多位元速率分散式 MP4 (Smooth Streaming) 作為輸出的即時編碼器：
- Media Excel Hero Live 和 Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3

> [!NOTE]
> 即時編碼器可傳送單一位元速率資料流至傳遞通道，但不建議使用此設定，因為它不允序針對用戶端使用彈性位元速率串流。

## <a name="how-to-become-an-on-premises-encoder-partner"></a>如何成为本地编码器合作伙伴
作为 Azure 媒体服务本地编码器合作伙伴，媒体服务通过向企业客户推荐编码器来推广你的产品。 若要成为本地编码器合作伙伴，必须验证本地编码器与媒体服务的兼容性。 若要這樣做，請完成下列驗證：

傳遞通道驗證
1. 建立或造訪您的 Azure 媒體服務帳戶
2. 建立並啟動**傳遞**通道
3. 設定您的編碼器以推送多位元速率即時資料流。
4. 建立已發行的即時事件
5. 將您的即時編碼器執行約 10 分鐘的時間
6. 停止即時事件
7. 建立、啟動串流端點，使用 [Azure 媒體播放器](https://ampdemo.azureedge.net/azuremediaplayer.html)等播放程式來觀看已封存的資產，以確保所有品質層級的播放沒有可見問題 (或者，在步驟 6 之前的即時工作階段期間透過預覽 URL 觀看和驗證)
8. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本
9. 於建立每個範例之後，重設通道狀態
10. 針對您編碼器所支援的所有設定 (包含及不包含廣告訊號/字幕/不同的編碼速度) 重複步驟 3 到步驟 9

即時編碼通道驗證
1. 建立或造訪您的 Azure 媒體服務帳戶
2. 建立及啟動**即時編碼**通道
3. 設定您的編碼器以推送單一位元速率即時資料流。
4. 建立已發行的即時事件
5. 將您的即時編碼器執行約 10 分鐘的時間
6. 停止即時事件
7. 建立、啟動串流端點，使用 [Azure 媒體播放器](https://ampdemo.azureedge.net/azuremediaplayer.html)等播放程式來觀看已封存的資產，以確保所有品質層級的播放沒有可見問題 (或者，在步驟 6 之前的即時工作階段期間透過預覽 URL 觀看和驗證)
8. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本
9. 於建立每個範例之後，重設通道狀態
10. 針對您編碼器所支援的所有設定 (包含及不包含廣告訊號/字幕/各種編碼速度) 重複步驟 3 到步驟 9

使用壽命驗證
1. 建立或造訪您的 Azure 媒體服務帳戶
2. 建立並啟動**傳遞**通道
3. 設定您的編碼器以推送多位元速率即時資料流。
4. 建立已發行的即時事件
5. 將您的即時編碼器執行一週或更久的時間
6. 使用 [Azure 媒體播放器](https://ampdemo.azureedge.net/azuremediaplayer.html)等播放程式不時觀看即時串流 (或已封存的資產)，以確保播放沒有可見問題
7. 停止即時事件
8. 記錄資產識別碼、即時封存的已發行串流 URL，以及來自您即時編碼器的所使用設定和版本

最後，將您已記錄的設定和即時封存參數，以電子郵件傳送至媒體服務 (amsstreaming@microsoft.com)。 媒體服務收到之後，便會針對您即時編碼器的範例執行驗證測試。 若有任何關於此程序的問題，請連絡媒體服務。
