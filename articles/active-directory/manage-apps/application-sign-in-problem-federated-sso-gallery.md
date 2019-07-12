---
title: 登入針對同盟單一登入設定之資源庫應用程式的問題 | Microsoft Docs
description: 當登入您已使用 Azure AD 針對 SAML 型同盟單一登入設定之應用程式時所發生特定錯誤的指引
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: mimart
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1985b7bbcfdaab2aa303f67a9b1d090c85eedd5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825199"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>登入針對同盟單一登入設定之資源庫應用程式的問題

若要疑難排解下方的登入問題，我們建議您遵循這些建議，以取得更好的診斷，並自動解決步驟：

- 安裝[My Apps 安全瀏覽器延伸模組](access-panel-extension-problem-installing.md)協助 Azure Active Directory (Azure AD)，以提供更好的診斷和使用的測試時的解決方式體驗在 Azure 入口網站中。
- 重現錯誤時在 Azure 入口網站中的 [應用程式組態] 頁面中使用的測試經驗。 深入了解[偵錯 SAML 型單一登入應用程式](../develop/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>在目錄中找不到應用程式

*錯誤 AADSTS70001：應用程式識別碼為 ' https:\//contoso.com' 找不到目錄中*。

**可能的原因**

`Issuer`從 SAML 要求中的 Azure ad 應用程式傳送的屬性不符合已針對 Azure AD 中的應用程式的識別碼值。

**解決方案**

請確認`Issuer`SAML 要求中的屬性符合 Azure AD 中設定的識別碼值。 如果您使用[測試經驗](../develop/howto-v1-debug-saml-sso-issues.md)在 My Apps 安全瀏覽器延伸模組的 Azure 入口網站，您不需要手動進行這些步驟。

1.  開啟 [Azure 入口網站  ](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

1.  開啟**Azure Active Directory 延伸模組**藉由選取**所有服務**左側主導覽功能表的頂端。

1.  型別 **[Azure Active Directory]** 篩選搜尋方塊，然後選取**Azure Active Directory**項目。

1.  選取 **企業應用程式**Azure Active Directory 左側導覽功能表。

1.  選取 [所有應用程式]  ，以檢視所有應用程式的清單。

    若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單]  頂端的 [篩選]  控制項，並將 [顯示]  選項設定為 [所有應用程式]  。

1.  選取您要設為單一登入的應用程式。

1.  在應用程式載入後，開啟 [基本 SAML 組態]  。 確認 [識別碼] 文字方塊中的值符合錯誤中所顯示的識別碼值的值。



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>回覆位址與應用程式設定的回覆位址不符

*錯誤 AADSTS50011：回覆位址 ' https:\//contoso.com' 不符合為應用程式設定的回覆地址*

**可能的原因**

`AssertionConsumerServiceURL` SAML 要求中的值不符合模式的 Azure AD 中設定的回覆 URL 值。 `AssertionConsumerServiceURL` SAML 要求中的值是您在錯誤中看到的 URL。

**解決方案**

請確認`AssertionConsumerServiceURL`SAML 要求中的值符合 Azure AD 中設定的回覆 URL 值。 如果您使用[測試經驗](../develop/howto-v1-debug-saml-sso-issues.md)在 My Apps 安全瀏覽器延伸模組的 Azure 入口網站，您不需要手動進行這些步驟。

1.  開啟 [Azure 入口網站  ](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

1.  開啟**Azure Active Directory 延伸模組**藉由選取**所有服務**左側主導覽功能表的頂端。

1.  型別 **[Azure Active Directory]** 篩選搜尋方塊，然後選取**Azure Active Directory**項目。

1.  選取 **企業應用程式**Azure Active Directory 左側導覽功能表。

1.  選取 [所有應用程式]  ，以檢視所有應用程式的清單。

    若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單]  頂端的 [篩選]  控制項，並將 [顯示]  選項設定為 [所有應用程式]  。

1.  選取您要設為單一登入的應用程式。

1.  在應用程式載入後，開啟 [基本 SAML 組態]  。 確認或更新以符合 [回覆 URL] 文字方塊中的值`AssertionConsumerServiceURL`SAML 要求中的值。    
    
您已在 Azure AD 中，更新 [回覆 URL] 值，而且它符合 SAML 要求中的應用程式所傳送的值之後，您應該能夠登入應用程式。

## <a name="user-not-assigned-a-role"></a>使用者未指派角色

*錯誤 AADSTS50105：登入的使用者 ' brian\@contoso.com' 未指派給應用程式角色*。

**可能的原因**

尚未將 Azure AD 中應用程式的存取權授與使用者。

**解決方案**

若要直接將一或多個使用者指派至應用程式，請遵循下列步驟。 如果您使用[測試經驗](../develop/howto-v1-debug-saml-sso-issues.md)在 My Apps 安全瀏覽器延伸模組的 Azure 入口網站，您不需要手動進行這些步驟。

1.  開啟 [Azure 入口網站  ](https://portal.azure.com/)，以**全域管理員**身分登入。

1.  開啟**Azure Active Directory 延伸模組**藉由選取**所有服務**左側主導覽功能表的頂端。

1.  型別 **「 Azure Active Directory**"在篩選搜尋方塊，然後選取**Azure Active Directory**項目。

1.  選取 **企業應用程式**Azure Active Directory 左側導覽功能表。

1.  選取 [所有應用程式]  ，以檢視所有應用程式的清單。

    若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單]  頂端的 [篩選]  控制項，並將 [顯示]  選項設定為 [所有應用程式]  。

1.  從應用程式清單中，選取您想要指派使用者。

1.  應用程式載入之後，選取**使用者和群組**應用程式的左側導覽功能表。

1.  按一下 [使用者和群組]  清單頂端的 [新增]  按鈕，以開啟 [新增指派]  窗格。

1.  選取 [新增指派]  窗格中的 [使用者和群組]  選取器。

1. 在 [依名稱或電子郵件地址搜尋]  搜尋方塊中，針對您要新增的使用者輸入其完整名稱或電子郵件地址。

1. 將滑鼠停留在清單中的**使用者**上方，以顯示**核取方塊**。 按一下 使用者的設定檔照片或標誌，以將使用者加入旁邊的核取方塊**Selected**清單。

1. **選擇性：** 如果您想要**加入一個以上的使用者**中，輸入另一個完整的名稱或電子郵件地址插入**依名稱或電子郵件地址**搜尋 方塊中，然後按一下核取方塊，以將使用者加入**選取**清單。

1. 當您完成選取使用者時，按一下**選取**按鈕以新增至使用者和群組指派給應用程式的清單。

1. **選擇性：** 按一下 **選取的角色**中的選取器**新增指派**窗格，即可選取要指派給您已選取的使用者角色。

1. 按一下 [指派]  按鈕，將應用程式指派給選取的使用者。

一段簡短時間，您已選取的使用者將能夠啟動這些應用程式使用解決方案描述一節中所述的方法。

## <a name="not-a-valid-saml-request"></a>不是有效的 SAML 要求

*錯誤 AADSTS75005：要求不是有效的 Saml2 通訊協定訊息。*

**可能的原因**

Azure AD 不支援進行單一登入應用程式所傳送的 SAML 要求。 以下為一些常見問題：

-   SAML 要求中遺漏必要的欄位
-   SAML 要求編碼方法

**解決方案**

1. 擷取 SAML 要求。 在本教學課程[如何在 Azure AD 中的偵錯 SAML 型單一登入應用程式](../develop/howto-v1-debug-saml-sso-issues.md)以了解如何擷取 SAML 要求。

1. 請連絡應用程式廠商並提供下列資訊︰

   -   SAML 要求

   -   [Azure AD 單一登入 SAML 通訊協定需求](../develop/single-sign-on-saml-protocol.md)

應用程式廠商應該驗證，還支援 Azure AD SAML 實作進行單一登入。

## <a name="misconfigured-application"></a>設定不正確的應用程式

*錯誤 AADSTS650056:設定不正確的應用程式。這可能是下列其中一個原因所造成：用戶端具有未列出任何 ' AAD Graph' 要求的權限，在用戶端應用程式註冊中的權限。或者，系統管理員尚未同意的租用戶中。或者，您也可以檢查應用程式中的識別項的要求，以確保其符合設定的用戶端應用程式識別碼。請連絡您的系統管理員修正組態，或代表租用戶的同意。*

**可能的原因**

`Issuer`從 SAML 要求中的 Azure ad 應用程式傳送的屬性不符合為應用程式在 Azure AD 中設定的識別碼值。

**解決方案**

請確認`Issuer`SAML 要求中的屬性符合 Azure AD 中設定的識別碼值。 如果您使用[測試經驗](../develop/howto-v1-debug-saml-sso-issues.md)在 My Apps 安全瀏覽器延伸模組的 Azure 入口網站，您不需要手動進行下列步驟：

1.  開啟 [Azure 入口網站  ](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

1.  開啟**Azure Active Directory 延伸模組**藉由選取**所有服務**左側主導覽功能表的頂端。

1.  型別 **[Azure Active Directory]** 篩選搜尋方塊，然後選取**Azure Active Directory**項目。

1.  選取 **企業應用程式**Azure Active Directory 左側導覽功能表。

1.  選取 [所有應用程式]  ，以檢視所有應用程式的清單。

    若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單]  頂端的 [篩選]  控制項，並將 [顯示]  選項設定為 [所有應用程式]  。

1.  選取您要設為單一登入的應用程式。

1.  在應用程式載入後，開啟 [基本 SAML 組態]  。 確認 [識別碼] 文字方塊中的值符合錯誤中所顯示的識別碼值的值。


## <a name="certificate-or-key-not-configured"></a>未設定憑證或金鑰

*錯誤 AADSTS50003：未設定簽署金鑰。*

**可能的原因**

應用程式物件已損毀，Azure AD 無法辨識為應用程式設定的憑證。

**解決方案**

若要刪除與建立新的憑證，請依照下列步驟執行：

1. 開啟 [Azure 入口網站  ](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

1. 按一下左側主導覽功能表底部的 [所有服務]  ，以開啟 [Azure Active Directory 延伸模組]  。

1. 型別 **[Azure Active Directory]** 篩選搜尋方塊，然後選取**Azure Active Directory**項目。

1. 選取 **企業應用程式**Azure Active Directory 左側導覽功能表。

1. 選取 [所有應用程式]  ，以檢視所有應用程式的清單。

    若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單]  頂端的 [篩選]  控制項，並將 [顯示]  選項設定為 [所有應用程式]  。

1. 選取您要設定單一登入的應用程式

1. 應用程式載入後，按一下應用程式的左側導覽功能表中的 [單一登入]  。

1. 選取 **建立新憑證**下方**SAML 簽署憑證**一節。

1. 選取到期日，然後按一下**儲存**。

1. 勾選 [Make new certificate active (啟用新憑證)]  以覆寫作用中的憑證。 然後按一下窗格頂端的 [儲存]  ，並按 [接受] 以啟動變換憑證。

1. 在 [SAML 簽署憑證] 區段下，按一下 [移除] 以移除   **未使用**的憑證。

## <a name="saml-request-not-present-in-the-request"></a>要求中沒有 SAML 要求

*錯誤 AADSTS750054：SAMLRequest 或 SAMLResponse 必須在 HTTP 要求中以查詢字串參數的形式存在，才能進行 SAML 重新導向繫結。*

**可能的原因**

Azure AD 無法識別 HTTP 要求中 URL 參數內的 SAML 要求。 如果應用程式未使用 HTTP 重新導向繫結傳送至 Azure AD 的 SAML 要求時，會發生這項目。

**解決方案**

應用程式需要將 SAML 要求編碼為使用 HTTP 的 location 標頭傳送重新導向繫結。 如需有關如何進行此實作的詳細資訊，請參閱 [SAML 通訊協定規格文件](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf) \(英文\) 中的＜HTTP 重新導向繫結＞一節。

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD 將權杖傳送至正確的端點

**可能的原因**

在 單一登入，如果登入要求不包含明確的回覆 URL (判斷提示取用者服務 URL)，則 Azure AD 將會選取已設定任一會依賴 Url 該應用程式。 應用程式在沒有設定明確的回覆 URL，則使用者可能要重新導向 https://127.0.0.1:444 。 

當應用程式已新增為非資源庫應用程式時，Azure Active Directory 就會建立此回覆 URL 做為預設值。 此行為已變更，Azure Active Directory 預設不會再新增此 URL。 

**解決方案**

刪除未使用的回覆 Url 為應用程式設定。

1.  開啟 [Azure 入口網站  ](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

2.  開啟**Azure Active Directory 延伸模組**藉由選取**所有服務**左側主導覽功能表的頂端。

3.  型別 **[Azure Active Directory]** 篩選搜尋方塊，然後選取**Azure Active Directory**項目。

4.  選取 **企業應用程式**Azure Active Directory 左側導覽功能表。

5.  選取 [所有應用程式]  ，以檢視所有應用程式的清單。

    若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單]  頂端的 [篩選]  控制項，並將 [顯示]  選項設定為 [所有應用程式]  。

6.  選取您要設為單一登入的應用程式。

7.  在應用程式載入後，開啟 [基本 SAML 組態]  。 在 **回覆 URL (判斷提示取用者服務 URL)** ，由系統建立刪除未使用 或 預設回覆 Url。 例如： `https://127.0.0.1:444/applications/default.aspx` 。

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>自訂傳送至應用程式的 SAML 宣告時發生問題

若要了解如何自訂傳送至您的應用程式的 SAML 屬性宣告，請參閱[Azure Active Directory 中的宣告對應](../develop/active-directory-claims-mapping.md)。

## <a name="next-steps"></a>後續步驟

[如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式](../develop/howto-v1-debug-saml-sso-issues.md)
