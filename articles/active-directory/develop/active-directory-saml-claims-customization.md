---
title: 自訂 SAML 權杖對 Azure AD 中的企業應用程式發出的宣告 | Microsoft Docs
description: 了解如何針對 Azure AD 中的企業應用程式，自訂 SAML 權杖中發出的宣告
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: celested
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6fe74852824c10d24729f785e5e33a17b793161
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878565"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>作法：針對 Azure AD 中的企業應用程式，自訂 SAML 權杖中發出的宣告

現在，Azure Active Directory (Azure AD) 支援單一登入 (SSO) 與大部分的企業應用程式，包括兩個預先整合的 Azure AD 應用程式庫以及自訂應用程式的應用程式。 當使用者利用 SAML 2.0 通訊協定來透過 Azure AD 向應用程式驗證時，Azure AD 會將權杖傳送給應用程式 (透過 HTTP POST)。 然後，應用程式會驗證並使用權杖將使用者登入，而不會提示輸入使用者名稱和密碼。 這些 SAML 權杖包含稱為使用者的相關資訊的片段*宣告*。

*宣告*是身分識別提供者核發權杖給使用者時，所提供的權杖內部使用者相關資訊。 在 [SAML 權杖](https://en.wikipedia.org/wiki/SAML_2.0)中，此資料通常包含在 SAML 屬性陳述式中。 使用者的唯一識別碼通常在 SAML Subject 中表示，也稱為「名稱識別碼」。

根據預設，Azure AD 會發出 SAML 權杖中包含您應用程式`NameIdentifier`宣告可唯一識別使用者的 Azure AD 中使用者的使用者名稱 （也稱為使用者主體名稱） 的值。 SAML 權杖也包含額外的宣告，其包含使用者的電子郵件地址、名字和姓氏。

若要檢視或編輯在 SAML 權杖中對應用程式發出的宣告，請在 Azure 入口網站中開啟應用程式。 然後開啟**使用者屬性] & [宣告**一節。

![使用者屬性與宣告區段](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

編輯在 SAML 權杖中簽發的宣告有兩個可能原因：

* 應用程式需要`NameIdentifier`或 NameID 宣告必須是儲存在 Azure AD 使用者名稱 （或使用者主體名稱） 以外的項目。
* 應用程式是設計為要求不同的宣告 URI 組或宣告值。

## <a name="editing-nameid"></a>編輯 NameID

若要編輯 NameID （名稱識別項值）：

1. 開啟**命名的識別項值**頁面。
1. 選取您想要套用至屬性的轉換的屬性。 （選擇性） 您可以指定您想要他有的 NameID 宣告的格式。

   ![編輯 NameID （名稱識別項） 值](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID 格式

如果 SAML 要求中包含以特定格式的 NameIDPolicy 元素，Azure AD 會接受要求的格式。

如果 SAML 要求不包含 NameIDPolicy 元素，然後 Azure AD 會發出 NameID 與您指定的格式。 如果沒有指定任何格式是 Azure AD 會使用選取的宣告來源相關聯的預設來源格式。

從**選擇名稱識別碼格式**下拉式清單中，您可以選取下列選項之一。

| NameID 格式 | 描述 |
|---------------|-------------|
| **預設值** | Azure AD 會使用預設的來源格式。 |
| **持續性** | Azure AD 會使用永續做為 NameID 格式。 |
| **EmailAddress** | Azure AD 會使用 EmailAddress 做為 NameID 格式。 |
| **不透露** | Azure AD 會使用未指定，做為 NameID 格式。 |
| **暫時性** | Azure AD 會使用暫時性做為 NameID 格式。 |

若要深入了解 NameIDPolicy 屬性，請參閱[單一登入 SAML 通訊協定](single-sign-on-saml-protocol.md)。

### <a name="attributes"></a>屬性

選取 `NameIdentifier` (或 NameID) 宣告的所需來源。 您可以從下列選項選取。

| 名稱 | 描述 |
|------|-------------|
| 電子郵件 | 使用者的電子郵件地址 |
| userprincipalName | 使用者主體名稱 (UPN)，使用者的 |
| onpremisessamaccount | 已從內部部署 Azure AD 同步處理的 SAM 帳戶名稱 |
| objectid | 在 Azure AD 中使用者的 objectid |
| employeeid | 使用者 employeeid |
| 目錄擴充 | 目錄擴充[從使用 Azure AD Connect 同步的內部部署 Active Directory 同步處理](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| 擴充屬性 1-15 | 內部部署擴充屬性，用來擴充 Azure AD 結構描述 |

如需詳細資訊，請參閱[表 3:每個來源的有效識別碼值](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)。

### <a name="special-claims---transformations"></a>特殊宣告-轉換

您也可以使用宣告轉換函式。

| 函式 | 描述 |
|----------|-------------|
| **ExtractMailPrefix()** | 移除電子郵件地址或使用者主體名稱的網域尾碼。 這只會擷取使用者名稱的第一個部分 (例如，"joe_smith" 而不是 joe_smith@contoso.com)。 |
| **Join()** | 加入具有已驗證網域的屬性。 如果選取的使用者識別碼值具有網域，它會擷取使用者名稱以附加所選的已驗證網域。 例如，如果您選取電子郵件 (joe_smith@contoso.com) 作為使用者識別碼值，並選取 contoso.onmicrosoft.com 作為已驗證的網域，這樣會產生 joe_smith@contoso.onmicrosoft.com。 |
| **ToLower()** | 將所選取屬性中的字元轉換成小寫字元。 |
| **ToUpper()** | 將所選取屬性中的字元轉換成大寫字元。 |

## <a name="adding-application-specific-claims"></a>新增應用程式特定的宣告

若要新增應用程式特定的宣告：

1. 在**使用者屬性和宣告**，選取**新增新的宣告**以開啟**管理使用者宣告**頁面。
1. 請輸入**名稱**的宣告。 值不嚴格遵循 URI 模式，每個 SAML 規格的需要。如果您需要的 URI 模式，您可以將它放**命名空間**欄位。
1. 選取 **來源**宣告要擷取其值。 您可以從 來源 屬性下拉式清單中選取 使用者屬性或轉換套用至的使用者屬性，再發出做為宣告。

### <a name="application-specific-claims---transformations"></a>應用程式特定的宣告集轉換

您也可以使用宣告轉換函式。

| 函式 | 描述 |
|----------|-------------|
| **ExtractMailPrefix()** | 移除電子郵件地址或使用者主體名稱的網域尾碼。 這只會擷取使用者名稱的第一個部分 (例如，"joe_smith" 而不是 joe_smith@contoso.com)。 |
| **Join()** | 藉由聯結兩個屬性建立新的值。 （選擇性） 您可以使用兩個屬性之間的分隔符號。 |
| **ToLower()** | 將所選取屬性中的字元轉換成小寫字元。 |
| **ToUpper()** | 將所選取屬性中的字元轉換成大寫字元。 |
| **Contains （)** | 輸出的屬性或常數，如果輸入符合指定的值。 否則您可以指定另一個輸出，如果沒有相符項目。<br/>例如，如果您想要發出的宣告，其中的值是使用者的電子郵件地址時如果它包含網域"@contoso.com」，否則在您想要輸出的使用者主體名稱。 若要這樣做，您會設定下列值：<br/>*參數 1(input)*: user.email<br/>*值*: 「@contoso.com"<br/>參數 2 （輸出）： user.email<br/>參數 3 （如果沒有相符的輸出）： user.userprincipalname |
| **EndWith()** | 輸出的屬性或常數，如果輸入結束使用指定的值。 否則您可以指定另一個輸出，如果沒有相符項目。<br/>比方說，如果您想要發出的宣告，其中的值是使用者的 employeeid 如果 employeeid 結尾為"000"，否則您想要輸出的延伸模組屬性。 若要這樣做，您會設定下列值：<br/>*參數 1(input)*: user.employeeid<br/>*值*："000"<br/>參數 2 （輸出）： user.employeeid<br/>參數 3 （如果沒有相符的輸出）： user.extensionattribute1 |
| **StartWith()** | 輸出的屬性或常數，如果輸入的開頭指定的值。 否則您可以指定另一個輸出，如果沒有相符項目。<br/>比方說，如果您想要發出的宣告，其中的值是使用者的 employeeid 如果國家/地區的開頭 「 美國 」，否則您想要輸出的延伸模組屬性。 若要這樣做，您會設定下列值：<br/>*參數 1(input)*: user.country<br/>*值*：「 我們的 」<br/>參數 2 （輸出）： user.employeeid<br/>參數 3 （如果沒有相符的輸出）： user.extensionattribute1 |
| **Extract （)-比對之後** | 它會比對指定的值之後，傳回子字串。<br/>比方說，如果輸入的值為"Finance_BSimon 」，相符的值是"Finance_ 」，然後宣告的輸出是"是 BSimon"。 |
| **Extract （)-之前比對** | 傳回子字串，直到比對指定的值。<br/>比方說，如果輸入的值為"BSimon_US 」，相符的值是"_US 」，然後宣告的輸出是"是 BSimon"。 |
| **Extract （)-之間比對** | 傳回子字串，直到比對指定的值。<br/>比方說，如果輸入的值為"Finance_BSimon_US 」，第一個相符的值是"Finance_ 」，第二個相符的值是"_US 」，然後宣告的輸出是"是 BSimon"。 |
| **ExtractAlpha()-前置詞** | 傳回字串的前置詞依字母順序排列的一部分。<br/>例如，如果輸入的值為"BSimon_123 」，然後它會傳回"是 BSimon"。 |
| **ExtractAlpha() - Suffix** | 傳回字串的後置詞依字母順序排列的一部分。<br/>例如，如果輸入的值為"123_Simon 」，然後它會傳回"是 BSimon"。 |
| **ExtractNumeric() - Prefix** | 傳回字串的前置詞數值部分。<br/>例如，如果輸入的值為"123_BSimon 」，然後它會傳回"123"。 |
| **ExtractNumeric() - Suffix** | 傳回字串的後置詞數值部分。<br/>例如，如果輸入的值為"BSimon_123 」，然後它會傳回"123"。 |
| **IfEmpty()** | 輸出的屬性或常數，如果輸入是 null 或空白。<br/>比方說，如果您想要輸出儲存在 extensionattribute，如果是空的指定使用者的 employeeid 屬性。 若要這樣做，您會設定下列值：<br/>參數 1(input): user.employeeid<br/>參數 2 （輸出）： user.extensionattribute1<br/>參數 3 （如果沒有相符的輸出）： user.employeeid |
| **IfNotEmpty()** | 輸出的屬性或常數，如果輸入不是 null 或空白。<br/>比方說，如果您想要輸出儲存在 extensionattribute，如果指定使用者的 employeeid 不是空的屬性。 若要這樣做，您會設定下列值：<br/>參數 1(input): user.employeeid<br/>參數 2 （輸出）： user.extensionattribute1 |

如果您需要額外的轉換，請提交您的想法，在[在 Azure AD 中的意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599)下方*SaaS 應用程式*類別目錄。

## <a name="next-steps"></a>後續步驟

* [在 Azure AD 中的應用程式管理](../manage-apps/what-is-application-management.md)
* [針對不在 Azure AD 應用程式資源庫中的應用程式設定單一登入](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [針對 SAML 型單一登入進行疑難排解](howto-v1-debug-saml-sso-issues.md)
