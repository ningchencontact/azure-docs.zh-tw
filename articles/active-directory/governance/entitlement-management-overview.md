---
title: 什麼是 Azure AD 權限管理？ （預覽）-Azure Active Directory
description: 取得 Azure Active Directory 權限管理，以及如何使用它來管理內部與外部使用者的存取權群組、 應用程式和 SharePoint Online 網站的總覽。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efd3ff8a6e7ddf2aa6242cc322d8a6536a6bd26b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66474060"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>什麼是 Azure AD 權限管理？ (預覽)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在組織中的員工需要存取各種群組、 應用程式，以及站台來執行其工作。 管理此存取權，是一項挑戰。 在大部分情況下，沒有任何組織的清單，使用者需要專案的所有資源。 專案經理有充分的了解所需的資源，多久以及相關的每個人將最後一個專案。 不過，專案經理通常沒有核准，或授與給其他人的存取權的權限。 當您嘗試使用外部的個人或公司時，此案例中變得很複雜。

Azure Active Directory (Azure AD) 權利管理可協助您管理的內部使用者以及在組織外部的使用者群組、 應用程式和 SharePoint Online 網站的存取權。

## <a name="why-use-entitlement-management"></a>為何要使用權利管理？

企業組織經常面臨的挑戰，例如管理資源的存取權時：

- 使用者可能不知道他們應該有何種存取權
- 使用者可能難以找出適當的人員或適當的資源
- 一旦使用者找到並收到資源的存取權，他們可能要抓緊頭上再高於所需的商務目的存取

需要從另一個目錄，例如來自供應鏈的組織或其他商務夥伴的外部使用者的存取權的使用者更難處理這些問題。 例如:

- 組織可能不知道所有其他目錄中的特定個人能夠邀請他們
- 即使組織能夠邀請這些使用者，組織可能不記得要以一致的方式管理所有使用者的存取權

Azure AD 權限管理可以協助解決這些挑戰。

## <a name="what-can-i-do-with-entitlement-management"></a>我可以使用權利管理來做什麼？

以下是一些權限管理的功能：

- 建立封裝的使用者可以要求的相關資源
- 定義規則來要求資源存取的到期時
- 管理內部與外部使用者的存取權的生命週期
- 資源的委派管理
- 指定核准者核准要求
- 建立報表，以追蹤歷程記錄

如需身分識別控管和權限管理的概觀，觀看下列影片 Ignite 2018 大會中：

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>可以管理哪些資源？

以下是您可以管理存取權與權限管理的資源類型：

- Azure AD 安全性群組
- Office 365 群組
- Azure AD 企業應用程式，包括 SaaS 應用程式和自訂整合的應用程式支援同盟或佈建
- SharePoint Online 網站集合和網站

您也可以控制其他依賴 Azure AD 安全性群組或 Office 365 群組的資源的存取權。  例如:

- 您也可以為使用者授權的 Microsoft Office 365 中，提供存取封裝中使用的 Azure AD 安全性群組，並設定[群組型授權](../users-groups-roles/licensing-groups-assign.md)該群組
- 您可以存取封裝中使用的 Azure AD 安全性群組，並建立來管理 Azure 資源的存取權授與使用者[Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)該群組

## <a name="what-are-access-packages-and-policies"></a>存取封裝和原則是什麼？

權利管理介紹的概念*存取套件*。 存取封裝是使用者需要處理專案，或執行其作業的所有資源的組合。 資源包括群組、 應用程式或站台的存取權。 存取封裝用來管理您的內部員工，以及在組織外部的使用者的存取。 存取套件會定義在名為*目錄*的容器中。

存取封裝也包含一或多個*原則*。 原則定義的規則或 guardrails 存取套件。 啟用原則會強制執行適當的使用者會被授與存取適當的資源，並正確數量的時間。

![存取封裝和原則](./media/entitlement-management-overview/elm-overview-access-package.png)

存取封裝和其原則，會定義存取套件管理員：

- 資源
- 角色的使用者需要的資源
- 內部使用者以及外部使用者有資格以要求存取權
- 核准程序和使用者可以核准或拒絕存取
- 使用者的存取權的持續時間

下圖顯示的不同元素的範例，在權限管理。 它會顯示兩個範例中存取套件。

- **存取封裝 1**包含做為資源的單一群組。 可讓一組目錄中的使用者來要求權限的原則來定義存取。
- **存取封裝 2**包含群組、 應用程式，以及 SharePoint Online 網站做為資源。 存取會使用兩個不同的原則定義。 第一個原則可讓一組目錄中的使用者來要求權限。 第二個原則可讓外部目錄中的使用者來要求權限。

![權利管理概觀](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>外部使用者

使用時[Azure AD 企業對企業 (B2B)](../b2b/what-is-b2b.md)邀請體驗，您必須已經知道您想要帶入您的資源目錄，並使用的外部來賓使用者的電子郵件地址。 此絕佳正努力的較小或短期專案和您已經知道所有參與者，但這是很難管理，如果您有許多您想要使用的使用者時，或經過一段時間變更的參與者的運作方式。  比方說，您可以使用另一個組織並有一個連絡窗口與該組織中，但經過一段時間來自該組織的其他使用者也需要存取的功能。

使用權利管理時，您可以定義原則，可讓您指定，也可以要求存取封裝使用 Azure AD 中，組織的使用者。 您可以指定是否需要核准 」 和 「 存取的到期日。 如果需要核准，您也可以指定為核准者從您先前受邀-因為它們都可能會知道哪些從其組織的外部使用者需要存取的外部組織的一或多個使用者。 一旦您已設定存取套件，您可以傳送存取套件連結至您在外部組織的聯絡人。 該連絡人可以與其他使用者共用之外部組織，以及他們可以使用此連結來要求存取套件。  來自該組織已邀請到目錄的使用者也可以使用該連結。

要求獲准後，權利管理將會佈建必要的存取權，可能包括邀請使用者，如果它們尚不在您的目錄中的使用者。 Azure AD 會自動為它們建立 B2B 帳戶。  請注意，系統管理員可能會有先前限制的組織允許共同作業，藉由設定[B2B 允許或拒絕清單](../b2b/allow-deny-list.md)來允許或封鎖邀請其他組織。  如果使用者不允許所允許或封鎖清單中，則不會受邀。

因為您不想要永久的外部使用者的存取權，您會指定在原則中，例如 180 天的到期日。 在 180 天後，若不更新其存取權，權利管理將會移除該存取套件相關聯的所有存取。  如果已透過權利管理受邀的使用者不有任何其他存取封裝指派，然後時，他們會失去其過去的指派，其 B2B 帳戶將 30 天內，封鎖登入並接著移除。  這可防止暴增的不必要的帳戶。  

## <a name="terminology"></a>術語

若要進一步了解權限管理和其文件，您應該檢閱下列詞彙。

| 詞彙或概念 | 描述 |
| --- | --- |
| 權利管理 | 指派、 撤銷，並管理存取套件的服務。 |
| 存取封裝 | 權限和使用者可以要求的資源原則的集合。 存取封裝一定會包含在目錄中。 |
| 存取要求 | 要求存取存取套件。 要求通常會透過工作流程。 |
| policies | 一組規則來定義存取生命週期，例如使用者如何取得存取、 誰可以認可，以及使用者可以存取的時間長度。 原則的範例包括員工存取，以及外部存取。 |
| catalog | 相關的資源和存取封裝的容器。 |
| 一般類別目錄 | 始終是可用的內建目錄。 若要將資源新增至一般類別目錄中，需要特定權限。 |
| resource | 資產或使用者可以授與權限的服務 （例如群組、 應用程式或站台）。 |
| 資源類型 | 資源類型，其中包括群組、 應用程式和 SharePoint Online 網站。 |
| 資源角色 | 與資源相關聯的權限集合。 |
| 資源目錄 | 有一或多個共用的資源目錄。 |
| 指派的使用者 | 存取封裝的使用者或群組的指派。 |
| enable | 讓使用者要求存取封裝程序。 |

## <a name="roles-and-permissions"></a>角色和權限

權利管理具有不同的角色，根據工作功能。

| 角色 | 描述 |
| --- | --- |
| [使用者管理員](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) | 管理權限管理的所有的層面。<br/>建立使用者和群組。 |
| 類別目錄的建立者 | 建立及管理目錄。 通常是 IT 系統管理員或資源擁有者。 會自動建立目錄的人會成為類別目錄的第一個類別目錄的擁有者。 |
| 目錄擁有者 | 編輯及管理現有的目錄。 通常是 IT 系統管理員或資源擁有者。 |
| 存取封裝管理員 | 編輯和管理目錄內的所有現有存取套件。 |
| 核准者 | 核准要求，以存取套件。 |
| 要求者 | 要求存取套件。 |

下表列出每個角色的權限。

| Task | 使用者管理員 | 類別目錄的建立者 | 目錄擁有者 | 存取封裝管理員 | 核准者 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [在一般的目錄中建立新的存取封裝](entitlement-management-access-package-create.md) | :heavy_check_mark: |  :heavy_check_mark: |  |  |  |
| [在目錄中建立新的存取封裝](entitlement-management-access-package-create.md) | :heavy_check_mark: |   | :heavy_check_mark: |  |  |
| [新增/移除從存取套件的資源角色](entitlement-management-access-package-edit.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [指定誰可以要求存取套件](entitlement-management-access-package-edit.md#add-a-new-policy) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [直接將使用者指派給存取套件](entitlement-management-access-package-edit.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [檢視指派給存取套件的人員](entitlement-management-access-package-edit.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [檢視存取套件的要求](entitlement-management-access-package-edit.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [檢視要求的傳遞錯誤](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [取消暫止的要求](entitlement-management-access-package-edit.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [隱藏存取套件](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [刪除存取封裝](entitlement-management-access-package-edit.md#delete) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [核准存取要求](entitlement-management-request-approve.md) |  |  |  |  | :heavy_check_mark: |
| [建立目錄](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [新增/移除從一般的目錄資源](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  |  |  |  |
| [新增/移除從目錄資源](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [新增目錄擁有者或存取套件管理員](entitlement-management-catalog-create.md#add-catalog-owners-or-access-package-managers) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [編輯/刪除目錄](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |

## <a name="license-requirements"></a>授權需求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

特製化的雲端，例如 Azure Government、 Azure 德國和 Azure 中國 21Vianet 目前不適用於此預覽版本中。

## <a name="next-steps"></a>後續步驟

- [教學課程：建立您第一次存取封裝](entitlement-management-access-package-first.md)
- [常見案例](entitlement-management-scenarios.md)
