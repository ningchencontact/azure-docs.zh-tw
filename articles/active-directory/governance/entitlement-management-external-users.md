---
title: 在 Azure AD 權利管理（預覽）中管理外部使用者的存取權-Azure Active Directory
description: 深入瞭解您可以指定的設定，以在 Azure Active Directory 權利管理中管理外部使用者的存取權。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 419970985b9531ffab348491730aaf6c00e143b1
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72527107"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 權利管理（預覽）中管理外部使用者的存取權

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure AD 權利管理利用[Azure AD 的企業對企業（B2B）](../b2b/what-is-b2b.md) ，與組織外部的其他人共同作業。 在 Azure AD B2B 中，外部使用者會向其主目錄進行驗證，但在您的目錄中有標記法。 目錄中的標記法可讓使用者獲指派您資源的存取權。

本文說明您可以指定的設定，以管理外部使用者的存取權。

## <a name="how-entitlement-management-can-help"></a>權利管理有何説明

當您使用[AZURE AD B2B](../b2b/what-is-b2b.md)邀請體驗時，您必須已經知道您想要帶入資原始目錄並使用的外部來賓使用者的電子郵件地址。 當您處理較小或短期的專案，而且您已經知道所有參與者，但如果您有許多想要使用的使用者，或參與者隨著時間改變，這就很難管理。  例如，您可能會與另一個組織合作，並與該組織有一個聯繫點，但經過一段時間之後，該組織的其他使用者也會需要存取權。

您可以使用「權利管理」定義原則，讓您指定的組織使用者能夠自行要求存取套件。 您可以指定是否需要核准和存取的到期日。 如果需要核准，您也可以邀請外部組織中的一或多個使用者到您的目錄，並將其指定為核准者，因為他們可能會知道組織中的哪些外部使用者需要存取權。 設定存取套件之後，您就可以將存取套件的連結傳送給外部組織的連絡人（贊助商）。 該連絡人可以與外部組織中的其他使用者共用，也可以使用此連結來要求存取封裝。 來自該組織且已受邀進入您目錄的使用者，也可以使用該連結。

當要求獲得核准時，權利管理會將必要的存取權提供給使用者，其中可能包括邀請使用者（如果他們還不在您的目錄中）。 Azure AD 會自動為他們建立 B2B 來賓帳戶。 請注意，系統管理員可能先前已限制允許共同作業的組織，方法是設定[B2B 允許或拒絕清單](../b2b/allow-deny-list.md)，以允許或封鎖其他組織的邀請。  如果允許或封鎖清單不允許使用者，則不會邀請他們。

由於您不想讓外部使用者存取最後一次，因此您可以在原則中指定到期日，例如180天。 180天后，如果未擴充其存取權，權利管理將會移除所有與該存取套件相關聯的存取權。 根據預設，如果透過 [權利管理] 邀請的使用者沒有其他存取套件指派，那麼當他們失去最後一項指派時，其來賓帳戶將會遭到封鎖而無法在30天內登入，然後再移除。 這可避免不必要的帳戶激增。 如下列各節所述，這些設定是可設定的。

## <a name="how-access-works-for-external-users"></a>外部使用者存取的運作方式

下列圖表和步驟概述如何將存取套件的存取權授與外部使用者。

![顯示外部使用者生命週期的圖表](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. 您會在目錄中建立存取封裝，其中包含[不在您目錄中的使用者](entitlement-management-access-package-create.md#for-users-not-in-your-directory)原則。

1. 您可以將[我的存取權入口網站連結](entitlement-management-access-package-settings.md)傳送至外部組織的連絡人，他們可以與其使用者共用以要求存取封裝。

1. 外部使用者（此範例中**的**要求者）會使用我的存取權入口網站連結來要求存取封裝的[存取](entitlement-management-request-access.md)權。

1. 核准者[核准要求](entitlement-management-request-approve.md)（或要求已自動核准）。

1. 要求會進入「傳遞中」[狀態](entitlement-management-process.md)。

1. 使用 B2B 邀請程式，會在您的目錄中建立來賓使用者帳戶（在此範例中為要求者**a （guest）** ）。 如果已定義[允許清單或拒絕清單](../b2b/allow-deny-list.md)，則會套用清單設定。

1. 來賓使用者會獲指派存取封裝中所有資源的存取權。 在 Azure AD 和其他 Microsoft Online Services 或連線的 SaaS 應用程式中進行變更可能需要一些時間。 如需詳細資訊，請參閱套用[變更時](entitlement-management-access-package-resources.md#when-changes-are-applied)。

1. 外部使用者會收到一封電子郵件，指出已[傳遞](entitlement-management-process.md)其存取權。

1. 若要存取資源，外部使用者可以按一下電子郵件中的連結，或嘗試直接存取任何目錄資源，以完成邀請程式。

1. 根據原則設定，當時間通過時，外部使用者的存取套件指派會過期，而且會移除外部使用者的存取權。

1. 視 [外部使用者的生命週期] 設定而定，當外部使用者不再具有任何存取套件指派時，系統會封鎖外部使用者進行登入，並從您的目錄中移除來賓使用者帳戶。

## <a name="manage-the-lifecycle-of-external-users"></a>管理外部使用者的生命週期

您可以選取當外部使用者透過核准的存取套件要求受到邀請，而已不再具有任何存取套件指派時，會發生什麼情況。 如果使用者讓出其所有存取套件指派，或其上次存取套件指派過期，就可能發生這種情況。 根據預設，當外部使用者不再具有任何存取套件指派時，系統會封鎖他們登入您的目錄。 30天后，就會從您的目錄中移除其來賓使用者帳戶。

**先決條件角色：** 全域管理員或使用者系統管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表的 [**權利管理**] 區段中，按一下 [**設定**]。

1. 按一下 [編輯]。

    ![管理外部使用者生命週期的設定](./media/entitlement-management-external-users/settings-external-users.png)

1. 在 [**管理外部使用者的生命週期**] 區段中，為 [外部使用者] 選取不同的設定。

1. 一旦外部使用者失去對任何存取套件的最後一次指派，如果您想要封鎖他們登入此目錄，請將 [**封鎖外部使用者登入此目錄**] 設定為 **[是]** 。

    > [!NOTE]
    > 如果使用者遭到封鎖而無法登入此目錄，則使用者將無法在此目錄中重新要求存取套件或要求其他存取權。 如果後續需要要求存取其他存取套件，請勿設定封鎖它們進行登入。

1. 一旦外部使用者失去對任何存取套件的最後一次指派，如果您想要移除其在此目錄中的來賓使用者帳戶，請將 [**移除外部使用者**] 設定為 **[是]** 。

    > [!NOTE]
    > 權利管理只會移除透過權利管理所邀請的帳戶。 此外，請注意，即使該使用者已新增至此目錄中未存取套件指派的資源，也會封鎖該使用者，使其無法登入並從這個目錄移除。 如果在接收存取套件指派之前，此目錄中已有來賓，則會保留它們。 不過，如果來賓是透過存取套件指派進行邀請，而且受到邀請之後也已指派給商務用 OneDrive 或 SharePoint Online 網站，他們仍然會被移除。

1. 如果您想要移除此目錄中的來賓使用者帳戶，您可以設定移除之前的天數。 如果您想要在來賓使用者帳戶失去對任何存取套件的最後一次指派時將其移除，請設定 [**從這個目錄移除外部使用者到0之前的天數**]。

1. 按一下 [儲存]。

## <a name="enable-a-catalog-for-external-users"></a>為外部使用者啟用類別目錄

當您建立[新的類別目錄](entitlement-management-catalog-create.md)時，有一項設定可讓來自外部目錄的使用者要求目錄中的存取封裝。 如果您不想讓外部使用者擁有在目錄中要求存取封裝的許可權，請將 [**為外部使用者啟用**] 設為 [**否**]。

**先決條件角色：** 全域管理員、使用者系統管理員或目錄擁有者

![新增目錄窗格](./media/entitlement-management-shared/new-catalog.png)

您也可以在建立目錄之後變更此設定。

![編輯目錄設定](./media/entitlement-management-shared/catalog-edit.png)

## <a name="next-steps"></a>後續步驟

- [針對不在您目錄中的使用者](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [建立和管理資原始目錄](entitlement-management-catalog-create.md)
- [委派和角色](entitlement-management-delegate.md)
