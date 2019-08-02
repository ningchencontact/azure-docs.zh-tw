---
title: 要求存取 Azure AD 權利管理 (預覽) 中存取套件-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理 (預覽) 中使用我的存取權入口網站來要求存取套件的存取權。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64a81a871d811c68f5e92822ca88446966be4d5c
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678119"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD 權利管理 (預覽) 中的存取權套件要求存取權

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="sign-in-to-the-my-access-portal"></a>登入我的存取權入口網站

第一個步驟是登入我的存取權入口網站, 您可以在其中要求存取權套件的存取權。

**必要角色：** 要求者

1. 從您正在使用的專案或商務經理尋找電子郵件或訊息。 電子郵件應該包含您需要存取的存取套件連結。 此連結將以開頭:

    `https://myaccess.microsoft.com`

1. 開啟連結。

1. 登入我的存取權入口網站。

    請務必使用您的組織 (公司或學校) 帳戶。 如果您不確定, 請洽詢您的專案或商務經理。

## <a name="request-an-access-package"></a>要求存取套件

在我的存取權入口網站中找到存取套件之後, 您就可以提交要求。

**必要角色：** 要求者

1. 在清單中尋找存取封裝。  如有需要, 您可以輸入搜尋字串, 然後選取 [**名稱**]、[**目錄**] 或 [**資源**] 篩選來進行搜尋。

    ![我的存取權入口網站-資源搜尋](./media/entitlement-management-request-access/elm-myaccess-resource-search.png)
1. 按一下核取記號以選取存取封裝。

    ![我的存取權入口網站 - 存取套件](./media/entitlement-management-shared/my-access-access-packages.png)

1. 按一下 [要求存取] 以開啟 [要求存取] 窗格。

1. 如果顯示 [**商業理由**] 方塊, 請輸入需要存取的理由。

1. 如果已啟用 [**特定期間的要求？** ], 請選取 **[是] 或 [** **否**]。

1. 如有必要, 請指定開始日期和結束日期。

    ![我的存取權入口網站 - 要求存取](./media/entitlement-management-shared/my-access-request-access.png)

1. 完成後, 按一下 [**提交**] 以提交您的要求。

1. 按一下 [**要求歷程記錄**] 以查看您的要求清單和狀態。

    如果存取套件需要核准, 則要求現在會處於擱置中的核准狀態。

## <a name="cancel-a-request"></a>取消要求

如果您提交存取要求, 而要求仍在**等待核准**狀態, 您可以取消要求。

**必要角色：** 要求者

1. 在我的存取權入口網站中, 按一下左側的 [**要求歷程記錄**], 以查看您的要求清單和狀態。

1. 針對您想要取消的要求, 按一下 [ **View** ] 連結。

1. 如果要求仍在**等待核准**狀態, 您可以按一下 [**取消要求**] 來取消要求。

    ![我的存取權入口網站-取消要求](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. 按一下 [**要求歷程記錄**] 確認已取消要求。

## <a name="select-a-policy"></a>選取原則

如果您要求存取已套用多項原則的存取封裝, 可能會要求您選取原則。 例如, 存取套件管理員可能會為兩個內部員工群組設定具有兩個原則的存取封裝。 第一個原則可能會允許存取60天, 而且需要核准。 第二個原則可能允許存取2天, 而不需要核准。 如果您遇到這種情況, 您必須選取您想要使用的原則。

**必要角色：** 要求者

## <a name="next-steps"></a>後續步驟

- [核准或拒絕存取要求](entitlement-management-request-approve.md)
- [要求處理和電子郵件通知](entitlement-management-process.md)
