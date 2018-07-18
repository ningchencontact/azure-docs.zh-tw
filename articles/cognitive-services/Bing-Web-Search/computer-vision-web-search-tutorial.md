---
title: 圖像式搜尋行動應用程式教學課程
description: 使用認知服務電腦視覺 API、Bing Web 搜尋 API 和 Xamarin.Forms 跨平台架構開啟來源 C# 應用程式，以實作圖像式搜尋。
services: bing-web-search, computer-vision
author: Aristoddle
manager: bking
ms.service: cognitive-services
ms.devlang: csharp
ms.topic: article
ms.date: 06/22/2017
ms.author: t-jolan
ms.openlocfilehash: 54dabaeaad2e49ba7cc138636054bc3dfa4b8379
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368598"
---
# <a name="visual-search-mobile-app-tutorial"></a>圖像式搜尋行動應用程式教學課程

## <a name="introduction"></a>簡介  
本教學課程將探索[電腦視覺 API](https://azure.microsoft.com/services/cognitive-services/computer-vision/) 和 [Bing Web 搜尋 API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 端點，並且示範如何使用它們來搭配 [Xamarin.Forms](https://developer.xamarin.com/guides/xamarin-forms/)，建置基本的圖像式搜尋應用程式。  整體來說，本教學課程涵蓋下列主題： 

> [!div class="checklist"]
> * 設定 Visual Studio 來開發 Xamarin.Forms 應用程式
> * 使用 [Xamarin 媒體外掛程式](https://github.com/jamesmontemagno/MediaPlugin) 來擷取及匯入影像資料
> * 使用電腦視覺 API 從影像中剖析文字
> * 將搜尋要求傳送至 Bing Web 搜尋 API
> * 使用 [Json.NET](https://github.com/JamesNK/Newtonsoft.Json) (透過 LINQ 與模型物件還原序列化) 剖析這兩個 API 的 JSON 回應
> * 建立跨平台 Xamarin.Forms 使用者介面以進行圖像式搜尋 

## <a name="prerequisites"></a>先決條件

此範例是使用 Xamarin.Forms 與 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 來開發的。 可使用 Visual Studio 2017 的 Community 版本。 如需有關使用 Xamarin 與 Visual Studio 的詳細資訊，請參閱 [Xamarin 文件](https://developer.xamarin.com/guides/cross-platform/getting_started/)。

此範例會使用下列 NuGet 套件：

> [!div class="checklist"]
> * [Xamarin 媒體外掛程式](https://github.com/jamesmontemagno/MediaPlugin)
> * [Json.NET](https://github.com/JamesNK/Newtonsoft.Json)

應用程式會使用下列認知服務 API:

> [!div class="checklist"]
> * [Bing Web 搜尋 API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 
> * [電腦視覺 API](https://azure.microsoft.com/services/cognitive-services/computer-vision/)

若要取得這些 API 的 30 天試用版金鑰，請參閱[試用認知服務](https://azure.microsoft.com/try/cognitive-services/)。 如需取得商業用金鑰的詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/calculator/)。

## <a name="setup-for-development"></a>開發設定  

### <a name="installing-xamarin-on-windows"></a>在 Windows 上安裝 Xamarin
安裝任一版本的 Visual Studio 2017 之後，開啟 Visual Studio 安裝程式，選取與您 Visual Studio 安裝相關聯的三橫線功能表，並選擇 [修改]。  

![Visual Studio 安裝程式](./media/computer-vision-web-search-tutorial/visual-studio-installer.png) 

向下捲動至 [行動與遊戲]，並啟用 [使用 .NET 進行行動裝置開發] (如果未啟用)。

![選取 Xamarin.Forms 的 Visual Studio安裝程式](./media/computer-vision-web-search-tutorial/xamarin-forms-is-enabled.png)

最後，按一下視窗右下角的 [修改]，並等候 Xamarin 安裝完成。

### <a name="installing-xamarin-on-macos"></a>在 macOS 上安裝 Xamarin
Xamarin 已預先封裝在 Visual Studio for Mac 中。 您不需要進行任何安裝。

## <a name="building-and-running-the-app"></a>正在建置與執行應用程式

適用於圖像式搜尋應用程式的 Visual Studio 解決方案檔案 (.sln) 可從＜[使用認知服務的圖像式搜尋應用程式](https://azure.microsoft.com/resources/samples/cognitive-services-xamarin-forms-computer-vision-search/)＞中取得。 您可以使用網頁瀏覽器下載 ZIP 封存，將其從 GitHub 複製到工作站，或使用 Visual Studio 下載。

若要開始使用範例，請在 Visual Studio 中開啟 `VisualSearchApp.sln`。  初始化所需元件可能需要一點時間。

應用程式需要兩個第三方程式庫：**Json.NET** 和 **Xamarin 媒體外掛程式**。 您可以使用 NuGet 套件管理員直接將這些程式庫安裝在 Visual Studio 中。 選擇 [工具] > [NuGet 套件管理員] > [管理解決方案的 NuGet 套件]，或在方案總管中以滑鼠右鍵按一下解決方案，然後從捷徑功能表中選擇 [管理 NuGet 套件]。

在 NuGet 視窗中，搜尋並安裝 **Xamarin 媒體外掛程式** (Xam.Plugin.Media) 2.6.2 版和 **Json.NET** (Newtonsoft.Json) 10.0.3。 安裝時請務必選取所有專案。

若要為所有可用平台建置應用程式，請按 [Ctrl + Shift + B]，或按一下功能區功能表上的 [建置] 並選擇 [建置解決方案]。  若要從 Windows 系統上編譯和測試 iOS 的程式碼，請參閱[本指南](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/)的說明。

執行應用程式之前，您必須選取目標組態、平台和專案。  Xamarin.Forms 應用程式可編譯為適用於 Windows、Android 和 iOS 的原生程式碼。 本教學課程包含範例的 Windows 版螢幕擷取畫面，但所有版本的功能都相同。 本指南中使用的部署設定如下所示。  

![將 Visual Studio 設定為適用於 Android phone 的編譯](./media/computer-vision-web-search-tutorial/configuration-selection.png)

一旦建置程序成功完成，而且您也選取目標平台後，按一下工具列中的 [啟動] 按鈕或按 [F5]。 Visual Studio 會將您的解決方案部署到目標平台並將其啟動。

[新增金鑰] 頁面隨即出現。 (此頁面定義於 `AddKeysPage.xaml`。)  

![[新增金鑰] 頁面的影像](./media/computer-vision-web-search-tutorial/add-keys-page.png)  

於此處貼上您的電腦視覺和 Bing Web 搜尋 API 金鑰。 電腦視覺 API 也需要您選擇裝載端點的伺服器。

> [!TIP]
> 若要略過此頁面，請將此資訊輸入 `App.xaml.cs` 中的適當位置。 

應用程式會執行測試查詢來驗證您的金鑰，然後會引導您前往 OCR 選取頁面 (定義於 `OcrSelectPage.xaml`)。
   
![OCR 選取頁面的影像](./media/computer-vision-web-search-tutorial/ocr-select-page.png)  

在此畫面頂端，您可以選擇要辨識列印文字或是手寫文字。

> [!TIP]
> 盡量讓您嘗試從中辨識文字的項目保持水平，並確保其亮度平均且沒有反光。 我們發現有時在手寫字體或其他「花俏」文字上，手寫 OCR 的效果較好。

接下來，按一下 [拍照] 或 [匯入相片]，以使用裝置相機拍照，或選擇裝置上儲存的相片。

已拍攝或選擇相片後，影像會傳遞給電腦視覺 API。 [找到的字組] 頁面 (定義於`OcrResultsPage.xaml`) 會顯示在此影像中辨識出的任何字組。

![OCR 結果頁面的影像](./media/computer-vision-web-search-tutorial/ocr-results-page.png)  

> [!NOTE]
> 我們用來取得這些結果的影像位在來源存放庫中的 `SamplePhotos\TestImage.jpg`。

當您在 [找到的字組] 頁面上按一下項目時，Web 結果頁面 (`WebResultsPage.xaml`) 會隨即出現，並顯示該搜尋的前幾個 Bing 結果。

![Web 結果頁面的影像](./media/computer-vision-web-search-tutorial/web-results-page.png)  
最後，選擇 [Web 結果] 頁面上的項目，以在內嵌的 WebView 中開啟連結。 (或往回瀏覽以選擇不同結果)。

![網頁檢視頁面的影像](./media/computer-vision-web-search-tutorial/web-view-page.png)  

您可以在任何網頁瀏覽器中與頁面互動，或往回瀏覽至 [Web 結果] 頁面。 

## <a name="review-and-learn"></a>檢閱和學習

現在，您已試過圖像式搜尋應用程式，接著讓我們來探索如何確實使用兩個 Microsoft 認知服務 API。  無論您是使用此範例作為應用程式的起點，或只是作為 API 的操作說明，逐一瀏覽應用程式畫面來細查其運作方式十分有用。

### <a name="add-keys-page"></a>新增金鑰頁面
[新增金鑰] 頁面的 UI 定義於 `AddKeysPage.xaml`，而其主要邏輯定義於 `AddKeysPage.xaml.cs`。 由於金鑰會藉由測試要求來進行驗證，因此這個時間點適合用來決定如何在 C# 中使用認知服務端點。  

此互動的基本結構是： 

1. 從 System.Net.Http 具現化 HttpResponseMessage 和 HttpClient 物件
2. 將任何所需的標頭 (定義於每個端點的 API 參考) 連結至 HttpClient 物件
3. 以您的資料傳送 GET 或 POST 要求，將任何必要參數新增至您的端點 URI
4. 確認回應成功
5. 傳遞回應以進行進一步處理

用來檢查 Bing 搜尋 API 金鑰有效性的函式是 `CheckBingSearchKey()`，如下所示。

```csharp
async Task CheckBingSearchKey(object sender = null, EventArgs e = null)
{
    HttpResponseMessage response;
    HttpClient SearchApiClient = new HttpClient();

    SearchApiClient.DefaultRequestHeaders.Add(AppConstants.OcpApimSubscriptionKey, BingSearchKeyEntry.Text);

    try
    {
        response = await SearchApiClient.GetAsync(AppConstants.BingWebSearchApiUrl + "q=test");

        if (response.StatusCode != System.Net.HttpStatusCode.Unauthorized)
        {
            BingSearchKeyEntry.BackgroundColor = Color.Green;
            AppConstants.BingWebSearchApiKey = BingSearchKeyEntry.Text;
            bingSearchKeyWorks = true;
        }
        else
        {
            BingSearchKeyEntry.BackgroundColor = Color.Red;
            bingSearchKeyWorks = false;
        }
    }
    catch( Exception exception )
    {
        BingSearchKeyEntry.BackgroundColor = Color.Red;
        Console.WriteLine($"ERROR: {exception.Message}");
    }
}
```

用來驗證電腦視覺金鑰的類似函式是 `CheckComputerVisionKey()`。

### <a name="ocr-select-page"></a>OCR 選取頁面

OCR 選取頁面 (`OcrSelectPage.xaml`) 有兩個重要角色。 第一，判斷目標相片上要執行何種 OCR。 第二，讓使用者擷取或開啟他們想要處理的影像。

傳統上，第二個工作是跨平台應用程式中難以處理的部分，因為每個平台都需要不同的程式碼。 Xamarin 媒體外掛程式只需少數程式碼就能處理此情況。

下列函式會加入使用 Xamarin 媒體外掛程式擷取相片的範例。  在這之中，我們會：

1. 確認目前裝置上有可用相機
2. 具現化 `StoreCameraMediaOptions` 物件，以指定儲存所擷取影像的位置
3. 擷取影像並取得包含影像資料的 `MediaFile` 物件
4. 將 `MediaFile`解除封裝至位元組陣列
5. 傳回位元組陣列以進行進一步處理

下方 `TakePhoto()` 是使用 Xamarin 媒體外掛程式擷取相片的函數。  

```csharp
async Task<byte[]> TakePhoto()
{
    MediaFile photoMediaFile = null;
    byte[] photoByteArray = null;

    if (CrossMedia.Current.IsCameraAvailable)
    {
        var mediaOptions = new StoreCameraMediaOptions
        {
            PhotoSize = PhotoSize.Medium,
            AllowCropping = true,
            SaveToAlbum = true,
            Name = $"{DateTime.UtcNow}.jpg"
        };
        photoMediaFile = await CrossMedia.Current.TakePhotoAsync(mediaOptions);
        photoByteArray = MediaFileToByteArray(photoMediaFile);
    }
    else
    {
        await DisplayAlert("Error", "No camera found", "OK");
        Console.WriteLine($"ERROR: No camera found");
    }
    return photoByteArray;
}
```
相片匯入公用程式的運作方式也與此類似。 這兩項功能皆可在 `OcrSelectPage.xaml.cs` 中找到。 
 
> [!NOTE]
> 手寫 OCR 端點只能處理小於 4 MB 的相片。 為避免發生檔案大小問題，我們會透過設定 `StoreCameraMediaOptions` 物件上的 `PhotoSize = PhotoSize.Medium` 選項，將相片縮減為原始大小的 50%。  如果您裝置所拍攝的相片品質特別高，而且出現錯誤訊息，您可以改為嘗試 `PhotoSize = PhotoSize.Small`。

以下是用來將 MediaFile 轉換成位元組陣列的公用程式函式。

```csharp
byte[] MediaFileToByteArray(MediaFile photoMediaFile)
{
    using (var memStream = new MemoryStream())
    {
        photoMediaFile.GetStream().CopyTo(memStream);
        return memStream.ToArray();
    }
}
```

### <a name="ocr-results-page"></a>OCR 結果頁面

[OCR 結果] 頁面會顯示使用 **Json.NET** [SelectToken 方法](http://www.newtonsoft.com/json/help/html/SelectToken.htm)從影像擷取的文字。  兩個 OCR 端點的運作方式稍有不同，因此有必要討論這兩者。  

由於電腦視覺 API 只能裝載在少數伺服器位置，因此其端點必須以動態方式建構在執行階段。 `SetOcrLocation` 函式 (屬於靜態 AppConstants，可在 `AppConstants.cs` 類別中找到) 會設定 URI 端點的位置。 這會與使用者在 [新增金鑰] 頁面上選取的項目相對應，或使用 `App.xaml.cs` 中設定的值。 

```csharp
public static void SetOcrLocation(string location)
{
    ComputerVisionApiOcrUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/ocr?language=en&detectOrientation=true";
    ComputerVisionApiHandwritingUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/recognizeText?handwriting=true";
}
```

讓我們仔細看看這些 API 要求。 電腦視覺 OCR API 能夠從不明語言中剖析文字，但我們告訴它要搜尋英文文字以改善結果。 我們也可讓 API 為我們偵測文字方向。 使用固定方向可改善我們的剖析結果，但方向偵測功能可能適合行動裝置應用程式。

您可以從[列印光學字元辨識 API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fc)中，進一步了解可與此端點搭配的參數。  

手寫 OCR API 仍為預覽版本，且目前只適用於英文文字。  基於這個理由，其目前的唯一參數是一個旗標，指出是否要完全剖析手寫文字。 手寫 OCR API 可以剖析機器列印文字和手寫文字，但 `handwriting=false` 在印刷文字上產生的效果較好。 

由於此應用程式使用英文，因此我們可以只對此範例使用手寫 OCR，並根據使用者要我們辨識的內容來設定 `handwriting` 旗標。  基於說明目的，我們使用了兩個端點。  

您可以從[手寫光學字元辨識 API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200)中，進一步了解可與此端點搭配的參數。

現在，讓我們看看呼叫這些 API 的函式。

`FetchPrintedWordList()` 會使用列印 OCR 端點來剖析影像中的列印文字。 HTTP 要求遵循的結構類似於 [新增金鑰] 頁面中用來驗證金鑰的呼叫，但我們需要傳送圖片。 由於相片太大會無法傳入查詢字串，因此我們必須使用 HTTP POST 要求，而不是 GET 要求。 我們需要將相片 (以位元組陣列的形式存在記憶體中) 編碼為 `ByteArrayContent` 物件，並新增標頭至此物件，以定義所傳送資料的類型。 

您可以從[電腦視覺 API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200)中了解可接受的內容類型。  

```csharp
async Task<ObservableCollection<string>> FetchPrintedWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiOcrUrl, content);
        }

        string ResponseString = await response.Content.ReadAsStringAsync();
        JObject json = JObject.Parse(ResponseString);

        // Here, we pull down each "line" of text and then join it to 
        // make a string representing the entirety of each line.  
        // In the Handwritten endpoint, you are able to extract the 
        // "line" without any further processing.  If you would like 
        // to simply get a list of all extracted words,* you can do 
        // this with:
        // json.SelectTokens("$.regions[*].lines[*].words[*].text) 
        IEnumerable<JToken> lines = json.SelectTokens("$.regions[*].lines[*]");
        if (lines != null)
        {
            foreach (JToken line in lines)
            {
                IEnumerable<JToken> words = line.SelectTokens("$.words[*].text");
                if (words != null)
                {
                    wordList.Add(string.Join(" ", words.Select(x => x.ToString())));
                }
            }
        }
    }
    return wordList;
}
```
> [!TIP]
> 請注意，我們會使用 **Json.NET** [SelectToken 方法](http://www.newtonsoft.com/json/help/html/SelectToken.htm)從回應物件中擷取文字。 當我們僅需要特定部分的 JSON 回應時，`SelectToken` 是最好的選擇，我們可以接著將此部分回應傳遞至下一個函式。 對於程式碼的其他部分，我們會將 JSON 回應還原序列化為 `ModelObjects.cs` 中所定義的模型物件。

列印 OCR 與手寫 OCR 要求之間的主要差異是，列印 OCR 服務傳回的辨識文字屬於 HTTP 回應，而手寫 OCR 服務需要其他要求來取得此資訊。 一開始，手寫 OCR 要求會傳回 HTTP 202 狀態，指示處理已開始。 此回應包含一個端點，用戶端必須確認此端點，才能取得完成的回應。 請參閱 `FetchHandwrittenWordList()` 以查看其運作方式。

```csharp
async Task<ObservableCollection<string>> FetchHandwrittenWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        // Make the POST request to the handwriting recognition URL
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiHandwritingUrl, content);
        }

        // Fetch results
        IEnumerable<string> operationLocationValues;
        string statusUri = string.Empty;
        if (response.Headers.TryGetValues("Operation-Location", out operationLocationValues))
        {
            statusUri = operationLocationValues.FirstOrDefault();

            // Ping status URL, wait for processing to finish 
            JObject obj = await FetchResultFromStatusUri(statusUri);
            IEnumerable<JToken> strings = obj.SelectTokens("$.recognitionResult.lines[*].text");
            foreach (string s in strings)
            {
                wordList.Add((string)s);
            }
        }
    }
    return wordList;
}
```

`FetchResultFromStatusUri()` 是手寫 OCR 程序的第二個部分。 它會偵測從最初回應中繼資料內擷取的 URI，直到取得結果或函式逾時。務必在此函式的執行緒上以非同步方式呼叫此函式。 否則，此方法會鎖定使用者介面，直到處理完成。

```csharp
// Takes in the url to check for handwritten text parsing results, and pings it per second until processing is finished
// Returns the JObject holding data for a successful parse
async Task<JObject> FetchResultFromStatusUri(string statusUri)
{
    JObject obj = null;
    int timeoutcounter = 0;
    HttpResponseMessage response = await visionApiClient.GetAsync(statusUri);
    string responseString = await response.Content.ReadAsStringAsync();
    obj = JObject.Parse(responseString);
    while ((!((string)obj.SelectToken("status")).Equals("Succeeded")) && (timeoutcounter++ < 60))
    {
        await Task.Delay(1000);
        response = await visionApiClient.GetAsync(statusUri);
        responseString = await response.Content.ReadAsStringAsync();
        obj = JObject.Parse(responseString);
    } 
    return obj;
}
```

### <a name="web-results-page"></a>Web 結果頁面
在使用者選取 [OCR 結果] 頁面上顯示的搜尋字詞後，我們隨之移至 [Web 結果] 頁面。  在此處，我們會建構 [Bing Web 搜尋 API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 要求，並將其傳送給服務的端點，以及使用 **Json.NET** [DeserializeObject](http://www.newtonsoft.com/json/help/html/DeserializeObject.htm) 方法將 JSON 回應還原序列化。  

```csharp
async Task<WebResultsList> GetQueryResults()
{
    // URL-encode the query term
    var queryString = System.Net.WebUtility.UrlEncode(queryTerm);

    // Here we encode the URL that will be used for the GET request to 
    // find query results.  Its arguments are as follows:
    // 
    // - [count=20] This sets the number of webpage objects returned at 
    //   "$.webpages" in the JSON response.  Currently, the API asks for 
    //   20 webpages in the response
    //
    // - [mkt=en-US] This sets the market where the results come from.
    //   Currently, the API looks for english results based in the 
    //   United States.
    //
    // - [q=queryString] This sets the string queried using the Search 
    //   API.   
    //
    // - [responseFilter=Webpages] This filters the response to only 
    //   include Webpage results.  This tag can take a comma seperated 
    //   list of response types that you are looking for.  If left 
    //   blank, all responses (webPages, News, Videos, etc) are 
    //   returned.
    //
    // - [setLang=en] This sets the languge for user interface strings. 
    //   To learn more about UI strings, check the Web Search API 
    //   reference.
    //
    // - [API Reference] https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference
    string uri = AppConstants.BingWebSearchApiUrl + $"count=20&mkt=en-US&q={queryString}&responseFilter=Webpages&setLang=en";

    // Make the HTTP Request
    WebResultsList webResults = null;
    HttpResponseMessage httpResponseMessage = await searchApiClient.GetAsync(uri);
    var responseContentString = await httpResponseMessage.Content.ReadAsStringAsync();
    JObject json = JObject.Parse(responseContentString);
    JToken resultBlock = json.SelectToken("$.webPages");
    if (resultBlock != null)
    {
        webResults = JsonConvert.DeserializeObject<WebResultsList>(resultBlock.ToString());
    }
    return webResults;
}
```

只要您提供愈多有關使用者需求的資訊，Bing Web 搜尋 API 就會產生最好的結果。 特別是，您應該盡可能使用 `mkt` 和 `setLang` 參數 (此處設定為適用英文 (美國)) 來識別使用者的位置和慣用語言。

> [!NOTE]
> 我們的 Bing Web 搜尋查詢一直以來都很簡單，這是為了確保來源程式碼易於了解。  在實際的應用程式中，您應將下列標頭新增至 HTTP 要求以取得改善的結果。 
> * User-Agent  
> * X-MSEdge-ClientID  
> * X-Search-ClientIP  
> * X-Search-Location  
>
> 您可以在 [Bing Web 搜尋 API 參考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers)中進一步了解這些標頭值

## <a name="next-steps"></a>後續步驟
Microsoft 認知服務提供許多其他服務，您可以輕鬆地將這些服務整合到此應用程式。  例如，您可以：

* 新增 [Bing 實體搜尋](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/)以擴大您的 Web 搜尋結果
* 以 [Bing 自訂搜尋](https://azure.microsoft.com/services/cognitive-services/bing-custom-search/)替代 Bing Web 搜尋
* 使用 [Bing 影像搜尋](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)的影像見解功能，來深入了解擷取的影像，並在網站上尋找類似的影像
* 採用 [Bing 拼字檢查](https://azure.microsoft.com/services/cognitive-services/spell-check/)以進一步改善剖析文字的品質
* 整合 [Microsoft Translator](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)，以使用不同語言來檢視擷取的文字
* 從[認知服務入口網站](https://azure.microsoft.com/services/cognitive-services/)中混合使用其他服務；完全沒有限制！
