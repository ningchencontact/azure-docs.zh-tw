---
title: 在 Azure AD 中管理同盟憑證 | Microsoft Docs
description: 了解如何自訂同盟憑證的到期日期，以及如何更新即將到期的憑證。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecadb499d140ccfc993820080cae0b749977fc61
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824750"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>在 Azure Active Directory 中管理同盟單一登入的憑證

在本文中，我們將討論常見的問題和與 Azure Active Directory (Azure AD) 建立以建立同盟單一登入 (SSO) 您的軟體即服務 (SaaS) 應用程式的憑證相關的資訊。 從 Azure AD 應用程式資源庫，或使用非資源庫的應用程式範本新增應用程式。 使用同盟 SSO 選項以設定應用程式。

這篇文章是只能夠使用 Azure AD SSO，透過設定應用程式與相關[安全性聲明標記語言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)(SAML) 同盟。

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>為資源庫和非資源庫應用程式自動產生的憑證

當您從資源庫新增新的應用程式，並設定 SAML 型登入 (方法是選取**單一登入** > **SAML**從應用程式的 [概觀] 頁面)，Azure AD 會產生應用程式三年的有效憑證。 若要下載作用中的憑證，做為安全性憑證 (**.cer**) 檔案，請回到該頁面 (**SAML 型登入**)，然後選取中的下載連結**SAML 簽署憑證**標題。 您可以選擇原始 （二進位） 的憑證或 Base64 （基底 64 編碼的文字） 的憑證。 資源庫應用程式，本節也可能會顯示的連結，以做為同盟中繼資料 XML 下載的憑證 ( **.xml**檔案)，這取決於應用程式的需求。

![SAML 作用中簽署憑證下載選項](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

您也可以藉由選取下載的作用中或非使用中的憑證**SAML 簽署憑證**標題的**編輯**圖示 （鉛筆），顯示**SAML 簽署憑證**  頁面。 選取省略符號 (**...**) 在您想要下載，然後選擇哪一種憑證格式之憑證旁邊。 您已下載的憑證，以強化隱私權的郵件 (PEM) 格式的其他選項。 此格式完全相同，為 Base64，但 **.pem**檔案名稱副檔名，做為憑證格式的 Windows 中無法辨識。

![SAML 簽署憑證下載選項 （作用中和非使用中）](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>自訂同盟憑證的到期日期及變換新的憑證

根據預設，Azure 會設定為會在建立時自動在 SAML 單一登入設定期間的三年之後到期的憑證。 因為您在儲存之後，您無法變更憑證的日期，您必須：

1. 建立新的憑證所需的日期。
2. 儲存新的憑證。
3. 下載新的憑證，以正確的格式。
4. 將新的憑證上傳至應用程式。
5. 啟用新憑證在 Azure Active Directory 入口網站中。

下列兩節可協助您執行這些步驟。

### <a name="create-a-new-certificate"></a>建立新的憑證

首先，建立並儲存新的憑證不同的到期日：

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)。 **Azure Active Directory 系統管理中心**頁面隨即出現。

2. 在左側窗格中，選取 [企業應用程式]。 在您的帳戶中的企業應用程式清單隨即出現。

3. 選取受影響的應用程式。 應用程式的 [概觀] 頁面隨即出現。

4. 在 應用程式的 概觀 頁面的左窗格中，選取**單一登入**。

5. 如果**選取 [單一登入方法**] 頁面出現時，選取**SAML**。

6. 在 **設定設定單一登入與 SAML-Preview**頁面上，尋找**SAML 簽署憑證**標題並選取**編輯**圖示 （鉛筆）。 **SAML 簽署憑證**頁面隨即出現，其中會顯示狀態 (**Active**或是**Inactive**)，到期日及每個憑證的指紋 （雜湊字串）。

7. 選取 **新的憑證**。 新的資料列會顯示如下憑證清單中，到期日預設為完全三年在目前的日期之後的位置。 （您的變更尚未儲存，因此您仍然可以修改的到期日。）

8. 在新的憑證資料列，將滑鼠停留的到期日期資料行，然後選取**選取日期**圖示 （行事曆）。 月曆控制項出現時，它會顯示新的資料列目前的到期日期的月份中的天數。

9. 若要設定新的日期，使用日曆控制項。 您可以設定目前的日期與目前的日期後三年之間的任何日期。

10. 選取 [ **儲存**]。 新憑證現在出現時會帶有狀態**Inactive**，到期日期，您選擇和憑證指紋。

11. 選取**X**以返回**將設定單一登入與 SAML-Preview**頁面。

### <a name="upload-and-activate-a-certificate"></a>上傳並啟用憑證

接下來，下載新的憑證，以正確的格式、 將它上傳至應用程式，並讓它成為作用中 Azure Active Directory:

1. 檢視應用程式的其他 SAML 登入組態指示，透過下列方式：
   - 選取**設定指南**連結來檢視中的另一個瀏覽器視窗或索引標籤上，或
   - 前往**設定**標題，然後選取**檢視的逐步指示**資訊看板中檢視。

2. 中的指示，請注意憑證上傳所需的編碼格式。

3. 請依照下列中的指示[資源庫和非資源庫應用程式的自動產生的憑證](#auto-generated-certificate-for-gallery-and-non-gallery-applications)稍早一節。 此步驟中下載的憑證上傳應用程式所需的編碼格式。

4. 當您想要變換新憑證時，請返回**SAML 簽署憑證**頁面，然後在新儲存的憑證資料列中，選取省略符號 (**...**)，然後選取**製作憑證 active**。 新憑證的狀態會變更為**Active**，和先前的作用中憑證變更為狀態**Inactive**。

5. 繼續的下列以正確的編碼格式的憑證，讓您可以上傳 SAML 簽章稍早顯示的應用程式的 SAML 登入組態指示。

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>新增憑證到期日的電子郵件通知地址

Azure AD 將傳送的 SAML 憑證到期前的電子郵件通知 60、 30 和 7 天。 您可以新增多個電子郵件地址以接收通知。 若要指定您想要傳送至通知的電子郵件地址：

1. 在  **SAML 簽署憑證**頁面上，移至**通知電子郵件地址**標題。 根據預設，此標題會使用僅加入應用程式系統管理員的電子郵件地址。

2. 最終的電子郵件地址下方輸入電子郵件地址，應該會收到的憑證到期通知，然後按 Enter 鍵。

3. 每個您想要新增的電子郵件地址重複上述步驟。

4. 每個您想要刪除的電子郵件地址，選取**刪除**電子郵件地址旁的圖示 （記憶體回收可以）。

5. 選取 [ **儲存**]。

您會收到來自 aadnotification@microsoft.com 的通知電子郵件。 若要避免電子郵件進入您的垃圾郵件位置，請新增這封電子郵件給您的連絡人。

## <a name="renew-a-certificate-that-will-soon-expire"></a>更新即將到期的憑證

如果憑證即將到期，您可以更新它使用的程序，導致您的使用者沒有顯著的停機時間。 若要更新過期的憑證：

1. 請依照下列中的指示[建立新的憑證](#create-a-new-certificate)區段更早版本，使用重疊的日期與現有的憑證。 該日期會限制憑證過期所造成的停機時間。

2. 如果應用程式可以自動變換憑證的限制，設定為 作用中新的憑證，遵循下列步驟：
   1. 請返回**SAML 簽署憑證**頁面。
   2. 在最近儲存的憑證資料列中，選取省略符號 (**...**)，然後選取**製作憑證 active**。
   3. 略過後續兩個步驟。

3. 如果應用程式一次只能處理一個憑證，請挑選執行下一個步驟的停機時間間隔。 （否則如果應用程式不會自動挑選新的憑證，但可以處理多個簽章的憑證，您可以執行下一個步驟隨時。）

4. 在舊的憑證到期之前，請依照下列中的指示[上傳並啟用憑證](#upload-and-activate-a-certificate)稍早一節。

5. 登入應用程式，請確定憑證正常運作。

## <a name="related-articles"></a>相關文章

* [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](../saas-apps/tutorial-list.md)
* [與 Azure Active Directory 的應用程式管理](what-is-application-management.md)
* [Azure Active Directory 中的應用程式單一登入](what-is-single-sign-on.md)
* [偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式](../develop/howto-v1-debug-saml-sso-issues.md)
