---
title: 動態群組成員資格新增/移除使用者 - 教學課程 - Azure Active Directory
description: 在本教學課程中，您會使用群組與使用者成員資格規則自動新增或移除使用者
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3883ddcad1c41e131d52016e4fa94a3e668adcd1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209722"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>教學課程：自動新增或移除群組成員

在 Azure Active Directory (Azure AD) 中，您可以自動新增或移除安全性群組或 Office 365 群組的使用者，而無須一直以手動方式處理。 每當使用者或裝置的任何屬性有所變更時，Azure AD 就會評估您租用戶中的所有動態群組規則，以查看變更是否會新增或移除成員。

在本教學課程中，您了解如何：
> [!div class="checklist"]
> * 從特定合作夥伴公司建立會自動填入的來賓使用者群組
> * 為來賓使用者群組指派存取合作夥伴專屬功能的授權
> * 額外好處：藉由移除來賓使用者，以便您為成員指定僅限內部網站的使用者存取權 (舉例而言)，以保障**所有使用者**群組的安全性

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

您必須是租用戶的系統管理員，且具有一個 Azure AD Premium 授權，才能使用此功能。 如果您沒有授權，請在 Azure AD 中選取 [授權] > [產品] > [試用/購買]。

您不需要為使用者指派授權，他們即會成為動態群組的成員。 只要您在租用戶中具有最低數量的可用 Azure AD Premium P1 授權，即可涵蓋所有這類的使用者。 

## <a name="create-a-group-of-guest-users"></a>建立來賓使用者的群組

首先，您將為全部來自單一合作夥伴公司的來賓使用者建立一個群組。 他們需要特殊的授權，因此，針對此用途建立一個群組通常會較有效率。

1. 使用具備租用戶全域管理員身分的帳戶來登入 Azure 入口網站 (https://portal.azure.com)。
2. 選取 [Azure Active Directory] > [群組] > [新增群組]。
  ![選取新增群組命令](./media/groups-dynamic-tutorial/new-group.png)
3. 在 [群組] 刀鋒視窗上：
  
  * 選取 [安全性] 作為群組類型
  * 輸入 `Guest users Contoso` 作為群組的名稱和描述
  * 將 [成員資格類型] 變更為 [動態使用者]
  * 選取 [新增動態查詢]
  
4. 選取 [進階規則]，然後在 [進階規則] 方塊中輸入：`(user.userType -eq "Guest") -and (user.companyName -eq "Contoso")`
5. 選取 [新增查詢] 以關閉刀鋒視窗。
6. 在 [群組] 刀鋒視窗上選取 [建立]，以建立群組。

## <a name="assign-licenses"></a>指派授權

現在您已有新的群組，接下來可以套用這些合作夥伴使用者需要的授權。

1. 在 Azure AD 中選取 [授權]，選取一或多個授權，然後選取 [指派]。
2. 選取 [使用者和群組]，然後選取 [來賓使用者 Contoso] 群組，並儲存您的變更。
3. [指派選項] 可讓您開啟或關閉您所選授權中包含的服務方案。 當您進行變更時，請務必按一下 [確定] 以儲存變更。
4. 若要完成指派，在 [指派授權] 窗格中，按一下窗格底部的 [指派]。

## <a name="remove-guests-from-all-users-group"></a>從所有使用者群組中移除來賓

您最終的管理計劃可能是要依公司將所有的來賓使用者指派給其本身的群組。 現在您也可以變更**所有使用者**群組，使其保留給租用戶中的成員使用者專用。 然後，您可以使用它來指派您的主要組織專屬的應用程式和授權。

   ![將所有使用者群組變更為僅限成員](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>清除資源

**移除來賓使用者群組**

1. 使用具備租用戶全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [Azure Active Directory] > [群組]。 依序選取 [來賓使用者 Contoso] 群組、省略符號 (...) 和 [刪除]。 當您刪除群組時，任何已指派的授權都會移除。

**還原所有使用者群組**
1. 選取 [Azure Active Directory] > [群組]。 從 [所有使用者] 群組的名稱，以開啟該群組。
1. 選取 [動態成員資格規則]，清除規則中的所有文字，然後選取 [儲存]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：
> [!div class="checklist"]
> * 建立來賓使用者的群組
> * 將授權指派給新群組
> * 將所有使用者群組變更為僅限成員

繼續參考下一篇文章，以深入了解群組型授權的基本概念
> [!div class="nextstepaction"]
> [群組授權基本概念](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



