---
title: 了解並解決 Azure AD 應用程式 Proxy CORS 問題
description: 提供了解 Azure AD 應用程式 Proxy，以及如何找出並解決 CORS 問題的 CORS。
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 2b6adcf4231aa44a4f28d277e963efa16de8af81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399336"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>了解並解決 Azure Active Directory 應用程式 Proxy CORS 問題

[跨原始資源共用 (CORS)](http://www.w3.org/TR/cors/) 有時可以呈現您透過 Azure Active Directory 應用程式 Proxy 發佈應用程式和 Api 的挑戰。 這篇文章討論的 Azure AD 應用程式 Proxy CORS 問題和解決方案。

瀏覽器安全性通常會防止網頁對另一個網域提出 AJAX 要求。 這項限制稱為*同源原則*，可防止惡意網站從另一個網站讀取敏感性資料。 不過，有時候您可能想要讓呼叫您的 web API 的其他站台。 CORS 是 W3C 標準，可讓伺服器放寬同源原則，並允許某些跨源要求，並拒絕其他。

## <a name="understand-and-identify-cors-issues"></a>了解並找出 CORS 問題

兩個 Url 有相同的原點，如果它們有相同的配置、 主機和連接埠 ([RFC 6454](https://tools.ietf.org/html/rfc6454))，例如：

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

下列 Url 會有兩個不同的來源，比上一個：

-   http:\//contoso.net-不同的網域
-   http:\//contoso.com:9000/foo.html-不同的連接埠
-   https:\//contoso.com/foo.html-不同的配置
-   http:\//www.contoso.com/foo.html-不同的子網域

同源原則可防止應用程式從其他來源存取資源，除非它們使用正確的存取控制標頭。 如果 CORS 標頭不存在或不正確，跨原始來源要求將會失敗。 

您可以使用瀏覽器偵錯工具，以識別 CORS 問題：

1. 啟動瀏覽器並瀏覽至 web 應用程式。
1. 按下**F12**即可啟動偵錯主控台。
1. 嘗試重現交易，並檢閱主控台訊息。 CORS 違規情形會產生原始的主控台錯誤。

在下列螢幕擷取畫面中，選取**試試** 按鈕會造成 CORS 錯誤訊息，https:\//corswebclient-contoso.msappproxy.net 找不到存取控制項-允許-原始標頭中。

![CORS 問題](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>使用應用程式 Proxy 的 CORS 挑戰

下列範例示範典型 Azure AD 應用程式 Proxy CORS 案例。 內部伺服器主機**CORSWebService** web API 控制器，以及**CORSWebClient**呼叫 **CORSWebService**。 從 AJAX 要求**CORSWebClient**要**CORSWebService**。

![在內部部署相同原始來源要求](./media/application-proxy-understand-cors-issues/image1.png)

當您裝載在內部，但要載入的失敗或發生錯誤時透過 Azure AD 應用程式 Proxy 發佈時，就會運作 CORSWebClient 應用程式。 如果您分別為不同的應用程式，透過應用程式 Proxy 發佈的 CORSWebClient 和 CORSWebService 應用程式，就會將兩個應用程式裝載於不同的網域。 CORSWebService 的 AJAX 要求從 CORSWebClient 是跨原始來源要求，而且它會失敗。

![應用程式 Proxy CORS 要求](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>應用程式 Proxy CORS 問題的解決方案

您可以解決上述的 CORS 問題，任何一種數種方式。

### <a name="option-1-set-up-a-custom-domain"></a>選項 1：設定自訂網域

使用 Azure AD 應用程式 Proxy[自訂網域](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains)發行相同的來源，而不必對應用程式的原始來源、 程式碼或標頭中的任何變更。 

### <a name="option-2-publish-the-parent-directory"></a>選項 2：發行的上層目錄

發佈兩個應用程式的上層目錄。 這特別是良好，如果您有只有兩個應用程式 web 伺服器上的方案可以運作。 而不是個別發行每個應用程式，您可以發佈常見的父代目錄，這會產生相同的來源。

下列範例會顯示入口網站 CORSWebClient 應用程式的 Azure AD 應用程式 Proxy 頁面。  當**內部 URL**設為*contoso.com/CORSWebClient*，應用程式無法進行要求成功*contoso.com/CORSWebService*目錄中，因為它們是跨原始來源。 

![個別發佈的應用程式](./media/application-proxy-understand-cors-issues/image4.png)

相反地，設定**內部 URL**發佈父代目錄，其中同時包含*CORSWebClient*並*CORSWebService*目錄：

![發佈上層目錄](./media/application-proxy-understand-cors-issues/image5.png)

產生的應用程式 Url 有效地解決 CORS 問題：

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>選項 3：更新 HTTP 標頭

尋找相符的原始來源要求對 web 服務中新增自訂的 HTTP 回應標頭。 網際網路資訊服務 (IIS) 中執行的網站，使用 IIS 管理員來修改標頭：

![在 [IIS 管理員] 中新增自訂的回應標頭](./media/application-proxy-understand-cors-issues/image6.png)

這項修改都不需要變更任何程式碼。 您可以確認它在 Fiddler 追蹤：

**Post 標頭加入**\
HTTP/1.1 200 OK\
Cache-control︰ 否 cache\
Pragma: no cache\
內容類型： text/plain;charset = utf-8 \
到期:-1
而有所不同：接受 Encoding\
伺服器：Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**存取控制-允許的原始來源： https://corswebclient-contoso.msappproxy.net** \
X-AspNet-Version:4.0.30319\
X 供電的：ASP.NET\
內容長度：17

### <a name="option-4-modify-the-app"></a>選項 4：修改應用程式

您可以變更您的應用程式，以支援 CORS 新增存取控制-允許-原始標頭，以適當的值。 若要加入標頭的方式取決於應用程式的程式碼語言。 變更程式碼是最不建議的選項，，因為它需要的大部分工作。

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>選項 5:擴充存取權杖存留的期

一些 CORS 問題無法解析的例如當您的應用程式重新導向至*login.microsoftonline.com*進行驗證，而存取權杖到期。 CORS 呼叫，則會失敗。 此案例的因應措施是要擴充的存取權杖，以防止它過期的使用者工作階段存留期。 如需如何執行這項操作的詳細資訊，請參閱[在 Azure AD 中的設定權杖存留期](../develop/active-directory-configurable-token-lifetimes.md)。

## <a name="see-also"></a>請參閱
- [教學課程：Azure Active Directory 中新增透過應用程式 Proxy 遠端存取內部部署應用程式](application-proxy-add-on-premises-application.md) 
- [規劃 Azure AD 應用程式 Proxy 部署](application-proxy-deployment-plan.md) 
- [在內部部署應用程式，透過 Azure Active Directory 應用程式 Proxy 遠端存取](application-proxy.md) 
