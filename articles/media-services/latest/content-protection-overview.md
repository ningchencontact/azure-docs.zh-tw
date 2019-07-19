---
title: 使用媒體服務動態加密來保護您的內容-Azure |Microsoft Docs
description: 本文提供使用動態加密的內容保護總覽。 它也會討論串流通訊協定和加密類型。
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
ms.date: 07/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 5d31e4a523fdedf9907e33c70638f07a08461ed1
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310309"
---
# <a name="content-protection-with-dynamic-encryption"></a>使用動態加密的內容保護

您可以使用 Azure 媒體服務來保護媒體從離開電腦到進行儲存、處理和傳遞時的安全。 使用媒體服務，您就能傳遞利用進階加密標準 (AES-128) 或下列三個主要數位版權管理 (DRM) 系統中任一個所動態加密的即時與隨選內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 

在媒體服務 v3 中, 內容金鑰會與串流定位器相關聯 (請參閱[此範例](protect-with-aes128.md))。 如果使用媒體服務金鑰傳遞服務, 您可以讓 Azure 媒體服務為您產生內容金鑰。 如果您使用自己的金鑰傳遞服務, 或如果您需要處理高可用性案例, 且在兩個資料中心內必須有相同的內容金鑰, 您應該自行產生內容金鑰。

播放程式要求串流時，媒體服務便會使用 AES 清除金鑰或 DRM 加密，使用指定的金鑰動態加密您的內容。 為了將串流解密，播放程式會向媒體服務金鑰傳遞服務或您指定的金鑰傳遞服務要求金鑰。 為了決定使用者是否有權取得金鑰，服務會評估您為金鑰指定的內容金鑰原則。

您可以使用 REST API 或媒體服務用戶端程式庫，來設定授權和金鑰的授權和驗證原則。

下圖說明媒體服務內容保護工作流程： 

![保護內容](./media/content-protection/content-protection.svg)

&#42; *動態加密支援 AES-128 「清除金鑰」、CBCS 和 CENC。如需詳細資訊，請參閱[這裡](#streaming-protocols-and-encryption-types)的支援矩陣。*

本文說明可了解如何使用媒體服務進行內容保護的相關概念與術語。

## <a name="main-components-of-a-content-protection-system"></a>內容保護系統的主要元件

若要成功完成「內容保護」系統/應用程式的設計，您需要徹底了解投入量範圍。 以下清單提供您必須實作的三部分概觀。 

1. Azure 媒體服務程式碼
  
   [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)範例示範如何使用 .net 以媒體服務 v3 來執行多重 DRM 系統。 它也會說明如何使用媒體服務授權/金鑰傳遞服務。 您可以使用多種加密類型 (AES-128、PlayReady、Widevine、FairPlay) 來加密每項資產。 請參閱[串流通訊協定和加密類型](#streaming-protocols-and-encryption-types)，以查看哪些組合可行。
  
   下列範例示範如何：

   1. 建立和設定[內容金鑰原則](content-key-policy-concept.md)。 您會建立**內容金鑰原則**, 以設定內容金鑰 (提供資產的安全存取) 如何傳遞給終端用戶端。    

      * 定義授權傳遞授權，根據 JWT 中的宣告指定授權檢查的邏輯。
      * 設定[PlayReady](playready-license-template-overview.md)、 [Widevine](widevine-license-template-overview.md)和/或[FairPlay](fairplay-license-overview.md)授權。 範本可讓您設定每個已使用之 DRM 的權力與權限。

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. 建立[串流定位器](streaming-locators-concept.md), 其設定為串流加密的資產。 
  
      **串流定位器**必須與[串流原則](streaming-policy-concept.md)相關聯。 在範例中, 我們會將 StreamingLocator StreamingPolicyName 設定為 "Predefined_MultiDrmCencStreaming" 原則。 PlayReady 和 Widevine 加密會套用, 金鑰會根據所設定的 DRM 授權傳遞至播放用戶端。 如果您也想要使用 CBCS (FairPlay) 為串流加密，請使用 "Predefined_MultiDrmStreaming"。
      
      串流定位器也會與已定義的**內容金鑰原則**相關聯。
    
   3. 建立測試權杖。

      **GetTokenAsync** 方法顯示如何建立測試權杖。
   4. 建置串流 URL。

      **GetDASHStreamingUrlAsync** 方法說明如何建置串流 URL。 在此案例中，URL 會串流處理 **DASH** 內容。

2. 使用 AES 或 DRM 用戶端的播放器。 以播放器 SDK (原生或以瀏覽器為基礎) 為基礎的影片播放器應用程式必須符合下列需求：
   * 播放器 SDK 支援所需的 DRM 用戶端
   * 播放器 SDK 支援所需的串流通訊協定：Smooth、DASH 和/或 HLS
   * 播放器 SDK 必須能夠處理在授權取得要求中傳遞 JWT 權杖
  
     您可以使用 [Azure 媒體播放器 API](https://amp.azure.net/libs/amp/latest/docs/) 來建立播放器。 使用 [Azure 媒體播放器的 ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) 來指定要在不同的 DRM 平台上使用哪個 DRM 技術。

     若要測試 AES 或 CENC (Widevine 和/或 PlayReady) 加密的內容，您可以使用 [Azure 媒體播放器](https://aka.ms/azuremediaplayer) \(英文\)。 務必按一下 [進階選項] 並檢查您的加密選項。

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
|全部|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

下列裝置支援 HLS/CMAF + FairPlay (包括 HEVC/H. 265):

* iOS v11 或更高版本 
* iPhone 8 或更新版本
* Intel 7 Gen CPU 的 MacOS 高塞拉里昂

### <a name="mpeg-dash"></a>MPEG-DASH

MPEG-破折號通訊協定支援下列容器格式和加密配置。

|容器格式|加密配置|URL 範例
|---|---|---|
|全部|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Smooth Streaming 通訊協定支援下列容器格式和加密配置。

|Protocol|容器格式|加密配置|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>瀏覽器

常見的瀏覽器支援下列 DRM 用戶端:

|Browser|加密|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="control-content-access"></a>控制內容存取

您可以設定內容金鑰原則來控制可存取內容的人員。 媒體服務支援多種方式來授權提出金鑰要求的使用者。 您必須設定內容金鑰原則。 用戶端 (播放器) 必須先符合原則，系統才會將金鑰傳遞給用戶端。 內容金鑰原則可具有**開啟**或**權杖**限制。 

使用權杖限制的內容金鑰原則時，系統只會將內容金鑰傳送給可在金鑰/授權要求中提供有效 JSON Web 權杖 (JWT) 或簡單 Web 權杖 (SWT) 的用戶端。 這個權杖必須由 Security Token Service (STS) 來核發。 您可以使用 Azure Active Directory 作為 STS，或部署自訂 STS。 STS 必須設定為建立使用指定的索引鍵和問題宣告您在權杖限制組態中指定簽署的權杖。 如果權杖有效，且權杖中的宣告符合針對金鑰/授權所設定的宣告，則媒體服務金鑰傳遞服務會將所要求的金鑰/授權傳回給用戶端。

設定權杖限制的原則時，您必須指定主要驗證金鑰、簽發者和對象參數。 主要驗證金鑰包含簽署權杖用的金鑰。 簽發者為發行權杖的安全性權杖服務。 對象 (有時稱為「範圍」) 描述權杖或權杖獲授權存取之資源的用途。 媒體服務金鑰傳遞服務會驗證權杖中的這些值符合在範本中的值。

客戶通常會使用自訂的 STS 來包含權杖中的自訂宣告, 以在具有不同 DRM 授權參數 (訂用帳戶授權與出租授權) 的不同 ContentKeyPolicyOptions 之間進行選取, 或包含代表內容金鑰的索賠權杖授與存取權的金鑰識別碼。

### <a name="token-replay-prevention"></a>權杖重新執行防止

「*權杖*重新執行防護」功能可讓媒體服務客戶設定相同權杖可用來要求金鑰或授權的次數限制。 客戶可以在權杖中新增類型`urn:microsoft:azure:mediaservices:maxuses`的宣告, 其中的值是權杖可用於取得授權或金鑰的次數。 對金鑰傳遞具有相同權杖的所有後續要求將會傳回未經授權的回應。 請參閱如何在[DRM 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)中新增宣告。
 
#### <a name="considerations"></a>考量

* 客戶必須擁有權杖產生的控制權。 宣告必須放在權杖本身。
* 使用這項功能時, 會拒絕權杖的到期時間超過一小時的要求, 而不會收到未經授權的回應。
* 權杖是由其簽章唯一識別。 對承載所做的任何變更 (例如, 更新到期時間或宣告) 都會變更權杖的簽章, 並將其計為新的權杖, 表示先前尚未遇到金鑰傳遞。
* 如果權杖已超過客戶所設定的`maxuses`值, 播放就會失敗。
* 這項功能可用於所有現有的受保護內容 (只需要變更發行的權杖)。
* 這項功能適用于 JWT 和 SWT。

## <a name="custom-key-and-license-acquisition-url"></a>自訂金鑰和授權取得 URL

如果您想要指定不同的金鑰和授權傳遞服務 (而不是媒體服務), 請使用下列範本。 範本中的兩個可取代欄位是, 因此您可以在許多資產之間共用串流原則, 而不是針對每個資產建立串流原則。 

* EnvelopeEncryption. CustomKeyAcquisitionUrlTemplate-提供金鑰給使用者播放程式之自訂服務 URL 的範本。 使用 Azure 媒體服務來發行金鑰時不需要。 此範本支援可取代的權杖, 服務會在執行時間以要求的特定值進行更新。  目前支援的標記值為 {AlternativeMediaId}, 會取代為 StreamingLocatorId 的值, 而 {ContentKeyId} 會取代為所要求之金鑰的識別碼值。
* StreamingPolicyPlayReadyConfiguration. CustomLicenseAcquisitionUrlTemplate-提供授權給使用者播放程式之自訂服務 URL 的範本。 使用 Azure 媒體服務發行授權時不需要。 此範本支援可取代的權杖, 服務會在執行時間以要求的特定值進行更新。 目前支援的標記值為 {AlternativeMediaId}, 會取代為 StreamingLocatorId 的值, 而 {ContentKeyId} 會取代為所要求之金鑰的識別碼值。 
* StreamingPolicyWidevineConfiguration. CustomLicenseAcquisitionUrlTemplate-與上述相同, 僅適用于 Widevine。 
* StreamingPolicyFairPlayConfiguration. CustomLicenseAcquisitionUrlTemplate-與上述相同, 僅適用于 FairPlay。  

例如:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

具有所要求之金鑰的值, 如果您想`AlternativeMediaId`要將要求對應至您端的實體, 則可以使用。 `ContentKeyId` 例如, `AlternativeMediaId`可以用來協助您查閱許可權。

如需使用自訂金鑰和授權取得 Url 的 REST 範例, 請參閱[串流原則-建立](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="troubleshoot"></a>疑難排解

如果您收到`MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY`錯誤, 請確定您指定的是適當的串流原則。

如果您收到以`_NOT_SPECIFIED_IN_URL`結尾的錯誤, 請確定您在 URL 中指定加密格式。 例如： `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)` 。 請參閱[串流通訊協定和加密類型](#streaming-protocols-and-encryption-types)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* [使用 AES 加密保護](protect-with-aes128.md)
* [使用 DRM 保護](protect-with-drm.md)
* [設計具有存取控制的多重 DRM 內容保護系統](design-multi-drm-system-with-access-control.md)
* [儲存端加密](storage-account-concept.md#storage-side-encryption)
* [常見問題集](frequently-asked-questions.md)

