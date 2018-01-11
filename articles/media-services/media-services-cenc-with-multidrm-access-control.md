---
title: "多重 DRM 和存取控制與 CENC： 參考設計及實作 Azure 和 Azure Media Services |Microsoft 文件"
description: "深入了解如何授權 Microsoft Smooth Streaming Client Porting Kit。"
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 7814739b-cea9-4b9b-8370-538702e5c615
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;kilroyh;yanmf;juliako
ms.openlocfilehash: cac1513d805e01f2c9633b3b318ad6808027904e
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>多重 DRM 和存取控制與 CENC： 參考設計及實作 Azure 和 Azure Media Services
 
## <a name="introduction"></a>簡介
設計和建立數位版權管理 (DRM) 子系統的容錯移轉-機上盒 (OTT) 或線上串流處理解決方案是一個複雜的工作。 運算子/線上視訊提供者通常外包這項工作以特殊的 DRM 服務提供者。 這份文件的目標是要呈現參考設計和實作 OTT 或線上串流處理解決方案中的端對端 DRM 子系統。

這份文件的目標的讀者是工程師中 DRM 子系統 OTT 或線上串流/多重畫面解決方案或感興趣 DRM 子系統的讀取器的工作。 此處的假設是讀者已熟悉至少其中一個上購物，例如 PlayReady、 Widevine、 FairPlay 或 Adobe Access 的 DRM 技術。

在本討論 DRM 中，我們也會包含使用多重 DRM 的一般加密 (CENC)。 CENC 搭配各種用戶端的平台的原生多 DRM 是線上串流和 OTT 產業中的主要趨勢。 這種趨勢項與前一個單一的 DRM 與用戶端 SDK 用於各種用戶端平台。 當您使用多重原生 DRM 使用 CENC 時，會將 PlayReady 和 Widevine 加密每個[一般加密 (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/)規格。

使用多重 DRM CENC 的優點包括：

* 減少加密成本，因為單一加密程序用來以其原生 DRMs 不同的平台為目標。
* 降低的加密的資產管理，因為需要有加密的資產的單一複本。
* 排除 DRM 授權成本，因為原生 DRM 用戶端通常是免費的原生平台上的用戶端。

Microsoft 會與某些類型的主要企業播放機的虛線和 CENC active 升級程式。 Azure Media Services 提供虛線和支援 CENC。 新公告，請參閱下列部落格：

*  [宣布在 Azure 媒體服務中推出 Google Widevine 授權傳遞服務](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services 將 Google Widevine 封裝來傳送多 DRM 資料流](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="overview-of-this-article"></a>本文概觀：
本文宗旨如下：

* 提供參考的設計會使用 CENC 多重 DRM DRM 子系統。
* 提供 Azure/Media Services 平台上的參考實作。
* 討論某些設計和實作的主題。

本文件 「 多重 DRM 」 一詞會涵蓋下列產品：

* Microsoft PlayReady (英文)
* Google Widevine
* Apple FairPlay 

下表摘要說明每個 DRM 所支援的瀏覽器與原生的平台/原生應用程式。

| **用戶端平台** | **原生 DRM 支援** | **瀏覽器/應用程式** | **資料流的格式** |
| --- | --- | --- | --- |
| **智慧型電視、運算子 STB、OTT STB** |主要為 PlayReady，及/或 Widevine，及/或其他的 DRM |Linux、 Opera、 WebKit，其他 |各種格式 |
| **Windows 10 裝置 （Windows 電腦、 Windows 平板電腦、 Windows Phone、 Xbox）** |PlayReady |MS Edge/IE11/EME<br/><br/><br/>通用 Windows 平台 |虛線 （為 HLS，PlayReady 不支援）<br/><br/>DASH、 Smooth Streaming （HLS 的 PlayReady 不支援） |
| **Android 裝置 （手機、 平板電腦、 電視節目）** |Widevine |Chrome/EME |DASH、 HLS |
| **iOS (iPhone、iPad)、OS X 用戶端和 Apple 電視** |FairPlay |Safari 8+/EME |HLS |


請考慮為每個 DRM 部署的目前狀態，服務通常想要實作兩個或三個 DRMs 先確定您最佳的方式來處理所有類型的端點。

您必須在服務邏輯複雜度和用戶端複雜度之間做出取捨，以便在各種不同的用戶端上讓使用者經驗能達到某種等級。

若要讓您的選擇，請記住：

* PlayReady 是原生實作每個 Windows 裝置、 一些 Android 的裝置上，而且可透過幾乎任何平台上的軟體 Sdk。
* Widevine 是原生實作，每個 Android 裝置、 Chrome 及一些其他裝置。
* FairPlay 只使用在 iOS 和 Mac OS 用戶端中，或是透過 iTunes 來提供。

有兩個典型的多重 DRM 的選項：

* PlayReady 和 Widevine
* PlayReady、 Widevine 和 FairPlay

## <a name="a-reference-design"></a>參考設計
本節介紹無從驗證要用來實作它的技術參考設計。

DRM 子系統可以包含下列元件：

* 金鑰管理
* DRM 封裝
* DRM 授權傳遞
* 權利檢查
* 驗證/授權
* 播放器
* 來源/內容傳遞網路 (CDN)

下圖說明 DRM 子系統中的元件之間的高層級互動：

![DRM 子系統與 CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

設計具有三個基本層級：

* 外部未公開端層 （黑色）。
* 周邊網路圖層 （深藍色） 包含面對大眾的所有端點。
* 公用的網際網路層 （淺藍色） 包含 CDN 與播放程式的流量透過公用網際網路。

也應該要有控制項 DRM 保護，無論它是靜態或動態加密的內容管理工具。 DRM 加密的輸入包括：

* MBR 視訊內容
* 內容金鑰
* 授權取得 Url

以下是高階流程期間播放時間：

* 在驗證使用者。
* 為使用者建立的授權權杖。
* 受 DRM 保護內容 （資訊清單） 被下載到播放程式。
* 玩家送出以及金鑰的授權伺服器的授權取得要求識別碼和授權權杖。

下列章節會討論的金鑰管理的設計。

| **ContentKey 的資產** | **案例** |
| --- | --- |
| 1-1 |最簡單的案例。 它提供最佳的控制。 但是，這種排列方式通常會產生最高的授權傳遞成本。 至少一個授權要求都需要每個受保護的資產。 |
| 1 對多 |您可以對多個資產使用相同的內容金鑰。 例如，請為邏輯群組中的所有資產，例如內容類型或子集的內容類型 （或電影 gene），您可以使用單一的內容金鑰。 |
| 多對 1 |每個資產需要有多個內容金鑰。 <br/><br/>例如，如果您需要套用動態 CENC 保護使用適用於 MPEG DASH 的多重 DRM 和動態的 AES 128 加密的 HLS，您需要兩個不同的內容金鑰。 每個內容的索引鍵必須自己 ContentKeyType。 （用於動態 CENC 保護內容金鑰的情況下，使用 ContentKeyType.CommonEncryption。 針對用來動態 AES 128 加密的內容金鑰，使用 ContentKeyType.EnvelopeEncryption）。<br/><br/>另舉一例，CENC 保護的虛線的內容，理論上，您可以使用一個內容金鑰來保護視訊串流和保護的音訊串流的另一個內容金鑰。 |
| 多對多 |前兩個案例的組合。 一組內容金鑰用於每個同一個資產群組中的多個資產。 |

另一個需要考慮的重要因素是持續性和非保存的授權的使用。

這些考量為何重要？

如果您使用公用雲端授權傳遞時，持續性和非保存的授權會直接影響對授權傳遞成本。 下列兩種不同的設計情況下會用來說明：

* 每月訂用帳戶： 使用持續性的授權，1 對多內容金鑰-資產對應。 比方說，所有兒童的電影，我們使用單一內容金鑰加密。 在此案例中：

    要求之所有孩童電影/裝置的授權總數 = 1

* 每月訂用帳戶： 使用非持續的授權，1-1 的內容金鑰與資產之間的對應。 在此案例中：

    要求之所有孩童電影/裝置的授權總數 = [監看的電影數目] x [工作階段的數目]

兩個不同的設計導致非常不同的授權要求模式。 不同的模式會導致不同授權成本如果授權傳遞服務係由 Media Services 例如公用雲端的傳遞。

## <a name="map-design-to-technology-for-implementation"></a>對應設計和實作的技術
接下來，泛型的設計會藉由指定要用於每個建置組塊哪一種技術對應到 Azure/Media Services 平台上的技術。

下表顯示的對應。

| **建置組塊** | **Technology** |
| --- | --- |
| **播放器** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **身分識別提供者 (IDP)** |Azure Active Directory (Azure AD) |
| **安全性權杖服務 (STS)** |Azure AD |
| **DRM 保護的工作流程** |Media Services 動態保護 |
| **DRM 授權傳遞** |* Media Services 授權傳遞 (PlayReady Widevine，FairPlay) <br/>* Axinom 授權伺服器 <br/>* 自訂 PlayReady 授權伺服器 |
| **原始** |媒體服務串流端點 |
| **金鑰管理** |不需要參考實作 |
| **內容管理** |C# 主控台應用程式 |

換句話說，IDP 和 STS 會搭配 Azure AD。 [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/)使用播放程式。 Media Services 和 Media Player 支援 DASH 和 CENC 多重 DRM。

下圖顯示整體結構與流程與上一個技術對應：

![在 Media Services CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

若要設定動態 CENC 加密，內容管理工具會使用下列輸入：

* 開啟內容
* 內容金鑰從金鑰產生/管理
* 授權取得 Url
* 從 Azure AD 的資訊清單

內容管理工具的輸出如下：

* ContentKeyAuthorizationPolicy 包含 DRM 授權規格與上如何授權傳遞驗證 JSON Web Token (JWT) 規格。
* AssetDeliveryPolicy 包含串流處理格式、 DRM 保護和授權取得 Url 的規格。

以下是在執行階段期間的流程：

* 使用者驗證後，就會產生 JWT。
* 包含群組物件識別碼 EntitledUserGroup groups 宣告其中一個 JWT 中包含的宣告。 這個宣告會通過權限檢查。
* 播放程式下載用戶端的資訊清單 CENC 保護的內容，並識別下列項目：
   * 索引鍵識別碼。
   * 內容是受保護的 CENC。
   * 授權取得 URL。
* 播放程式就會根據支援的瀏覽器/DRM 授權擷取要求。 在授權取得要求中，索引鍵識別碼和 JWT 也會提交。 授權傳遞服務會驗證 JWT 包含發出的所需的授權之前的宣告。

## <a name="implementation"></a>實作
### <a name="implementation-procedures"></a>實作程序
實作包含下列步驟：

1. 準備測試資產。 編碼/封裝至 Media Services 中的多位元速率分散 MP4 測試視訊。 這個資產是*不*受 DRM 保護。 DRM 保護的方式是稍後動態保護。

2. 建立金鑰識別碼和內容金鑰 （選擇性地從金鑰種子中）。 在本例中，因為，不需要金鑰管理系統的單一索引鍵識別碼和內容金鑰所需的數個測試資產。

3. 您可以使用 Media Services API 來設定測試的資產的多重 DRM 授權傳遞服務。 如果您的公司或您公司的廠商，而不是在媒體服務授權服務所使用自訂的授權伺服器，您可以略過此步驟。 當您設定授權傳遞時，您可以在步驟中指定授權取得 Url。 若要指定授權原則限制和不同的 DRM 授權服務的授權回應範本之類的某些詳細的組態需要媒體服務應用程式開發介面。 此時，Azure 入口網站不提供這項設定所需的 UI。 應用程式開發介面層級資訊及範例程式碼，請參閱[使用 PlayReady 和/或 Widevine 動態一般加密](media-services-protect-with-playready-widevine.md)。

4. 您可以使用 Media Services API 來設定測試資產的資產傳遞原則。 應用程式開發介面層級資訊及範例程式碼，請參閱[使用 PlayReady 和/或 Widevine 動態一般加密](media-services-protect-with-playready-widevine.md)。

5. 建立並在 Azure 中設定的 Azure AD 租用戶。

6. Azure AD 租用戶中建立幾個使用者帳戶和群組。 建立至少一個 「 標題為使用者 」 群組，並將使用者新增至此群組。 此群組中的使用者取得授權中傳遞的權限檢查。 不在此群組中的使用者才能通過驗證檢查失敗，且無法取得的授權。 這個 「 標題為使用者 」 群組的成員資格是在 Azure AD 所簽發的 JWT 的所需的群組宣告。 當您設定多 DRM 授權傳遞服務時，您可以指定在步驟中的此宣告需求。

7. 建立 ASP.NET MVC 應用程式來裝載您的視訊播放程式。 對 Azure AD 租用戶的使用者驗證受到此 ASP.NET 應用程式。 適當的宣告會包含在使用者驗證之後取得存取權杖。 我們建議此步驟 OpenID 連接 API。 安裝下列 NuGet 封裝：

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. 建立使用播放程式[Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/)。 使用[Azure Media Player ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/)來指定要使用不同的 DRM 平台上的 DRM 技術。

9. 下表顯示測試矩陣。

    | **DRM** | **[瀏覽器]** | **使用者有權使用的結果** | **Unentitled 使用者的結果** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge 或 Internet Explorer 11 Windows 10 上 |成功 |不合格 |
    | **Widevine** |Windows 10 上的 Chrome |成功 |不合格 |
    | **FairPlay** |TBD | | |

如需如何設定 Azure AD 進行 ASP.NET MVC 播放器應用程式資訊，請參閱[與 Azure Active Directory 整合 Azure Media Services OWIN MVC 為基礎的應用程式，並限制根據 JWT 宣告的內容金鑰傳遞](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)。

如需詳細資訊，請參閱[Azure 媒體服務和動態加密的 JWT 權杖驗證](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)。  

如需 Azure AD 的資訊：

* 開發人員資訊，請在[Azure Active Directory 開發人員手冊 》](../active-directory/active-directory-developers-guide.md)。
* 系統管理員資訊，請在[管理您的 Azure AD 租用戶目錄](../active-directory/active-directory-administer.md)。

### <a name="some-issues-in-implementation"></a>某些實作中的問題
如需實作問題的說明使用下列的疑難排解資訊。

* 簽發者 URL 的結尾必須"/"。 適用對象必須是播放器應用程式用戶端識別碼。 此外，請加入"/"結尾的簽發者 URL。

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    在[JWT 解碼器](http://jwt.calebb.net/)，您會看到**則**和**iss**、 JWT 中所示：

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* 在加入 Azure AD 中的應用程式的權限**設定**應用程式 索引標籤。 權限所需的每個應用程式中，本機和已部署的版本。

    ![權限](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* 當您設定動態 CENC 保護時，請使用正確的簽發者。

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    下列無法運作：

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    GUID 是 Azure AD 租用戶識別碼。 中可以找到 GUID**端點**在 Azure 入口網站的快顯功能表。

* 授與群組成員資格宣告權限。 請確定以下是 Azure AD 應用程式資訊清單檔中： 

    "groupMembershipClaims":"All"（預設值是 null）

* 當您建立限制需求時，請設定適當 TokenType。

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    您加入支援 JWT (Azure AD) 除了 SWT (ACS)，因為預設 TokenType 是 TokenType.JWT。 如果您使用 SWT/ACS，必須以 TokenType.SWT 設語彙基元。

## <a name="additional-topics-for-implementation"></a>其他實作的主題
本章節討論某些設計與實作中的其他主題。

### <a name="http-or-https"></a>HTTP 或 HTTPS？
ASP.NET MVC 播放器應用程式必須支援下列功能：

* 使用者驗證，透過 Azure AD，低於 HTTPS。
* Azure AD，正在進行 HTTPS 用戶端之間的 JWT 交換。
* 取得由用戶端，如果由 Media Services 提供授權傳遞，則必須在 HTTPS 的 DRM 授權。 PlayReady 產品套件不會授權傳遞託管 HTTPS。 如果外部媒體服務 PlayReady 授權伺服器，您可以使用 HTTP 或 HTTPS。

ASP.NET 播放器應用程式最佳作法是使用 HTTPS，所以 Media Player 是在 HTTPS 頁面上。 但 HTTP 是慣用的串流處理，因此您必須考慮混合內容的問題。

* 瀏覽器不允許混合的內容。 但外掛程式例如 Silverlight 和 OSMF plug-in for smooth 和破折號允許執行。 混合的內容會是能力的有安全性考量，因為插入惡意的 JavaScript，導致客戶資料會有風險的威脅。 瀏覽器預設封鎖這項功能。 若要暫時解決它的唯一方式是在伺服器 （來源） 端藉由使用 （不論 HTTPS 或 HTTP） 的所有網域。 這可能也不是個好主意。
* 避免混合的內容。 播放器應用程式和 Media Player 應該使用 HTTP 或 HTTPS。 當播放混合的內容，silverlightSS 技術需要清除的混合內容的警告。 FlashSS 技術會處理混合的內容，而不混合內容發出警告。
* 如果您的串流端點建立在 2014 年 8 月之前，它將不會支援 HTTPS。 在此情況下，建立並使用為 HTTPS 使用新的串流端點。

在受 DRM 保護內容，應用程式和資料流是在 HTTPS 的參考實作。 開啟的內容，播放程式不需要驗證或授權，因此您可以使用 HTTP 或 HTTPS。

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory 簽署金鑰變換
簽署金鑰變換是納入考量，在您實作的重點。 如果您選擇忽略它，完成的系統最終會停止在最完整地運作六週內。

Azure AD 會使用業界標準，本身和使用 Azure AD 的應用程式之間建立信任。 具體而言，Azure AD 使用簽署金鑰，該金鑰是由公開和私密金鑰組所組成。 當 Azure AD 會建立包含使用者的相關資訊的安全性權杖時，它是由 Azure AD 使用私密金鑰簽署傳送回應用程式之前。 若要確認語彙基元有效且來自 Azure AD，應用程式必須驗證權杖之簽章。 應用程式會使用由 Azure AD 租用戶的同盟中繼資料文件中所包含的公開金鑰。 此公開金鑰，並從它衍生，簽署金鑰是用於在 Azure AD 中的所有租用戶相同。

如需有關 Azure AD 金鑰變換的詳細資訊，請參閱[重要資訊在 Azure AD 中簽署金鑰變換](../active-directory/active-directory-signing-key-rollover.md)。

之間[公開-私密金鑰組](https://login.microsoftonline.com/common/discovery/keys/):

* 私密金鑰可由 Azure AD 來產生 JWT。
* 公開金鑰可由 Media Services 中的 DRM 授權傳遞服務之類的應用程式來確認 JWT。

基於安全性考量，Azure AD 會將憑證定期 （每隔六週）。 在安全性漏洞的情況下可能會隨時發生金鑰變換。 因此，在 Media Services 授權傳遞服務需要更新做為 Azure AD 會旋轉的金鑰組的公開金鑰。 否則，Media Services 中的權杖驗證失敗，並會發出任何授權。

若要設定此服務，您會設定 TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument，當您設定 DRM 授權傳遞服務。

以下是 JWT 流程：

* Azure AD 會發出 JWT 與已驗證使用者目前的私密金鑰。
* 當播放程式看到 CENC 具有多重 DRM 保護內容時，它會先尋找 Azure AD 所簽發的 JWT。
* 播放程式會將 JWT 的授權取得要求傳送至 Media Services 中的授權傳遞服務。
* Media Services 中的授權傳遞服務會使用目前/有效的公用金鑰，從 Azure AD 發出授權之前，先驗證 JWT。

DRM 授權傳遞服務一定要檢查的從 Azure AD 目前/有效的公開金鑰。 Azure AD 所呈現的公開金鑰是用來驗證 Azure AD 所簽發的 JWT 的金鑰。

如果金鑰變換所發生的 Azure AD 會產生 JWT 之後，但是 JWT 播放程式傳送至媒體服務進行驗證的 DRM 授權傳遞服務之前嗎？

索引鍵可以在任何時間變換，因為多個有效的公開金鑰始終是同盟中繼資料文件中可用的。 媒體服務授權傳遞可使用任何文件中指定的索引鍵。 因為一個金鑰可能很快就交易回復，另一個可能是一個取代，等等。

### <a name="where-is-the-access-token"></a>其中是存取權杖？
如果您查看如何 web 應用程式呼叫 API 應用程式下的[使用 OAuth 2.0 用戶端認證授與應用程式識別](../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api)，驗證流程如下所示：

* 使用者登入 Azure AD web 應用程式中。 如需詳細資訊，請參閱[web 應用程式的網頁瀏覽器](../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application)。
* Azure AD 授權端點會將使用者代理程式重新導向回到具有授權碼的用戶端應用程式。 使用者代理程式授權碼傳回用戶端應用程式重新導向 URI。
* Web 應用程式需要取得存取權杖，才能向 Web API 驗證和擷取所需的資源。 它對 Azure AD 權杖端點提出要求，並提供認證、 用戶端識別碼和 web API 的應用程式識別碼 URI。 它會提供授權碼，證明使用者已經同意。
* Azure AD 驗證應用程式，並傳回用來呼叫 web API 的 JWT 存取權杖。
* 透過 HTTPS，web 應用程式會使用傳回的 JWT 存取權杖加入含有"Bearer"稱號的 JWT 字串 「 授權 」 標頭的要求中 web API。 Web 應用程式開發介面，然後驗證 JWT。 如果驗證成功，它會傳回所需的資源。

在此應用程式身分識別流程中，web API 信任 web 應用程式驗證使用者。 基於這個理由，這種模式稱為受信任子系統。 [授權流程圖](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code)描述如何授權的程式碼-授與流程運作。

取得授權權杖限制會遵循相同的受信任的子系統模式。 Web API 資源或 「 後端資源 」 的 web 應用程式需要存取授權傳遞服務，在 Media Services。 那麼存取權杖在哪裡？

從 Azure AD 取得存取權杖。 使用者驗證成功後，會傳回授權碼。 授權碼會再使用，以及用戶端識別碼和應用程式金鑰，來交換存取權杖。 存取權杖用來存取 「 指標 」 應用程式指向或代表 Media Services 授權傳遞服務。

若要註冊，並在 Azure AD 中設定指標應用程式，執行下列步驟：

1. 在 Azure AD 租用戶：

   * 新增登入 URL https://[resource_name].azurewebsites.net/ 的應用程式 （資源）。 
   * 新增應用程式識別碼與 URL https://[aad_tenant_name].onmicrosoft.com/[resource_name]。

2. 加入資源應用程式的新機碼。

3. 更新應用程式資訊清單檔案，使 groupMembershipClaims 屬性具有值"groupMembershipClaims":"All"。

4. 指向 player web 應用程式，一節中的 Azure AD 應用程式中**其他應用程式的權限**，加入在步驟 1 中已加入的資源應用程式。 在下**委派權限**，選取**存取 [resource_name]**。 此選項可讓您建立存取資源應用程式的存取權杖的 web 應用程式權限。 如果此作業的 web 應用程式的本機和已部署版本與 Visual Studio 和 Azure web 應用程式開發。

Azure AD 所簽發的 JWT 是用來存取指標資源的存取權杖。

### <a name="what-about-live-streaming"></a>即時串流呢？
先前的討論內容著重於視資產。 即時串流呢？

您可以使用完全相同的設計和實作，以保護實況串流處理媒體服務中將做為 VOD 資產的程式相關聯的資產。

執行即時串流處理媒體服務中，特別是，您需要建立通道，然後再建立通道下的程式。 若要建立程式，您需要建立包含程式的即時封存資產。 若要提供 CENC 多重 DRM 保護即時內容，套用相同的安裝程式/處理資產，就好像 VOD 資產之前啟動程式。

### <a name="what-about-license-servers-outside-media-services"></a>外部媒體服務的授權伺服器呢？
通常，客戶投資在授權伺服器陣列中是在其自己的資料中心或 DRM 服務提供者所裝載的一個。 使用媒體服務內容保護，您可以在混合模式運作。 內容可以裝載，以動態方式保護在 Media Services DRM 授權都已傳遞方法外 Media Services 的伺服器。 在此情況下，請考慮下列變更：

* STS 發出權杖可接受及可驗證的授權伺服器陣列的需求。 例如，Axinom 所提供的 Widevine 授權伺服器需要特定的 JWT 包含權利訊息。 因此，您需要有 STS 發出這類 JWT。 如需這個實作型別資訊，請移至[Azure 文件中心](https://azure.microsoft.com/documentation/)並查看[Widevine 授權傳遞到 Azure Media Services 使用 Axinom](media-services-axinom-integration.md)。
* 您不再需要在 Media Services 中設定授權傳遞服務 (ContentKeyAuthorizationPolicy)。 您需要提供授權取得 Url （PlayReady、 Widevine 和 FairPlay） 當您設定使用多重 DRM 設定 CENC AssetDeliveryPolicy。

### <a name="what-if-i-want-to-use-a-custom-sts"></a>如果我想要使用自訂 STS？
客戶可能會選擇使用自訂 STS 提供 Jwt。 原因包括：

* 客戶使用的 IDP 不支援 STS。 在此情況下，自訂 STS 可能是個選項。
* 客戶可能需要更多彈性或更嚴格的控制與客戶的訂閱者 」 計費系統整合 STS。 例如，多個 OTT 訂閱者的封裝，像是 premium、 basic 和運動，可能會提供 MVPD 運算子。 運算子可能會想要相符的語彙基元與訂閱者的封裝中的宣告，以便讓特定套件中的內容可供使用。 在此情況下，自訂 STS 提供所需的彈性和控制。

當您使用自訂的 STS 時，就必須進行兩項變更：

* 當您設定資產的授權傳遞服務時，您必須指定用於自訂的 sts，而不是目前的索引鍵，從 Azure AD 驗證的安全性金鑰。 （更多詳細資料如下）。 
* 安全性金鑰產生 JTW 語彙基元時，而不是目前的 X509 私密金鑰指定 Azure AD 中的憑證。

有兩種類型的安全性金鑰：

* 對稱金鑰： 相同的金鑰用來產生並驗證 JWT。
* 非對稱金鑰： 公開-私密金鑰組在 X509 憑證會使用私密金鑰來加密/產生 JWT 與公開金鑰來驗證權杖。

> [!NOTE]
> 如果您使用.NET Framework / C# 做為開發平台，X509 用於非對稱安全性金鑰的憑證必須具有金鑰長度為至少 2048年。 這是 .NET Framework 中的 System.IdentityModel.Tokens.X509AsymmetricSecurityKey 類別的需求。 否則，會擲回下列例外狀況：

> IDX10630：用於簽署的 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' 不能小於 '2048' 位元。

## <a name="the-completed-system-and-test"></a>完整的系統和測試
本節將引導您透過下列案例中已完成的端對端系統，讓您取得登入帳戶之前，您可以有基本行為的圖片：

* 如果您需要的非整合案例：

    * 在 Media Services 中所裝載的視訊資產是未受保護或 DRM 保護但無 （發行至所有人員都要求它的授權） 的驗證權杖，您可以進行測試而不用登入。 如果您的視訊串流處理透過 HTTP，則，切換到 HTTP。

* 如果您需要的端對端整合的案例：

    * 在 Media Services 動態的 DRM 保護，Azure AD 中，所產生的 JWT 權杖的驗證與視訊資產，您需要登入。

播放程式的 web 應用程式和其登入，請參閱[此網站](https://openidconnectweb.azurewebsites.net/)。

### <a name="user-sign-in"></a>使用者登入
要測試端對端整合的 DRM 系統，您必須先建立或加入的帳戶。

哪個帳戶？

雖然 Azure 原先允許只能由 Microsoft 帳戶使用者存取，從兩個系統的使用者現在可以存取。 現在所有 Azure 屬性信任的 Azure AD 進行驗證，以及 Azure AD 驗證組織使用者。 已建立的同盟關係，讓 Azure AD 信任 Microsoft 帳戶消費者識別系統，若要驗證的使用者取用者。 如此一來，Azure AD 便可以驗證來賓 Microsoft 帳戶以及以原生 Azure AD 帳戶。

因為 Azure AD 信任 Microsoft 帳戶的網域，您可以加入任何帳戶從下列網域的任何自訂的 Azure ad 租用戶，並登入使用的帳戶：

| **網域名稱** | **網域** |
| --- | --- |
| **自訂 Azure AD 租用戶網域** |somename.onmicrosoft.com |
| **公司網域** |microsoft.com |
| **Microsoft 帳戶網域** |outlook.com、live.com、hotmail.com |

您可以連絡任何帳戶建立或加入您的作者。

下列螢幕擷取畫面顯示不同登入頁面不同的網域帳戶所使用：

**自訂的 Azure AD 租用戶的網域帳戶**: 自訂的登入頁面自訂的 Azure ad 租用戶網域。

![自訂 Azure AD 租用戶網域帳戶](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft 網域帳戶使用智慧卡**： 登入頁面自訂 microsoft 公司 IT 與雙因素驗證。

![自訂 Azure AD 租用戶網域帳戶](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft 帳戶**： 取用者的 Microsoft 帳戶的登入頁面。

![自訂 Azure AD 租用戶網域帳戶](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>使用的 PlayReady 加密的媒體擴充功能
PlayReady 支援，例如在 Windows 8.1 或更新版本的 Internet Explorer 11 和 Windows 10 上的 Microsoft Edge 瀏覽器的新式瀏覽器與加密媒體擴充功能 (EME) PlayReady 是 EME 為基礎的 DRM。

![使用 PlayReady EME](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

深色的播放程式區域是視訊的因為 PlayReady 保護防止您進行螢幕擷取的受保護。

下列螢幕擷取畫面顯示播放程式的外掛程式和 Microsoft 安全性基本資訊 (MSE) / EME 支援：

![Media Player 外掛程式 PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

在 Microsoft Edge 及 Windows 10 上的 Internet Explorer 11 EME 允許[PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/)要支援的 Windows 10 裝置上叫用。 PlayReady SL3000 解除鎖定增強的優質內容 (4k，HDR) 和新的流程內容 （如增強的內容） 的傳遞模型。

若要將焦點放在 Windows 裝置，PlayReady 是唯一的 DRM (PlayReady SL3000) 的 Windows 裝置上可用的硬體。 使用 PlayReady 透過 EME 或通用 Windows 平台應用程式並使用 PlayReady SL3000 比另一個 DRM 提供更高的視訊品質的串流服務。 一般而言，最多為 2 K 內容流經 Chrome 或 Firefox 和內容向上 4k 流程透過 Microsoft Edge/Internet Explorer 11 或通用 Windows 平台上的應用程式相同的裝置。 數量取決於服務設定和實作。

#### <a name="use-eme-for-widevine"></a>用於 Widevine EME
在支援 EME/Widevine 的現代瀏覽器上，例如 Windows 10、Windows 8.1、Mac OSX Yosemite 上的 Chrome 41+，以及 Android 4.4.4 上的 Chrome，Google Widevine 是 EME 背後的 DRM。

![用於 Widevine EME](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine 不會防止您的受保護的視訊的螢幕擷取。

![Media Player 外掛程式 Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Unentitled 的使用者
如果使用者不是 「 標題為使用者 」 群組的成員，使用者不會通過權限檢查。 多重 DRM 授權服務然後拒絕發出要求的授權，如所示。 詳細的描述是 「 授權取得失敗，「 這是專為。

![Unentitled 的使用者](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>執行自訂安全性權杖服務
如果您執行自訂 STS，JWT 是自訂 STS 所發出使用對稱或非對稱金鑰。

下列螢幕擷取畫面會顯示使用對稱金鑰 （使用 Chrome） 的案例：

![使用對稱金鑰的自訂 STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

下列螢幕擷取畫面顯示案例中使用非對稱金鑰透過 X509 憑證 （使用 Microsoft 新式瀏覽器）：

![使用非對稱金鑰的自訂 STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

在這兩個先前的情況下，使用者驗證會保持相同。 它會透過 Azure AD。 唯一的差別是自訂的 STS，而不是 Azure AD 所簽發的 Jwt。 當您設定動態 CENC 保護時，授權傳遞服務限制指定 JWT 對稱或非對稱金鑰的類型。

## <a name="summary"></a>總結
這份文件會透過權杖驗證、 設計及實作多原生 DRM 和存取控制與 CENC 討論使用 Azure Media Services，Media Player。

* 參考設計會看到包含 DRM/CENC 子系統中的所有必要元件。
* Azure Media Services，Media Player 中已顯示參考實作。
* 也討論某些設計與實作中直接相關的主題。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
