---
title: 如何為 Azure AD 資源庫應用程式設定同盟單一登入 | Microsoft Docs
description: 如何為現有的 Azure AD 資源庫應用程式設定同盟單一登入, 以及如何使用教學課程快速開始
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
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 8e50a495e1b0406e0c935ac31111dc6b5d0c0821
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207127"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>如何為 Azure AD 資源庫應用程式設定同盟單一登入

具有「企業單一登入」功能的 Azure Active Directory (Azure AD) 元件庫中的所有應用程式都有提供逐步教學課程。 您可以存取[如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)，以取得詳細的逐步指引。

## <a name="overview-of-steps-required"></a>所需步驟的概觀
若要設定 Azure AD 資源庫中的應用程式，您必須：

-   [從 Azure AD 資源庫新增應用程式](#add-an-application-from-the-azure-ad-gallery)

-   [在 Azure AD 中設定應用程式的中繼資料值 (登入 URL、識別碼、回復 URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [選取使用者識別碼並新增要傳送到應用程式的使用者屬性](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [取出 Azure AD 中繼資料和憑證](#download-the-azure-ad-metadata-or-certificate)

-   [在應用程式中設定 Azure AD 中繼資料值 (登入 URL、簽發者、登出 URL 和憑證)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [將使用者指派給應用程式](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>從 Azure AD 資源庫新增應用程式

若要從 Azure AD 資源庫新增應用程式，請遵循下列步驟：

1.  開啟 [Azure 入口網站](https://portal.azure.com)，然後以**全域管理員**或**共同管理員**身分登入。

2.  選取主要左側導覽功能表頂端的 [**所有服務**], 以開啟 [ **Azure Active Directory] 延伸**模組。

3.  在搜尋方塊中輸入 "Azure Active Directory", 然後選取 [ **Azure Active Directory**]。

4.  從 Azure AD 的左側導覽功能表中, 選取 [**企業應用程式**]。

5.  選取 [**企業應用程式**] 窗格右上角的 [**新增**]。

6.  在 [**從資源庫新增**] 區段中的 [**輸入名稱**] 方塊中, 輸入應用程式的名稱。

7.  選取您要設為單一登入的應用程式。

8.  在新增應用程式之前, 您可以在 [**名稱**] 方塊中變更其名稱。

9.  選取 [**新增**] 以新增應用程式。

一小段時間之後, 您應該就能看到應用程式的 [設定] 窗格。

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>從 Azure AD 資源庫為應用程式設定單一登入

若要設定應用程式使用單一登入，請遵循下列步驟：

1. 開啟 [Azure 入口網站](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。

2. 選取主要左側導覽功能表頂端的 [**所有服務**], 以開啟 [ **Azure Active Directory] 延伸**模組。

3. 在搜尋方塊中輸入 "Azure Active Directory", 然後選取 [ **Azure Active Directory**]。

4. 從 Azure Active Directory 的左側導覽功能表中, 選取 [**企業應用程式**]。

5. 選取 [所有應用程式]，以檢視所有應用程式的清單。

   * 如果您在這裡沒有看到想要的應用程式, 請使用 [**所有應用程式清單**] 頂端的 [**篩選**] 控制項, 並將 [**顯示**] 選項設定為 [**所有應用程式]。**

6. 選取您要設定單一登入的應用程式。

7. 應用程式載入之後, 請從應用程式的左側導覽功能表中選取 [**單一登入**]。

8. 從 [模式] 下拉式清單選取 [SAML 登入]。

9. 在 [網域及 URL] 中輸入必要值。 這些值應從應用程式廠商處取得。

   1. 若要將應用程式設定為 SP 啟始的 SSO，則登入 URL 為必要值。 對於某些應用程式，識別碼也是必要值。

   2. 若要將應用程式設定為 IdP 啟始的單一登入，則 [登入 URL] 為必要值。 對於某些應用程式，識別碼也是必要值。

10. **選擇性**：如果您想要查看非必要的值, 請選取 [**顯示 ADVANCED URL 設定**]。

11. 在 [使用者屬性] 中，從 [使用者識別碼] 下拉式清單選取使用者的唯一識別碼。

12. **選擇性**：選取 [**查看和編輯所有其他使用者屬性**], 以編輯當使用者登入時要以 SAML 權杖傳送至應用程式的屬性。

    若要新增屬性︰
   
    1. 選取 [新增屬性]。 輸入 [名稱]，然後從下拉式清單中選取 [值]。

    1. 選取 [儲存]。 您會在資料表中看到新屬性。

13. 選取 **[ &lt;設定應用&gt;程式名稱**] 以存取如何在應用程式中設定單一登入的檔。 此外, 您也有必要的中繼資料 Url 和憑證, 可設定應用程式的 SSO。

14. 選取 [儲存] 以儲存組態。

15. 將使用者指派至應用程式。

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>選取使用者識別碼並新增要傳送到應用程式的使用者屬性

若要選取 [使用者識別碼] 或 [新增使用者屬性], 請遵循下列步驟:

1. 開啟[Azure 入口網站](https://portal.azure.com/), 並以**全域管理員**或共同管理員身分登入 **。**

2. 選取主要左側導覽功能表頂端的 [**所有服務**], 以開啟 [ **Azure Active Directory] 延伸**模組。

3. 在搜尋方塊中輸入 "Azure Active Directory", 然後選取 [ **Azure Active Directory**]。

4. 從 Azure Active Directory 的左側導覽功能表中, 選取 [**企業應用程式**]。

5. 選取 [所有應用程式]，以檢視所有應用程式的清單。

   * 如果您在這裡沒有看到想要的應用程式, 請使用 [**所有應用程式清單**] 頂端的 [**篩選**] 控制項, 並將 [**顯示**] 選項設定為 [**所有應用程式]。**

6. 選取您已使用單一登入設定的應用程式。

7. 應用程式載入之後, 請從應用程式的左側導覽功能表中選取 [**單一登入**]。

8. 在 [使用者屬性] 區段下，從 [使用者識別碼] 下拉式清單選取使用者的唯一識別碼。 所選的選項必須符合應用程式中預期的值，才能驗證使用者。

   >[!NOTE] 
   >Azure AD 會根據在 SAML AuthRequest 中選取的值或應用程式所要求的格式, 來選取 NameID 屬性 (使用者識別碼) 的格式。 如需詳細資訊, 請參閱 NameIDPolicy 一節中的[單一登入 SAML 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)。
   >
   >

9. 若要新增使用者屬性, 請選取 [**視圖和編輯所有其他使用者屬性**], 以編輯當使用者登入時要以 SAML 權杖傳送至應用程式的屬性。

   若要新增屬性︰
  
   1. 選取 [新增屬性]。 輸入 [名稱]，然後從下拉式清單中選取 [值]。

   2. 選取 [ **儲存**]。 您會在資料表中看到新屬性。

## <a name="download-the-azure-ad-metadata-or-certificate"></a>下載 Azure AD 中繼資料或憑證

若要從 Azure AD 下載應用程式中繼資料或憑證，請遵循下列步驟：

1. 開啟[Azure 入口網站](https://portal.azure.com/), 並以**全域管理員**或共同管理員身分登入 **。**

2. 選取主要左側導覽功能表頂端的 [**所有服務**], 以開啟 [ **Azure Active Directory] 延伸**模組。

3. 在搜尋方塊中輸入 "Azure Active Directory", 然後選取 [ **Azure Active Directory**]。

4. 從 Azure Active Directory 的左側導覽功能表中, 選取 [**企業應用程式**]。

5. 選取 [所有應用程式]，以檢視所有應用程式的清單。

   *  如果您在這裡沒有看到想要的應用程式, 請使用 [**所有應用程式清單**] 頂端的 [**篩選**] 控制項, 並將 [**顯示**] 選項設定為 [**所有應用程式**]。

6. 選取您已使用單一登入設定的應用程式。

7. 應用程式載入之後, 請從應用程式的左側導覽功能表中選取 [**單一登入**]。

8. 移至 [ **SAML 簽署憑證**] 區段, 然後選取 [**下載**資料行值]。 根據應用程式設定單一登入時所需的項目，您會看到下載中繼資料 XML 或憑證的選項。

Azure AD 也會提供用來存取中繼資料的 URL。 使用下列模型來取得應用程式特定的中繼資料 URL:`https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>將使用者指派給應用程式

若要直接將一或多個使用者指派給應用程式，請遵循下列步驟︰

1. 開啟[Azure 入口網站](https://portal.azure.com/), 並以**全域管理員**身分登入。

2. 選取主要左側導覽功能表頂端的 [**所有服務**], 以開啟 [ **Azure Active Directory] 延伸**模組。

3. 在搜尋方塊中輸入 "Azure Active Directory", 然後選取 [ **Azure Active Directory**]。

4. 從 Azure Active Directory 的左側導覽功能表中, 選取 [**企業應用程式**]。

5. 選取 [所有應用程式]，以檢視所有應用程式的清單。

   * 如果您在這裡沒有看到想要的應用程式, 請使用 [**所有應用程式清單**] 頂端的 [**篩選**] 控制項, 並將 [**顯示**] 選項設定為 [**所有應用程式]。**

6. 從清單中選取您想要指派使用者的應用程式。

7. 應用程式載入之後, 請從應用程式的左側導覽功能表中選取 [**使用者和群組**]。

8. 選取 [**使用者和群組**] 清單頂端的 [**新增**] 按鈕, 以開啟 [**新增指派**] 窗格。

9. 選取 [新增指派] 窗格中的 [使用者和群組] 選取器。

10. 在 [**依名稱或電子郵件地址**搜尋] 搜尋方塊中, 輸入您要指派之使用者的**完整名稱**或**電子郵件地址**。

11. 將滑鼠停留在清單中的**使用者**上方, 以顯示**核取方塊**。 選取使用者設定檔相片或標誌旁的核取方塊, 以將您的使用者新增至 [已**選取**] 清單。

12. **選擇性**：如果您想要**新增多個使用者**, 請在 [**依名稱或電子郵件地址搜尋**] 搜尋方塊中輸入另一個**完整名稱**或**電子郵件地址**, 然後選取核取方塊, 將此使用者新增至 [已**選取**] 清單。

13. 當您完成選取使用者時, 請選取 [**選取**] 按鈕, 將其新增至要指派給應用程式的使用者和群組清單。

14. **選擇性**：選取 [**新增指派**] 窗格中的 [**選取角色**] 選取器, 以選取要指派給您已選取之使用者的角色。

15. 選取 [**指派**] 按鈕, 將應用程式指派給選取的使用者。

稍待片刻，您已選取的使用者就能夠使用解決方案描述一節所述的方法，啟動這些應用程式。

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>自訂傳送至應用程式的 SAML 宣告

若要瞭解如何自訂傳送至應用程式的 SAML 屬性宣告, 請參閱[Azure Active Directory 中的宣告對應](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)。

## <a name="next-steps"></a>後續步驟
[使用應用程式 Proxy 提供單一登入應用程式](application-proxy-configure-single-sign-on-with-kcd.md)



