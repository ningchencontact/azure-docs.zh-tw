---
title: 要求 Azure AD 權限管理 （預覽）-Azure Active Directory 中存取套件的存取權
description: 了解如何使用我的存取入口網站要求 Azure Active Directory 權限管理 （預覽） 中存取套件的存取權。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39a50240b4360c5b4adcd6020c2b80b0f06315f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541551"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>要求 Azure AD 權限管理 （預覽） 中存取套件的存取權

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="sign-in-to-the-my-access-portal"></a>登入我存取入口網站

第一個步驟是登入我存取入口網站，您可以在其中要求存取套件的存取權。

**必要角色：** 要求者

1. 電子郵件或從您正在使用的專案或商務管理員訊息的外觀。 電子郵件應該包含您將需要存取存取套件的連結。 連結會從開始：

    `https://myaccess.microsoft.com`

1. 開啟連結。

1. 登入我存取入口網站。

    請務必使用您的組織帳戶。 如果您不確定，請洽詢您的專案或商務管理員。

## <a name="request-an-access-package"></a>要求存取的封裝

一旦找到我存取入口網站中存取套件，您可以提交要求。

**必要角色：** 要求者

1. 按一下核取記號以選取 存取套件。

    ![我的存取權入口網站 - 存取套件](./media/entitlement-management-shared/my-access-access-packages.png)

1. 按一下 [要求存取]  以開啟 [要求存取] 窗格。

1. 如果**業務正當理由**顯示方塊中，輸入需要存取權的理由。

1. 如果**特定的期間內要求嗎？** 已啟用，請選取**是**或**否**。

1. 如有必要，指定開始日期和結束日期。

    ![我的存取權入口網站 - 要求存取](./media/entitlement-management-shared/my-access-request-access.png)

1. 完成後，按一下**送出**提交要求。

1. 按一下 **要求歷程記錄**以查看您的要求和狀態的清單。

    如果存取套件需要核准，要求現在處於待決的核准狀態。

## <a name="cancel-a-request"></a>取消要求

如果您提交存取要求，而且要求仍處於**等待核准**狀態時，您可以取消要求。

**必要角色：** 要求者

1. 在我存取入口網站中，在左側，按一下**要求歷程記錄**以查看您的要求和狀態的清單。

1. 按一下 **檢視**您想要取消要求的連結。

1. 如果要求仍處於**等待核准**狀態時，您可以按一下**取消要求**取消要求。

    ![我存取入口網站-取消要求](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. 按一下 **要求歷程記錄**以確認已取消要求。

## <a name="select-a-policy"></a>選取原則

如果要求有多個原則適用於存取套件的存取權時，可能會要求您選取的原則。 比方說，存取封裝管理員可能會存取套件使用兩個原則設定的內部員工的兩個群組。 第一個原則可能會允許存取的 60 天，而且需要核准。 第二個原則可能會允許存取 2 天，並不需要核准。 如果您遇到這種情況下，您必須選取您想要使用的原則。

**必要角色：** 要求者

## <a name="next-steps"></a>後續步驟

- [核准或拒絕存取要求](entitlement-management-request-approve.md)
- [要求處理程序與電子郵件通知](entitlement-management-process.md)
