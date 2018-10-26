---
title: 從翻譯語音 API 移轉至語音服務
titleSuffix: Azure Cognitive Services
description: 使用本主題來將應用程式從翻譯語音 API 移轉至語音服務。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 10/01/2018
ms.author: aahi
ms.openlocfilehash: b50cdc6978519c0ec9da447c324237c00577d9fd
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885267"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>從翻譯語音 API 移轉至語音服務

使用本文來將應用程式從 Microsoft 翻譯語音 API 移轉至[語音服務](index.yml)。 本指南會概述翻譯語音 API 和語音服務之間的差異，並針對移轉應用程式的策略提供建議。

> [!NOTE]
> 語音服務不會接受您的翻譯語音 API 訂用帳戶金鑰。 您必須啟用新的語音服務訂用帳戶。

## <a name="comparison-of-features"></a>功能比較

| 功能                                           | Translator Speech API                                  | 語音服務 | 詳細資料                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 翻譯為文字                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 翻譯為語音                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 全域端點                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | 語音服務目前不提供全域端點。 全域端點可自動將流量導向最接近的區域端點，並降低應用程式中的延遲。                                                    |
| 區域端點                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 連線時間限制                             | 90 分鐘                                               | 搭配 SDK 時沒有限制。 搭配 WebSocket 連線時為 10 分鐘                                                                                                                                                                                                                                                                                   |
| 標頭中的驗證金鑰                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 在單一要求中翻譯多個語言 | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 可用的 SDK                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 請參閱[語音服務文件](index.yml)以取得可用的 SDK。                                                                                                                                                    |
| WebSocket 連線                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 語言 API                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | 語音服務所支援的語言範圍與[翻譯 API 語言參考](../translator-speech/languages-reference.md)一文中所述的相同。 |
| 粗話篩選和標記                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM 作為輸入                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 其他檔案類型作為輸入                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| 部分結果                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 計時資訊                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| 相互關連識別碼                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| 自訂語音模型                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 語音服務能提供自訂語音模型，可讓您針對您組織的獨特詞彙自訂語音辨識。                                                                                                                                           |
| 自訂翻譯模型                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 訂閱 Microsoft 文字翻譯 API 可讓您使用[自訂翻譯器](https://www.microsoft.com/translator/business/customization/) (目前處於預覽狀態)，來使用您自己的資料取得更精確的翻譯。                                                 |

## <a name="migration-strategies"></a>移轉策略

如果您或您的組織具有使用翻譯語音 API 的開發中或生產環境應用程式，您應該更新它們以使用語音服務。 請參閱[語音服務](index.yml)文件以取得可用的 SDK、程式碼範例及教學課程。 下面是移轉時應考慮的一些事項：

* 語音服務目前不提供全域端點。 您必須判斷自己的應用程式是否能在針對所有流量使用單一區域端點的情況下有效運作。 如果不行，您便需使用地理位置來判斷最有效的端點。

* 如果您的應用程式會使用長時間執行的連線，且無法使用可用的 SDK，您可以使用 WebSocket 連線，並透過在適當的時機重新連線來管理 10 分鐘的逾時限制。

* 如果您的應用程式使用翻譯文字 API 及翻譯語音 API 以啟用自訂翻譯模型，您將能使用語音服務來直接新增「類別」識別碼。

* 語音服務和翻譯語音 API 不同，可以在單一要求中完成多個語言的翻譯。

## <a name="next-steps"></a>後續步驟

* [免費試用語音服務](get-started.md)
* [快速入門：使用語音 SDK 在 UWP 應用程式中辨識語音](quickstart-csharp-uwp.md)

## <a name="see-also"></a>另請參閱

* [什麼是語音服務](overview.md)
* [語音服務和 SDK 文件](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
