---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的 Velpic |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建使用者帳戶到 Velpic。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16c302fbe151d6cd8c2198240bc31a2bd69dbd7b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270898"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的 Velpic

本教學課程的目的是要說明的步驟，您需要在 Velpic 和 Azure AD 自動佈建和取消佈建到 Velpic 的使用者帳戶從 Azure AD 中執行。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。

## <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

* Azure Active Directory 租用戶
* Velpic 租用戶[企業計劃](https://www.velpic.com/pricing.html)或進一步啟用
* Velpic 中具有系統管理員權限的使用者帳戶

## <a name="assigning-users-to-velpic"></a>將使用者指派到 Velpic

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者帳戶佈建的內容中，只有已「指派」至 Azure AD 中的應用程式之使用者和群組會進行同步處理。 

在設定並啟用佈建服務之前，您必須決定哪些使用者及/或 Azure AD 中的群組代表需要 Velpic 應用程式存取權的使用者。 一旦決定後，您可以將這些使用者指派到 Velpic 應用程式，依照此處的指示：

[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>將使用者指派到 Velpic 的重要秘訣

* 建議將單一 Azure AD 使用者指派到 Velpic，以測試佈建設定。 其他使用者及/或群組可能會稍後再指派。

* 當將使用者指派到 Velpic，您必須選取**使用者**角色或另一個有效應用程式特有角色 （如果有的話） 在 [指派] 對話方塊中的。 請注意，**預設存取**角色不適用於佈建，以及這些使用者將會略過。

## <a name="configuring-user-provisioning-to-velpic"></a>設定使用者佈建到 Velpic

本節會引導您將 Azure AD 連線至 Velpic 的使用者帳戶佈建 API，並設定佈建服務以建立、 更新和停用指派的在於 Velpic，根據使用者和群組指派 Azure AD 中的使用者帳戶。

> [!TIP]
> 您也可以選擇啟用 Velpic SAML 型單一登入，遵循指示中提供[Azure 入口網站](https://portal.azure.com)。 可以獨立設定自動佈建的單一登入，雖然這兩個功能彼此補充。

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>若要設定自動使用者帳戶佈建到 Velpic 在 Azure AD 中：

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 [Azure Active Directory > 企業應用程式 > 所有應用程式] 區段。

2. 如果您已經設定單一登入 Velpic，搜尋您的 Velpic 使用 [搜尋] 欄位的執行個體。 否則，請選取**新增**並搜尋**Velpic**應用程式庫中。 從搜尋結果中，選取 Velpic，並將它新增至您的應用程式清單。

3. 選取您的 Velpic，執行個體，然後選取**佈建** 索引標籤。

4. 將 [佈建模式] 設定為 [自動]。

    ![Velpic 佈建](./media/velpic-provisioning-tutorial/Velpic1.png)

5. 底下**系統管理員認證**區段中，輸入**租用戶 URL 與祕密權杖**的 Velpic。 (您可以在您的 Velpic 帳戶下找到這些值：**管理** > **整合** > **外掛程式** > **SCIM**)

    ![授權值](./media/velpic-provisioning-tutorial/Velpic2.png)

6. 在 Azure 入口網站中，按一下**測試連接**以確保 Azure AD 可以連線到您的 Velpic 應用程式。 如果連線失敗，請確定您的 Velpic 帳戶具有系統管理員權限並再試一次步驟 5。

7. 在 [通知電子郵件] 欄位中輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選下列核取方塊。

8. 按一下 [檔案] 。

9. 在 [對應] 區段中，選取**同步處理 Azure Active Directory 使用者至 Velpic**。

10. 在 **屬性對應**區段中，檢閱將從 Azure AD 同步至 Velpic 的使用者屬性。 請注意，為選取的屬性**比對**屬性會用來比對進行更新作業的 Velpic 中的使用者帳戶。 選取 [儲存] 按鈕以認可任何變更。

11. 若要啟用 Azure AD 佈建服務 Velpic，變更**佈建狀態**要**上**中**設定**區段

12. 按一下 [檔案] 。

這會啟動初始同步任何的處理使用者和/或群組指派給使用者和群組] 區段中的 [Velpic。 請注意，初始同步處理會比後續同步處理花費更多時間執行，只要服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告，報告中會描述佈建服務執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)