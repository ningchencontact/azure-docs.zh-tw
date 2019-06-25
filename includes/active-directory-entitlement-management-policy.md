---
title: 包含檔案
description: 包含檔案
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 05/16/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: 6711506c1e489dcbd50aedd36241affc3bbed80b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174150"
---
### <a name="policy-for-users-in-your-directory"></a>原則：您的目錄中的使用者

如果您想要您的原則適用於使用者和群組，您可以要求此存取套件的目錄中，請遵循下列步驟。

1. 在 **可以要求存取權的使用者**區段中，選取**您的目錄中的使用者**。

1. 在 **選取 使用者和群組**區段中，按一下**加入使用者和群組**。

1. 在 選取使用者和群組 窗格中，選取 使用者和您想要新增的群組。

    ![存取封裝-原則-選取使用者和群組](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. 按一下 **選取**新增的使用者和群組。

1. 往下跳到[原則：要求](#policy-request)一節。

### <a name="policy-for-users-not-in-your-directory"></a>原則：不在您的目錄中的使用者

如果您想要針對不在您可以要求此存取套件的目錄中的使用者原則，請遵循下列步驟。 目錄必須設定為允許**組織關係共同作業限制**設定。

> [!NOTE]
> Guest 使用者帳戶會建立使用者，尚未在您核准或自動核准其要求的目錄。 客體可獲邀但不是會收到邀請電子郵件。 相反地，他們會收到一封電子郵件傳遞其存取套件指派時。 根據預設，稍後當來賓使用者不再擁有任何存取權封裝指派，因為其最後一項指派已過期或已取消，guest 使用者帳戶將會封鎖從登入，並刪除。 如果您想要有無限期保留在您目錄的來賓使用者，即使它們有沒有任何存取封裝指派，您可以變更設定，您的權限管理組態。

1. 在 **可以要求存取權的使用者**區段中，選取**不在您的目錄中的使用者**。

1. 在 **選取外部 Azure AD directory**區段中，按一下**將目錄加入**。

1. 輸入網域名稱和外部搜尋 Azure AD 目錄。

1. 請確認它是正確的目錄所提供的目錄名稱和初始網域。

    > [!NOTE]
    > 從目錄的所有使用者將都能夠要求此存取套件。 這包括從目錄中，不只是將在搜尋中使用的網域相關聯的所有子網域的使用者。

    ![存取封裝-原則選取目錄](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. 按一下 **新增**，新增您的目錄。

1. 重複此步驟，新增任何其他的目錄。

1. 當您新增所有目錄之後您想要包含在原則中，按一下**選取**。

1. 往下跳到[原則：要求](#policy-request)一節。

### <a name="policy-none-administrator-direct-assignments-only"></a>原則：無 （系統管理員直接指派只）

如果您想要您略過存取要求，並允許將特定使用者直接指派給存取套件的系統管理員的原則，請遵循下列步驟。 使用者不需要要求存取套件。 您仍然可以設定到期日設定，但沒有要求的設定。

1. 在 **可以要求存取權的使用者**區段中，選取**None (系統管理員直接指派只**。

    建立存取套件之後，您可以直接將特定的內部和外部使用者指派給存取套件。 如果您指定外部使用者，將目錄中建立來賓使用者帳戶。

1. 往下跳到[原則：到期](#policy-expiration)一節。

### <a name="policy-request"></a>原則：要求

在 [要求] 區段中，您會指定核准設定，當使用者要求存取套件。

1. 若要針對選取的使用者要求需要核准，將**需要核准**切換至**是**。 若要有自動核准的要求，請將設定切換**No**。

1. 如果您在需要核准**選取核准者**區段中，按一下**新增核准者**。

1. 在 [選取核准者] 窗格中，選取一或多個使用者和/或群組為核准者。

    只有其中一個選取的核准者必須核准要求。 不需要從所有核准的核准。 核准決策根據任何核准者檢閱要求第一次。

    ![存取封裝-原則選取核准者](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. 按一下 **選取**新增核准者。

1. 按一下 **顯示進階要求設定**會顯示額外的設定。

    ![存取封裝-原則選取目錄](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. 若要要求使用者提供理由才能要求存取套件，將**需要理由**要**是**。

1. 如果需要提供理由來核准要求，以存取套件的核准者，請設定**要求核准者提供理由**要**是**。

1. 在 **核准要求逾時 （天）** 方塊中，指定的核准者檢閱要求時所需要的時間量。 如果任何核准者不檢閱其在此天數，要求到期，而且使用者必須提交存取套件的另一個要求。

### <a name="policy-expiration"></a>原則：到期

在 [到期] 區段中，您可以指定使用者的指派，以存取套件的到期時。

1. 在**到期**區段中，將**存取套件到期**來**日期**，**天數**，或**永不**.

    針對**日期**，選取未來的到期日。

    針對**天數**，指定一個數字，介於 0 到 3660 天之間。

    在特定日期，在特定天數後在已核准，取決於您選擇，以存取套件的使用者的指派到期或永遠不會。

1. 按一下 **顯示進階到期設定**會顯示額外的設定。

1. 若要允許使用者將其指派，設定**允許使用者存取**要**是**。

    如果延伸模組可以在原則中，使用者會收到一封電子郵件 14 天和其存取套件的指派就會設定為過期的 1 天也提示他們延長指派。

    ![存取封裝-原則到期設定](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>原則：啟用原則

1. 如果您想要存取封裝會立即提供給使用者的原則中，按一下**是**可啟用此原則。

    您隨時可以啟用它未來之後您完成建立存取套件。

    ![存取封裝-原則啟用原則設定](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. 按一下 **下一步**或是**建立**。
