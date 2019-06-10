---
title: 使用 API 管理中的用戶端憑證驗證保護 API - Azure API 管理 | Microsoft Docs
description: 了解如何使用用戶端憑證來保護對 API 的存取
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 5427c4050b6b70c18da7a1899d16e448c41e81c6
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427323"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>如何使用 API 管理中的用戶端憑證驗證保護 API

API 管理提供以用戶端憑證保護對 API 之存取 (例如，用戶端對 API 管理) 的功能。 您可以驗證連入的憑證，並檢查憑證內容，針對想要使用原則運算式的值。

如需保護後端服務的 API 使用用戶端憑證 （亦即，API 管理到後端） 的存取權的資訊，請參閱[如何保護後端服務使用用戶端憑證驗證](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> 接收並確認使用情況層中的用戶端憑證必須先開啟 [要求用戶端憑證] 設定的 [自訂網域] 刀鋒視窗上，如下所示。

![要求用戶端憑證](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>檢查簽發者和主旨

您可以設定下面的原則來檢查用戶端憑證的簽發者和主旨：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 若要停用檢查憑證撤銷清單使用`context.Request.Certificate.VerifyNoRevocation()`而不是`context.Request.Certificate.Verify()`。
> 如果用戶端憑證是自我簽署，根 （或中繼） 必須是 CA 憑證[上傳](api-management-howto-ca-certificates.md)至 API 管理，如`context.Request.Certificate.Verify()`和`context.Request.Certificate.VerifyNoRevocation()`運作。

## <a name="checking-the-thumbprint"></a>檢查指紋

您可以設定下面的原則來檢查用戶端憑證的指紋：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 若要停用檢查憑證撤銷清單使用`context.Request.Certificate.VerifyNoRevocation()`而不是`context.Request.Certificate.Verify()`。
> 如果用戶端憑證是自我簽署，根 （或中繼） 必須是 CA 憑證[上傳](api-management-howto-ca-certificates.md)至 API 管理，如`context.Request.Certificate.Verify()`和`context.Request.Certificate.VerifyNoRevocation()`運作。

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>檢查指紋是否符合已上傳到 API 管理的憑證

下列範例說明如何檢查用戶端憑證的指紋是否符合已上傳到 API 管理的憑證：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> 若要停用檢查憑證撤銷清單使用`context.Request.Certificate.VerifyNoRevocation()`而不是`context.Request.Certificate.Verify()`。
> 如果用戶端憑證是自我簽署，根 （或中繼） 必須是 CA 憑證[上傳](api-management-howto-ca-certificates.md)至 API 管理，如`context.Request.Certificate.Verify()`和`context.Request.Certificate.VerifyNoRevocation()`運作。

> [!TIP]
> 用戶端憑證的死結問題在此所述[一文](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672)可能會出現在數種方式，例如要求凍結，要求結果`403 Forbidden`之後逾時，狀態碼`context.Request.Certificate`是`null`。 此問題通常會影響`POST`和`PUT`大約 60 KB 或更高的內容長度要求。
> 若要避免這個問題發生開啟所需的主機名稱，如下所示的 [自訂網域] 刀鋒視窗上的 「 Negotiate 用戶端憑證 」 設定。 這項功能不適用於使用情況層。

![交涉用戶端憑證](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>後續步驟

-   [如何使用用戶端憑證驗證來保護後端服務](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [如何上傳憑證](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
