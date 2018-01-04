---
title: "使用 Azure 媒體服務保護您的內容 | Microsoft Docs"
description: "此文章簡介如何利用 Media Services 保護內容。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 13447fd9193374d80ed5c2e6af8543f11b95e709
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="content-protection-overview"></a>內容保護概觀
 您可以使用 Azure Media Services 來保護您的媒體離開您的電腦，透過儲存體、 處理和傳遞時間。 您可以使用 Media Services 傳遞實況和點播內容動態加密使用進階加密標準 (AES 128) 或任何三個主要的數位版權管理 (DRM) 系統： Microsoft PlayReady、 Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 

下圖將說明媒體服務內容保護工作流程： 

![利用 PlayReady 保護](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

本文說明概念與術語與了解使用媒體服務內容保護相關。 本文也會提供討論如何保護內容的文章連結。 

## <a name="dynamic-encryption"></a>動態加密
 您可以使用 Media Services 來傳遞使用 PlayReady、 Widevine 或 FairPlay 動態加密使用 AES 清除金鑰或 DRM 加密內容。 目前，您可以加密的 HTTP Live Streaming (HLS)、 MPEG DASH 和 Smooth Streaming 格式。 不支援在漸進式下載上加密。 每一種加密方法都支援下列串流處理通訊協定：

- AES：MPEG DASH、 Smooth Streaming 和 HLS
- PlayReady：MPEG DASH、 Smooth Streaming 和 HLS
- Widevine：MPEG DASH
- FairPlay：HLS

若要加密資產，您需要建立加密內容金鑰與您資產的關聯，同時也要設定金鑰的授權原則。 內容金鑰可由您指定或由媒體服務自動產生。

您也需要設定資產的傳遞原則。 如果您要串流處理的儲存體加密資產時，請務必指定您要如何傳遞藉由設定資產遞送原則。

當播放程式要求資料流時，Media Services 會使用指定的索引鍵來動態加密使用 AES 清除金鑰或 DRM 加密的內容。 若要解密的資料流，播放程式會從 Media Services 金鑰傳遞服務要求的索引鍵。 為了決定使用者是否有權取得金鑰，服務會評估為金鑰指定的授權原則。

## <a name="aes-128-clear-key-vs-drm"></a>清除金鑰與 AES 128。DRM
客戶通常不知道他們應該使用 AES 加密，還是 DRM 系統。 兩個系統之間的主要差異是使用 AES 加密傳輸的內容金鑰是以加密格式 （「 清除 」） 中的用戶端。 如此一來，可以檢視用來加密內容的索引鍵中以純文字在用戶端網路追蹤。 清除金鑰加密的 AES 128 適合使用的情況下，檢視器的受信任的合作對象 （例如，加密公司視訊分散式檢視的員工公司內）。

相較於 AES-128 未加密金鑰加密，PlayReady、Widevine 及 FairPlay 全都會提供較高層級的加密。 內容金鑰會以加密格式傳輸。 此外，解密會在作業系統層級，它是惡意使用者攻擊更加困難的安全環境中處理。 DRM 被建議使用案例，其中的檢視器可能不是受信任的合作對象，而您需要最高層級的安全性。

## <a name="storage-encryption"></a>儲存體加密
您可以使用儲存體加密來加密在本機使用 AES 256 位元加密您的清除內容。 您接著可以將它上傳至 Azure 儲存體，儲存它以靜態加密。 使用儲存體加密保護的資產會自動加密，並在編碼之前加密的檔案系統中。 資產是上傳為新輸出資產之前選擇性地重新加密。 儲存體加密的主要使用案例是，讓您可以使用強式加密來保護磁碟中靜止的高品質輸入媒體檔。

若要傳遞儲存體加密的資產，您必須設定資產的傳遞原則，讓媒體服務知道要如何傳遞內容。 在您的資產進行串流處理之前，串流伺服器解密，並使用指定的傳遞原則 （例如 AES、 一般加密或不加密） 串流您的內容。

## <a name="types-of-encryption"></a>加密的類型
PlayReady 和 Widevine 利用一般加密 （AES CTR 模式）。 FairPlay 使用 CBC 模式 AES 加密。 清除金鑰加密的 AES 128 會利用信封加密。

## <a name="licenses-and-keys-delivery-service"></a>授權和金鑰傳遞服務
媒體服務提供一種金鑰傳遞服務，可將 DRM (PlayReady、Widevine 與 FairPlay) 授權和 AES 金鑰傳遞給授權用戶端。 您可以使用[Azure 入口網站](media-services-portal-protect-content.md)，REST API 或 Media Services SDK for.NET 來設定您的授權和金鑰的授權和驗證原則。

## <a name="control-content-access"></a>控制內容存取
您可以設定內容金鑰授權原則來控制可存取內容的人員。 內容金鑰授權原則支援 open 或權杖限制。

### <a name="open-authorization"></a>Open 授權
利用 open 授權原則，可將內容金鑰傳送給任何用戶端 (無限制)。

### <a name="token-authorization"></a>權杖授權
權杖限制授權原則，內容金鑰只會傳送到用戶端授權金鑰/要求呈現有效 JSON Web Token (JWT) 或簡易 web 權杖 (SWT)。 這個語彙基元必須發出安全性權杖服務 (STS)。 您可以使用 Azure Active Directory 做為 STS，或部署自訂 STS。 STS 必須設定為建立使用指定的索引鍵和問題宣告您在權杖限制組態中指定簽署的權杖。 Media Services 金鑰傳遞服務會傳回所要求的索引鍵/授權給用戶端的語彙基元有效，且權杖中的宣告符合為金鑰/授權設定。

當您設定權杖限制的原則時，您必須指定主要驗證金鑰、 簽發者和對象的參數。 主要驗證金鑰包含簽署權杖的金鑰。 發行者為發行權杖的安全權杖服務。 對象 (有時稱為「範圍」) 描述權杖或權杖獲授權存取之資源的用途。 媒體服務金鑰傳遞服務會驗證權杖中的這些值符合在範本中的值。

## <a name="streaming-urls"></a>串流 URL
如果您的資產使用一個以上的 DRM 加密，使用加密標記中的串流 URL: (格式 ='m3u8-aapl' 加密 = 'xxx')。

您必須考量下列事項：

* 無法指定一個以上的加密類型。
* 加密類型沒有指定 URL 中，如果只有一個加密已套用至資產。
* 加密類型不區分大小寫。
* 可以指定下列加密類型︰
  * **cenc**： 為 PlayReady 或 Widevine （一般加密）
  * **cbcs aapl**： 針對 FairPlay （AES CBC 加密）
  * **cbc**： 如 AES 信封加密

## <a name="next-steps"></a>後續步驟
下列文章描述可協助您快速入門內容保護的後續步驟：

* [使用儲存體加密保護](media-services-rest-storage-encryption.md)
* [使用 AES 加密保護](media-services-protect-with-aes128.md)
* [使用 PlayReady 和/或 Widevine 保護](media-services-protect-with-playready-widevine.md)
* [使用 FairPlay 保護](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>相關連結

* [Azure 媒體服務 PlayReady 授權傳遞定價說明](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)
* [偵錯在 Azure Media Services AES 加密的資料流。](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)
* [JWT 權杖驗證](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Azure Media Services OWIN MVC 架構應用程式與 Azure Active Directory 和整合限制 JWT 宣告為基礎的內容金鑰傳遞](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
