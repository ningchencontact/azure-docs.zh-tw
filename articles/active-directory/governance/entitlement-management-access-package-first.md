---
title: 教學課程 - 在 Azure AD 權利管理 (預覽) 中建立您的第一個存取套件 - Azure Active Directory
description: 在 Azure Active Directory 權限管理 (預覽) 中建立第一個存取套件的逐步教學課程。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1688651466ba6748e1254c9d33bb24435602868b
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489158"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>教學課程：在 Azure AD 權利管理 (預覽) 中建立您的第一個存取套件

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

對員工所需的各種資源 (例如群組、應用程式和網站) 進行存取管理，對組織而言是很重要的功能。 您想要為員工授與維持其生產力所需的存取權，並且在不再需要時移除其存取權。

在本教學課程中，您將以 IT 系統管理員身分為 Woodgrove Bank 工作。 公司要求您建立 Web 專案的資源套件，供內部使用者透過自助式要求取用。 要求需經過核准，且使用者的存取權會在 30 天後到期。 在本教學課程中，Web 專案資源只是單一群組中的成員資格，但它可能是群組、應用程式或 SharePoint Online 網站的集合。

![案例概觀](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 透過群組建立存取套件作為資源
> * 指定核准者
> * 示範內部使用者如何要求存取套件
> * 核准存取要求

如果您沒有 Azure AD Premium P2 或 Enterprise Mobility + Security E5 授權，請建立免費的 [Enterprise Mobility + Security E5 試用版](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1)。

## <a name="prerequisites"></a>必要條件

若要使用 Azure AD 權利管理 (預覽)，您必須具有下列其中一個授權：

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 授權

## <a name="step-1-set-up-users-and-group"></a>步驟 1：設定使用者和群組

資源目錄會有一或多個共用的資源。 在此步驟中，您會在 Woodgrove Bank 目錄中建立名為**工程群組**的群組，作為權利管理的目標資源。 您也會設定內部要求者。

**必要角色：** 全域管理員或使用者管理員

![建立 [使用者和群組]](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. 以全域管理員或使用者管理員身分登入 [Azure 入口網站](https://portal.azure.com)。  

1. 在左側導覽列中，按一下 [Azure Active Directory]  。

1. 建立或設定以下兩個使用者。 您可以使用這些名稱或不同的名稱。 **Admin1** 可以是您目前以其身分登入的使用者。

    | Name | 目錄角色 | 說明 |
    | --- | --- | --- |
    | **Admin1** | 全域管理員<br/>-或-<br/>受限的系統管理員 (使用者管理員) | 系統管理員和核准者 |
    | **Requestor1** | 使用者 | 內部要求者 |

    在本教學課程中，系統管理員和核准者是同一人，但您通常會指定一或多人作為核准者。

1. 建立名為**工程群組**、成員資格類型為**已指派**的 Azure AD 安全性群組。

    此群組將是權利管理的目標資源。 此群組一開始應不含任何成員。

## <a name="step-2-create-an-access-package"></a>步驟 2：建立存取套件

*存取套件*是使用者在處理專案或執行其作業時所需之各種資源的組合。 存取套件會定義在名為*目錄*的容器中。 在此步驟中，您會在**一般**目錄中建立 **Web 專案存取套件**。

**必要角色：** 全域管理員或使用者管理員

![建立存取套件](./media/entitlement-management-access-package-first/elm-access-package.png)

1. 在 Azure 入口網站的左側瀏覽窗格中，按一下 [Azure Active Directory]  。

1. 在左側功能表中，按一下 [身分識別治理] 

1. 在左側功能表中，按一下 [存取套件]  。  如果您看到「拒絕存取」  ，請確定該目錄中有 Azure AD Premium P2 授權。

1. 按一下 [新增存取套件]  。

    ![Azure 入口網站中的權利管理](./media/entitlement-management-access-package-first/access-packages-list.png)

1. 在 [基本]  索引標籤上，輸入名稱 (**Web 專案存取套件**) 和描述 (**工程 Web 專案的存取套件**)。

1. 讓 [目錄]  下拉式清單設為 [一般]  。

    ![新增存取套件 - [基本] 索引標籤](./media/entitlement-management-access-package-first/basics.png)

1. 按 [下一步]  以開啟 [資源角色]  索引標籤。

    在此索引標籤上，您可以選取要包含在存取套件中的權限。

1. 按一下 [群組]  。

1. 在 [選取群組] 窗格中，尋找並選取您先前建立的**工程群組**群組。

    根據預設，您會看到 [一般]  目錄內部和外部的群組。 當您選取 [一般]  目錄外部的群組時，該群組將會新增至 [一般]  目錄。

    ![新增存取套件 - [資源角色] 索引標籤](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. 按一下 [選取]  將群組新增至清單。

1. 在 [角色]  下拉式清單中，選取 [成員]  。

    ![新增存取套件 - [資源角色] 索引標籤](./media/entitlement-management-access-package-first/resource-roles.png)

1. 按 [下一步]  以開啟 [原則]  索引標籤。

1. 將 [建立第一個原則]  切換為 [稍後]  。

    您將在下一節建立原則。

    ![新增存取套件 - [原則] 索引標籤](./media/entitlement-management-access-package-first/policy.png)

1. 按 下一步  以開啟 檢閱 + 建立  索引標籤。

    ![新增存取套件 - [檢閱 + 建立] 索引標籤](./media/entitlement-management-access-package-first/review-create.png)

1. 檢閱存取套件設定，然後按一下 [建立]  。

    您可能會看到一則訊息，指出存取套件因目錄尚未啟用而不會對使用者顯示。

    ![新增存取封裝 - 不會顯示的訊息](./media/entitlement-management-access-package-first/not-visible.png)

1. 按一下 [確定]  。

    幾分鐘後，您應該會看到已成功建立存取套件的通知。

## <a name="step-3-create-a-policy"></a>步驟 3：建立原則

*原則*會定義存取套件的存取規則或準則。 在此步驟中，您會建立允許資源目錄中的特定使用者要求存取套件的原則。 您也會指定要求必須經過核准，並指定核准者應由誰擔任。

![建立存取套件原則](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**必要角色：** 全域管理員或使用者管理員

1. 在 [Web 專案存取套件]  的左側功能表中，按一下 [原則]  。

    ![存取套件原則清單](./media/entitlement-management-access-package-first/policies-list.png)

1. 按一下 [新增原則]  以開啟 [建立原則]。

1. 輸入名稱 (**內部要求者原則**) 和描述 (**允許此目錄中的使用者要求存取 Web 專案資源**)。

1. 在 [可要求存取的使用者]  區段中，按一下 [您目錄中的使用者]  。

    ![建立原則](./media/entitlement-management-access-package-first/policy-create.png)

1. 向下捲動至 [選取使用者和群組]  區段，然後按一下 [新增使用者和群組]  。

1. 在 [選取使用者和群組] 窗格中，選取您先前建立的使用者 **Requestor1**，然後按一下 [選取]  。

1. 在 [要求]  區段中，將 [需要核准]  設為 [是]  。

1. 在 [選取核准者]  區段中，按一下 [新增核准者]  。

1. 在 [選取核准者] 窗格中，選取您先前建立的 **Admin1**，然後按一下 [選取]  。

    在本教學課程中，系統管理員和核准者是同一人，但您可以指定其他人作為核准者。

1. 在 [期限]  區段中，將 [存取套件期限]  設為 [天數]  。

1. 將 [存取有效期]  設為 **30** 天。

1. 針對 [啟用原則]  ，按一下 [是]  。

    ![建立原則設定](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. 按一下 [建立]  以建立**內部要求者原則**。

1. 在 Web 專案存取套件的左側功能表中，按一下 [概觀]  。

1. 複製**我的存取權入口網站連結**。

    您將在下一個步驟使用此連結。

    ![存取套件概觀 - 我的存取權入口網站的連結](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>步驟 4：要求存取

在此步驟中，您會以**內部要求者**的身分執行步驟，並要求存取套件的存取權。 要求者會使用名為「我的存取權入口網站」的網站提交其要求。 「我的存取權入口網站」可讓要求者提交存取套件的要求、查看他們已有存取權的存取套件，以及檢視其要求歷程記錄。

**必要角色：** 內部要求者

1. 登出 Azure 入口網站。

1. 在新的瀏覽器視窗中，瀏覽至您在上一個步驟中複製的「我的存取權入口網站」連結。

1. 以 **Requestor1** 的身分登入「我的存取權入口網站」。

    您應該會看到 **Web 專案存取套件**。

1. 如有必要，請按一下 [描述]  資料行中的箭頭，以檢視關於存取套件的詳細資料。

    ![我的存取權入口網站 - 存取套件](./media/entitlement-management-shared/my-access-access-packages.png)

1. 按一下核取記號以選取套件。

1. 按一下 [要求存取]  以開啟 [要求存取] 窗格。

1. 在 [業務理由]  方塊中，輸入**處理 Web 專案**的理由。

1. 將 [要求特定期間]  切換為 [是]  。

1. 將 [開始日期]  設為當天，並將 [結束日期]  設為隔天。

    ![我的存取權入口網站 - 要求存取](./media/entitlement-management-shared/my-access-request-access.png)

1. 按一下 [提交]  。

1. 在左側功能表中按一下 [要求歷程記錄]  ，以確認您的要求已提交。

## <a name="step-5-approve-access-request"></a>步驟 5：核准存取要求

在此步驟中，您會以**核准者**使用者身分登入，並核准內部要求者的存取要求。 核准者會使用與要求者用來提交要求的同一個「我的存取權入口網站」。 核准者可以使用「我的存取權入口網站」來檢視待決的核准，以及核准或拒絕要求。

**必要角色：** 核准者

1. 登出「我的存取權入口網站」。

1. 以 **Admin1** 的身分登入[我的存取權入口網站](https://myaccess.microsoft.com)。

1. 在左側功能表中，按一下 [核准]  。

1. 在 [待決]  索引標籤上，尋找 **Requestor1**。

    若未看到 Requestor1 的要求，請等待幾分鐘再重試。

1. 按一下 [檢視]  連結以開啟 [存取要求] 窗格。

1. 按一下 [核准]  。

1. 在 [原因]  方塊中，輸入原因 (**已核准 Web 專案的存取權**)。

    ![我的存取權入口網站 - 存取要求](./media/entitlement-management-shared/my-access-approve-request.png)

1. 按一下 [提交]  以提交您的決定。

    您應該會看到成功通過核准的訊息。

## <a name="step-6-validate-that-access-has-been-assigned"></a>步驟 6：驗證存取已指派

現在您已核准存取要求，在此步驟中，您將確認已為**內部要求者**指派存取套件，且他們此時已是**工程群組**群組的成員。

**必要角色：** 全域管理員或使用者管理員

1. 登出「我的存取權入口網站」。

1. 以 **Admin1** 的身分登入 [Azure 入口網站](https://portal.azure.com)。

1. 按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [存取套件]  。

1. 找出 **Web 專案存取套件**並加以點選。

1. 在左側功能表中，按一下 [要求]  。

    您應該會看到 Requestor1，以及狀態為「已傳遞」  的內部要求者原則。

1. 按一下要求，以查看要求詳細資料。

    ![存取套件 - 要求詳細資料](./media/entitlement-management-access-package-first/request-details.png)

1. 在左側導覽列中，按一下 [Azure Active Directory]  。

1. 按一下 [群組]  ，然後開啟**工程群組**群組。

1. 按一下 [成員]  。

    您應該會看到 **Requestor1** 列為成員。

    ![工程群組成員](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>步驟 7：清除資源

在此步驟中，您將移除先前所做的變更，並刪除 **Web 專案存取套件**存取套件。

**必要角色：** 全域管理員或使用者管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 開啟 **Web 專案存取套件**。

1. 按一下 [指派]  。

1. 針對 **Requestor1** 按一下省略符號 ( **...** )，然後按一下 [移除存取權]  。

    狀態會從「已傳遞」變更為「已過期」。

1. 按一下 [原則]  。

1. 針對**內部要求者原則**按一下省略符號 ( **...** )，然後按一下 [刪除]  。

1. 按一下 [資源角色]  。

1. 針對**工程群組**按一下省略符號 ( **...** )，然後按一下 [移除資源角色]  。

1. 開啟存取套件的清單。

1. 針對 **Web 專案存取專案**按一下省略符號 ( **...** )，然後按一下 [刪除]  。

1. 在 Azure Active Directory 中刪除您所建立的任何使用者，例如 **Requestor1** 和 **Admin1**。

1. 刪除**工程群組**群組。

## <a name="next-steps"></a>後續步驟

前往下一篇文章，以了解權利管理的常見案例步驟。
> [!div class="nextstepaction"]
> [常見案例](entitlement-management-scenarios.md)
