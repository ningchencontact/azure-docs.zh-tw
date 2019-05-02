---
title: 自訂 Azure AD 屬性對應 | Microsoft Docs
description: 了解 Azure Active Directory 中 SaaS 應用程式有哪些屬性對應，以及如何修改屬性對應來應付業務需求。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: celested
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: b63118a7c5fae49edebe4ae4976a1362781ae8cf
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712861"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>在 Azure Active Directory 中自訂 SaaS 應用程式的使用者佈建屬性對應
Microsoft Azure AD 使用者佈建到例如 Salesforce、 G Suite 等協力廠商 SaaS 應用程式提供支援。 如果您啟用使用者佈建的第三方 SaaS 應用程式，Azure 入口網站會控制其屬性值，透過 屬性對應。

沒有一組預先設定的屬性和 Azure AD 使用者物件和每個 SaaS 應用程式的使用者物件之間的屬性對應。 某些應用程式管理物件，例如群組的使用者，以及其他的類型。

您可以根據您的業務需求自訂預設的屬性對應。 因此，您可以變更或刪除現有的屬性對應，或建立新的屬性對應。
 
## <a name="editing-user-attribute-mappings"></a>編輯使用者屬性對應

請遵循下列步驟來存取**對應**使用者佈建的功能：

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com)。

1. 選取 **企業應用程式**從左窗格中。 會顯示所有設定的應用程式的清單，包括從資源庫新增的應用程式。

1. 選取要載入其應用程式管理 窗格，您可以在其中檢視報表以及管理應用程式設定的任何應用程式。

1. 選取 **佈建**來管理使用者帳戶佈建所選的應用程式的設定。

1. 依序展開**對應**來檢視和編輯 Azure AD 之間流動的使用者屬性和目標應用程式。 如果目標應用程式支援的話，這個區段可讓您選擇性地設定 佈建群組和使用者帳戶。

   ![Salesforce](./media/customize-application-attributes/21.png) 

1. 選取 **對應**以開啟相關的組態**屬性對應**螢幕。 若要正常運作的 SaaS 應用程式需要某些屬性對應。 若為必要的屬性，[刪除] 功能就無法使用。

   ![Salesforce](./media/customize-application-attributes/22.png)

   在這個螢幕擷取畫面中，您可以看到**使用者名稱**Salesforce 中受控物件的屬性會填入**userPrincipalName**連結 Azure Active Directory 物件的值。

1. 選取現有**屬性對應**來開啟**編輯屬性**螢幕。 您可以在這裡編輯 Azure AD 之間流動的使用者屬性和目標應用程式。

   ![Salesforce](./media/customize-application-attributes/23.png)


### <a name="understanding-attribute-mapping-types"></a>了解屬性對應類型
透過屬性對應，您將可控制屬性在第三方 SaaS 應用程式中填入的方式。 支援四種不同的對應類型：

* **直接** - 目標屬性會填入 Azure AD 中連結物件的屬性值。
* **常數**– 目標屬性會填入您所指定的特定字串。
* **運算式** - 目標屬性會根據類似指令碼的運算式結果填入。 
  如需詳細資訊，請參閱[在 Azure Active Directory 中撰寫屬性對應的運算式](functions-for-customizing-application-data.md)。
* **無** - 目標屬性保留未修改。 不過，如果目標屬性是空的它會填入您所指定的預設值。

這四個基本類型，以及自訂的屬性對應還支援選擇性的概念**預設**值指派。 預設值指派可確保目標屬性會填入值，如果沒有值，在 Azure AD 中，或在目標物件上。 最常見的設定是將其保留空白。


### <a name="understanding-attribute-mapping-properties"></a>了解屬性對應屬性

在上一個區段中，您已經引進的屬性對應類型屬性。
這個屬性，以及屬性對應還支援下列屬性：

- **來源屬性** - 來源系統中的使用者屬性 (例如：Azure Active Directory)。
- **目標屬性** - 目標系統中的使用者屬性 (例如：ServiceNow)。
- **使用此屬性比對物件**– 是否應該將此對應用於唯一識別來源和目標系統之間的使用者。 它通常是 userPrincipalName 或 mail 屬性上設定，在 Azure AD 中，通常會對應至目標應用程式中的使用者名稱 欄位。
- **比對優先順序** – 您可以設定多個比對屬性。 如果有多個，則會評估此欄位所定義的順序。 只要找到相符項目，便不會評估進一步比對屬性。
- **套用此對應**
    - **一律**– 將此對應套用於使用者建立和更新動作。
    - **僅限建立期間**-僅將此對應套使用者建立動作。


## <a name="editing-group-attribute-mappings"></a>編輯群組屬性對應

選取的數字的應用程式，例如 ServiceNow、 Box 和 G Suite 支援佈建群組物件和使用者物件的能力。 群組物件可以包含群組的屬性，例如顯示名稱和電子郵件別名，以及群組成員。

![ServiceNow](./media/customize-application-attributes/24.png)

群組佈建可以選擇性地啟用或停用選取下方的群組對應**對應**，並設定**已啟用**以在您想要的選項**屬性對應**螢幕。

佈建為群組物件一部分的屬性，可依照與使用者物件相同的方式進行自訂，如前所述。 

>[!TIP]
>佈建群組物件 (屬性和成員) [與將群組指派](assign-user-or-group-access-portal.md)給應用程式是不同的概念。 您可以將群組指派給應用程式，但只能佈建群組中所包含的使用者物件。 要在指派中使用群組，並不需要佈建整個群組的物件。


## <a name="editing-the-list-of-supported-attributes"></a>編輯支援的屬性清單

系統會預先設定支援指定應用程式的使用者屬性。 大部分應用程式的使用者管理 Api 不支援結構描述探索。 因此，Azure AD 佈建服務無法以動態方式產生藉由呼叫應用程式的 支援的屬性清單。 

不過，有些應用程式支援自訂屬性，而且可以讀取 Azure AD 佈建服務，並將其寫入自訂屬性。 若要在 Azure 入口網站中輸入其定義，請選取**顯示進階選項**底部的核取方塊**屬性對應**畫面上，然後按**編輯屬性清單**您的應用程式。

支援屬性清單自訂的應用程式和系統包括：

* Salesforce
* ServiceNow
* Workday
* Azure Active Directory (支援 [Azure AD Graph API 預設屬性](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity)和自訂目錄擴充功能)
* 支援 [SCIM 2.0](https://tools.ietf.org/html/rfc7643) 的應用程式，定義於[核心結構描述](https://tools.ietf.org/html/rfc7643)中的屬性必須新增至此處

>[!NOTE]
>我們建議，只有已自訂應用程式和系統的結構描述，並確知其自訂屬性如何定義的系統管理員，才可編輯支援的屬性清單。 有時，這會需要熟悉應用程式或系統所提供的 API 和開發人員工具。 

編輯支援的屬性清單時，必須提供下列屬性：

* **名稱** - 屬性的系統名稱，如目標物件的結構描述所定義。 
* **型別**-的資料類型的屬性存放區，它可以是下列類型的其中一個目標物件的結構描述中所定義：
   * *二進位* - 屬性包含二進位資料。
   * *布林值* - 屬性包含 True 或 False 值。
   * *日期時間* - 屬性包含日期字串。
   * *整數* - 屬性包含整數。
   * *參考* - 屬性包含對目標應用程式中的另一個資料表所儲存的值進行參考的識別碼。
   * *字串* - 屬性包含文字字串。 
* **是主索引鍵嗎？** -是否屬性定義為目標物件的結構描述中的主索引鍵欄位。
* **必要？** -屬性是否必須填入目標應用程式或系統中。
* **是多重值嗎？** -是否屬性支援多個值。
* **大小寫完全相符嗎？** -是否區分大小寫的方式評估屬性值。
* **API 運算式**-未使用，除非特定佈建連接器 （例如 Workday) 的文件的指示。
* **參考的物件屬性**-如果它是參考型別屬性，則此功能表可讓您選取的資料表和屬性的目標應用程式，其中包含與屬性相關聯的值。 例如，如果您有名為 "Department" 的屬性，且其儲存值參考了個別 "Departments" 資料表中的物件，則您會選取 "Departments.Name"。 參考資料表和指定的應用程式支援的主要識別碼欄位都預先設定目前無法使用 Azure 入口網站中，編輯但可使用編輯[Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)。

若要新增新的屬性，請捲動至支援的屬性清單結尾處，使用提供的輸入填入上方的欄位，然後選取 [新增屬性]。 新增屬性完成後，請選取 [儲存]。 接著，您需要重新載入**佈建**變成可用屬性對應編輯器中的新屬性的索引標籤。

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>還原預設屬性和屬性對應

您應該要重新開始 」 和 「 重設您的現有對應回其預設狀態，您可以選取**還原預設對應**核取方塊，並儲存設定。 如此一來設定所有對應，如同應用程式剛從應用程式資源庫新增到 Azure AD 租用戶。 

選取此選項在佈建服務執行時，實際上會強制所有使用者的重新同步處理。 

>[!IMPORTANT]
>我們強烈建議**佈建狀態**設為**關閉**之前叫用此選項。


## <a name="what-you-should-know"></a>您應該知道的事情

* Microsoft Azure AD 提供有效率的同步處理程序實作。 在初始化環境中，同步處理期間只會處理需要更新的物件。 

* 更新屬性對應會影響同步處理期間的效能。 更新屬性對應組態需要重新評估所有受控物件。 

* 建議的最佳作法是將連續的變更數目至少您屬性對應。


## <a name="next-steps"></a>後續步驟

* [自動化 SaaS 應用程式使用者佈建/解除佈建](user-provisioning.md)
* [撰寫屬性對應的運算式](functions-for-customizing-application-data.md)
* [適用於使用者佈建的範圍篩選器](define-conditional-rules-for-provisioning-user-accounts.md)
* [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](use-scim-to-provision-users-and-groups.md)
* [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)


