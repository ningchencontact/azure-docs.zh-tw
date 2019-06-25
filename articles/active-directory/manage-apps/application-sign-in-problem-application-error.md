---
title: 登入後應用程式 頁面上會出現錯誤訊息 |Microsoft Docs
description: 如何解決 Azure AD 登入應用程式時傳回錯誤訊息。
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41ec1f510b028a2ffe2554bfcbd77bc439c4e79
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272960"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>使用者登入之後，應用程式頁面會顯示錯誤訊息

在此案例中，Azure Active Directory (Azure AD) 會將使用者登入。 但是，應用程式會顯示錯誤訊息，並不會讓使用者完成登入流程。 問題在於應用程式不接受 Azure AD 發出的回應。

有幾個可能的原因，為什麼應用程式不接受來自 Azure AD 的回應。 如果錯誤訊息並不清楚地識別遺漏的項目從回應，請嘗試下列方法：

-   如果應用程式的 Azure AD 資源庫，請確認您已遵循的步驟[如何在 Azure AD 中的偵錯 SAML 型單一登入應用程式](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)。

-   使用之類的工具[Fiddler](https://www.telerik.com/fiddler)擷取 SAML 要求、 回應和語彙基元。

-   SAML 回應傳送至應用程式廠商，並要求他們遺漏的項目。

## <a name="attributes-are-missing-from-the-saml-response"></a>在 SAML 回應中遺漏了屬性的

若要將 Azure AD 在回應中傳送的 Azure AD 組態中新增的屬性，請遵循下列步驟：

1. 開啟[ **Azure 入口網站**](https://portal.azure.com/)並以全域管理員或共同管理員。

2. 在左側瀏覽窗格頂端，選取**所有服務**若要開啟 Azure AD 延伸模組。

3. 型別**Azure Active Directory**篩選搜尋方塊中，然後選取**Azure Active Directory**。

4. 選取 **企業應用程式**Azure AD 瀏覽窗格中。

5. 選取 **所有應用程式**若要檢視您的應用程式清單。

   > [!NOTE]
   > 如果您沒有看到您想要的應用程式，使用**篩選條件**控制項頂端**所有應用程式清單**。 設定**顯示**選項，所有應用程式。 」

6. 選取您要讓單一登入設定的應用程式。

7. 應用程式載入之後，請選取**單一登入**瀏覽窗格中。

8. 在 **使用者屬性**區段中，選取**檢視和編輯所有其他使用者屬性**。 您可以在這裡變更傳送至 SAML 權杖中的應用程式，當使用者登入的屬性。

   若要新增屬性︰

   1. 選取 [新增屬性]  。 請輸入**名稱**，然後選取**值**從下拉式清單。

   1.  選取 [ **儲存**]。 您會看到資料表中新的屬性。

9. 儲存組態。

   下次使用者登入應用程式，Azure AD 會在 SAML 回應中傳送新的屬性。

## <a name="the-app-doesnt-identify-the-user"></a>應用程式不會識別使用者

登入應用程式會失敗，因為 SAML 回應遺漏的屬性，例如角色。 或以不同格式或值，必須要有應用程式因**NameID** （使用者識別碼） 屬性。

如果您使用[Azure AD 自動使用者佈建](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)若要建立，維護，並在應用程式中移除使用者，請確認使用者已佈建至 SaaS 應用程式。 如需詳細資訊，請參閱 <<c0> [ 沒有任何使用者佈建至 Azure AD 資源庫應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)。

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>將屬性新增至 Azure AD 應用程式設定

若要變更使用者識別碼的值，請遵循下列步驟︰

1. 開啟[ **Azure 入口網站**](https://portal.azure.com/)並以全域管理員或共同管理員。

2. 選取 **所有服務**在若要開啟 Azure AD 延伸模組的左側導覽窗格頂端。

3. 型別**Azure Active Directory**篩選搜尋方塊中，然後選取**Azure Active Directory**。

4. 選取 **企業應用程式**Azure AD 瀏覽窗格中。

5. 選取 **所有應用程式**若要檢視您的應用程式清單。

   > [!NOTE]
   > 如果您沒有看到您想要的應用程式，使用**篩選條件**控制項頂端**所有應用程式清單**。 設定**顯示**選項，所有應用程式。 」

6. 選取您想要為 SSO 設定的應用程式。

7. 應用程式載入之後，請選取**單一登入**瀏覽窗格中。

8. 底下**使用者屬性**，選取 從使用者的唯一識別碼**使用者識別碼**下拉式清單。

## <a name="change-the-nameid-format"></a>變更 NameID 格式

如果應用程式需要另一種格式**NameID** （使用者識別碼） 屬性，請參閱[編輯 nameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization.md#editing-nameid) NameID 格式變更。

Azure AD 中選取的格式**NameID**屬性 （使用者識別碼） 會根據為選取的值或在 SAML AuthRequest 中應用程式所要求的格式。 如需詳細資訊，請參閱"NameIDPolicy 」 一節[單一登入 SAML 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)。

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>應用程式預期在 SAML 回應不同的簽章方法

若要變更 Azure ad，經過數位簽署 SAML 權杖哪些部分，請遵循下列步驟：

1. 開啟[Azure 入口網站](https://portal.azure.com/)並以全域管理員或共同管理員。

2. 選取 **所有服務**在若要開啟 Azure AD 延伸模組的左側導覽窗格頂端。

3. 型別**Azure Active Directory**篩選搜尋方塊中，然後選取**Azure Active Directory**。

4. 選取 **企業應用程式**Azure AD 瀏覽窗格中。

5. 選取 **所有應用程式**若要檢視您的應用程式清單。

   > [!NOTE]
   > 如果您沒有看到您所要的應用程式，使用**篩選條件**控制項頂端**所有應用程式清單**。 設定**顯示**選項，所有應用程式。 」

6. 選取您要讓單一登入設定的應用程式。

7. 應用程式載入之後，請選取**單一登入**瀏覽窗格中。

8. 底下**SAML 簽署憑證**，選取**顯示進階憑證簽署設定**。

9. 選取 **簽署選項**應用程式需要從這些選項：

   * **簽署 SAML 回應**
   * **簽署 SAML 回應及判斷提示**
   * **簽署 SAML 判斷提示**

   下次使用者登入應用程式，Azure AD 會簽署 SAML 回應您所選取的一部分。

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>應用程式預期 sha-1 簽署演算法

根據預設，Azure AD 會使用最安全的演算法簽署 SAML 權杖。 我們建議您不要變更的簽署演算法*sha-1*除非應用程式需要 SHA-1。

若要變更簽署演算法，請遵循下列步驟：

1. 開啟[Azure 入口網站](https://portal.azure.com/)並以全域管理員或共同管理員。

2. 選取 **所有服務**在若要開啟 Azure AD 延伸模組的左側導覽窗格頂端。

3. 型別**Azure Active Directory**篩選搜尋方塊中，然後選取**Azure Active Directory**。

4. 選取 **企業應用程式**Azure AD 瀏覽窗格中。

5. 選取 **所有應用程式**若要檢視您的應用程式清單。

   > [!NOTE]
   > 如果您沒有看到您所要的應用程式，使用**篩選條件**控制項頂端**所有應用程式清單**。 設定**顯示**選項，所有應用程式。 」

6. 選取您要讓單一登入設定的應用程式。

7. 應用程式載入之後，請選取**單一登入**從應用程式左邊瀏覽窗格。

8. 底下**SAML 簽署憑證**，選取**顯示進階憑證簽署設定**。

9. 選取  **sha-1**作為**簽署演算法**。

   下次使用者登入應用程式，Azure AD 會使用 sha-1 演算法簽署 SAML 權杖。

## <a name="next-steps"></a>後續步驟
[如何在 Azure AD 中的偵錯 SAML 型單一登入應用程式](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)。
