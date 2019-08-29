---
title: 如何為 Azure AD 資源庫應用程式設定密碼單一登入 | Microsoft Docs
description: 當應用程式已列于 Azure AD 應用程式資源庫時, 如何為其設定以安全密碼為基礎的單一登入
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
ms.openlocfilehash: 9fb33c4110a590539c85364885da9a27853f6bd0
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146879"
---
# <a name="configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>為 Azure AD 資源庫應用程式設定密碼單一登入

當您從[Azure Active Directory (Azure AD) 應用程式庫](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)新增應用程式時, 您可以選擇要讓使用者登入該應用程式的方式。 您可以隨時在[Azure 入口網站](https://portal.azure.com/)的企業應用程式上選取 [**單一登入**] 來設定此選項。

其中一個可用的單一登入 (SSO) 選項是以[密碼為基礎的單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。 這是開始快速將應用程式整合到 Azure AD 的絕佳方式。 它會啟用下列功能:

-   安全地儲存和重新執行您已整合之應用程式的使用者名稱和密碼 Azure AD

-   針對需要多個登入欄位的應用程式提供支援, 但只限于使用者名稱和密碼欄位

-   可讓您自訂使用者在輸入其認證時, 在[應用存取面板程式](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)上看到的 [使用者名稱] 和 [密碼] 欄位的標籤

-   允許您的使用者針對他們在[應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)上手動輸入的任何現有應用程式帳戶, 提供自己的使用者名稱和密碼存取面板

-   允許商務群組的成員使用[自助應用程式存取](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)功能, 指定指派給使用者的使用者名稱和密碼

-   讓系統管理員在[將使用者指派給應用程式](#assign-a-user-to-an-application-directly)時, 使用 [更新認證] 功能指定指派給使用者的使用者名稱和密碼

-   可讓系統管理員在[將群組指派給應用程式](#assign-an-application-to-a-group-directly)時, 使用更新認證功能來指定人員群組的共用使用者名稱或密碼

下一節將說明如何為已在[Azure AD 應用程式庫](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)中的應用程式啟用[密碼型單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="overview-of-required-steps"></a>必要步驟的總覽
若要從 Azure AD 資源庫設定應用程式, 您需要:

-   [從 Azure AD 資源庫新增應用程式](#add-an-application-from-the-azure-ad-gallery)

-   [設定應用程式使用密碼單一登入](#configure-the-application-for-password-single-sign-on)

-   將應用程式指派給使用者或群組:

    -   [將使用者直接指派至應用程式](#assign-a-user-to-an-application-directly)

    -   [將應用程式直接指派至群組](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>從 Azure AD 資源庫新增應用程式

若要從 Azure AD 資源庫新增應用程式, 請遵循下列步驟:

1.  開啟[Azure 入口網站](https://portal.azure.com), 然後以**全域管理員**或**共同**管理員身分登入。

2.  選取左側功能表頂端的 [**所有服務**], 以開啟 [ **Azure Active Directory] 延伸**模組。

3.  在搜尋方塊中輸入**azure active directory** , 然後選取 [ **Azure Active Directory** ] 專案。

4.  在左側的 [Azure AD] 功能表上, 選取 [**企業應用程式**]。

5.  選取 [**企業應用程式**] 窗格右上角的 [**新增**] 按鈕。

6.  在 [**從資源庫新增**] 區段的 [**輸入名稱**] 方塊中, 輸入應用程式的名稱。

7.  選取您要設定單一登入的應用程式。

8.  新增應用程式之前, 您可以在 [**名稱**] 方塊中變更其名稱。

9.  選取 [**新增**] 以新增應用程式。

稍候片刻，您便能看見應用程式的設定窗格。

## <a name="configure-the-application-for-password-single-sign-on"></a>設定應用程式使用密碼單一登入

若要設定應用程式使用單一登入，請遵循下列步驟：

1. 開啟[Azure 入口網站](https://portal.azure.com/), 並以**全域管理員**或**共同管理員**身分登入。

2. 選取左側功能表頂端的 [**所有服務**], 以開啟 [ **Azure Active Directory] 延伸**模組。

3. 在搜尋方塊中輸入**azure active directory** , 然後選取 [ **Azure Active Directory** ] 專案。

4. 從左側的 [Azure Active Directory] 功能表中選取 [**企業應用程式**]。

5. 選取 [所有應用程式]，以檢視所有應用程式的清單。

   如果您在這裡看不到想要的應用程式, 請使用 [**所有應用程式清單**] 頂端的 [**篩選**] 控制項, 並將 [**顯示**] 選項設定為 [**所有應用程式**]。

6. 選取您要設定單一登入的應用程式。

7. 載入應用程式之後, 從左側的應用程式功能表中選取 [**單一登入**]。

8. 選取 [**密碼型登入**模式]。

9. [將使用者指派給應用程式](#assign-a-user-to-an-application-directly)。

10. 您也可以藉由選取使用者的資料列、選取 [**更新認證**], 然後輸入使用者名稱和密碼, 來代表使用者提供認證。 否則, 系統會提示使用者在啟動應用程式時輸入其認證。

## <a name="assign-a-user-to-an-application-directly"></a>將使用者直接指派至應用程式

若要直接將一或多個使用者指派給應用程式，請遵循下列步驟︰

1. 開啟[Azure 入口網站](https://portal.azure.com/), 並以**全域管理員**身分登入。

2. 選取左側功能表頂端的 [**所有服務**], 以開啟 [ **Azure Active Directory] 延伸**模組。

3. 在搜尋方塊中輸入**azure active directory** , 然後選取 [ **Azure Active Directory** ] 專案。

4. 從左側的 [Azure Active Directory] 功能表中選取 [**企業應用程式**]。

5. 選取 [所有應用程式]，以檢視所有應用程式的清單。

   如果您在這裡看不到想要的應用程式, 請使用 [**所有應用程式清單**] 頂端的 [**篩選**] 控制項, 並將 [**顯示**] 選項設定為 [**所有應用程式**]。

6. 選取您想要指派給使用者的應用程式。

7. 載入應用程式之後, 從左側的應用程式功能表中選取 [**使用者和群組**]。

8. 選取 [**使用者和群組**] 清單頂端的 [**新增**] 按鈕, 以開啟 [**新增指派**] 窗格。

9. 從 [**新增指派**] 窗格中選取 [**使用者和群組**]。

10. 在 [**依名稱或電子郵件地址搜尋**] 搜尋方塊中, 輸入使用者的完整名稱或電子郵件地址。

11. 將滑鼠停留在清單中的使用者上方, 然後選取使用者設定檔相片或標誌旁邊的核取方塊, 將其新增至 [**選取**的清單]。

12. 選擇性：如果您想要新增多個使用者, 請在 [**依名稱或電子郵件地址搜尋**] 方塊中輸入另一個完整名稱或電子郵件地址, 然後選取該使用者的核取方塊, 將其新增至 [**選取**的清單]。

13. 當您完成選取使用者時, 請使用 [**選取**] 按鈕, 將他們新增到要指派給應用程式的使用者和群組清單。

14. 選擇性：使用 [**新增指派**] 窗格中的 [**選取角色**] 命令, 選取要指派給您已選取之使用者的角色。

15. 選取 [**指派**], 將應用程式指派給選取的使用者。

## <a name="assign-an-application-to-a-group-directly"></a>將應用程式直接指派至群組

若要將一或多個群組直接指派給應用程式，請遵循下列步驟：

1. 開啟[Azure 入口網站](https://portal.azure.com/), 並以**全域管理員**身分登入。

2. 選取左側功能表頂端的 [**所有服務**], 以開啟 [ **Azure Active Directory] 延伸**模組。

3. 在搜尋方塊中輸入**azure active directory** , 然後選取 [ **Azure Active Directory** ] 專案。

4. 從左側的 [Azure AD] 功能表中選取 [**企業應用程式**]。

5. 選取 [所有應用程式]，以檢視所有應用程式的清單。

   如果您在這裡看不到想要的應用程式, 請使用 [**所有應用程式清單**] 頂端的 [**篩選**] 控制項, 並將 [**顯示**] 選項設定為 [**所有應用程式**]。

6. 選取您想要指派給使用者的應用程式。

7. 載入應用程式之後, 從左側的應用程式功能表中選取 [**使用者和群組**]。

8. 選取 [**使用者和群組**] 清單頂端的 [**新增**] 按鈕, 以開啟 [**新增指派**] 窗格。

9. 從 [**新增指派**] 窗格中選取 [**使用者和群組**]。

10. 在 [**依名稱或電子郵件地址搜尋**] 搜尋方塊中, 輸入您要指派之群組的完整組名。

11. 將滑鼠停留在清單中的 [**群組**] 上方, 然後選取群組設定檔相片或標誌旁邊的核取方塊, 將群組新增至 [**選取**的清單]。

12. 選擇性：如果您想要新增多個群組, 請在 [**依名稱或電子郵件地址搜尋**] 搜尋方塊中輸入另一個完整組名, 然後選取對應的核取方塊, 將此群組新增至 [已**選取**] 清單。

13. 當您完成選取群組時, 請使用 [**選取**] 按鈕, 將其新增至要指派給應用程式的使用者和群組清單。

14. 選擇性：使用 [**新增指派**] 窗格中的 [**選取角色**] 命令, 選取要指派給您所選取之群組的角色。

15. 選取 [**指派**], 將應用程式指派給選取的群組。

一小段時間之後, 您選取的使用者應該能夠從存取面板啟動這些應用程式。

## <a name="next-steps"></a>後續步驟
[透過應用程式 Proxy 提供應用程式的單一登入](application-proxy-configure-single-sign-on-with-kcd.md)。
