---
title: Azure Content Moderator - 使用 .NET 來建立影片文字記錄審核項目 | Microsoft Docs
description: 如何使用 Azure Content Moderator SDK for .NET 來建立影片文字記錄審核項目
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/19/2018
ms.author: sajagtap
ms.openlocfilehash: 3286da6e38f0fba02386d877a835fb694ed0fdec
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "35368554"
---
# <a name="create-video-transcript-reviews-using-net"></a>使用 .NET 來建立影片文字記錄審核項目

本文提供資訊和範例程式碼，可協助您以 C# 快速開始使用 Content Moderator SDK 來執行下列操作：

- 建立給人工仲裁者的影片審核項目
- 將仲裁過的文字記錄新增至審核項目
- 發佈審核項目

## <a name="prerequisites"></a>先決條件

本文假設您已[仲裁影片](video-moderation-api.md)，並已在審核工具中[建立影片審核項目](video-reviews-quickstart-dotnet.md)以供進行人工決策。 您現在想要在審核工具中新增仲裁過的影片文字記錄。

本文同時也假設您已經熟悉 Visual Studio 和 C#。

### <a name="sign-up-for-content-moderator-services"></a>註冊 Content Moderator 服務

您必須有訂用帳戶金鑰，才能透過 REST API 或 SDK 使用 Content Moderator 服務。

在「Content Moderator 儀表板」中，您可以在 [設定] > [認證] > [API] > [試用 Ocp-Apim-Subscription-Key] 中，找到您的訂用帳戶金鑰。 如需詳細資訊，請參閱[概觀](overview.md)。

### <a name="prepare-your-video-for-review"></a>準備影片以供審核

將文字記錄新增至影片審核項目。 影片必須在線上發佈。 您需要其串流端點。 串流端點可讓審核工具影片播放程式播放影片。

![影片示範縮圖](images/ams-video-demo-view.PNG)

- 複製這個 [Azure 媒體服務示範](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest)頁面上的 **URL** 作為資訊清單 URL。

## <a name="create-your-visual-studio-project"></a>建立 Visual Studio 專案

1. 將一個新的 [主控台應用程式 (.NET Framework)] 專案新增到您的解決方案。

1. 將專案命名為 **VideoTranscriptReviews**。

1. 選取此專案作為解決方案的單一啟始專案。

### <a name="install-required-packages"></a>安裝必要的套件

安裝下列適用於 TermLists 專案的 NuGet 套件。

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>更新程式的 using 陳述式

修改程式的 using 陳述式，如下所示。

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;
    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;


### <a name="add-private-properties"></a>新增私有屬性

將下列私有屬性新增至 VideoTranscriptReviews 命名空間、Program 類別。

依指示取代這些屬性的範例值。


    namespace VideoReviews
    {
        class Program
        {
            // NOTE: Replace this example location with the location for your Content Moderator account.
            /// <summary>
            /// The region/location for your Content Moderator account, 
            /// for example, westus.
            /// </summary>
            private static readonly string AzureRegion = "YOUR CONTENT MODERATOR REGION";

            // NOTE: Replace this example key with a valid subscription key.
            /// <summary>
            /// Your Content Moderator subscription key.
            /// </summary>
            private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

            // NOTE: Replace this example team name with your Content Moderator team name.
            /// <summary>
            /// The name of the team to assign the job to.
            /// </summary>
            /// <remarks>This must be the team name you used to create your 
            /// Content Moderator account. You can retrieve your team name from
            /// the Conent Moderator web site. Your team name is the Id associated 
            /// with your subscription.</remarks>
            public static readonly string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

            /// <summary>
            /// The base URL fragment for Content Moderator calls.
            /// </summary>
            private static readonly string AzureBaseURL =
                $"{AzureRegion}.api.cognitive.microsoft.com";

            /// <summary>
            /// The minimum amount of time, in milliseconds, to wait between calls
            /// to the Content Moderator APIs.
            /// </summary>
            private const int throttleRate = 2000;


### <a name="create-content-moderator-client-object"></a>建立 Content Moderator 用戶端物件

將下列方法定義新增至 VideoTranscriptReviews 命名空間、Program 類別。

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
        {
            BaseUrl = AzureBaseURL
        };
    }

## <a name="create-a-video-review"></a>建立影片審核項目

使用 **ContentModeratorClient.Reviews.CreateVideoReviews** 來建立影片審核項目。 如需詳細資訊，請參閱 [API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) \(英文\)。

**CreateVideoReviews** 具有下列必要參數：
1. 一個包含 MIME 類型的字串，這應該是 "application/json"。 
1. 您的 Content Moderator 小組名稱。
1. 一個 **IList<CreateVideoReviewsBodyItem>** 物件。 每個 **CreateVideoReviewsBodyItem** 物件皆代表一個影片審核項目。 這個快速入門會一次建立一個審核項目。

**CreateVideoReviewsBodyItem** 具有數個屬性。 您必須至少設定下列屬性：
- **Content**。 要審核的影片 URL。
- **ContentId**。 要指派給影片審核項目的識別碼。
- **Status**。 請將值設定為 "Unpublished"。 如果您未設定此屬性，則會預設為 "Pending"，意謂著已發佈影片審核項目而正等待人工審核。 發佈影片審核項目之後，您就無法再將影片畫面、文字記錄或文字記錄仲裁結果新增至該審核項目。

> [!NOTE]
> **CreateVideoReviews** 會傳回 IList<string>。 這些字串中每個都包含影片審核項目的識別碼。 這些識別碼是 GUID 且與 **ContentId** 屬性的值不同。 

將下列方法定義新增至 VideoReviews 命名空間、Program 類別。

    /// <summary>
    /// Create a video review. For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="id">The ID to assign to the video review.</param>
    /// <param name="content">The URL of the video to review.</param>
    /// <returns>The ID of the video review.</returns>
    private static string CreateReview(ContentModeratorClient client, string id, string content)
    {
        Console.WriteLine("Creating a video review.");

        List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
            new CreateVideoReviewsBodyItem
            {
                Content = content,
                ContentId = id,
                /* Note: to create a published review, set the Status to "Pending".
                However, you cannot add video frames or a transcript to a published review. */
                Status = "Unpublished",
            }
        };

        var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

        Thread.Sleep(throttleRate);

        // We created only one review.
        return result[0];
    }

> [!NOTE]
> 您的 Content Moderator 服務金鑰會有每秒要求數目 (RPS) 的速率限制。 如果您超出此限制，SDK 就會擲回錯誤碼為 429 的例外狀況。 
>
> 免費層金鑰有一個 RPS 速率限制。

## <a name="add-transcript-to-video-review"></a>將文字記錄新增至影片審核項目

您可以使用 **ContentModeratorClient.Reviews.AddVideoTranscript** 將文字記錄新增至影片審核項目。 **AddVideoTranscript** 具有下列必要參數：
1. 您的 Content Moderator 小組識別碼。
1. **CreateVideoReviews** 所傳回的影片審核項目識別碼。
1. 一個包含文字記錄的 **Stream** 物件。

文字記錄的格式必須是 WebVTT。 如需詳細資訊，請參閱 [WebVTT：網路影片文字播放軌格式](https://www.w3.org/TR/webvtt1/) \(英文\)。

> [!NOTE]
> 此程式使用 VTT 格式的範例文字記錄。 在真實世界解決方案中，您會使用「Azure 媒體索引器」服務從影片[產生文字記錄](https://docs.microsoft.com/azure/media-services/media-services-index-content)。

將下列方法定義新增至 VideotranscriptReviews 命名空間、Program 類別。

    /// <summary>
    /// Add a transcript to the indicated video review.
    /// The transcript must be in the WebVTT format.
    /// For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="transcript">The video transcript.</param>
    static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
    {
        Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
        client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
        Thread.Sleep(throttleRate);
    }

## <a name="add-a-transcript-moderation-result-to-video-review"></a>將文字記錄仲裁結果新增至影片審核項目

除了將文字記錄新增至影片審核項目之外，您還需新增該文字記錄的仲裁結果。 您可以使用 **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**來達到此目的。 如需詳細資訊，請參閱 [API 參考](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff) \(英文\)。

**AddVideoTranscriptModerationResult** 具有下列必要參數：
1. 一個包含 MIME 類型的字串，這應該是 "application/json"。 
1. 您的 Content Moderator 小組名稱。
1. **CreateVideoReviews** 所傳回的影片審核項目識別碼。
1. 一個 IList<TranscriptModerationBodyItem>。 **TranscriptModerationBodyItem** 具有下列屬性：
- **Terms**。 一個 IList<TranscriptModerationBodyItemTermsItem>。 **TranscriptModerationBodyItemTermsItem** 具有下列屬性：
- **Index**。 字詞的從零開始索引。
- **Term**。 一個包含字詞的字串。
- **Timestamp**。 一個包含在文字記錄中找到字詞之時間 (以秒為單位) 的字串。

文字記錄的格式必須是 WebVTT。 如需詳細資訊，請參閱 [WebVTT：網路影片文字播放軌格式](https://www.w3.org/TR/webvtt1/) \(英文\)。

將下列方法定義新增至 VideoTranscriptReviews 命名空間、Program 類別。 此方法會將文字記錄提交給 **ContentModeratorClient.TextModeration.ScreenText** 方法。 此外，也會將會將結果轉譯成 IList<TranscriptModerationBodyItem>，然後提交給 **AddVideoTranscriptModerationResult**。

    /// <summary>
    /// Add the results of moderating a video transcript to the indicated video review.
    /// For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="transcript">The video transcript.</param>
    static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
    {
        Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

        // Screen the transcript using the Text Moderation API. For more information, see:
        // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
        Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

        // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
        List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
        if (null != screen.Terms)
        {
            foreach (var term in screen.Terms)
            {
                if (term.Index.HasValue)
                {
                    terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
                }
            }
        }

        List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
        {
            new TranscriptModerationBodyItem()
            {
                Timestamp = "0",
                Terms = terms
            }
        };

        client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

        Thread.Sleep(throttleRate);
    }


## <a name="publish-video-review"></a>發佈影片審核項目

您可以使用 **ContentModeratorClient.Reviews.PublishVideoReview** 來發佈影片審核項目。 **PublishVideoReview** 具有下列必要參數：
1. 您的 Content Moderator 小組名稱。
1. **CreateVideoReviews** 所傳回的影片審核項目識別碼。

將下列方法定義新增至 VideoReviews 命名空間、Program 類別。

    /// <summary>
    /// Publish the indicated video review. For more information, see the reference API:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    private static void PublishReview(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Publishing the review with ID {0}.", review_id);
        client.Reviews.PublishVideoReview(TeamName, review_id);
        Thread.Sleep(throttleRate);
    }

## <a name="putting-it-all-together"></a>總整理

將 **Main** 定義新增至 VideoTranscriptReviews 命名空間、Program 類別。 最後，結束 Program 類別和 VideoTranscriptReviews 命名空間。

> [!NOTE]
> 此程式使用 VTT 格式的範例文字記錄。 在真實世界解決方案中，您會使用「Azure 媒體索引器」服務從影片[產生文字記錄](https://docs.microsoft.com/azure/media-services/media-services-index-content)。 

    static void Main(string[] args)
    {
        using (ContentModeratorClient client = NewClient())
        {
            // Create a review with the content pointing to a streaming endpoint (manifest)
            var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
            string review_id = CreateReview(client, "review1", streamingcontent);

            var transcript = @"WEBVTT

            01:01.000 --> 02:02.000
            First line with a crap word in a transcript.

            02:03.000 --> 02:25.000
            This is another line in the transcript.
            ";

            AddTranscript(client, review_id, transcript);

            AddTranscriptModerationResult(client, review_id, transcript);

            // Publish the review
            PublishReview(client, review_id);

            Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
            Console.WriteLine("Press any key to close the application.");
            Console.Read();
        }
    }

## <a name="run-the-program-and-review-the-output"></a>執行程式並檢閱輸出

當您執行應用程式時，會看到以下幾行的輸出：

    Creating a video review.
    Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="navigate-to-your-video-transcript-review"></a>瀏覽至您的影片文字記錄審核項目

移至 Content Moderator 審核工具中的 [審核]>[影片]>[文字記錄] 畫面上的影片文字記錄審核項目。

您會看到下列功能：
- 您新增的兩行文字記錄
- 已找到並由文字仲裁服務醒目提示的粗話字詞
- 選取文字記錄文字會從該時間戳記開始播放影片

![給人工仲裁者的影片文字記錄審核項目](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>後續步驟

了解如何在審核工具中產生[影片審核項目](video-reviews-quickstart-dotnet.md)。

查看有關如何開發[完整影片仲裁解決方案](video-transcript-moderation-review-tutorial-dotnet.md)的詳細教學課程。

針對這個及其他適用於 .NET 的 Content Moderator 快速入門，[下載 Visual Studio 解決方案](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)。
