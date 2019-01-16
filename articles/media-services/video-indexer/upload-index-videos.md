---
title: 使用影片索引器上傳影片及編製影片索引
titlesuffix: Azure Media Services
description: 本主題將示範如何透過影片索引器使用 API 來上傳影片及編製影片索引。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 12/10/2018
ms.author: juliako
ms.openlocfilehash: 2af3e75e3810284d9740a3d87d681d9ee31f14d3
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052831"
---
# <a name="upload-and-index-your-videos"></a>上傳影片及編製影片索引  

使用影片索引器 API 上傳視訊時，您會有下列上傳選項： 

* 從 URL 上傳您的影片 (首選)、
* 將影片檔案當作要求本文中的位元組陣列傳送、
* 藉由提供[資產識別碼](https://docs.microsoft.com/azure/media-services/latest/assets-concept)來使用現有的 Azure 媒體服務資產 (僅支援付費帳戶)。

本文將示範如何根據 URL 使用[上傳影片](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API 來上傳影片及編製影片索引。 文章中的程式碼範例包含加上註解的程式碼，用來說明如何上傳位元組陣列。 <br/>本文也會探討一些可以在 API 上設定的參數，以變更 API 的程序和輸出。

上傳您的視訊之後，影片索引器會選擇性地對視訊進行編碼 (如文章中所討論)。 建立影片索引器帳戶時，您可以選擇免費試用帳戶 (您可取得特定的免費編製索引分鐘數) 或付費選項 (您不會受限於配額)。 使用免費試用時，影片索引器最多可為網站使用者提供 600 分鐘的免費編製索引，以及為 API 使用者提供 2400 分鐘的免費索引編製。 使用付費選項時，您建立的影片索引器帳戶會[連線到您的 Azure 訂用帳戶和 Azure 媒體服務帳戶](connect-to-azure.md)。 您需支付已編製索引的分鐘數，以及媒體帳戶相關費用。 

## <a name="uploading-considerations"></a>上傳考量
    
- 若根據 URL 上傳影片 (首選)，則必須使用 TLS 1.2 (或更高版本) 來保護端點
- 具有 URL 選項的上傳大小限制為 10 GB
- 具有位元組陣列選項的上傳大小限制為 2 GB 
- 位元組陣列選項會在 30 分鐘後逾時
- 在 `videoURL` 參數中提供的 URL 需要進行編碼

> [!Tip]
> 建議使用 .NET Framework 4.6.2 版。 或更高版本，因為舊版 .NET Framework 不會預設使用 TLS 1.2。
>
> 如果您必須使用舊版 .NET Framework，請在進行 REST API 呼叫之前，在程式碼中新增一行：  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>設定與參數

本節將說明一些選擇性參數及設定這些參數的時機。

### <a name="externalid"></a>externalID 

此參數可讓您指定要與影片建立關聯的識別碼。 識別碼可以套用至外部的「影片內容管理」(VCM) 系統整合。 您可以使用指定的外部識別碼，搜尋位在影片索引器入口網站中的影片。

### <a name="callbackurl"></a>callbackUrl

用來通知客戶下列事件的 URL (使用 POST 要求)：

- 索引狀態變更： 
    - 屬性：    
    
        |Name|說明|
        |---|---|
        |id|視訊的識別碼|
        |state|影片狀態|  
    - 範例： https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- 在影片中識別到的人員：
    - properties
    
        |Name|說明|
        |---|---|
        |id| 視訊的識別碼|
        |faceId|影片索引中出現的臉部識別碼|
        |knownPersonId|臉部模型中唯一的個人識別碼|
        |personName|人員名稱|
        
     - 範例： https://test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

#### <a name="notes"></a>注意

- 影片索引器會傳回原始 URL 中提供的任何現有參數。
- 提供的 URL 必須進行編碼。

### <a name="indexingpreset"></a>indexingPreset

如果原始或外部錄影包含背景雜訊，請使用此參數。 此參數會用來設定索引編製程序。 您可以指定下列值：

- `Default` – 使用音訊和視訊來編製索引及擷取深入解析
- `AudioOnly` – 只使用音訊 (忽略視訊) 來編製索引及擷取深入解析
- `DefaultWithNoiseReduction` – 從音訊及視訊編製索引及擷取深入解析的同時，在音訊串流上套用減少雜訊演算法

價格取決於選取的索引編製選項。  

### <a name="priority"></a>優先順序

影片索引器會根據影片的優先順序來為其編製索引。 請使用 **priority** 參數來指定索引的優先順序。 下列是有效值：**Low**、**Normal** (預設值) 和 **High**。

僅有付費帳戶可支援 **Priority** 參數。

### <a name="streamingpreset"></a>streamingPreset

上傳您的影片後，影片索引器會選擇性地對影片進行編碼。 然後，繼續進行索引編製並分析影片。 當影片索引器完成分析時，您會收到包含影片識別碼的通知。  

使用[上傳影片](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)或[重新編製影片索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API 時，其中一個選擇性參數為 `streamingPreset`。 如果您將 `streamingPreset` 設定為 `Default`、`SingleBitrate` 或 `AdaptiveBitrate`，就會觸發編碼程序。 完成編製索引及編碼工作後，影片就會發佈，因此您也可以對影片進行串流處理。 您想要串流影片的串流端點必須處於 [執行中] 狀態。

若要執行編製索引及編碼工作，[連線至您影片索引器帳戶的 Azure 媒體服務帳戶](connect-to-azure.md)需要保留單元。 如需詳細資訊，請參閱[調整媒體處理](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview)。 由於這些都是計算密集的作業，強烈建議使用 S3 單元類型。 RU 數量會定義可以平行執行的作業數目上限。 基準建議是 10 個 S3 RU。 

如果您只想要編製影片索引，但不要對影片進行編碼，請將 `streamingPreset` 設定為 `NoStreaming`。

### <a name="videourl"></a>videoUrl

要對其編製索引的視訊/音訊檔案 URL。 URL 必須指向媒體檔案 (不支援 HTML 網頁)。 作為 URI 一部分提供的存取權杖可以保護檔案，而處理檔案的端點必須透過 TLS 1.2 或更高版本來保護。 URL 必須進行編碼。 

如果未指定 `videoUrl`，影片索引器將預期您會以多部分/表單主體內容的形式傳遞檔案。

## <a name="code-sample"></a>程式碼範例

下列 C# 程式碼片段會示範一起使用所有影片索引器 API 的方式。

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =newbyte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(newByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video id from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Debug.WriteLine("");
        Debug.WriteLine("State:");
        Debug.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Debug.WriteLine("");
            Debug.WriteLine("Full JSON:");
            Debug.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```
## <a name="common-errors"></a>常見錯誤

下表列出上傳作業可能會傳回的狀態碼。

|狀態碼|ErrorType (在回應本文中)|說明|
|---|---|---|
|400|VIDEO_ALREADY_IN_PROGRESS|指定帳戶中已有正在處理的相同影片。|
|400|VIDEO_ALREADY_FAILED|不到 2 小時前，指定帳戶中有相同的影片處理失敗。 API 用戶端應該等待至少 2 小時，才能重新上傳影片。|

## <a name="next-steps"></a>後續步驟

[檢查 v2 API 產生的 Azure 影片索引器輸出](video-indexer-output-json-v2.md)
