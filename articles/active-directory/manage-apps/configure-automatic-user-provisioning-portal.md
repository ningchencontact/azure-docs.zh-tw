---
title: Azure Active Directory 中企業應用程式的使用者佈建管理 |Microsoft Docs
description: 了解如何使用 Azure Active Directory 管理企業應用程式的使用者帳戶佈建
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: d03ca64f3f3d2f034433f2aaa49f6babb7f9e5b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60442082"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>在 Azure 入口網站中管理企業應用程式的使用者帳戶佈建

本文說明如何使用 [Azure 入口網站](https://portal.azure.com)管理自動使用者帳戶的佈建和解除佈建，以用於支援它的應用程式。 如需深入了解自動化使用者佈建，請參閱 [自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](user-provisioning.md)。

## <a name="finding-your-apps-in-the-portal"></a>在入口網站中尋找您的應用程式

使用 Azure Active Directory 入口網站來檢視和管理已針對單一登入的目錄中的所有應用程式。 企業應用程式是您組織內部署和使用的應用程式。 請遵循下列步驟來檢視及管理企業應用程式：

1. 開啟[Azure Active Directory 入口網站](https://aad.portal.azure.com)。

1. 選取 **企業應用程式**從左窗格中。 會顯示所有設定的應用程式的清單，包括從資源庫新增的應用程式。

1. 選取要載入其資源窗格中，您可以在其中檢視報表以及管理應用程式設定的任何應用程式。

1. 選取 **佈建**來管理使用者帳戶佈建所選的應用程式的設定。

   ![應用程式資源窗格](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>佈建模式

**佈建**窗格中的開頭**模式**功能表中，會顯示企業應用程式，支援的佈建模式，並可讓您設定它們。 可用的選項包括：

* **自動**-這個選項會顯示 Azure AD 是否支援自動的 API 型佈建或解除佈建此應用程式的使用者帳戶。 選取此模式中顯示的介面，可協助系統管理員：

  * 設定 Azure AD 以連線至應用程式的使用者管理 API
  * 建立帳戶對應和工作流程定義之間 Azure AD 使用者帳戶資料流動的方式和應用程式
  * 管理 Azure AD 佈建服務

* **手動**-是否 Azure AD 不支援自動佈建此應用程式的使用者帳戶來顯示這個選項。 在此情況下，您必須使用外部處理序，根據該應用程式 （這可以包括 SAML Just-In-Time 佈建） 所提供的使用者管理和佈建功能來管理記錄儲存在應用程式的使用者帳戶。

## <a name="configuring-automatic-user-account-provisioning"></a>設定使用者帳戶自動佈建

選取 **自動**選項來指定系統管理員認證、 對應、 啟動和停止，以及同步處理設定。

### <a name="admin-credentials"></a>管理員認證

依序展開**系統管理員認證**輸入 Azure AD 以連線至應用程式的使用者管理 API 所需的認證。 所需的輸入依應用程式而有所不同。 若要深入了解認證類型，以及針對特定應用程式的需求，請參閱 [針對該特定應用程式的設定教學課程](user-provisioning.md)。

選取 **測試連接**以測試的認證，azure AD 會嘗試連線到應用程式的佈建應用程式使用所提供的認證。

### <a name="mappings"></a>對應

依序展開**對應**來檢視和編輯 Azure AD 之間流動的使用者屬性和目標應用程式在使用者帳戶佈建或更新。

沒有一組預先的設定的 Azure AD 使用者物件和每個 SaaS 應用程式的使用者物件之間的對應。 有些 app 則管理其他類型的物件，例如群組或連絡人。 選取對應資料表中，開啟對應編輯器的右邊，您可以在其中檢視並加以自訂。

![應用程式資源窗格](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

支援的自訂項目包含：

* 啟用和停用特定物件的對應，例如 Azure AD 使用者物件對應至 SaaS 應用程式的使用者物件。
* 編輯會從 Azure AD 使用者物件流向應用程式使用者物件的屬性。 如需有關屬性對應的詳細資訊，請參閱 [了解屬性對應類型](customize-application-attributes.md#understanding-attribute-mapping-types)。
* 篩選目標的應用程式執行的 Azure AD 佈建動作。 您不需要完整同步處理物件的 Azure AD，您可以限制執行的動作。 

  例如，只選取**更新**和 Azure AD 只會更新現有的使用者帳戶的應用程式中，但並不會建立新的。 只能選取**建立**和 Azure 只會建立新的使用者帳戶，但不會更新現有的。 這項功能可讓系統管理員建立不同的對應，為帳戶建立和更新工作流程。

* 加入新的屬性對應。 選取 **加入新的對應**底部**屬性對應**窗格。 填寫**編輯屬性**表單，然後選取**確定**將新的對應新增到清單。 

### <a name="settings"></a>設定

您可以啟動和停止 Azure AD 佈建服務中的所選應用程式**設定**區域**佈建**螢幕。 您也可以選擇清除佈建的快取，並重新啟動服務。

如果是初次為應用程式啟用佈建，將 [佈建狀態] 變更為 [開啟] 即可開啟服務。 這項變更會導致 Azure AD 佈建服務以執行初始同步處理。它會讀取中指派的使用者**使用者和群組**區段中，查詢目標應用程式，然後再執行 佈建在 Azure AD 中定義的動作**對應**一節。 在此過程中，佈建服務會儲存有關其所管理，哪些使用者帳戶快取的資料，所以不曾在指派範圍的目標應用程式內的非受控帳戶不會受到解除佈建作業。 初始同步處理之後，每隔十分鐘佈建服務會自動同步處理使用者和群組物件。

變更**佈建狀態**要**關閉**暫停佈建服務。 在此狀態下，Azure 不會建立、 更新或移除應用程式中的任何使用者或群組物件。 變更狀態回到**上**和服務將收取離開的地方。

選取 **清除目前的狀態，並重新啟動同步處理**核取方塊，然後選取**儲存**來：

* 停止佈建服務
* 傾印的快取的資料，有關 Azure AD 來管理哪些帳戶
* 重新啟動服務，並執行初始同步處理一次

此選項可讓系統管理員 」 重新開始佈建的部署程序。

### <a name="synchronization-details"></a>同步處理詳細資料

本章節會提供作業的佈建服務，包括佈建服務執行對應用程式，以及管理多少使用者和群組物件的第一個和最後一個時間相關的其他詳細資料。

若要提供的連結**佈建活動報告**，其中提供記錄檔的所有使用者和群組建立、 更新和移除之間的 Azure AD 和目標應用程式。 也會提供連結**佈建錯誤報表**，其中提供更詳細的錯誤訊息，使用者和群組物件，而無法讀取、 建立、 更新或移除。
