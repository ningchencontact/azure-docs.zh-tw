---
title: 保護您的內容使用 Media Services 動態加密-Azure |Microsoft Docs
description: 本文提供內容保護與動態加密的概觀。 它也會討論資料流通訊協定和加密類型。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 984c5d6b5e6e2010489533a3889501c5b524a6bd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311317"
---
# <a name="content-protection-with-dynamic-encryption"></a>內容保護與動態加密

您可以使用 Azure 媒體服務來保護媒體從離開電腦到進行儲存、處理和傳遞時的安全。 使用媒體服務，您就能傳遞利用進階加密標準 (AES-128) 或下列三個主要數位版權管理 (DRM) 系統中任一個所動態加密的即時與隨選內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 

下圖說明媒體服務內容保護工作流程： 

![保護內容](./media/content-protection/content-protection.svg)

&#42; *動態加密支援 AES-128 「清除金鑰」、CBCS 和 CENC。如需詳細資訊，請參閱[這裡](#streaming-protocols-and-encryption-types)的支援矩陣。*

本文說明可了解如何使用媒體服務進行內容保護的相關概念與術語。

## <a name="main-components-of-a-content-protection-system"></a>內容保護系統的主要元件

若要成功完成「內容保護」系統/應用程式的設計，您需要徹底了解投入量範圍。 以下清單提供您必須實作的三部分概觀。 

1. Azure 媒體服務程式碼
  
   [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)範例會示範如何實作使用媒體服務 v3 的多重 DRM 系統，而且也使用媒體服務授權/金鑰傳遞服務。 您可以使用多種加密類型 (AES-128、PlayReady、Widevine、FairPlay) 來加密每項資產。 請參閱[串流通訊協定和加密類型](#streaming-protocols-and-encryption-types)，以查看哪些組合可行。
  
   下列範例示範如何：

   1. 建立和設定[內容的索引鍵原則](https://docs.microsoft.com/rest/api/media/contentkeypolicies)。

      * 定義授權傳遞授權，根據 JWT 中的宣告指定授權檢查的邏輯。
      * 指定的內容金鑰，以設定 DRM 加密。
      * 設定[PlayReady](playready-license-template-overview.md)， [Widevine](widevine-license-template-overview.md)，和/或[FairPlay](fairplay-license-overview.md)授權。 範本可讓您設定每個已使用之 DRM 的權力與權限。

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. 建立[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)設定為串流處理加密的資產。 
  
      **串流定位器**有相關聯[串流原則](https://docs.microsoft.com/rest/api/media/streamingpolicies)。 在範例中，我們會設定 StreamingLocator.StreamingPolicyName"Predefined_MultiDrmCencStreaming 」 原則。 此原則會指出我們想要將兩個內容金鑰 （信封和 CENC），來取得產生和設定的定位器。 因此，會套用信封、PlayReady 及 Widevine 加密 (會根據設定的 DRM 授權將金鑰傳遞至播放用戶端)。 如果您也想要使用 CBCS (FairPlay) 為串流加密，請使用 "Predefined_MultiDrmStreaming"。
    
      因為我們想要加密影片，請**內容的索引鍵原則**我們稍早設定也會有相關聯**串流定位器**。 
    
   3. 建立測試權杖。

      **GetTokenAsync** 方法顯示如何建立測試權杖。
   4. 建置串流 URL。

      **GetDASHStreamingUrlAsync** 方法說明如何建置串流 URL。 在此案例中，URL 會串流處理 **DASH** 內容。

2. 使用 AES 或 DRM 用戶端的播放器。 以播放器 SDK (原生或以瀏覽器為基礎) 為基礎的影片播放器應用程式必須符合下列需求：
   * 播放器 SDK 支援所需的 DRM 用戶端
   * 播放器 SDK 支援所需的串流通訊協定：Smooth、DASH 和/或 HLS
   * 播放器 SDK 必須能夠處理在授權取得要求中傳遞 JWT 權杖
  
     您可以使用 [Azure 媒體播放器 API](https://amp.azure.net/libs/amp/latest/docs/) 來建立播放器。 使用 [Azure 媒體播放器的 ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) 來指定要在不同的 DRM 平台上使用哪個 DRM 技術。

     若要測試 AES 或 CENC (Widevine 和/或 PlayReady) 加密的內容，您可以使用 [Azure 媒體播放器](https://ampdemo.azureedge.net/azuremediaplayer.html) \(英文\)。 務必按一下 [進階選項] 並檢查您的加密選項。

     如果您想要測試 FairPlay 加密內容，請使用[此測試播放程式](https://aka.ms/amtest)。 該播放程式支援 Widevine、PlayReady 及 FairPlay DRM，以及 AES-128 清除金鑰加密。 
    
     您必須選擇適當的瀏覽器來測試不同的 DRM：適用於 Widevine 的 Chrome/Opera/Firefox、適用於 PlayReady 的 Microsoft Edge/IE11、macOS 上適用於 FairPlay 的 Safari。

3. 安全性權杖服務 (STS) 會發出 JSON Web 權杖 (JWT) 作為後端資源存取的存取權杖。 您可以使用 AMS 授權傳遞服務作為後端資源。 STS 必須定義以下項目：

   * 簽發者和對象 (或範圍)
   * 宣告取決於內容保護中的商務需求
   * 簽章驗證的對稱或非對稱驗證
   * 金鑰變換支援 (如有必要)

     您可以使用[這個 STS 工具](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt)來測試 STS，此工具可支援全部 3 種類型的驗證金鑰：對稱、非對稱，或可變換金鑰的 Azure AD。 

> [!NOTE]
> 強烈建議專注並完整測試每個部分 (如上所述) 再移到下一個部分。 若要測試您的「內容保護」系統，請使用上述清單中指定的工具。  

## <a name="streaming-protocols-and-encryption-types"></a>串流通訊協定和加密類型

您可以使用媒體服務，來傳遞藉由使用 PlayReady、Widevine 或 FairPlay 並透過 AES 未加密金鑰或 DRM 加密所動態加密的內容。 您目前可以加密 HTTP 即時串流 (HLS)、MPEG DASH 和 Smooth Streaming 格式。 每個通訊協定支援下列加密方法：

### <a name="hls"></a>HLS

HLS 通訊協定支援下列容器格式和加密配置。

|容器格式|加密配置|URL 範例|
|---|---|---|
|全部|AES|`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) ||
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) ||
|CMAF(fmp4) |CENC (PlayReady) ||

HLS/CMAF + FairPlay (包括 HEVC / H.265) 支援下列裝置：

* iOS v11 或更高版本 
* iPhone 8 或更新版本
* Intel 的 MacOS high Sierra 7 Gen CPU

### <a name="mpeg-dash"></a>MPEG-DASH

MPEG DASH 通訊協定支援下列容器格式和加密配置。

|容器格式|加密配置|URL 範例
|---|---|---|
|全部|AES|`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)||

### <a name="smooth-streaming"></a>Smooth Streaming

Smooth Streaming 通訊協定支援下列容器格式和加密配置。

|通訊協定|容器格式|加密配置|
|---|---|---|
|fMP4|AES||
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>瀏覽器

常見的瀏覽器都支援下列的 DRM 用戶端：

|[瀏覽器]|加密|
|---|---|
|Chrome|Widevine|
|Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 未加密金鑰與DRM

客戶通常不知道他們應該使用 AES 加密，還是 DRM 系統。 這兩個系統之間的主要差異是，使用 AES 加密內容金鑰傳輸至用戶端透過 TLS 以便在傳輸過程中，但沒有任何額外的加密 （「 以純文字 」） 來加密金鑰。 如此一來，用來解密內容的金鑰存取用戶端播放程式，並可以純文字中的用戶端網路追蹤中檢視。 AES-128 未加密金鑰加密適用於檢視者為受信任合作對象的使用案例 (例如，將在公司內部散發的公司影片加密，以供員工檢視)。

DRM 系統，例如 PlayReady、 Widevine 和 FairPlay 全部都會提供一層額外的加密金鑰用來解密內容相較於 AES-128 清除金鑰。 內容金鑰加密中的 DRM 執行階段所保護的金鑰來進行其他任何 TLS 所提供的傳輸層級加密。 此外，解密會在安全環境中，惡意使用者更難攻擊的作業系統層級上進行處理。 針對檢視者可能不是受信任的合作對象，而且您需要最高層級安全性的使用案例，建議使用 DRM。

## <a name="dynamic-encryption-and-key-delivery-service"></a>動態加密和金鑰傳遞服務

在媒體服務 v3 串流定位器相關聯的內容金鑰是 (請參閱[本例](protect-with-aes128.md))。 如果使用媒體服務金鑰傳遞服務，您可以讓 Azure 媒體服務為您產生內容金鑰。 如果您使用自己的金鑰傳遞服務，或需要處理高可用性案例，其中需要在兩個資料中心具有相同的內容金鑰，則您應自行產生內容金鑰。

播放程式要求串流時，媒體服務便會使用 AES 清除金鑰或 DRM 加密，使用指定的金鑰動態加密您的內容。 為了將串流解密，播放程式會向媒體服務金鑰傳遞服務或您指定的金鑰傳遞服務要求金鑰。 為了決定使用者是否有權取得金鑰，服務會評估您為金鑰指定的內容金鑰原則。

媒體服務提供一種金鑰傳遞服務，可將 DRM (PlayReady、Widevine 與 FairPlay) 授權和 AES 金鑰傳遞給授權用戶端。 您可以使用 REST API 或媒體服務用戶端程式庫，來設定授權和金鑰的授權和驗證原則。

### <a name="custom-key-and-license-acquisition-url"></a>自訂的索引鍵和授權取得 URL

如果您想要指定不同金鑰和授權傳遞服務 （不是媒體服務），請使用下列範本。 在範本中的兩個可取代欄位有使您可以跨許多的資產，而不是建立每個資產的串流處理原則來共用您串流處理的原則。 

* EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate-自訂的服務金鑰傳遞給使用者播放程式的 URL 範本。 不需要使用 Azure 媒體服務，以便在發出金鑰時。 範本支援本服務會更新在執行階段與要求的特定值的取代權杖。  目前支援的語彙基元值為 {AlternativeMediaId}，這會取代 StreamingLocatorId.AlternativeMediaId 和 {ContentKeyId} 的值，它會取代所要求的金鑰識別項的值。
* StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate-範本的自訂服務授權傳遞到使用者播放程式的 URL。 使用 Azure 媒體服務，可發出授權時，不需要。 範本支援本服務會更新在執行階段與要求的特定值的取代權杖。 目前支援的語彙基元值為 {AlternativeMediaId}，這會取代 StreamingLocatorId.AlternativeMediaId 和 {ContentKeyId} 的值，它會取代所要求的金鑰識別項的值。 
* StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate-與上述相同，只針對 Widevine。 
* StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate-與上述相同，只適用於 FairPlay。  

例如︰

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`的值為所要求的索引鍵和`AlternativeMediaId`可用如果您想要將要求對應至您這端上的實體。 比方說，`AlternativeMediaId`可用來協助您尋找的權限。

使用自訂的 REST 範例金鑰和授權取得 Url，請參閱[串流原則-建立](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="control-content-access"></a>控制內容存取

您可以設定內容金鑰原則來控制可存取內容的人員。 媒體服務支援多種方式來授權提出金鑰要求的使用者。 您必須設定內容金鑰原則。 用戶端 (播放器) 必須先符合原則，系統才會將金鑰傳遞給用戶端。 內容金鑰原則可具有**開啟**或**權杖**限制。 

使用權杖限制的內容金鑰原則時，系統只會將內容金鑰傳送給可在金鑰/授權要求中提供有效 JSON Web 權杖 (JWT) 或簡單 Web 權杖 (SWT) 的用戶端。 這個權杖必須由 Security Token Service (STS) 來核發。 您可以使用 Azure Active Directory 作為 STS，或部署自訂 STS。 STS 必須設定為建立使用指定的索引鍵和問題宣告您在權杖限制組態中指定簽署的權杖。 如果權杖有效，且權杖中的宣告符合針對金鑰/授權所設定的宣告，則媒體服務金鑰傳遞服務會將所要求的金鑰/授權傳回給用戶端。

設定權杖限制的原則時，您必須指定主要驗證金鑰、簽發者和對象參數。 主要驗證金鑰包含簽署權杖用的金鑰。 簽發者為發行權杖的安全性權杖服務。 對象 (有時稱為「範圍」) 描述權杖或權杖獲授權存取之資源的用途。 媒體服務金鑰傳遞服務會驗證權杖中的這些值符合在範本中的值。

客戶通常會使用自訂 STS 使用不同的 DRM 授權參數 （與預約租車授權的訂用帳戶授權） 的不同 ContentKeyPolicyOptions 之間做選擇，或包含表示內容的索引鍵之宣告的權杖中包含自訂宣告權杖授與存取權的金鑰識別項。
 
## <a name="storage-side-encryption"></a>儲存端加密

若要保護待用資產，資產應該透過儲存端加密來進行加密。 下表顯示儲存端加密在媒體服務 v3 中的運作方式：

|加密選項|描述|媒體服務 v3|
|---|---|---|
|媒體服務的儲存體加密| AES-256 加密，由媒體服務管理金鑰|不受支援<sup>(1)</sup>|
|[待用資料的儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure 儲存體提供的伺服器端加密，由 Azure 或客戶管理金鑰|支援|
|[儲存體用戶端加密](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure 儲存體提供的用戶端加密，由客戶管理 Key Vault 中的金鑰|不支援|

<sup>1</sup> 在媒體服務 v3 中，如果您的資產是以媒體服務 v2 建立，則儲存體加密 (AES-256 加密) 只對回溯相容性有所支援。 這表示 v3 可用於現有的儲存體加密資產，但不允許建立新的。

## <a name="troubleshoot"></a>疑難排解

如果您收到`MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY`錯誤，請確定您指定的資料流的適當原則。

如果您收到錯誤訊息結尾`_NOT_SPECIFIED_IN_URL`，請確定您在 URL 中指定的加密格式。 例如： `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`。 請參閱[串流通訊協定和加密類型](#streaming-protocols-and-encryption-types)。


## <a name="next-steps"></a>後續步驟

* [使用 AES 加密保護](protect-with-aes128.md)
* [使用 DRM 保護](protect-with-drm.md)
* [設計多重 DRM 內容保護系統存取控制](design-multi-drm-system-with-access-control.md)
* [常見問題集](frequently-asked-questions.md)

