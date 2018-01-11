---
title: "使用 Azure 入口網站設定內容金鑰授權原則 |Microsoft 文件"
description: "了解如何設定內容金鑰的授權原則。"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: a0ab954bda3340b9010b16f54e343933808cc463
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="configure-a-content-key-authorization-policy"></a>設定內容金鑰授權原則
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>概觀
 您可以使用 Azure Media Services 來傳遞使用 128 位元加密金鑰來保護使用進階加密標準 (AES) 的 MPEG DASH、 Smooth Streaming 和 HTTP Live Streaming (HLS) 資料流或[PlayReady 數位版權管理 (DRM)](https://www.microsoft.com/playready/overview/). 使用 Media Services 中，您也可以傳遞 Widevine DRM 以加密的 DASH 資料流。 PlayReady 和 Widevine 會加密依據一般加密 (ISO/IEC 23001-7 CENC) 規格中。

Media Services 也提供用戶端可從其中取得 AES 金鑰或 PlayReady/Widevine 授權才能播放加密的內容金鑰/授權傳遞服務。

本文示範如何使用 Azure 入口網站設定內容金鑰授權原則。 金鑰稍後可以用來動態加密您的內容。 目前，您可以加密 HLS、 MPEG-DASH 以及 Smooth Streaming 格式。 您無法加密的漸進式下載。

當播放程式要求設定為動態加密的資料流時，Media Services 會使用動態加密您的內容使用 AES 或 DRM 加密設定的金鑰。 為了將串流解密，播放程式將向金鑰傳遞服務要求金鑰。 若要判斷是否授權使用者取得的金鑰，服務會評估您指定的索引鍵的授權原則。

如果您計劃有多個內容的索引鍵或想要指定 Media Services 金鑰傳遞服務以外的金鑰/授權傳遞服務 URL，請使用媒體服務.NET SDK 或 REST Api。 如需詳細資訊，請參閱

* [使用 Media Services.NET SDK 設定內容金鑰授權原則](media-services-dotnet-configure-content-key-auth-policy.md)
* [使用媒體服務 REST API 設定內容金鑰授權原則](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>某些考量適用於
* 建立 Media Services 帳戶時，串流端點的預設值會加入至您的帳戶處於 「 已停止 」 狀態。 若要開始將內容串流，並利用動態封裝和動態加密，您的串流端點必須處於 「 執行中 」 狀態。 
* 您的資產必須包含一組調適性位元速率 MP4 或調適性位元速率 Smooth Streaming 檔案。 如需詳細資訊，請參閱 [為資產編碼](media-services-encode-asset.md)。
* 金鑰傳遞服務會快取 ContentKeyAuthorizationPolicy 及其相關的物件 （原則選項和限制） 15 分鐘。 您可以建立 ContentKeyAuthorizationPolicy 並指定若要使用權杖限制，測試它，然後將原則更新為開放的限制。 這個程序需要大約 15 分鐘，原則切換到開啟的版本。
* 媒體服務串流端點設定 CORS 存取控制-允許的原始標頭的值做為萬用字元事前回應中 」\*"。 這個值適用於大部分的播放程式，包括 Azure Media Player、 Roku 和 JWPlayer，以及其他等等。 不過，有些播放程式使用 dash.js 不適因為認證設定為"include"模式中，在其 dash.js XMLHttpRequest 不允許萬用字元"\*"做為存取控制-允許的原始值。 因應措施 dash.js 中這項限制，如果您裝載您的用戶端，從單一網域，Media Services 可以指定該網域預檢回應標頭中。 如需協助，請開啟支援票證，透過 Azure 入口網站。

## <a name="configure-the-key-authorization-policy"></a>設定金鑰授權原則
若要設定金鑰授權原則，請選取 [ **內容保護** ] 頁面。

Media Services 支援多個方法來驗證提出金鑰要求的使用者。 內容金鑰授權原則能開啟、 語彙基元或 IP 授權限制。 （IP 可以設定與 REST 或.NET SDK）。

### <a name="open-restriction"></a>Open 限制
開放限制表示系統傳遞給任何提出金鑰要求的任何人的索引鍵。 這項限制可用於測試用途。

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>權杖限制
選擇權杖限制的原則，請選取**語彙基元** 按鈕。

權杖限制的原則必須隨附由安全性權杖服務 (STS) 所簽發的權杖。 Media Services 支援簡單 web 權杖中的語彙基元 ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) 和 JSON Web Token (JWT) 格式。 如需詳細資訊，請參閱[JWT 驗證](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)。

Media Services 不提供 STS。 您可以建立自訂的 STS，或使用 Azure 存取控制服務，以簽發權杖。 STS 必須設定為建立使用指定的索引鍵和問題宣告您在權杖限制組態中指定簽署的權杖。 如果權杖是有效的權杖中的宣告符合為內容金鑰設定，Media Services 金鑰傳遞服務會傳回給用戶端的加密金鑰。 如需詳細資訊，請參閱[使用 Azure 存取控制服務以簽發權杖](http://mingfeiy.com/acs-with-key-services)。

當您設定權杖限制原則時，您必須指定主要驗證金鑰、 簽發者和對象的參數。 主要驗證金鑰包含簽署權杖的金鑰。 發行者為發行權杖的 STS。 對象 (有時稱為範圍) 描述權杖或權杖獲授權存取之資源的用途。 媒體服務金鑰傳遞服務會驗證權杖中的這些值符合在範本中的值。

### <a name="playready"></a>PlayReady
當您保護使用 PlayReady 時，其中一項操作，您必須在您的授權原則中指定為 XML 字串定義 PlayReady 授權範本。 依預設，會設定下列原則：

    <PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
          <LicenseTemplates>
            <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
              <ContentKey i:type="ContentEncryptionKeyFromHeader" />
              <LicenseType>Nonpersistent</LicenseType>
              <PlayRight>
                <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
              </PlayRight>
            </PlayReadyLicenseTemplate>
          </LicenseTemplates>
        </PlayReadyLicenseResponseTemplate>

您可以選取**匯入原則的 xml**按鈕，並提供不同的 XML 定義中的 XML 結構描述符合[Media Services PlayReady 授權範本概觀](media-services-playready-license-template-overview.md)。

## <a name="next-steps"></a>後續步驟
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

