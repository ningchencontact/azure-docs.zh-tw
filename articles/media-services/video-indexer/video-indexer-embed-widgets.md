---
title: 將影片索引子小工具內嵌到應用程式中
titlesuffix: Azure Media Services
description: 了解如何將影片索引子小工具內嵌到應用程式中。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 06/05/2019
ms.author: juliako
ms.openlocfilehash: 6b5422e2eb67eb309c086c023df9f733940e5e44
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735081"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>將影片索引子小工具內嵌到應用程式中

本文說明如何將影片索引子小工具內嵌到應用程式中。 影片索引子支援將兩種類型的小工具內嵌到應用程式中：**認知深入解析**和**播放器**。 

從第 2 版開始，小工具基底 URL 包括帳戶的區域。 例如，美國西部區域中的帳戶會產生：`https://wus2.videoindexer.ai/embed/insights/...`。

## <a name="widget-types"></a>小工具類型

### <a name="cognitive-insights-widget"></a>認知深入解析小工具

**認知深入解析**小工具包含從您的影片索引編製程序中擷取的所有視覺化深入解析。 深入解析小工具支援下列選擇性 URL 參數：

|Name|定義|描述|
|---|---|---|
|widgets|以逗號分隔的字串|可讓您控制您想要呈現的深入解析。 <br/>範例：`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` 只會呈現人員和品牌 UI 深入解析<br/>可用的選項：人員、關鍵字、註解、品牌、情緒、文字記錄、搜尋。<br/>使用 version=2 時無法透過 URL 支援<br/><br/>**附註：** 第 2 版中不支援小工具的 URL 參數。 |

### <a name="player-widget"></a>播放器小工具

**播放器**小工具可讓您使用調適性位元速率來串流影片。 播放器小工具支援下列選擇性 URL 參數：

|Name|定義|描述|
|---|---|---|
|t|開始後的秒數|可讓播放器從指定的時間點開始播放。<br/>範例：t=60|
|captions|語言代碼|在可於標題功能表中載入小工具期間，擷取指定語言的標題。<br/>範例：captions=en-US|
|showCaptions|布林值|可讓播放器載入已啟用的標題。<br/>範例：showCaptions=true|
|type||啟動音訊播放器面板 (移除影片部分)。<br/>範例：type=audio|
|autoplay|布林值|指出播放器是否應在影片載入後開始播放 (預設值為 true)。<br/>範例：autoplay=false|
|語言|語言代碼|控制播放器語言 (預設值為 en-US)<br/>範例：language=de-DE|

## <a name="embedding-public-content"></a>內嵌公用內容

1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。
2. 按一下您要使用的影片。
3. 按一下影片下方顯示的 [內嵌] 按鈕。

    ![小工具](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    按一下按鈕後，畫面上會顯示內嵌強制回應，您可以在該處選擇您要在應用程式中內嵌的小工具。
    選取小工具 (**播放器**或**認知深入解析**) 後，會產生可讓您在應用程式中貼上的內嵌程式碼。
 
4. 選擇您想要的小工具類型 (**認知深入解析**或**播放器**)。
5. 複製內嵌程式碼，並新增至您的應用程式。 

    ![小工具](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> 如果您有問題，以共用您影片的 Url，請嘗試將 'location' 參數新增至連結。 參數應該設定為[影片索引器所在的 Azure 區域](regions.md)。 例如： `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`。

## <a name="embedding-private-content"></a>內嵌私人內容

從內嵌快顯視窗中只能取得**公用**影片的內嵌程式碼 (如上一節所說明)。 

如果您想要內嵌**私人**影片，您必須在 **iframe** 的 **src** 屬性中傳入存取權杖：

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
請使用[**取得深入解析小工具**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API 取得認知深入解析小工具內容，或使用[**取得影片存取權杖**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?)，並將其新增為 URL 的查詢參數，如前所述。 請將此 URL 指定為 **iframe** 的 **src** 值。

如果您想要在內嵌的小工具中提供編輯深入解析的功能 (如同我們在 Web 應用程式中使用的)，您必須傳入具有編輯權限的存取權杖。 請使用設定了 **&allowEdit=true** 的[**取得深入解析小工具**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)或[**取得影片存取權杖**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?)。 

## <a name="widgets-interaction"></a>小工具互動

**認知深入解析**小工具可與您應用程式上的影片互動。 本節說明如何進行此互動。

![小工具](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>跨來源通訊

若要讓影片索引子小工具能夠與其他元件通訊，影片索引子服務必須執行下列作業：

- 使用跨來源通訊 HTML5 方法 **postMessage**，並且 
- 驗證 VideoIndexer.ai 來源之間的訊息。 

如果您選擇自行實作播放器程式碼，並執行與**認知深入解析**小工具的整合，則必須自行驗證來自 VideoIndexer.ai 的訊息來源。

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>在應用程式/部落格中同時內嵌這兩種類型的小工具 (建議使用) 

本節說明如何達成兩個影片索引子小工具之間的互動，而讓使用者在點按您應用程式上的深入解析控制項時，即可將播放器跳到相關的時間點。

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

1. 複製**播放器**小工具的內嵌程式碼。
2. 複製**認知深入解析**的內嵌程式碼。
3. 新增[**中繼程序檔案**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)以處理兩個小工具之間的通訊：

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

現在，當使用者點按您應用程式上的深入解析控制項時，播放器即會跳到相關的時間點。

如需詳細資訊，請參閱[此示範](https://codepen.io/videoindexer/pen/NzJeOb)。

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>內嵌認知深入解析小工具，並使用 Azure 媒體播放器來播放內容

本節說明如何使用 [AMP 外掛程式](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)達成**認知深入解析**小工具與 Azure 媒體播放器執行個體之間的互動。
 
1. 新增 AMP 播放器的影片索引子外掛程式。<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. 使用影片索引子外掛程式將 Azure 媒體播放器具現化。

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. 複製**認知深入解析**的內嵌程式碼。

現在您應已能夠與 Azure 媒體播放器進行通訊。

如需詳細資訊，請參閱[此示範](https://codepen.io/videoindexer/pen/rYONrO)。

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>內嵌影片索引子認知深入解析小工具，並使用您自己的播放器 (可以是任何播放器)

如果您使用自己的播放器，您必須自行操作播放器以進行通訊。 

1. 插入影片播放器。

    例如，標準 HTML5 播放器

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. 內嵌認知深入解析小工具。
3. 接聽「訊息」事件，以實作播放器的通訊。 例如:

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

如需詳細資訊，請參閱[此示範](https://codepen.io/videoindexer/pen/YEyPLd)。

## <a name="adding-subtitles"></a>新增字幕

如果您用自己的 AMP 播放器內嵌影片索引子深入解析，您可以使用 **GetVttUrl** 方法來取得隱藏式輔助字幕 (字幕)。 您也可以從影片索引子 AMP 外掛程式 **getSubtitlesUrl** 呼叫 javascript 方法 (如先前所說明)。 

## <a name="customizing-embeddable-widgets"></a>自訂可內嵌的小工具

### <a name="cognitive-insights-widget"></a>認知深入解析小工具

您可以選擇您想要的深入解析類型，只要將其指定為下列 URL 參數的值，並新增至您取得的內嵌程式碼 (從 API 或 Web 應用程式) 即可：`&widgets=<list of wanted widgets>`。

可能的值為：人員、關鍵字、情緒、文字記錄、搜尋。

例如，如果您想要內嵌僅包含人員和搜尋見解的小工具，iframe 內嵌 URL 將顯示如下︰

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

iframe 視窗的標題也可透過將 `&title=<YourTitle>` 提供給 iframe URL 來自訂。 (它將會自訂 html \<title> 值)。
    
例如，若您想要為您的 iframe 視窗指定標題 "MyInsights"，URL w看起來會像這樣：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

請注意，只有您需要在新視窗中開啟深入解析時，才適用此選項。

### <a name="player-widget"></a>播放器小工具

在內嵌影片索引子播放器時，您可以藉由指定 iframe 的大小來選擇播放器的大小。

例如:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

根據預設，影片索引子播放器會根據從使用來源語言 (在影片上傳時選取的) 的影片中擷取的影片文字記錄，自動產生隱藏式輔助字幕。

如果您想要內嵌不同的語言，您可以新增 `&captions=< Language | ”all” | “false” >` 以內嵌播放器 URL；如果想要有所有可用語言的標題，則可以加上 "all" 值。
如果您想要依預設顯示標題，您可以傳入 `&showCaptions=true`。

內嵌 URL 將顯示如下︰ 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

如果您想要停用標題，您可以為標題參數傳入 "false" 值。

自動播放 – 根據預設，播放器會自動開始播放影片。 您可以在上述的內嵌 URL 中傳入 &autoplay=false，以選擇不自動播放。

## <a name="next-steps"></a>後續步驟

如需關於如何檢視和編輯影片索引子深入解析的詳細資訊，請參閱[這篇](video-indexer-view-edit.md)文章。

此外，請參閱[影片索引器 CodePen](https://codepen.io/videoindexer/pen/eGxebZ)。
