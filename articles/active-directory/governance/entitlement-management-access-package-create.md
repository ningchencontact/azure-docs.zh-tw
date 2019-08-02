---
title: 在 Azure AD 權利管理 (預覽) 中建立新的存取套件-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理 (預覽) 中建立您想要共用之資源的新存取封裝。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f033cf57c5a285e94372728677c91e021065fa9
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678211"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 權利管理 (預覽) 中建立新的存取套件

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

存取封裝可讓您進行一次性的資源和原則設定, 以自動管理存取套件的存取權。 本文說明如何建立新的存取封裝。

## <a name="overview"></a>總覽

所有存取封裝都必須放在稱為「目錄」的容器中。 目錄會定義您可以新增至存取套件的資源。 如果您未指定目錄, 則會將存取套件放入一般目錄。 目前, 您無法將現有的存取套件移至不同的目錄。

所有存取套件都必須至少有一個原則。 原則會指定誰可以要求存取套件, 以及核准和到期設定。 當您建立新的存取套件時, 您可以為目錄中的使用者建立初始原則, 針對不在您目錄中的使用者, 僅供系統管理員直接指派, 或者您可以選擇稍後再建立原則。

下圖顯示建立新存取套件的高階程式。

![建立存取封裝進程](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>啟動新的存取套件

**必要角色：** 全域管理員、使用者系統管理員或目錄擁有者

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [存取套件]。

    ![Azure 入口網站中的權利管理](./media/entitlement-management-shared/elm-access-packages.png)

1. 按一下 [新增存取套件]。

## <a name="basics"></a>基本知識

在 [**基本**] 索引標籤上, 您會提供存取封裝的名稱, 並指定要在哪一個目錄中建立存取封裝。

1. 輸入存取套件的 [顯示名稱] 和 [描述]。 當使用者提交存取套件的要求時, 將會看到這則資訊。

1. 在 [**類別目錄**] 下拉式清單中, 選取您想要在其中建立存取封裝的目錄。 例如, 您的目錄擁有者可能會管理所有可要求的行銷資源。 在此情況下, 您可以選取 [行銷目錄]。

    您只會看到您有權在中建立存取套件的目錄。 若要在現有的目錄中建立存取封裝, 您必須至少是全域管理員、使用者系統管理員、該目錄中的目錄擁有者, 或存取該目錄中的封裝管理員。

    ![存取套件-基本概念](./media/entitlement-management-access-package-create/basics.png)

    如果您是全域管理員或使用者系統管理員, 而且想要在未列出的新類別目錄中建立存取套件, 請按一下 [新建]。 輸入 [目錄名稱] 和 [描述], 然後按一下 [**建立**]。

    您所建立的存取套件和其中包含的任何資源都會新增至新的目錄。 您也可以稍後再新增其他目錄擁有者。


1. 按一下 [下一步]。

## <a name="resource-roles"></a>資源角色

在 [**資源角色**] 索引標籤上, 您可以選取要包含在存取套件中的資源。  要求和接收存取套件的使用者將會收到存取套件中的所有資源角色。

1. 按一下您要新增的資源類型 ([**群組**]、[**應用程式**] 或 [ **SharePoint 網站**])。

1. 在出現的 [選取] 窗格中, 從清單中選取一或多個資源。

    ![存取套件-資源角色](./media/entitlement-management-access-package-create/resource-roles.png)

    如果您要在一般類別目錄或新的目錄中建立存取套件, 您將能夠從您擁有的目錄中挑選任何資源。 您必須至少是一個全域管理員、一個使用者系統管理員或目錄建立者。

    如果您要在現有的目錄中建立存取套件, 您可以選取已在目錄中的任何資源, 而不擁有它。

    如果您是全域管理員、使用者系統管理員或目錄擁有者, 您可以選擇其他選項來選取您擁有但尚未在目錄中的資源。 如果您選取目前不在所選類別目錄中的資源, 這些資源也會新增至目錄, 供其他目錄管理員用來建立存取封裝。 如果您只想要選取目前在所選目錄中的資源, 請核取 [選取移動流覽] 頂端的 [**僅看到**] 核取方塊。

1. 選取資源之後, 請在 [**角色**] 清單中, 選取您想要讓使用者指派給資源的角色。

    ![存取套件-資源角色選取](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. 按一下 [下一步]。

## <a name="policy"></a>原則

在 [**原則**] 索引標籤上, 您可以建立第一個原則來指定誰可以要求存取套件, 以及核准和到期設定。 之後, 您可以建立更多原則, 讓其他使用者群組使用自己的核准和到期設定來要求存取封裝。 您也可以選擇稍後再建立原則。

1. 將 [**建立第一個原則**] 切換為 [**立即**] 或 [**稍後**]。

    ![存取套件-原則](./media/entitlement-management-access-package-create/policy.png)

1. 如果您選取 [**稍後**], 請跳至 [審核] 和 [[建立](#review--create)] 區段, 以建立您的存取封裝。

1. 如果您選取 [**現在**], 請執行下列其中一個原則區段中的步驟。

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>檢閱及建立

在 [**審查 + 建立**] 索引標籤上, 您可以檢查您的設定, 並檢查是否有任何驗證錯誤。

1. 檢查存取套件的設定

    ![存取套件-原則-啟用原則設定](./media/entitlement-management-access-package-create/review-create.png)

1. 按一下 [**建立**] 以建立存取封裝。

    新的存取套件會出現在存取套件清單中。

## <a name="next-steps"></a>後續步驟

- [編輯和管理現有的存取套件](entitlement-management-access-package-edit.md)
- [新增類別目錄擁有者或存取套件管理員](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [建立和管理目錄](entitlement-management-catalog-create.md)
