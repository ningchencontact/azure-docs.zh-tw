---
title: "教學課程︰以 Azure Active Directory 設定 Asana 來自動佈建使用者 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 將使用者帳戶自動佈建及取消佈建至 Asana。"
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 4f3bd11a99f43d6405ea285a7a283179d561f92a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>教學課程︰設定 Asana 來自動佈建使用者

本教學課程旨在說明您需要在 Asana 和 Azure AD 中執行的步驟，以將使用者帳戶從 Azure AD 自動佈建和取消佈建至 Asana。

## <a name="prerequisites"></a>先決條件

本教學課程中說明的案例假設您已經具有下列項目：

*   Azure Active Directory 租用戶
*   已啟用 [Enterprise 方案](https://www.asana.com/pricing)或更好方案的 Asana 租用戶 
*   Asana 中具有管理員權限的使用者帳戶 

> [!NOTE] 
> Azure AD 佈建整合需仰賴可供 Asana 使用的 [Asana API](https://app.asana.com/api/1.0/scim/Users)。

## <a name="assigning-users-to-asana"></a>將使用者指派給 Asana

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動佈建使用者帳戶的內容中，只有已「指派」至 Azure AD 中的應用程式之使用者會進行同步處理。 

在設定並啟用佈建服務之前，您必須決定 Azure AD 中的哪些使用者代表需要 Asana 應用程式存取權的使用者。 一旦決定後，您可以依照此處的指示，將這些使用者指派給 Asana 應用程式︰

[將使用者指派給企業應用程式](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>將使用者指派給 Asana 的重要秘訣

*   建議將單一 Azure AD 使用者指派給 Asana，以測試佈建設定。 其他使用者可能會稍後再指派。

## <a name="configuring-user-provisioning-to-asana"></a>設定使用者佈建至 Asana 

本節會引導您將 Azure AD 連線至 Asana 的使用者帳戶佈建 API，並以 Azure AD 中的使用者和群組指派為基礎設定佈建服務，以在 Asana 中建立、更新及停用指派的使用者帳戶。

> [!TIP]
> 您也可以選擇啟用 Asana 的 SAML 型單一登入，請遵循 [Azure 入口網站](https://portal.azure.com)中提供的指示。 可以獨立設定自動佈建的單一登入，雖然這兩個功能彼此補充。

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>若要在 Azure AD 中設定自動將使用者帳戶佈建至 Asana︰

1)  在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 [Azure Active Directory > 企業應用程式 > 所有應用程式] 區段。

2) 如果您已經設定單一登入的 Asana，請使用 [搜尋] 欄位搜尋您的 Asana 執行個體。 否則，請選取 [新增]，並在應用程式庫中搜尋 [Asana]。 從搜尋結果中選取 [Asana]，並將它新增至您的應用程式清單。

3)  選取您的 Asana 執行個體，然後選取 [佈建] 索引標籤。

4)  將 [佈建模式] 設定為 [自動]。

![Asana 佈建](./media/active-directory-saas-asana-provisioning-tutorial/asanaazureprovisioning.png)

5) 在 [系統管理員認證] 區段下，請遵循以下指示來產生權杖，並在 [祕密權杖] 文字方塊中輸入該權杖。

* 使用系統管理員帳戶登入 [Asana](https://app.asana.com)
* 按一下頂端列的設定檔照片，然後選取目前的組織名稱設定
* 瀏覽至 [服務帳戶] 索引標籤
* 按一下 [新增服務帳戶]
* 視需要更新 [名稱]、[關於] 及設定檔相片，複製 [權杖]，然後按一下 [儲存變更]

6) 在 Azure 入口網站中，按一下 [測試連線]，以確保 Azure AD 可以連線至您的 Asana 應用程式。 如果連線失敗，請確定您的 Asana 帳戶具有系統管理員權限，並再試一次「測試連線」步驟。

7) 在 [通知電子郵件] 欄位中輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選下列核取方塊。

8) 按一下 [檔案] 。 

9) 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 Asana]。

10) 在 [屬性對應] 區段中，檢閱將從 Azure AD 同步至 Asana 的使用者屬性。 請注意，選取為 [比對] 屬性的屬性會用來比對 Asana 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。 如需詳細資訊，請參閱[自訂使用者佈建的屬性對應](active-directory-saas-customizing-attribute-mappings.md)

11) 若要啟用 Asana 的 Azure AD 佈建服務，在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]

12) 按一下 [檔案] 。 

這會啟動在 [使用者] 區段中指派給 Asana 之任何使用者的首次同步處理。 首次同步處理會比後續的同步處理花費較多時間執行，只要服務正在執行，大約每 20 分鐘就會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告，當中會描述您 Asana 應用程式上的佈建服務所執行之所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](active-directory-enterprise-apps-manage-provisioning.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)
* [設定單一登入](active-directory-saas-asana-tutorial.md)
