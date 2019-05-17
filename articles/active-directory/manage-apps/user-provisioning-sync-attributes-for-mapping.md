---
title: 同步處理至 Azure AD 進行對應的屬性 |Microsoft Docs
description: 了解如何同步處理至 Azure AD 從您的內部部署 Active Directory 屬性。 在設定使用者佈建至 SaaS 應用程式時，可用於目錄擴充功能新增預設不同步處理的來源屬性。
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
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9460bc924ea662646360d1a3f5087949a39a03f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806112"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>同步處理至 Azure AD 佈建到應用程式從內部部署 Active Directory 屬性

當自訂的使用者佈建屬性對應，您可能會發現您想要對應的屬性不會出現在**來源屬性**清單。 這篇文章會示範如何從您內部部署 Active Directory (AD) 同步至 Azure Active Directory (Azure AD) 中加入遺漏的屬性。

Azure AD 必須包含建立使用者設定檔，佈建至 SaaS 應用程式的使用者帳戶從 Azure AD 時所需的所有資料。 在某些情況下，將資料提供您可能需要同步處理屬性從您內部部署至 Azure AD。 Azure AD Connect 會自動同步至 Azure AD 中，某些屬性，但並非所有屬性。 此外，某些屬性 （例如 SAMAccountName)，預設會同步處理可能不會透過 Azure AD Graph API 公開。 在這些情況下，您可以使用 Azure AD Connect 目錄擴充功能來同步處理至 Azure AD 的屬性。 如此一來，屬性將會顯示 Azure AD Graph API 和 Azure AD 佈建服務。

如果您需要佈建的資料位於 Active Directory，但不適用於佈建基於上述原因，請遵循下列步驟。
 
## <a name="sync-an-attribute"></a>同步處理屬性 

1. 開啟 Azure AD Connect 精靈，選擇 [工作]，然後選擇**自訂同步處理選項**。

   ![Azure Active Directory Connect 精靈其他工作 頁面](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Azure AD 全域管理員身分登入。 

3. 在 **選用功能**頁面上，選取**目錄擴充屬性同步處理**。
 
   ![Azure Active Directory Connect 精靈選用功能 頁面](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. 選取您想要擴充至 Azure AD 的屬性。
   > [!NOTE]
   > 在搜尋**可用的屬性**會區分大小寫。

   ![Azure Active Directory Connect 精靈目錄擴充功能選取 頁面](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. 完成 Azure AD Connect 精靈，並允許執行完整同步處理循環。 在週期完成後，會在延伸架構和新的值之間同步處理您內部部署 AD 與 Azure AD。
 
6. 在 Azure 入口網站中，當您進入[編輯使用者屬性對應](customize-application-attributes.md)，則**來源屬性**清單現在會包含新增的屬性格式`<attributename> (extension_<appID>_<attributename>)`。 選取屬性，然後將它對應至的目標應用程式佈建。

   ![Azure Active Directory Connect 精靈目錄擴充功能選取 頁面](media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> 能夠提供參考屬性，從內部部署 AD 中，這類**managedby**或是**DN/DistinguishedName**，目前不支援。 您可依要求這項功能[User Voice](https://feedback.azure.com/forums/169401-azure-active-directory)。 

## <a name="next-steps"></a>後續步驟

* [定義誰是佈建範圍中](define-conditional-rules-for-provisioning-user-accounts.md)
