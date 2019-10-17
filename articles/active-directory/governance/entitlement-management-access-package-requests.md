---
title: 在 Azure AD 權利管理（預覽）中查看和管理存取套件的要求-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理（預覽）中，查看、重新處理和取消存取套件的要求。
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
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7211fea4781904b9d97428ecf00b57970e4b57d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430260"
---
# <a name="view-and-manage-requests-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 權利管理（預覽）中查看和管理存取套件的要求

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在 Azure AD 權利管理 中，您可以看到誰已要求存取套件、其原則和狀態。 本文說明如何查看、重新處理和取消存取封裝的要求。

## <a name="view-requests"></a>View 要求

**先決條件角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 按一下 [**要求**]。

1. 按一下特定要求以查看其他詳細資料。

    ![存取套件的要求清單](./media/entitlement-management-access-package-requests/requests-list.png)

## <a name="view-a-requests-delivery-errors"></a>查看要求的傳遞錯誤

**先決條件角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 按一下 [**要求**]。

1. 選取您想要查看的要求。

    如果要求有任何傳遞錯誤，則要求狀態將會是 [**未傳遞] 或 [** **部分傳遞**]。

    如果有任何傳遞錯誤，在要求的詳細資料窗格中，將會有傳遞錯誤的計數。

1. 按一下 [計數]，以查看所有要求的傳遞錯誤。

## <a name="reprocess-a-request"></a>重新處理要求

如果要求遇到錯誤，您可以重新處理要求以再次嘗試。 您只能重新處理狀態為 [**傳遞失敗**] 或 [**部分傳遞**] 且完成日期不到一周的要求。

**先決條件角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 按一下 [**要求**]。

1. 按一下您要重新處理的要求。

1. 在 [要求詳細資料] 窗格中，按一下 [重新處理**要求**]。

    ![重新處理失敗的要求](./media/entitlement-management-access-package-requests/reprocess-request.png)

## <a name="cancel-a-pending-request"></a>取消擱置要求

您只能取消尚未傳遞或其傳遞失敗的擱置要求。

**先決條件角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 按一下 [**要求**]。

1. 按一下您要取消的要求。

1. 在 [要求詳細資料] 窗格中，按一下 [**取消要求**]。

## <a name="next-steps"></a>後續步驟

- [變更存取套件的要求和核准設定](entitlement-management-access-package-request-policy.md)
- [查看、新增和移除存取套件的指派](entitlement-management-access-package-assignments.md)