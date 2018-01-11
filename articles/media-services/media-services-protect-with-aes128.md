---
title: "使用 aes-128 動態加密和金鑰傳遞服務 |Microsoft 文件"
description: "提供您使用 Microsoft Azure Media Services 使用 AES 128 位元的加密金鑰加密的內容。 Media Services 也提供金鑰傳遞服務，將加密金鑰傳遞至獲得授權的使用者。 本主題展示如何利用 AES-128 動態加密，以及使用金鑰傳遞服務。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 013c14c00096c9958a732d1f0eaacc9248f57da9
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>使用 aes-128 動態加密和金鑰傳遞服務
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> 若要取得 Java SDK 的最新版本，並開始使用 Java 進行開發，請參閱[開始 Java 用戶端 SDK 使用 Azure Media Services for](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use)。 <br/>
> 若要下載最新的 PHP SDK，Media services，請尋找 0.5.7 封裝版本的 Microsoft/WindowsAzure 中[Packagist 儲存機制](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)。  

## <a name="overview"></a>概觀
> [!NOTE]
> 如需如何加密使用進階加密標準 (AES) 傳遞到 Safari macOS 上的內容資訊，請參閱[此部落格文章](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)。
> 如需如何保護您的媒體內容的 AES 加密的概觀，請參閱[這段影片](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption)。
> 
> 

 您可以使用 Media Services 提供 HTTP Live Streaming (HLS) 和使用 128 位元加密金鑰以 AES 加密的 Smooth Streaming。 Media Services 也提供金鑰傳遞服務，將加密金鑰傳遞至獲得授權的使用者。 如果您想要加密資產的 Media Services 時，您會將加密金鑰與資產產生關聯，並也設定機碼的授權原則。 當播放程式要求資料流時，Media Services 會使用指定的索引鍵來動態加密使用 AES 加密的內容。 為了將串流解密，播放程式將向金鑰傳遞服務要求金鑰。 若要判斷是否授權使用者取得的金鑰，服務會評估您指定的索引鍵的授權原則。

媒體服務支援多種方式來驗證提出金鑰要求的使用者。 內容金鑰授權原則可以有一或多個授權限制，開啟或語彙基元的限制。 權杖限制原則必須伴隨著安全性權杖服務 (STS) 所簽發的權杖。 Media Services 支援語彙基元中的[簡單 web 權杖](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)(SWT) 和[JSON Web 權杖](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)(JWT) 格式。 如需詳細資訊，請參閱[設定內容金鑰授權原則](media-services-protect-with-aes128.md#configure_key_auth_policy)。

若要利用動態加密，您需有一個資源，其中包含一組多位元速率 MP4 檔案或多位元速率 Smooth Streaming 來源檔案。 您也需要設定資產的傳遞原則 (本文稍後會加以描述)。 然後，根據串流 URL 中指定的格式，點播串流伺服器可確保您所選取的通訊協定，傳遞資料流。 如此一來，您要儲存並只支付一種儲存格式中的檔案。 Media Services 建置，並提供適當的回應，根據用戶端的要求。

這篇文章是用於開發人員在應用程式，以便將受保護的媒體上運作。 發行項會示範如何使用授權原則設定金鑰傳遞服務，使得只有授權的用戶端可以接收加密金鑰。 它也會展示如何使用動態加密。


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Aes-128 動態加密和金鑰傳遞服務工作流程

使用 Media Services 金鑰傳遞服務，以及使用動態加密，加密透過 AES 進行資產時，請執行下列一般步驟：

1. [建立資產，並將檔案上傳到資產](media-services-protect-with-aes128.md#create_asset)。

2. [包含彈性位元速率 MP4 集至檔案的資產編碼](media-services-protect-with-aes128.md#encode_asset)。

3. [建立內容金鑰，並且讓它與編碼資產](media-services-protect-with-aes128.md#create_contentkey)。 在 Media Services 內容金鑰包含資產的加密金鑰。

4. [設定內容金鑰授權原則](media-services-protect-with-aes128.md#configure_key_auth_policy)。 您必須設定內容金鑰授權原則。 用戶端必須符合原則，才能讓內容金鑰傳遞至用戶端。

5. [設定資產的傳遞原則](media-services-protect-with-aes128.md#configure_asset_delivery_policy)。 傳遞原則設定包含金鑰取得 URL 和初始化向量 (IV)。 （AES 128 會需要相同的 IV 的加密和解密）。組態也會包含傳遞通訊協定 （例如，MPEG DASH、 HLS、 Smooth Streaming 或所有） 和動態加密 （例如信封或非動態加密） 的類型。

    您可以將不同的原則套用至相同資產上的每個通訊協定。 例如，您可以將 PlayReady 加密套用至 Smooth/DASH 和 HLS 以 AES 信封。 傳遞原則中未定義任何通訊協定會封鎖資料流。 （範例是如果您將單一原則，指定只 HLS 作為通訊協定）。例外情況是如果您有未完全定義的資產傳遞原則。 接著，允許所有通訊協定，不受阻礙。

6. [建立 OnDemand 定位器](media-services-protect-with-aes128.md#create_locator)取得串流 URL。

本文也會示範[用戶端應用程式如何向金鑰傳遞服務要求金鑰](media-services-protect-with-aes128.md#client_request)。

您可以找到完整[.NET 範例](media-services-protect-with-aes128.md#example)文章結尾處。

下圖示範先前所述的工作流程。 在這裡，權杖用於驗證。

![利用 AES 128 保護](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

這篇文章的其餘部分提供說明、 程式碼範例和示範如何完成先前所述的工作的主題連結。

## <a name="current-limitations"></a>目前的限制
如果您加入或更新資產的傳遞原則，您必須刪除任何現有的定位器，並建立新的定位器。

## <a id="create_asset"></a>建立資產並將檔案上傳到資產
管理、 編碼和串流您的視訊，您必須先將內容上傳到 Media Services。 上傳之後，您的內容會安全地儲存在雲端中為進一步處理和串流。 

如需詳細資訊，請參閱[將檔案上傳到 Media Services 帳戶](media-services-dotnet-upload-files.md)。

## <a id="encode_asset"></a>包含彈性位元速率 MP4 集至檔案的資產編碼
使用動態加密，您可以建立包含一組多位元速率 MP4 檔案或多位元速率 Smooth Streaming 來源檔案的資產。 然後，根據在資訊清單或片段要求中指定的格式，點播串流伺服器可確保在您選取的通訊協定接收串流。 然後，您只需要儲存及支付一種儲存格式中的檔案。 Media Services 建置，並提供適當的回應，根據用戶端的要求。 如需詳細資訊，請參閱[動態封裝概觀](media-services-dynamic-packaging-overview.md)。

>[!NOTE]
>建立 Media Services 帳戶時，串流端點的預設值會加入至您的帳戶處於 「 已停止 」 狀態。 若要開始將內容串流，並利用動態封裝和動態加密，您要將內容串流的串流端點必須處於 「 執行中 」 狀態。 
>
>此外，若要使用動態封裝和動態加密，您的資產必須包含一組彈性位元速率 mp4 或彈性位元速率 Smooth Streaming 檔案。

如需有關如何編碼的指示，請參閱[使用標準的媒體編碼程式編碼資產](media-services-dotnet-encode-with-media-encoder-standard.md)。

## <a id="create_contentkey"></a>建立內容金鑰並將它與編碼的資產產生關聯
在媒體服務中，內容金鑰包含您要加密資產時使用的金鑰。

如需詳細資訊，請參閱[建立內容金鑰](media-services-dotnet-create-contentkey.md)。

## <a id="configure_key_auth_policy"></a>設定內容金鑰授權原則
媒體服務支援多種方式來驗證提出金鑰要求的使用者。 您必須設定內容金鑰授權原則。 索引鍵可以傳遞至用戶端之前，用戶端 （播放器） 必須符合原則。 內容金鑰授權原則可以有一或多個授權限制，請開啟、 權杖限制或 IP 限制。

如需詳細資訊，請參閱[設定內容金鑰授權原則](media-services-dotnet-configure-content-key-auth-policy.md)。

## <a id="configure_asset_delivery_policy"></a>設定資產遞送原則
設定資產的傳遞原則。 資產傳遞原則設定包括一些事項如下：

* 金鑰取得 URL。 
* 初始化向量 (IV) 要用於信封加密。 AES 128 加密和解密需要相同的 IV。 
* 資產傳遞通訊協定 （例如，MPEG DASH、 HLS、 Smooth Streaming，或全部）。
* 動態加密的類型 (例如，AES 信封) 或沒有動態加密。 

如需詳細資訊，請參閱[設定資產遞送原則](media-services-dotnet-configure-asset-delivery-policy.md)。

## <a id="create_locator"></a>建立串流定位器以取得串流 URL OnDemand
您需要使用者提供的 Smooth Streaming、 DASH 或 HLS 的串流 URL。

> [!NOTE]
> 如果您加入或更新資產的傳遞原則，您必須刪除任何現有的定位器，並建立新的定位器。
> 
> 

如需有關如何發佈資產，並建置串流 URL 的指示，請參閱 [建置串流 URL](media-services-deliver-streaming-content.md)。

## <a name="get-a-test-token"></a>取得測試權杖
根據用於金鑰授權原則的權杖限制取得測試權杖。

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

您可以使用[Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)來測試您的資料流。

## <a id="client_request"></a>您的用戶端如何從金鑰傳遞服務要求金鑰？
在上一個步驟中，您可以建構指向資訊清單檔案的 URL。 您的用戶端必須從要向金鑰傳遞服務提出要求的串流資訊清單檔案中擷取必要資訊。

### <a name="manifest-files"></a>資訊清單檔案
用戶端必須擷取 URL (其中也包含內容金鑰識別碼 [kid]) 從資訊清單檔案的值。 用戶端會嘗試從金鑰傳遞服務取得加密金鑰。 用戶端也必須擷取 IV 值，並使用它來解密資料流。 下列程式碼片段說明<Protection>Smooth Streaming 資訊清單的項目：

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

在 HLS 的案例中，根資訊清單會分成區段檔案。 

例如，根資訊清單是： http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl)。 它包含區段檔案名稱清單。

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

如果您在文字編輯器 (例如，http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), it contains # 開啟其中一個區段檔案EXT X 鍵，表示檔案已加密。

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>如果您計劃播放中 Safari 的 AES 加密的 HLS，請參閱[這篇部落格](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)。

### <a name="request-the-key-from-the-key-delivery-service"></a>從金鑰傳遞服務要求金鑰

下列程式碼會示範如何使用金鑰傳遞 Uri （也就擷取自資訊清單），將要求傳送至 Media Services 金鑰傳遞服務，而語彙基元。 （此文件並未說明如何從 STS 取得 Swt）。

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }

## <a name="protect-your-content-with-aes-128-by-using-net"></a>使用.NET 保護您的內容與 AES 128

### <a name="create-and-configure-a-visual-studio-project"></a>建立和設定 Visual Studio 專案

1. 設定開發環境，並填入連接資訊，app.config 檔案中所述[與.NET 的 Media Services 開發](media-services-dotnet-how-to-use.md)。

2. App.config 檔中所定義，請將下列項目加入至 appSettings 中：

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>範例

以本章節中所顯示的程式碼覆寫 Program.cs 檔案中的程式碼。
 
>[!NOTE]
>對於不同的媒體服務原則 (例如 Locator 原則或 ContentKeyAuthorizationPolicy) 有 1,000,000 個原則的限制。 如果您一律使用相同的天/存取權限，請使用相同的原則識別碼。 例如，維持備妥要很長的時間 （非上載原則） 給定位器的原則。 如需詳細資訊，請參閱 「 限制存取原則 」 一節[管理資產及使用媒體服務.NET SDK 的相關的實體](media-services-dotnet-manage-entities.md#limit-access-policies)。

請務必更新變數，以指向您的輸入檔案所在的資料夾。

    [!code-csharp[Main](../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

