---
title: 設定應用程式的發行者網域 |Azure
description: 了解如何設定，讓使用者知道他們的資訊會被傳送的應用程式的發行者網域。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d47075f9e18b299341a98983ffb8a47389fd7063
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540225"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>HOW TO：設定應用程式的發行者網域 （預覽）

應用程式的發行者網域上，會顯示給使用者[應用程式的同意提示](application-consent-experience.md)，讓使用者知道正在傳送他們的資訊。 多租用戶註冊的應用程式在 2019 5 月 21 日之後沒有發行者網域的形式出現**未經驗證**。 多租用戶應用程式都支援單一組織的目錄; 外部帳戶的應用程式例如，支援所有的 Azure AD 帳戶，或支援所有的 Azure AD 帳戶和個人 Microsoft 帳戶。

## <a name="new-applications"></a>新的應用程式

當您註冊新的應用程式時，您的應用程式發行者網域可能會設定為預設值中。 值取決於應用程式註冊所在，尤其是是否在租用戶中註冊應用程式和租用戶有租用戶是否已驗證的網域。

如果有租用戶驗證的網域，應用程式的發行者網域將會預設為主要租用戶的已驗證的網域。 如果有任何租用戶已驗證的網域 （不在租用戶中註冊應用程式時，此為案例）、 應用程式的發行者網域將會設定為 null。

下表摘要說明 「 發行者 」 的定義域值的預設行為。  

| 租用戶驗證的網域 | 預設值為發行者網域 |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>-domain2.com （主要） | domain2.com |

如果未設定的多租用戶應用程式發行者網域，或如果它設定為結尾的網域。 onmicrosoft.com，將會顯示應用程式的同意提示**未經驗證**發行者網域取代。

## <a name="grandfathered-applications"></a>Grandfathered 應用程式

如果在 2019，5 月 21 日之前註冊您的應用程式不會顯示您的應用程式的同意提示**未經驗證**如果您尚未設定發行者網域。 我們建議您設定 「 發行者 」 端的定義域值，讓使用者可以看到這項資訊在您的應用程式的同意提示。

## <a name="configure-publisher-domain-using-the-azure-portal"></a>設定使用 Azure 入口網站的發行者網域

若要設定您的應用程式發行者網域，請遵循下列步驟。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。

1. 您的帳戶是否存在於一個以上的 Azure AD 租用戶：
   1. 從右上角的頁面上，功能表中選取您的設定檔，然後**切換目錄**。
   1. 將您的工作階段變更為您想要用來建立您的應用程式的 Azure AD 租用戶中。

1. 瀏覽至[Azure Active Directory > 應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)來尋找並選取您想要設定的應用程式。

   一旦您已選取應用程式，您會看到應用程式的**概觀**頁面。

1. 從應用程式的**概觀**頁面上，選取**商標**一節。

1. 尋找**發行者網域**欄位，然後選取下列選項之一：

   - 選取 **設定網域**如果您還沒有已設定的網域。
   - 選取 **更新網域**如果已設定的網域。

如果租用戶中註冊您的應用程式，您會看到從選取的兩個索引標籤：**選取 已驗證的網域**並**確認新的定義域**。

如果您的應用程式未登錄在租用戶中，您只會看到 選擇驗證您的應用程式的新網域。

### <a name="to-verify-a-new-domain-for-your-app"></a>若要確認您的應用程式的新網域

1. 建立名為`microsoft-identity-association.json`並貼上下列 JSON 程式碼片段。

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. 取代預留位置 *{您的應用程式-ID-這裡}* 對應至您的應用程式的應用程式 （用戶端） 識別碼。

1. 裝載的檔案： `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`。 取代預留位置 *{您的網域-這裡}* 來比對已驗證的網域。

1. 按一下 [**驗證並儲存網域**] 按鈕。

### <a name="to-select-a-verified-domain"></a>若要選取的已驗證的網域

- 如果您的租用戶已驗證網域，選取其中一個來自網域**選取 已驗證的網域**下拉式清單。

## <a name="implications-on-the-app-consent-prompt"></a>影響應用程式的同意提示

設定發行者網域將會影響使用者看到的內容上的應用程式的同意提示。 若要完全了解同意提示的元件，請參閱[了解應用程式同意體驗](application-consent-experience.md)。

下表描述在 2019 5 月 21 日之前建立的應用程式的行為。

![2019 5 月 21 日之前建立的應用程式的同意提示](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

2019 5 月 21 日之後建立新的應用程式的行為將取決於發行者網域和應用程式的類型。 下表說明您應該會看到不同的設定組合的變更。

![2019 5 月 21 日之後建立的應用程式的同意提示](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>在重新導向 Uri 的含意

與任何工作或學校帳戶或個人 Microsoft 帳戶的使用者登入的應用程式 ([多租用戶](single-and-multi-tenant-apps.md)) 會受限於幾項限制時指定重新導向 Uri。

### <a name="single-root-domain-restriction"></a>單一根網域限制

當多租用戶應用程式的發行者網域值設定為 null，應用程式僅限於共用單一根網域的重新導向 Uri。 例如，下列值的組合不允許因為根網域，contoso.com，不符合 fabrikam.com。

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>子網域限制

子網域，但您必須明確註冊根網域。 例如，下列 Uri 會共用單一根網域，而不允許的組合。

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

不過，如果開發人員明確新增根網域，被允許的組合。

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>例外狀況

下列情況下不受限於單一根網域限制：

- 單一租用戶應用程式或在單一目錄中的帳戶為目標的應用程式
- 使用 localhost 作為重新導向 Uri 的
- 使用自訂配置 （非 HTTP 或 HTTPS） 的重新導向 Uri

## <a name="configure-publisher-domain-programmatically"></a>以程式設計方式設定發行者網域

目前，沒有 REST API 或 PowerShell 以程式設計方式設定 「 發行者 」 網域支援。
