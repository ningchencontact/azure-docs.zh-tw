---
title: 內容感知編碼的實驗性預設值-Azure |Microsoft Docs
description: 本文討論 Microsoft Azure 媒體服務 v3 中的內容感知編碼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9389466b6291542563c068706479bf981c5880da
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896130"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>內容感知編碼的實驗性預設值

為了準備以彈性[位元速率串流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)傳遞內容，必須以多個位元速率（高至低）編碼影片。 為了確保品質的效能降低，因為位元速率降低，因此是影片的解析度。 這會導致所謂的編碼階梯–一份解析度和位元速率表;請參閱媒體服務[內建的編碼](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)預設值。

## <a name="overview"></a>概觀

在 Netflix 于2015年12月發佈其[blog](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2)之後，移至超過一種全功能全影片的方法時感興趣。 從那時起，已在 marketplace 中發行多個內容感知編碼解決方案;如需總覽，請參閱[這篇文章](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx)。 其概念是要留意內容，以自訂或微調編碼階梯，以因應個別影片的複雜度。 每次解決時，都有比得品質還差的位元速率，編碼器會以此最佳位元速率值運作。 下一個優化層級是根據內容來選取解析度–例如，PowerPoint 簡報的影片無法從以下的720p 中獲益。 接下來，編碼器可能會負責將影片中每個拍照的設定優化。 Netflix 在2018中描述[了這種方法](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830)。

在2017年初，Microsoft 發行了彈性[資料流程](autogen-bitrate-ladder.md)預設值，以解決來源影片的品質和解析度變化的問題。 我們的客戶有不同的內容混合，有些是1080p，有些則是720p，而有些則是 SD 和較低的解析度。 此外，並非所有的來源內容都是來自電影或電視工作室的高品質 mezzanines。 彈性資料流程預設會藉由確保位元速率階梯永不超過輸入夾層的解析度或平均位元速率，來解決這些問題。

實驗性內容感知編碼預設會擴充該機制，藉由併入自訂邏輯，讓編碼器針對指定的解析度尋找最佳位元速率值，但不需要大量的計算分析。 最終結果是這個新的預設值會產生比彈性資料流程預設值低位元速率的輸出，但品質較高。 請參閱下列範例圖形，其中顯示使用品質標準（例如[PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio)和[VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)）的比較。 來源是藉由串連電影和電視節目的較短剪輯來建立，其目的是要強調編碼器。 根據定義，此預設會產生不同于內容的結果，這也表示對於某些內容，可能不會大幅降低位元速率或改善品質。

![使用 PSNR 的速率扭曲（RD）曲線](media/cae-experimental/msrv1.png)

**圖1：針對高複雜度來源使用 PSNR 度量的速率扭曲（RD）曲線**

![使用 VMAF 的速率扭曲（RD）曲線](media/cae-experimental/msrv2.png)

**圖2：對高複雜度來源使用 VMAF 度量的速率扭曲（RD）曲線**

預設值目前已針對高複雜性、高品質來源影片（電影、電視節目）進行調整。 工作正在進行中，以適應低複雜度的內容（例如 PowerPoint 簡報），以及品質較差的影片。 此預設值也會使用與彈性資料流程預設值相同的解析度集合。 Microsoft 正致力於根據內容來選取最少解決方案集的方法。 如下所示，另一個來源內容分類的結果，其中的編碼器能夠判斷輸入的品質不佳（許多壓縮成品是因為低位元速率）。 請注意，在實驗性的預設情況下，編碼器決定只產生一個輸出層–以較低的位元速率，讓大部分的用戶端能夠在不停止的情況下播放串流。

![使用 PSNR 的 RD 曲線](media/cae-experimental/msrv3.png)

**圖3：使用 PSNR 進行低品質輸入的 RD 曲線（1080p）**

![使用 VMAF 的 RD 曲線](media/cae-experimental/msrv4.png)

**圖4：使用 VMAF 進行低品質輸入的 RD 曲線（1080p）**

## <a name="use-the-experimental-preset"></a>使用實驗性預設值

您可以建立使用此預設值的轉換，如下所示。 如果使用如[這類](stream-files-tutorial-with-api.md)的教學課程，您可以更新程式碼，如下所示：

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
> 這裡會使用前置詞「實驗性」來表示基礎演算法仍在發展中。 有可能會隨著時間而改變，以用於產生位元速率 ladders，其目標是將整合至健全的演算法，並適應各種不同的輸入條件。 使用此預設值的編碼作業仍會根據輸出分鐘數計費，而輸出資產可從我們的串流端點以破折號和 HLS 等通訊協定傳遞。

## <a name="next-steps"></a>後續步驟

現在您已瞭解將影片優化的這個新選項，我們邀請您試試看。您可以使用本文結尾的連結傳送意見反應給我們，或直接在 <amsved@microsoft.com>與我們合作。
