---
title: 重新導向 URI/回覆 URL 限制事項-Microsoft 身分識別平台
description: 回覆 Url/重新導向 Url 限制和限制
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: article
ms.subservice: develop
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07be7d0c70193fec88782fea681e33d6b4cf4b40
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486229"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>重新導向 URI/回覆 URL 限制

重新導向 URI 或回覆 URL] 是 [已成功授權，授權伺服器會將使用者傳送一次應用程式的位置，並授與的授權碼或存取權杖。 包含程式碼或權杖中重新導向 URI 或回覆 token，因此請務必您註冊應用程式註冊程序的一部分使用正確的位置。

## <a name="maximum-number-of-redirect-uris"></a>重新導向 Uri 的最大數目

下表顯示重新導向 Uri，當您註冊您的應用程式時，您可以新增的最大數目。 

| 登入的帳戶 | 重新導向 Uri 的最大數目 | 描述 |
|--------------------------|---------------------------------|-------------|
| Microsoft 公司或學校帳戶，在任何組織的 Azure Active Directory (Azure AD) 租用戶 | 256 | `signInAudience` 在應用程式資訊清單中的欄位會設為*AzureADMyOrg*或*AzureADMultipleOrgs* |
| 個人 Microsoft 帳戶和工作和學校帳戶 | 100 | `signInAudience` 在應用程式資訊清單中的欄位設定為*AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>URI 的最大長度

您可以使用 256 個字元，最多為每個重新導向 URI，您將新增至應用程式註冊。

## <a name="restrictions-using-a-wildcard-in-uris"></a>在 Uri 中使用萬用字元的限制

萬用字元 Uri，例如`https://*.contoso.com`，這會很方便，但應該避免。 使用萬用字元，在重新導向 URI 會有安全性顧慮。 根據 OAuth 2.0 規格 ([一節的 RFC 6749 3.1.2](https://tools.ietf.org/html/rfc6749#section-3.1.2))，重新導向端點 URI 必須是絕對 URI。 

Azure AD 應用程式模型不支援萬用字元 Uri 的應用程式，會設定為登入個人 Microsoft 帳戶與工作或學校帳戶。 不過，萬用字元 Uri 會允許登入工作或學校組織的 Azure AD 租用戶中的帳戶目前設定的應用程式。 
 
> [!NOTE]
> 新[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗不允許開發人員在 UI 上將萬用字元 Uri。 新增萬用字元 URI 之應用程式的登入工作或學校帳戶只能透過應用程式資訊清單編輯器支援。 從現在開始，新的應用程式將無法在重新導向 URI 中使用萬用字元。 不過，較舊的應用程式包含萬用字元，在重新導向 Uri 會繼續運作。

如果您的案例需要多個重新導向 Uri 超過最大限制允許，而不是新增萬用字元重新導向 URI，請考慮下列方法之一。

### <a name="use-a-state-parameter"></a>使用 state 參數

如果您有許多子網域，而且如果您的案例會要求您驗證成功後的使用者重新導向至相同的頁面啟動的位置，請使用 state 參數可能會很有幫助。 

在這種方法：

1. 建立每個處理您從授權端點接收安全性權杖的應用程式的 「 共用 」 重新導向 URI。
1. 您的應用程式可以傳送為 state 參數中的應用程式特定的參數 （例如，產生的使用者，或任何項目像是商標資訊的子網域 URL)。 當使用狀態參數，防範 CSRF 防護，如中所指定[區段 10.12 的 RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12))。 
1. 應用程式特有參數會包含要呈現正確的應用程式體驗的使用者，也就是，建構適當的應用程式狀態所需的所有資訊。 從狀態參數的 HTML 因此請確定您的 Azure AD 授權端點區域不會傳遞 HTML 內容中此參數。
1. 當 Azure AD 傳送到 「 共用 」 重新導向 URI 的回應時，會傳送狀態參數傳回給應用程式。
1. 然後應用程式可以使用值為 state 參數中判斷要進一步傳送使用者的 URL。 請確定您 CSRF 防護的驗證。

> [!NOTE]
> 此方法可讓遭到入侵的用戶端，若要修改的狀態參數，藉此將使用者重新導向至不同的 URL，也就是在傳送的其他參數[開啟重新導向程式威脅](https://tools.ietf.org/html/rfc6819#section-4.2.4)RFC 6819 中所述。 因此，用戶端也必須保護這些參數，藉由加密狀態，或其他等驗證語彙基元的重新導向 URI 中的網域名稱的方式來進行驗證。

### <a name="add-redirect-uris-to-service-principals"></a>新增重新導向 Uri 與服務主體

另一種方法是新增重新導向 Uri[服務主體](app-objects-and-service-principals.md#application-and-service-principal-relationship)表示在任何 Azure AD 租用戶中的應用程式註冊。 您無法使用狀態參數，或您的案例需要您將新的重新導向 Uri 新增至應用程式註冊，如您所支援的每個新租用戶時，您可以使用這種方法。 

## <a name="next-steps"></a>後續步驟

- 深入了解[應用程式資訊清單](reference-app-manifest.md)
