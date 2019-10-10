---
title: 自訂 Azure AD 屬性對應 | Microsoft Docs
description: 了解 Azure Active Directory 中 SaaS 應用程式有哪些屬性對應，以及如何修改屬性對應來應付業務需求。
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
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef3d6a47986056925f9964638c9c7192341ca5f9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240998"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>在 Azure Active Directory 中自訂 SaaS 應用程式的使用者佈建屬性對應

Microsoft Azure AD 提供對協力廠商 SaaS 應用程式（例如 Salesforce、G Suite 等）進行使用者布建的支援。 如果您啟用協力廠商 SaaS 應用程式的使用者布建，Azure 入口網站會透過屬性對應來控制其屬性值。

Azure AD 使用者物件和每個 SaaS 應用程式的使用者物件之間，有一組預先設定的屬性和屬性對應。 有些應用程式會管理其他類型的物件以及使用者，例如群組。

您可以根據您的業務需求自訂預設的屬性對應。 因此，您可以變更或刪除現有的屬性對應，或建立新的屬性對應。

## <a name="editing-user-attribute-mappings"></a>編輯使用者屬性對應

請遵循下列步驟來存取使用者布**建的對應功能：**

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com)。
1. 從左窗格中選取 [**企業應用程式**]。 隨即會顯示所有已設定的應用程式清單，包括從資源庫新增的應用程式。
1. 選取 [任何應用程式] 以載入其 [應用程式管理] 窗格，您可以在其中查看報表及管理應用程式設定。
1. 選取 [布建 **] 以管理**所選應用程式的使用者帳戶布建設定。
1. 展開 **[** 對應] 以查看和編輯在 Azure AD 與目標應用程式之間流動的使用者屬性。 如果目標應用程式支援它，則此區段可讓您選擇性地設定群組和使用者帳戶的布建。

   ![使用對應來查看和編輯使用者屬性](./media/customize-application-attributes/21.png)

1. 選取 [對應] 設定，以開啟相關的 [**屬性對應**] 畫面。 SaaS 應用程式需要一些屬性對應，才能正常運作。 若為必要的屬性，[刪除] 功能就無法使用。

   ![使用屬性對應來設定應用程式的屬性對應](./media/customize-application-attributes/22.png)

   在此螢幕擷取畫面中，您可以看到 Salesforce 中受管理物件的**Username**屬性已填入已連結 Azure Active Directory 物件的**userPrincipalName**值。

1. 選取現有的**屬性對應**，以開啟 [**編輯屬性**] 畫面。 在這裡，您可以編輯在 Azure AD 和目標應用程式之間流動的使用者屬性。

   ![使用編輯屬性編輯使用者屬性](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>了解屬性對應類型

透過屬性對應，您將可控制屬性在第三方 SaaS 應用程式中填入的方式。
支援四種不同的對應類型：

- **直接** - 目標屬性會填入 Azure AD 中連結物件的屬性值。
- **常數**–目標屬性會填入您指定的特定字串。
- **運算式** - 目標屬性會根據類似指令碼的運算式結果填入。
  如需詳細資訊，請參閱[在 Azure Active Directory 中撰寫屬性對應的運算式](functions-for-customizing-application-data.md)。
- **無** - 目標屬性保留未修改。 不過，如果目標屬性是空的，則會以您指定的預設值填入。

除了這四種基本類型，自訂屬性對應還支援選擇性的**預設**值指派的概念。 如果 Azure AD 或目標物件上沒有值，則預設值指派可確保目標屬性會填入值。 最常見的設定是將其保留空白。

### <a name="understanding-attribute-mapping-properties"></a>了解屬性對應屬性

在上一節中，您已經引進 [屬性對應類型] 屬性。
除了這個屬性之外，屬性對應也支援下列屬性：

- **來源屬性** - 來源系統中的使用者屬性 (例如：Azure Active Directory)。
- **目標屬性** - 目標系統中的使用者屬性 (例如：ServiceNow)。
- **使用此屬性**比對物件–是否應該使用此對應來唯一識別來源與目標系統之間的使用者。 通常會在 Azure AD 中的 userPrincipalName 或 mail 屬性上設定，這通常會對應至目標應用程式中的使用者名稱欄位。
- **比對優先順序** – 您可以設定多個比對屬性。 當有多個時，就會依照此欄位所定義的順序來評估它們。 只要找到相符項目，便不會評估進一步比對屬性。
- **套用此對應**
  - **Always** –在使用者建立和更新動作上套用此對應。
  - **僅限建立期間**-僅適用于使用者建立動作的對應。

## <a name="editing-group-attribute-mappings"></a>編輯群組屬性對應

選取的應用程式數目（例如 ServiceNow、Box 和 G Suite）支援布建群組物件和使用者物件的能力。 群組物件可以包含群組屬性，例如顯示名稱和電子郵件別名，以及群組成員。

![範例顯示具有已布建群組和使用者物件的 ServiceNow](./media/customize-application-attributes/24.png)

您可以選擇性地啟用或停用群組布建，方法是選取 [對應] 底下的 [群組對應 **]，並**在 [**屬性對應**] 畫面中將 [**已啟用**] 設定為所要

佈建為群組物件一部分的屬性，可依照與使用者物件相同的方式進行自訂，如前所述。 

> [!TIP]
> 佈建群組物件 (屬性和成員) [與將群組指派](assign-user-or-group-access-portal.md)給應用程式是不同的概念。 您可以將群組指派給應用程式，但只能佈建群組中所包含的使用者物件。 要在指派中使用群組，並不需要佈建整個群組的物件。

## <a name="editing-the-list-of-supported-attributes"></a>編輯支援的屬性清單

系統會預先設定支援指定應用程式的使用者屬性。 大部分應用程式的使用者管理 Api 不支援架構探索。 因此，Azure AD 布建服務無法藉由呼叫應用程式，以動態方式產生支援的屬性清單。

不過，有些應用程式支援自訂屬性，而 Azure AD 布建服務可以讀取和寫入自訂屬性。 若要在 Azure 入口網站中輸入其定義，請選取 [**屬性對應**] 畫面底部的 [**顯示先進選項**] 核取方塊，然後選取應用程式的 [**編輯屬性清單**]。

支援屬性清單自訂的應用程式和系統包括：

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory (支援 [Azure AD Graph API 預設屬性](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity)和自訂目錄擴充功能)
- 支援 [SCIM 2.0](https://tools.ietf.org/html/rfc7643) 的應用程式，定義於[核心結構描述](https://tools.ietf.org/html/rfc7643)中的屬性必須新增至此處

> [!NOTE]
> 我們建議，只有已自訂應用程式和系統的結構描述，並確知其自訂屬性如何定義的系統管理員，才可編輯支援的屬性清單。 有時，這會需要熟悉應用程式或系統所提供的 API 和開發人員工具。

編輯支援的屬性清單時，必須提供下列屬性：

- **名稱** - 屬性的系統名稱，如目標物件的結構描述所定義。
- **類型**-屬性所儲存的資料類型（如目標物件的架構中所定義），可以是下列其中一種類型：
  - *二進位* - 屬性包含二進位資料。
  - *布林值* - 屬性包含 True 或 False 值。
  - *日期時間* - 屬性包含日期字串。
  - *整數* - 屬性包含整數。
  - *參考* - 屬性包含對目標應用程式中的另一個資料表所儲存的值進行參考的識別碼。
  - *字串* - 屬性包含文字字串。
- **是主索引鍵嗎？** -在目標物件的架構中，是否將屬性定義為主要索引鍵欄位。
- **必要？** -屬性是否必須填入目標應用程式或系統中。
- **是多重值嗎？** -屬性是否支援多個值。
- **大小寫完全相符嗎？** -屬性值是否會以區分大小寫的方式進行評估。
- **API 運算式**-請勿使用，除非是由特定布建連接器的檔（例如 Workday）指示。
- 參考的**物件屬性**-如果它是參考型別屬性，則此功能表可讓您選取目標應用程式中的資料表和屬性，其中包含與屬性相關聯的值。 例如，如果您有名為 "Department" 的屬性，且其儲存值參考了個別 "Departments" 資料表中的物件，則您會選取 "Departments.Name"。 指定應用程式支援的參考資料表和主要識別碼欄位已預先設定，而且目前無法使用 Azure 入口網站進行編輯，但可以使用[圖形 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)編輯。

若要新增新的屬性，請捲動至支援的屬性清單結尾處，使用提供的輸入填入上方的欄位，然後選取 [新增屬性]。 新增屬性完成後，請選取 [儲存]。 接著，您必須重載 [布**建] 索引**標籤，新屬性才會在 [屬性對應編輯器] 中變成可用。

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>還原預設屬性和屬性對應

如果您需要重新開機，並將現有的對應重設回其預設狀態，您可以選取 [**還原預設**對應] 核取方塊，並儲存設定。 這麼做會設定所有對應，如同應用程式剛從應用程式庫新增至您的 Azure AD 租使用者一樣。

選取此選項將會在布建服務執行時，有效地強制重新同步處理所有使用者。

> [!IMPORTANT]
> 我們強烈建議您將布建**狀態**設定為 [**關閉**]，再叫用此選項。

## <a name="what-you-should-know"></a>您應該知道的事情

- Microsoft Azure AD 提供有效率的同步處理程序實作。 在初始化環境中，同步處理期間只會處理需要更新的物件。
- 更新屬性對應會影響同步處理期間的效能。 更新屬性對應組態需要重新評估所有受控物件。
- 建議的最佳作法是將連續變更的次數至少保留給您的屬性對應。
- 目前不支援新增要布建到應用程式的相片屬性，因為您無法指定要同步處理相片的格式。 您可以在[User Voice](https://feedback.azure.com/forums/169401-azure-active-directory)上要求此功能
- 屬性 IsSoftDeleted 通常是應用程式預設對應的一部分。 IsSoftdeleted 在四個案例的其中之一（使用者因為未從應用程式解除指派而超出範圍、使用者因為不符合範圍篩選而超出範圍，使用者已在 Azure AD 中虛刪除，或屬性 AccountEnabled 設定為 false 在使用者上）。 
- Azure AD 布建服務不支援提供 null 值

## <a name="next-steps"></a>後續步驟

- [自動化 SaaS 應用程式使用者佈建/解除佈建](user-provisioning.md)
- [撰寫屬性對應的運算式](functions-for-customizing-application-data.md)
- [適用於使用者佈建的範圍篩選器](define-conditional-rules-for-provisioning-user-accounts.md)
- [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](use-scim-to-provision-users-and-groups.md)
- [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)
