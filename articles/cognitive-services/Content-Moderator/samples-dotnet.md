---
title: 程式碼範例 - Content Moderator (.NET)
titleSuffix: Azure Cognitive Services
description: 透過 SDK 在 .NET 應用程式中使用 Content Moderator。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 23ef7b515f5391a30f6d1fa9fce1dcb856643c69
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757270"
---
# <a name="content-moderator-net-sdk-samples"></a>Content Moderator .NET SDK 範例

以下清單包含使用 Azure Content Moderator SDK for .NET 建置之程式碼範例的連結。

## <a name="moderation"></a>審核

- **影像審核**：[評估影像中是否有成人或猥褻內容、文字及臉部](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs)。 請參閱[快速入門](image-moderation-quickstart-dotnet.md)。
- **自訂影像**：[使用自訂影像清單進行審核](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs)。 請參閱[快速入門](image-lists-quickstart-dotnet.md)。

> [!NOTE]
> 上限是 **5 個影像清單**，其中每個清單**不可超過 10,000 個影像**。
>

- **文字審核**：[過濾粗話和個人資料的文字](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs)。 請參閱[快速入門](text-moderation-quickstart-dotnet.md)。
- **自訂字詞**：[使用自訂字詞清單進行審核](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs)。 請參閱[快速入門](term-lists-quickstart-dotnet.md)。

> [!NOTE]
> 上限是 **5 個字詞清單**，其中每個清單**不可超過 10,000 個字詞**。
>

- **影片審核**：[掃描影片中是否有成人和猥褻內容，並取得結果](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs)。 請參閱[快速入門](video-moderation-api.md)。

## <a name="review"></a>審核

- **影像作業**：[啟動一個會掃描並建立審核項目的審核作業](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs)。 請參閱[快速入門](moderation-jobs-quickstart-dotnet.md)。
- **影像審核**：[建立人在迴路 (human-in-the-loop) 的審核項目](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs)。 請參閱[快速入門](moderation-reviews-quickstart-dotnet.md)。
- **影片審核**：[建立人在迴路 (human-in-the-loop) 的影片審核項目](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs)。 請參閱[快速入門](video-reviews-quickstart-dotnet.md)
- **影片文字記錄審核**：[建立人在迴路 (human-in-the-loop) 的影片文字記錄審核項目](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs)。請參閱[快速入門](video-reviews-quickstart-dotnet.md)

請參閱位於 [GitHub 上的 Content Moderator .NET 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)的所有 .NET 範例 \(英文\)。
