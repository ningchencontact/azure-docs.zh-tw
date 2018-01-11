---
title: "使用 Media Services.NET SDK 設定內容金鑰授權原則 |Microsoft 文件"
description: "了解如何使用 Media Services.NET SDK 設定內容金鑰授權原則。"
services: media-services
documentationcenter: 
author: mingfeiy
manager: cfowler
editor: 
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;mingfeiy
ms.openlocfilehash: 39782bd687c9c1b50699c05e61e57d9c767a8d32
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>動態加密： 設定內容金鑰授權原則
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>概觀
 您可以使用 Azure Media Services 來傳遞使用 128 位元加密金鑰來保護使用進階加密標準 (AES) 的 MPEG DASH、 Smooth Streaming 和 HTTP Live Streaming (HLS) 資料流或[PlayReady 數位版權管理 (DRM)](https://www.microsoft.com/playready/overview/). 使用 Media Services 中，您也可以傳遞 Widevine DRM 以加密的 DASH 資料流。 PlayReady 和 Widevine 會加密依據一般加密 (ISO/IEC 23001-7 CENC) 規格中。

Media Services 也提供用戶端可從其中取得 AES 金鑰或 PlayReady/Widevine 授權才能播放加密的內容金鑰/授權傳遞服務。

如果您想要加密資產的 Media Services 時，您需要將加密金鑰 （「 CommonEncryption 」 或 「 EnvelopeEncryption 」） 與資產。 如需詳細資訊，請參閱[建立 Contentkey.net](media-services-dotnet-create-contentkey.md)。 您也需要設定授權原則機碼 （如本文所述）。

當播放程式要求資料流時，Media Services 會使用指定的索引鍵來動態加密使用 AES 或 DRM 加密的內容。 為了將串流解密，播放程式將向金鑰傳遞服務要求金鑰。 若要判斷是否授權使用者取得的金鑰，服務會評估您指定的索引鍵的授權原則。

媒體服務支援多種方式來驗證提出金鑰要求的使用者。 內容金鑰授權原則可以有一或多個授權限制。 選項為開啟或語彙基元的限制。 權杖限制原則必須伴隨著安全性權杖服務 (STS) 所簽發的權杖。 Media Services 支援簡單 web 權杖中的語彙基元 ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) 的格式和 JSON Web 權杖 ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) 格式。

Media Services 不提供 STS。 您可以建立自訂的 STS，或使用 Azure 存取控制服務，以簽發權杖。 STS 必須設定為建立使用指定金鑰簽署的權杖，並發行在權杖限制組態中指定的宣告 (如本文中所述)。 如果權杖是有效的權杖中的宣告符合為內容金鑰設定，Media Services 金鑰傳遞服務會傳回給用戶端的加密金鑰。

如需詳細資訊，請參閱下列文章：

- [JWT 權杖驗證](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Azure Media Services OWIN MVC 架構應用程式與 Azure Active Directory 和整合限制 JWT 宣告為基礎的內容金鑰傳遞](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>某些考量適用於
* 建立 Media Services 帳戶時，串流端點的預設值會加入至您的帳戶處於 「 已停止 」 狀態。 若要開始將內容串流，並利用動態封裝和動態加密，您的串流端點必須處於 「 執行中 」 狀態。 
* 您的資產必須包含一組調適性位元速率 MP4 或調適性位元速率 Smooth Streaming 檔案。 如需詳細資訊，請參閱 [為資產編碼](media-services-encode-asset.md)。
* 上傳，並將您的資產編碼使用 AssetCreationOptions.StorageEncrypted 選項。
* 如果您計劃有多個內容的索引鍵，需要相同的原則設定，我們建議您建立單一授權原則，和其重複使用於多個內容的索引鍵。
* 金鑰傳遞服務會快取 ContentKeyAuthorizationPolicy 及其相關的物件 （原則選項和限制） 15 分鐘。 您可以建立 ContentKeyAuthorizationPolicy 並指定若要使用權杖限制，測試它，然後將原則更新為開放的限制。 這個程序需要大約 15 分鐘，原則切換為開啟的原則版本。
* 如果您加入或更新資產的傳遞原則，您必須刪除任何現有的定位器，並建立新的定位器。
* 目前，您也無法加密的漸進式下載。
* 媒體服務串流端點萬用字元預檢回應中設定 CORS '的存取控制-允許-原始' 標頭的值 '\*'。 這個值適用於大部分的播放程式，包括 Azure Media Player、 Roku 和 JWPlayer，以及其他等等。 不過，有些播放程式使用 dashjs 不適因為以認證的模式設定為"include"、 在其 dashjs XMLHttpRequest 不允許萬用字元"\*"做為 '的存取控制-允許-原始' 的值。 因應措施 dashjs 中這項限制，如果您裝載您的用戶端，從單一網域，Media Services 可以指定該網域預檢回應標頭中。 如需協助，請開啟支援票證，透過 Azure 入口網站。

## <a name="aes-128-dynamic-encryption"></a>Aes-128 動態加密
### <a name="open-restriction"></a>Open 限制
Open 限制表示系統會將金鑰傳遞給提出金鑰要求的任何人。 這項限制可用於測試用途。

下列範例會建立開放授權原則，並將它加入至內容金鑰：

    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }


### <a name="token-restriction"></a>權杖限制
本節描述如何建立內容金鑰授權原則，然後建立它與內容金鑰的關聯。 授權原則描述來判斷是否授權使用者能夠收到金鑰必須符合哪些授權需求。 例如，驗證索引鍵清單包含簽署權杖的金鑰嗎？

若要設定權杖限制選項，您需要使用 XML 來說明權杖的授權需求。 權杖限制設定 XML 必須符合下列 XML 結構描述：

#### <a name="token-restriction-schema"></a>權杖限制結構描述
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>

當您設定權杖限制原則時，您必須指定主要驗證金鑰、 簽發者和對象的參數。 主要驗證金鑰包含簽署權杖的金鑰。 發行者為發行權杖的 STS。 對象 (有時稱為範圍) 描述權杖或權杖獲授權存取之資源的用途。 媒體服務金鑰傳遞服務會驗證權杖中的這些值符合在範本中的值。

當您使用 Media Services SDK for.NET 時，您可以使用 TokenRestrictionTemplate 類別來產生限制權杖。
下列範例會建立具有 token 限制的授權原則。 在此範例中，用戶端必須出示包含簽署金鑰 (VerificationKey)、 權杖簽發者和必要的宣告的權杖。

    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }

#### <a name="test-token"></a>測試權杖
若要取得測試權杖，根據用於金鑰授權原則的權杖限制，執行下列作業：

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();


## <a name="playready-dynamic-encryption"></a>PlayReady 動態加密
您可以使用 Media Services 來設定權限，而且您想要在使用者嘗試播放時，強制執行 PlayReady DRM 執行階段的限制受保護的內容。 

當您保護使用 PlayReady 時，其中一項操作，您必須在您的授權原則中指定為 XML 字串，定義[PlayReady 授權範本](media-services-playready-license-template-overview.md)。 在 Media Services SDK for.NET，PlayReadyLicenseResponseTemplate 和 PlayReadyLicenseTemplate 類別幫助您定義 PlayReady 授權範本。

若要深入了解如何加密您以 PlayReady 和 Widevine 的內容，請參閱[使用 PlayReady 和/或 Widevine 動態一般加密](media-services-protect-with-playready-widevine.md)。

### <a name="open-restriction"></a>Open 限制
Open 限制表示系統會將金鑰傳遞給提出金鑰要求的任何人。 這項限制可用於測試用途。

下列範例會建立開放授權原則，並將它加入至內容金鑰：

    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }

### <a name="token-restriction"></a>權杖限制
若要設定權杖限制選項，您需要使用 XML 來說明權杖的授權需求。 權杖限制設定 XML 必須符合所示的 XML 結構描述 」[語彙基元限制結構描述](#token-restriction-schema)」 一節。

    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }


若要取得測試權杖根據用於金鑰授權原則的權杖限制，請參閱"[測試語彙基元](#test-token)」 一節。 

## <a id="types"></a>當您定義 ContentKeyAuthorizationPolicy 時使用的類型
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

### <a id="TokenType"></a>TokenType
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>後續步驟
既然您已設定的內容金鑰授權原則，請參閱[設定資產遞送原則](media-services-dotnet-configure-asset-delivery-policy.md)。

