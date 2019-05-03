---
title: 語音裝置 SDK 麥克風陣列建議語音服務
titleSuffix: Azure Cognitive Services
description: 語音裝置 SDK 麥克風陣列建議。 下列陣列幾何被建議搭配 Microsoft 音訊堆疊。 音效來源的位置和環境的噪音拒絕改進大量具有相依性的麥克風的特定應用程式、 使用者案例，以及裝置外型規格。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: c8bc11b8f81fe034ceaa93c7bd8a49771d9407f9
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026706"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>語音裝置 SDK 麥克風陣列建議

在本文中，您了解如何設計麥克風陣列語音裝置 sdk。

語音裝置 SDK 最適合搭配已根據下列指導方針，包括麥克風幾何和元件選取項目設計的麥克風陣列。 整合和電氣的考量，也會給予指引。

## <a name="microphone-geometry"></a>麥克風幾何

下列陣列幾何被建議搭配 Microsoft 音訊堆疊。 音效來源的位置和環境的噪音拒絕改進大量具有相依性的麥克風的特定應用程式、 使用者案例，以及裝置外型規格。

|          | 循環的陣列    |       |  線性的陣列              |                |
|----------|-------------------|-------------------|----------------|----------------|
|          |![7 的 mic 循環的陣列](media/speech-devices-sdk/7-mic-c.png)|![4 的 mic 循環的陣列](media/speech-devices-sdk/4-mic-c.png)|![4 mic 線性陣列](media/speech-devices-sdk/4-mic-l.png)|![2 的 mic 線性陣列](media/speech-devices-sdk/2-mic-l.png)|
| \# Mics  | 7                 | 4                 | 4              | 2              |
| 幾何 | 6 的外部，1 Center、 Radius = 42.5 公釐，平均分配間距| 3 個外部 1 Center、 Radius = 42.5 公釐，平均分配間距 | 長度 = 120 公釐，間距 = 40 公釐 | 間距 = 40 公釐 |

## <a name="component-selection"></a>元件選取項目

應該選取麥克風元件，以精確地重現訊號雜訊和扭曲的免費。

當您選取的麥克風時的建議的屬性如下：

| 參數                         | 建議                       |
|-----------------------------------|-----------------------------------|
| SNR                               | \> 65 dB （1khz 訊號 94 dBSPL、 的加權雜訊）   |
| Amplitude 比對                | 常见 @ 1khz 的 1 個 dB                     |
| 比對階段                    | 常见 @ 1khz 的 2 個 °                       |
| 原音多載點 (AOP)     | \> 120 dBSPL (THD = 10%)          |
| 位元速率                          | 最小值 24 位元                    |
| 取樣率                     | 最小 16 kHz\*                   |
| Directivity                       | 全方向                   |
| 頻率回應                | 常见 3 dB 200 8000 Hz 浮動遮罩\*|
| 可靠性                       | 儲存體溫度範圍到 70 ° C-40 ° C<br />溫度範圍為 55 ° C-20 ° C  |

*\*更高的取樣率或 「 寬 」 的頻率範圍可能需要高品質通訊 (VoIP) 應用程式*

選取良好的元件必須搭配 electroacoustic 的整合良好，以避免損害所用元件的效能。 唯一的使用案例可能也需要額外的需求 (例如： 運作溫度範圍)。

## <a name="microphone-array-integration"></a>麥克風陣列整合

陣列時整合到裝置，及之後的任何固定的提升或 EQ 效能應該符合下列建議：

|  參數        |    建議 |
|--------------------|----------------------------------------------------|
|  SNR                 | \> 65 dB （1khz 訊號 94 dBSPL、 的加權雜訊） |
|  輸出敏感度  | -26 dBFS/Pa @ 1khz （建議選項） |
|  Amplitude 比對  | 常见 2 dB 200 8000 Hz |
|  比對階段      | 常见 5 °，200 8000 Hz |
| THD %                 | ≤ 1%、 200 8000 Hz，94 dBSPL，第 5 個順序 |
|  頻率回應  | 常见 6 dB 200 8000 Hz 浮動遮罩\* |

*\*「 寬度 」 的頻率範圍可能需要高品質通訊 (VoIP) 應用程式*

## <a name="speaker-integration-recommendations"></a>說話者整合的建議

回應取消作業所需的包含演講者的語音辨識裝置項目，將可供其他建議說話者選取和整合。

| 參數                         | 建議                       |
|-----------------------------------|-----------------------------------|
| 線性考量          | 說話者的參考後面沒有非線性處理，否則為硬體架構的回送參考資料流是必要  |
| 說話者 Loopback                  | 提供透過 WASAPI、 私用 Api、 自訂 ALSA 外掛程式 (Linux)，或提供透過韌體通道      |
| THD %                              | 第 3 個 octave 群組列最小值 5 日順序，70 dBA 播放 @ 0.8 m ≤ 6.3%、 315 500 Hz ≤ 5%、 630 5000 Hz                 |
| Echo 結合麥克風的問題      | \> -10 dB TCLw 使用 ITU-T G.122 附錄 B.4 方法標準化為 mic 層級<br />TCLw = TCLwmeasured \+ （層級 – 測量目標輸出敏感度）<br />TCLw = TCLwmeasured \+ （度量層級--(26)） |

## <a name="integration-design-architecture"></a>整合設計架構

將麥克風整合到裝置時，所需架構的下列指導方針：

| 參數                         | 建議                    |
|-----------------------------------|-----------------------------------|
| Mic 連接埠相似度               | 麥克風的所有連接埠都有相同的長度，陣列中    |
| Mic 連接埠的維度               | 連接埠大小 Ø0.8 1.0 公釐。 連接埠長度 / 連接埠直徑\<2              |
| Mic 密封                       | 密封 gaskets 一致地實作在堆疊上。 建議\>發泡材料 gaskets 的 70%的壓縮比     |
| Mic 可靠性                   | 網狀結構應該用來防止灰塵和輸入 （下方的 PCB 之間移植麥克風及密封 gasket/top cover）  |
| Mic 隔離                     | 拖放 gaskets 和震動解除結合到結構，特別是針對隔離任何震動路徑，因為整合式的主講人      |
| 取樣時鐘                    | 裝置音訊必須是免費的抖動和放棄低漂移    |
| 記錄功能                 | 裝置必須能夠同時記錄個別通道未經處理資料流 |
| USB                               | 所有 USB 音訊輸入的裝置必須都設定根據描述元[USB 音訊裝置 Rev3 規格](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| 麥克風幾何               | 驅動程式必須實作[麥克風陣列 Geometry 描述元](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry)正確  |
| 可搜尋性                   | 裝置必須沒有任何 undiscoverable 或無法控制的硬體、 韌體或第 3 個合作對象以軟體為基礎的非線性音訊處理演算法，從裝置|
| 擷取格式                    | 擷取格式必須使用最小的取樣率為 16 kHz 和建議的 24 位元深度      |

## <a name="electrical-architecture-considerations"></a>電力架構方面的考量

情況適用時，陣列可能連接到 USB 主應用程式 （例如執行 Microsoft 音訊堆疊 SoC) 和語音服務或其他應用程式的介面。

硬體元件，例如 PDM-TDM 轉換應該確定麥克風的 /IPMO/ 與動態範圍會保留在重新取樣器。

應該支援高速 USB 音訊類別 2.0 內任何音訊 Mcu，以便提供最多七個通道，在更高的取樣率和位元深度的必要頻寬。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> 深入了解[語音裝置 SDK](speech-devices-sdk.md)