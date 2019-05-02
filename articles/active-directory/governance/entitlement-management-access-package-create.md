---
title: 在 Azure AD 權限管理 （預覽）-Azure Active Directory 中建立新的存取封裝
description: 了解如何建立新的存取套件，您想要在 Azure Active Directory 權限管理 （預覽） 中共用的資源。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/24/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad6570a3f30e40e4074502a8ce85bf739f58d3f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866429"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 權限管理 （預覽） 中建立新的存取封裝

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

存取封裝可讓您執行自動管理存取套件的生命週期的存取權的資源和原則的單次安裝。 本文說明如何建立新的存取封裝。

## <a name="overview"></a>概觀

所有存取套件必須都放在稱為目錄的容器中。 目錄會定義哪些資源，您可以新增至您的存取封裝。 如果您未指定目錄，便會存取套件置於一般的目錄。 目前，您無法移動現有的存取封裝至不同的目錄。

所有存取封裝都必須至少一個原則。 原則會指定誰可以要求存取套件以及核准與到期設定。 當您建立新的存取封裝時，您可以在您目錄中，不會在您的目錄，僅限系統管理員直接指派的使用者建立使用者的初始原則，或您可以選擇稍後建立的原則。

下圖顯示高階的程序來建立新的存取封裝。

![建立存取封裝的處理序](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>啟動新的存取套件

**必要的角色：** 使用者管理員或目錄的擁有者

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 按一下  **Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**存取套件**。

    ![在 Azure 入口網站中的權限管理](./media/entitlement-management-shared/elm-access-packages.png)

1. 按一下 **新的存取封裝**。

## <a name="basics"></a>基本概念

在 **基本概念**索引標籤上，您指定存取套件的名稱，並指定所在的目錄來建立存取封裝中的。

1. 輸入顯示名稱和存取套件的描述。 送出要求，以存取封裝時，使用者會看到這項資訊。

1. 在 **目錄**下拉式清單中，選取您想要建立存取權的目錄封裝中。 比方說，您可能必須管理所有您可以要求的行銷資源目錄擁有者。 在此情況下，您可以選擇行銷的目錄。

    您只會看到目錄已建立存取封裝中的權限。 若要在現有的目錄中建立存取套件，您必須是至少一個使用者系統管理員、 類別目錄的擁有者或存取套件管理員。

    ![存取封裝-基本概念](./media/entitlement-management-access-package-create/basics.png)

    如果您想要建立存取套件的新目錄中，按一下**新建**。 輸入目錄名稱和描述，然後按一下**建立**。

    您要建立存取套件，並包含在其中任何資源將會新增至新的類別目錄。 此外，您會自動成為目錄的第一個擁有者。 您可以新增額外的類別目錄的擁有者。

    若要建立新的目錄，您必須是至少一個使用者系統管理員或目錄建立者。

1. 单击“下一步”。

## <a name="resource-roles"></a>資源角色

在 **資源角色**索引標籤上，選取要包含在存取封裝中的資源。

1. 按一下您想要新增的資源類型 (**群組**，**應用程式**，或**SharePoint 網站**)。

1. 選取在窗格中會出現，請從清單中選取一或多個資源。

    ![存取封裝-資源角色](./media/entitlement-management-access-package-create/resource-roles.png)

    如果您要建立存取套件的一般類別目錄或新的目錄中，您可以挑選任何您所擁有的目錄中的資源。 您必須是至少一個使用者系統管理員，或目錄建立者。

    如果您要在現有的目錄中建立存取套件，您可以選取已在目錄中而不需要擁有它的任何資源。

    如果您是在使用者系統管理員或目錄擁有者，您會有另一個選項選取您擁有尚未在目錄中的資源。 如果您目前未選取的目錄中選取資源，這些資源也會加入至其他目錄系統管理員，來建置以存取套件的目錄。 如果您只想要選取目前在所選目錄中，核取的資源**只會看到**頂端的 [選取的移動瀏覽] 核取方塊。

1. 一旦您選取資源，請在**角色**清單中，選取您想要指派的使用者之資源的角色。

    ![存取封裝-資源角色選取](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. 单击“下一步”。

## <a name="policy"></a>原則

在 **原則**索引標籤上，建立指定使用者可以要求存取套件以及核准與到期日設定的第一個原則。 稍後，您可以建立更多的原則，以允許使用者使用他們自己的核准與到期日設定要求存取套件的其他群組。 您也可以選擇稍後建立的原則。

1. 設定**建立第一個原則**切換至**現在**或是**稍後**。

    ![存取封裝-原則](./media/entitlement-management-access-package-create/policy.png)

1. 如果您選取**稍後**，往下跳到[檢閱 + 建立](#review--create)一節，以建立您的存取封裝。

1. 如果您選取**現在**，在下列的原則區段中的一個執行步驟。

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>檢閱 + 建立

在 **檢閱 + 建立**索引標籤上，您可以檢閱您的設定和檢查是否有任何驗證錯誤。

1. 檢閱存取封裝的設定

    ![存取封裝-原則啟用原則設定](./media/entitlement-management-access-package-create/review-create.png)

1. 按一下 **建立**建立存取封裝。

    新的存取封裝會出現在存取套件的清單。

## <a name="next-steps"></a>後續步驟

- [編輯和管理現有的存取封裝](entitlement-management-access-package-edit.md)
- [建立和管理目錄](entitlement-management-catalog-create.md)
