---
title: Azure Content Moderator - 使用 .NET 來啟動審核作業 | Microsoft Docs
description: 如何使用 Azure Content Moderator SDK for .NET 來起始審核作業
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/06/2018
ms.author: sajagtap
ms.openlocfilehash: a103875607355993e216ce1ddea02009fc8fa1c4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368050"
---
# <a name="start-moderation-jobs-using-net"></a>使用 .NET 來啟動審核作業

本文提供資訊和範例程式碼，可協助您開始使用 Content Moderator SDK for .NET 來執行下列操作：
 
- 啟動審核作業以掃描和建立人工審核者的檢閱
- 取得擱置中檢閱的狀態
- 追蹤並取得檢閱的最終狀態
- 將結果提交至回呼 URL

本文假設您已經熟悉 Visual Studio 和 C#。

## <a name="sign-up-for-content-moderator-services"></a>註冊 Content Moderator 服務

您必須有訂用帳戶金鑰，才能透過 REST API 或 SDK 使用 Content Moderator 服務。
請參考[快速入門](quick-start.md)，以了解如何取得金鑰。

## <a name="define-a-custom-moderation-workflow"></a>定義自訂審核工作流程

審核作業會使用 API 掃描內容，並使用**工作流程**來判斷是否要建立檢閱。
雖然檢閱工具包含預設工作流程，不過讓我們針對本快速入門[定義自訂工作流程](Review-Tool-User-Guide/Workflows.md)。

您會在啟動審核作業的程式碼中使用工作流程的名稱。

## <a name="create-your-visual-studio-project"></a>建立 Visual Studio 專案

1. 將一個新的 [主控台應用程式 (.NET Framework)] 專案新增到您的解決方案。

   在範例程式碼中，將專案命名為 **CreateReviews**。

1. 選取此專案作為解決方案的單一啟始專案。

1. 新增對您在 [Content Moderator 用戶端協助程式快速入門](content-moderator-helper-quickstart-dotnet.md)中所建立 **ModeratorHelper** 專案組件的參考。

### <a name="install-required-packages"></a>安裝必要的套件

安裝下列 NuGet 封裝：

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>更新程式的 using 陳述式

修改程式的 using 陳述式。

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="initialize-application-specific-settings"></a>將應用程式特定的設定初始化

將下列內容和靜態欄位新增至 Program.cs 中的 **Program** 類別。

> [!NOTE]
> 您可以將 TeamName 常數設定為建立 Content Moderator 訂用帳戶時所使用的名稱。 您可以從 [Content Moderator 網站](https://westus.contentmoderator.cognitive.microsoft.com/)擷取 TeamName。
> 登入後，請從 [設定] (齒輪) 功能表選取 [認證]。
>
> 您小組的名稱會是 [API] 區段中 [識別碼] 欄位的值。


    /// <summary>
    /// The moderation job will use this workflow that you defined earlier.
    /// See the quickstart article to learn how to setup custom workflows.
    /// </summary>
    private const string WorkflowName = "OCR";
    
    /// <summary>
    /// The name of the team to assign the job to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Conent Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "***";

    /// <summary>
    /// The URL of the image to create a review job for.
    /// </summary>
    private const string ImageUrl =
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>新增程式碼來自動審核、建立檢閱和取得作業詳細資料

> [!Note]
> 實務上，您可以將回呼 URL **CallbackEndpoint** 設定為負責 (透過 HTTP POST 要求) 接收手動檢閱結果的 URL。

一開始，請先將以下程式碼新增至 **Main** 方法。

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the nameof the workflow defined in the online review tool.
            // See the quickstart article to learn more.
            var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                    TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

            // Record the job ID.
            var jobId = jobResult.Result.Body.JobIdProperty;

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                jobResult.Result.Body, Formatting.Indented));

            Thread.Sleep(2000);
            writer.WriteLine();

            writer.WriteLine("Get review job status.");
            var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                    TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                    jobDetails.Result.Body, Formatting.Indented));

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
            Thread.Sleep(latencyDelay * 1000);

            writer.WriteLine("Get review details.");
            jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
            TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
            jobDetails.Result.Body, Formatting.Indented));
        }
        writer.Flush();
        writer.Close();
    }

> [!NOTE]
> 您的 Content Moderator 服務金鑰會有每秒要求數目 (RPS) 的速率限制。 如果您超出此限制，SDK 就會擲回錯誤碼為 429 的例外狀況。 
>
> 免費層金鑰有一個 RPS 速率限制。

## <a name="run-the-program-and-review-the-output"></a>執行程式並檢閱輸出

您會在主控台中看到下列輸出範例：

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

請登入 Content Moderator 檢閱工具，以查看擱置中的影像檢閱。

使用 [下一步] 按鈕以提交影像。

![給人工審核者的影像檢閱](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>查看記錄檔中的輸出範例

> [!NOTE]
> 在輸出檔案中，**Teamname**、**ContentId**、**CallBackEndpoint** 和 **WorkflowId** 字串會反映您稍早所使用的值。

    Create moderation job for an image.
    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

    Get review details.
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": [
        {
            "Ts": "2018-01-07T00:38:26.7714671",
            "Msg": "Successfully got hasText response from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:26.4181346",
            "Msg": "Getting hasText from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:25.5122828",
            "Msg": "Starting Execution - Try 1"
        }
        ]
    }


## <a name="your-callback-url-if-provided-receives-this-response"></a>若有提供回呼 URL，則會收到此回應。

您會看到如下列範例所示的回應：

> [!NOTE]
> 在回呼回應中，**ContentId** 和 **WorkflowId** 字串會反映您稍早所使用的值。

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
        "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
        "WorkFlowId": "OCR",
        "Status": "Complete",
        "ContentType": "Image",
        "CallBackType": "Job",
        "ContentId": "contentID",
        "Metadata": {
            "hastext": "True",
            "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
            "imagename": "contentID"
        }
    }


## <a name="next-steps"></a>後續步驟

針對這個及其他適用於 .NET 的 Content Moderator 快速入門，[下載 Visual Studio 解決方案](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)，並開始進行您的整合。
