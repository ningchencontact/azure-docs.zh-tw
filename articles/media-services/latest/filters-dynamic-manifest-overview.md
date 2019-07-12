---
title: 篩選器和 Azure 媒體服務動態資訊清單 | Microsoft Docs
description: 本主題說明如何建立篩選器，讓您的用戶端可以使用篩選器來串流特定的資料流區段。 媒體服務會建立動態資訊清單來完成此選擇性資料流。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: ee0200f7c007b437a27b8e9d0f36becc13b8f611
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835825"
---
# <a name="pre-filtering-manifests-by-using-dynamic-packager"></a>使用動態封裝程式的預先篩選資訊清單

當您要提供自適性串流處理到裝置的內容時，您通常需要將資訊清單的多個版本發行至目標裝置特定功能或可用的網路頻寬。 [動態封裝程式](dynamic-packaging-overview.md)可讓您指定篩選，以篩選出特定的轉碼器，其解析度、 位元速率，以及音訊追蹤組合上即時不再需要建立多個複本。 您只需要發行新的 URL，以一組特定的篩選條件設定為您的目標裝置 （iOS、 Android、 SmartTV 或瀏覽器）] 和 [網路功能 （高頻寬、 行動或低頻寬的案例）。 在此情況下，用戶端可以操作您的內容，透過查詢字串的資料流 (藉由指定可用[資產篩選器或帳戶篩選器](filters-concept.md)) 和資料流的資料流的特定區段中使用篩選器。

有些傳遞案例需要您要確定客戶是無法存取特定的追蹤。 比方說，您可能不想將發佈包含特定的訂閱者層 HD 追蹤的資訊清單。 或者，您可能想要移除特定調適性位元速率 (ABR) 追蹤，以減少到特定的裝置，就不會受益於其他追蹤遞送的成本。 在此情況下您可以建立一份預先建立的篩選條件的關聯您[串流定位器](streaming-locators-concept.md)上建立。 在此情況下，用戶端無法操控內容資料流的方式，它由定義**串流定位器**。

您可以透過指定篩選來結合[串流定位器的篩選](filters-concept.md#associating-filters-with-streaming-locator)+ 用戶端所指定 URL 中的其他裝置特定篩選條件。 這可用來限制其他的追蹤，例如中繼資料或事件資料流、 音訊語言、 或描述性的音訊資料軌。 

這項功能在您的資料流中，指定不同的篩選器提供一個功能強大**動態資訊清單**操作解決方案，以多個使用案例適用於您的目標裝置為目標。 本主題說明**動態資訊清單**相關的概念，並提供您可能會想使用此功能的案例範例。

> [!NOTE]
> 動態資訊清單不會變更資產和該資產的預設資訊清單。 
> 

##  <a name="overview-of-manifests"></a>資訊清單的概觀

Azure 媒體服務支援 HLS、 MPEG DASH 和 Smooth Streaming 通訊協定。 做為一部分[動態封裝](dynamic-packaging-overview.md)，串流用戶端資訊清單 （HLS 主要播放清單、 DASH 媒體顯示說明 [MPD]，和 Smooth Streaming） 動態產生的 URL 中的格式選取器為基礎。 請參閱中的傳遞通訊協定[常見的依需求工作流程](dynamic-packaging-overview.md#delivery-protocols)。 

### <a name="get-and-examine-manifest-files"></a>取得並檢查資訊清單檔案

您指定的篩選器追蹤屬性條件依據動態建立資訊清單中應該包含哪些追蹤您的串流 （即時或隨選 [VOD] 視訊） 的清單。 您必須先載入 Smooth Streaming 資訊清單，才能取得並檢查資料軌的屬性。

[上傳、 編碼和串流處理使用.NET 的檔案](stream-files-tutorial-with-api.md#get-streaming-urls)教學課程會示範如何建置使用.NET 的串流 Url。 如果您執行應用程式，其中一個 URL 指向 Smooth Streaming 資訊清單：`https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`。<br/> 複製該 URL 並將它貼上到瀏覽器的網址列中。 瀏覽器會下載該檔案。 您可以在您選擇的文字編輯器中開啟它。

如需 REST 範例，請參閱[上傳、 編碼和串流處理使用 REST 檔案](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)。

### <a name="monitor-the-bitrate-of-a-video-stream"></a>監視視訊資料流的位元速率

您可以使用 [Azure 媒體播放器示範頁面](http://aka.ms/azuremediaplayer) \(英文\) 來監視視訊資料流的位元速率。 示範頁面會顯示診斷資訊**診斷** 索引標籤：

![Azure 媒體播放器診斷][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>範例：在查詢字串中與篩選的 Url

您可以將篩選套用至 ABR 串流通訊協定：HLS、MPEG-DASH 和 Smooth Streaming。 下表顯示包含篩選器之 URL 的一些範例：

|Protocol|範例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>轉譯篩選

您可以選擇將資產編碼成多個編碼設定檔 （H.264 Baseline、 H.264 高、 AACL、 AACH、 Dolby Digital Plus） 和多個品質位元速率。 不過，並非所有的用戶端裝置都支援您所有的資產和位元速率。 例如，較舊的 Android 裝置支援僅 H.264 Baseline + AACL。 較高的位元速率傳送到無法獲益的裝置會浪費頻寬及裝置計算。 這類裝置必須將解碼所有指定的資訊，才能顯示將其相應減少。

與動態資訊清單中，您可以建立裝置設定檔 (例如行動、 主控台或 HD/SD)，並包含與您想要的每個設定檔一部分的品質。 會呼叫轉譯篩選。 下圖顯示的範例。

![轉譯篩選範例][renditions2]

下列範例使用編碼器將夾層資產編碼成七個 ISO MP4 視訊轉譯 (從 180p 到 1080p)。 可以是編碼的資產[動態封裝](dynamic-packaging-overview.md)至任何下列串流通訊協定：HLS、MPEG DASH 和 Smooth。 

下圖的頂端顯示 HLS 資產資訊清單不含篩選器。 （它包含全部七個轉譯）。在左下方，圖表會顯示名為"ott"篩選器已套用至 HLS 資訊清單。 "Ott"篩選器會指定所有的位元速率低於 1 Mbps，移除，因此下兩個品質層級在回應中去除。 在右下角中，圖表會顯示名為"mobile"篩選器已套用到 HLS 資訊清單。 "Mobile"篩選器指定轉譯，解析度大於 720p，所以兩個 1080 p 轉譯已去除的移除。

![轉譯篩選][renditions1]

## <a name="removing-language-tracks"></a>移除語言資料軌
您的資產可能包含多個音訊語言，例如英文、西班牙文、法文等。通常，播放程式 SDK 管理員會預設音訊資料軌選取範圍，以及每個使用者可選擇的可用音訊資料軌。

開發這類的播放程式 Sdk 一項挑戰，因為跨裝置特有的播放器架構有不同的實作。 此外，在某些平台，播放程式 Api 限制，而且不包含音訊選擇功能，使用者無法選取或變更預設音訊資料軌的地方。有了資產篩選器，您可以藉由建立只包含所需音訊語言的篩選器來控制行為。

![語言資料軌篩選][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>修剪資產開頭

在大部分的即時資料流事件中，操作人員必須在實際的事件前先進行測試。 例如，他們可以在事件開始前包含如下 slate 訊息：「 程式將立刻開始。 」 

如果程式正在進行封存，則測試和靜態圖像資料也會一併封存並包含在簡報中。 但是此資訊不會對用戶端顯示。 透過動態資訊清單，您可以建立開始時間篩選器，並從資訊清單中移除不必要的資料。

![開始修剪][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>從即時封存中建立子剪輯 (檢視)

許多即時事件的執行時間長，且即時封存可能包括多個事件。 即時事件結束後，廣播者可能想要將即時封存分解成邏輯程式的啟動和停止序列。 

您可以個別發佈這些虛擬程式，但不後續處理即時封存，亦不建立個別的資產 （這不會在 Cdn 中取得現有的快取片段的優點）。 例如足球或籃球比賽的球季、棒球賽局，或是任何體育節目的個別事件，都是此類虛擬程式的範例。

與動態資訊清單中，您可以使用開始/結束時間來建立篩選器和即時封存頂端建立虛擬檢視。 

![子剪輯篩選][subclip_filter]

以下是已篩選的資產：

![滑動][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>調整簡報視窗 (DVR)

目前，Azure 媒體服務提供持續時間，可以設定介於 5 分鐘到 25 小時的循環封存。 資訊清單篩選可以在封存頂端建立循環 DVR 視窗封存，而不會刪除媒體。 在許多情況下，廣播者想要提供受限制的 DVR 時段以隨著即時邊緣移動，並同時保留更大的封存視窗。 廣播者可能會想要使用超出 DVR 視窗的資料來反白顯示剪輯，或者想要為不同的裝置提供不同的 DVR 視窗。 例如，大多數的行動裝置不處理大型 DVR 時段 （您可以為桌面用戶端有 2 分鐘 DVR 視窗的 行動裝置和 1 小時）。

![DVR 視窗][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>調整 LiveBackoff (即時位置)

資訊清單篩選可用來移除即時程式其即時邊緣幾秒鐘的時間。 篩選可讓廣播者先觀賞預覽發佈點的簡報，並建立廣告插入點之前在檢視者收到資料流 （受 30 秒）。 然後廣播者就可以這些廣告推送至其用戶端架構的時間才能接收和處理資訊，才有機會播放廣告。

除了廣告支援，即時退避法設定可用來調整檢視器的位置，以便當用戶端並點閱即時邊緣，則它們仍然可以從伺服器取得片段。 如此一來，用戶端不會收到 HTTP 404 或 412 錯誤。

![篩選的即時退避法][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>將多個規則結合成單一篩選器

您可以將多個篩選規則結合成單一篩選器。 例如，您可以定義一個「範圍規則」，將靜態圖像從即時封存中移除，並同時篩選出可用的位元速率。 當您套用多個篩選規則時，最終結果是所有規則的交集。

![多個篩選規則][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>結合多個篩選條件 (篩選器組合)

您也可以將多個篩選條件結合成單一 URL。 下列案例示範您可能會想結合篩選條件的理由：

1. 您要篩選例如 Android 或 iPad 等行動裝置的視訊品質 （以限制視訊品質）。 若要移除不必要的品質，您將建立的帳戶篩選器適用於裝置設定檔。 您可以使用帳戶篩選器，為所有資產相同的媒體服務帳戶，而不需要任何進一步的關聯。
1. 您也想要修剪資產的開始和結束時間。 若要達到此目的，您會建立資產篩選器，並設定開始/結束時間。 
1. 您想要結合這兩個篩選條件。 不組合，您必須將品質篩選加入修剪篩選器，會使篩選使用情況更加困難。


若要結合篩選條件，您需要將篩選條件名稱設定為資訊清單/播放清單以分號分隔的格式的 URL。 假設您擁有名為篩選器*MyMobileDevice*篩選品質，以及您有另一個名為*MyStartTime*設定特定的開始時間。 您可以結合最多三個篩選。 

如需詳細資訊，請參閱 [此部落格文章](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)。

## <a name="considerations-and-limitations"></a>考量與限制

- 值**forceEndTimestamp**， **presentationWindowDuration**，並**liveBackoffDuration**不應設為 VOD 篩選條件。 它們只會用於即時篩選案例。 
-  動態資訊清單會在 GOP 界限 （主要畫面格） 中運作，因此修剪 GOP 精確度。
-  您可以使用帳戶和資產篩選器相同的篩選器名稱。 資產篩選器有較高的優先順序，而且會覆寫的帳戶篩選器。
- 如果您更新篩選器時，可能需要 2 分鐘的時間來重新整理規則的串流端點。 如果您使用篩選來提供內容 （和快取在 proxy 中的內容而 CDN 會快取），則更新這些篩選器可以導致播放程式失敗。 我們建議您更新篩選器之後清除快取。 如果此選項不可行，請考慮使用不同的篩選條件。
- 客戶必須手動下載資訊清單和剖析的確切的開始時間戳記和時間間隔。
    
    - 若要判斷在資產中追蹤的屬性[取得，並檢查資訊清單檔](#get-and-examine-manifest-files)。
    - 若要設定資產篩選器的時間戳記屬性的公式為： <br/>startTimestamp =&lt;資訊清單的開始時間&gt; +  &lt;預期篩選開始時間 （秒）&gt; * 時幅

## <a name="next-steps"></a>後續步驟

下列文章會示範如何以程式設計方式建立篩選：  

- [使用 REST API 建立篩選器](filters-dynamic-manifest-rest-howto.md)
- [使用 .NET 建立篩選器](filters-dynamic-manifest-dotnet-howto.md)
- [使用 CLI 建立篩選器](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
