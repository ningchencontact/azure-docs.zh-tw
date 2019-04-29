---
title: 實驗性預設內容感知的編碼-Azure |Microsoft Docs
description: 這篇文章討論 Azure 媒體服務中的內容感知編碼
services: media-services
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: sethm
ms.custom: ''
ms.openlocfilehash: 3c50502a8b873503ee937914fac5f2d92cb23a2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733426"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>實驗性的內容感知的編碼預設

若要準備所傳遞的內容[調適性位元速率串流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，視訊必須編碼在多重位元速率 （高到低）。 為了確保正常降級的品質、 位元速率降低，因此是影片的解析度。 這會導致所謂的編碼階梯 – 的解析度和位元速率，資料表可以看到一些我們已修正的編碼預設，這類[H264MultipleBitrate1080p](../previous/media-services-mes-preset-h264-multiple-bitrate-1080p.md)。

## <a name="overview"></a>概觀

Netflix 發行之後，興趣不只一個-預設值-調整-all-影片方法將會增加其[部落格](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2)在 2015 年 12 月。 從那時起，感知內容的編碼方式的多個方案已發行到 marketplace 中;請參閱[這篇文章](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx)的概觀。 其概念是視訊的內容的要注意，若要自訂或微調個別的複雜度來編碼的階梯。 每個解決方式，在沒有位元速率超過此增加任何品質不 perceptive – 編碼器會在這個最佳位元速率值。 下一個層級的最佳化是選取的內容為基礎的解決方式 – PowerPoint 簡報的影片，例如未受益於以下 720p 的進行。 此外，編碼器可以負責以最佳化每個擷取畫面影片中的設定。 所述的 Netflix[這類方法](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830)在 2018 年。

在 2017 年初，Microsoft 已發行[彈性資料流](autogen-bitrate-ladder.md)預設位址的品質變化性的問題和來源影片的解析度。 我們的客戶有各種混合的內容，有些位於 1080p，720p，其他人，而少數 SD 和較低的解析度。 此外，並非所有的來源內容是從影片或電視 studios 的高品質 mezzanines。 調適性串流處理預設的位址這些問題，藉由確保位元速率階梯絕不會超出解析度或輸入夾層的平均位元速率。

實驗性內容感知的編碼預設擴充同樣的機制，透過加入自訂邏輯，可讓編碼器搜尋最佳位元速率值對於指定的解析，而不需要大量計算的分析。 最後結果就是這個新的預設會產生具有較低位元速率比彈性資料流的預設值，但是在更高品質的輸出。 請參閱下列的範例圖形，顯示使用品質計量，例如的比較[PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio)並[VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)。 來源由串連短片段來自電影的高複雜性擷取畫面，電視節目，壓力編碼器。 根據定義，此預設會產生結果而改變內容內容 – 這也表示某些內容中，有可能不會大幅降低位元速率或提升品質。

![使用 PSNR 速率扭曲 (RD) 曲線](media/cae-experimental/msrv1.png)

**圖 1：針對高的複雜性來源使用 PSNR 計量的速率扭曲 (RD) 曲線**

![使用 VMAF 速率扭曲 (RD) 曲線](media/cae-experimental/msrv2.png)

**圖 2：針對高的複雜性來源使用 VMAF 計量的速率扭曲 (RD) 曲線**

目前，預設值將調整為高的複雜性，（電影、 電視節目） 的高品質的來源影片。 工作正在進行，以適應複雜性低內容 （例如 PowerPoint 簡報），以及導致品質的視訊。 此預設也會使用相同的解析度一組彈性的資料流預設。 Microsoft 正在努力方法，以選取最少的內容為基礎的解決方法。 如下所示是結果的另一個類別目錄的來源內容，編碼器的能夠判斷輸入為品質不佳 （許多壓縮成品因為低的位元速率）。 請注意，使用實驗性預設編碼器決定產生一個輸出層 – 夠低位元速率，使大部分的用戶端就能夠播放不含懸置的資料流。

![使用 PSNR RD 曲線](media/cae-experimental/msrv3.png)

**圖 3：使用 PSNR （位於 1080p) 的低品質輸入 RD 曲線**

![使用 VMAF RD 曲線](media/cae-experimental/msrv4.png)

**圖 4：使用 VMAF （位於 1080p) 的低品質輸入 RD 曲線**

## <a name="use-the-experimental-preset"></a>使用實驗性的預設值

您可以建立使用此預設值，如下所示的轉換。 如果使用教學課程[這類](stream-files-tutorial-with-api.md)，您可以更新程式碼，如下所示：

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new experimental preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncodingExperimental
      }
   }
};
```

> [!NOTE]
> 這裡用 「 實驗性 」 的前置詞來表示的基礎演算法仍處於發展階段。 那里可以與一段時間，用來產生位元速率 ladders，目標是要結合演算法都是強固，配合各種不同的輸入條件的邏輯，將會變更。 編碼工作使用此預設的仍會計費的依據的輸出分鐘數，並可以從我們在 通訊協定，例如 DASH 和 HLS 的串流端點傳遞輸出資產。

## <a name="next-steps"></a>後續步驟

既然您已了解這個新的選項，最佳化您的影片，我們邀請您試用此功能。您可以傳送意見反應結尾的這篇文章中，使用連結，或連絡我們更直接在<amsved@microsoft.com>。
