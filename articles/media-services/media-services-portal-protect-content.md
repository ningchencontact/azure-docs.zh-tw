---
title: "使用 Azure 入口網站設定內容保護原則 |Microsoft 文件"
description: "本文章示範如何使用 Azure 入口網站設定內容保護原則。 文章也會說明如何啟用資產的動態加密。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 805e1246dbc984582528d2b351d2f14ab2e811fc
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>使用 Azure 入口網站設定內容保護原則
 透過 Azure Media Services，您可以保護離開您的電腦，透過儲存體、 處理和傳遞的時間從您的媒體。 您可以使用 Media Services 來傳遞內容以動態方式使用進階加密標準 (AES) 使用加密的 128 位元加密金鑰。 您也可以使用它使用一般加密 (CENC) 使用 PlayReady 和/或 Widevine 數位版權管理 (DRM) 和 Apple FairPlay。 

Media Services 提供服務，以傳遞授權 DRM 和 AES 清除金鑰來授權用戶端。 您可以使用 Azure 入口網站來建立一個索引鍵/授權授權原則，所有的加密類型。

本文示範如何使用入口網站設定內容保護原則。 文章也會說明如何將動態加密套用到資產。

## <a name="start-to-configure-content-protection"></a>開始設定內容保護
若要使用入口網站使用 Media Services 帳戶來設定全域內容的保護，採取下列步驟：

1. 在[入口網站](https://portal.azure.com/)，選取您的 Media Services 帳戶。

2. 選取 [設定] > [內容保護]。

    ![內容保護](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>金鑰/授權的授權原則
Media Services 支援多種方式來驗證提出金鑰或授權的使用者。 您必須設定內容金鑰授權原則。 您的用戶端再之前，必須符合原則機碼/授權可以傳遞給它。 內容金鑰授權原則可以有一或多個授權限制，開啟或語彙基元的限制。

您可以使用入口網站來建立一個索引鍵/授權授權原則，所有的加密類型。

### <a name="open-authorization"></a>Open 授權
開放限制表示，系統會提供金鑰提供給任何提出金鑰要求的人。 這項限制可用於測試用途。 

### <a name="token-authorization"></a>權杖授權
權杖限制原則必須伴隨著安全性權杖服務 (STS) 所簽發的權杖。 Media Services 支援簡單 web 權杖 (SWT) 和 JSON Web Token (JWT) 格式的權杖。 Media Services 不提供 STS。 您可以建立自訂的 STS，或使用 Azure 存取控制服務，以簽發權杖。 STS 必須設定為建立使用指定的索引鍵和問題宣告您在權杖限制組態中指定簽署的權杖。 如果權杖是有效的權杖中的宣告符合為金鑰 （或授權） 設定，Media Services 金鑰傳遞服務會傳回至用戶端要求的索引鍵 （或授權）。

當您設定權杖限制原則時，您必須指定主要驗證金鑰、 簽發者和對象的參數。 主要驗證金鑰包含簽署權杖的金鑰。 發行者為發行權杖的安全權杖服務。 對象 (有時稱為範圍) 描述權杖或權杖獲授權存取之資源的用途。 媒體服務金鑰傳遞服務會驗證權杖中的這些值符合在範本中的值。

![金鑰/授權的授權原則](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady 授權範本
PlayReady 授權範本設定 PlayReady 授權已啟用的功能。 如需 PlayReady 授權範本的詳細資訊，請參閱[Media Services PlayReady 授權範本概觀](media-services-playready-license-template-overview.md)。

### <a name="nonpersistent"></a>非持續
如果您設定為非保存的授權，它會保留在記憶體中只有時播放程式使用授權。  

![非保存的內容保護](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>持續性
如果您將授權設定為持續性時，將它儲存在永續性儲存體用戶端上。

![持續性的內容保護](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine 授權範本
Widevine 授權範本的 Widevine 授權設定已啟用的功能。

### <a name="basic"></a>基本
當您選取**基本**，使用所有預設值建立的範本。

### <a name="advanced"></a>進階
如需 Widevine 權限範本的詳細資訊，請參閱[Widevine 授權範本概觀](media-services-widevine-license-template-overview.md)。

![進階內容保護](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay 組態
若要啟用 FairPlay 加密，請選取**FairPlay 組態**。 然後選取**應用程式的憑證**輸入**應用程式密碼金鑰**。 如需 FairPlay 組態和需求的詳細資訊，請參閱[保護 HLS 內容與 Apple FairPlay 或 Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md)。

![FairPlay 組態](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>將動態加密套用到您的資產
若要利用動態加密，編碼一組彈性位元速率 MP4 檔案中的原始程式檔。

### <a name="select-an-asset-that-you-want-to-encrypt"></a>選取您要加密的資產
若要查看您所有的資產，請選取 [設定] > [資產]。

![資產選項](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>使用 AES 或 DRM 加密
當您選取**加密**資產，您會看到兩個選擇： **AES**或**DRM**。 

#### <a name="aes"></a>AES
AES 清除金鑰加密已啟用所有的資料流通訊協定： Smooth Streaming、 HLS 及 MPEG-DASH。

![加密設定](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. 選取後**DRM**，您會看到不同的內容保護原則 （這必須設定此點） 和一組的串流處理通訊協定：

    a. **PlayReady 和 MPEG DASH 與 Widevine**動態加密您的 MPEG DASH 串流以 PlayReady 和 Widevine DRMs。

    b. **PlayReady 和 MPEG DASH 與 Widevine + FairPlay 透過 HLS**動態加密您的 MPEG DASH 串流以 PlayReady 和 Widevine DRMs。 此選項也會加密與 FairPlay 您 HLS 資料流。

    c. **只能搭配 Smooth Streaming、 HLS 及 MPEG-DASH 的 PlayReady**動態加密 Smooth Streaming、 HLS 及 MPEG-DASH 的資料流使用 PlayReady DRM。

    d. **只能搭配 MPEG DASH Widevine**動態加密與 Widevine DRM 您 MPEG DASH。
    
    e. **只能搭配 HLS FairPlay**動態加密與 FairPlay 您 HLS 資料流。

2. 若要在啟用 FairPlay 加密**內容保護的全域設定**刀鋒視窗中，選取**FairPlay 組態**。 然後選取**應用程式的憑證**，並輸入**應用程式密碼金鑰**。

    ![加密類型](./media/media-services-portal-content-protection/media-services-content-protection009.png)

3. 加密選取選項之後，請選取**套用**。

>[!NOTE] 
>如果您計劃播放中 Safari 的 AES 加密的 HLS，請參閱部落格文章[Safari 中加密的 HLS](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)。

## <a name="next-steps"></a>後續步驟
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

