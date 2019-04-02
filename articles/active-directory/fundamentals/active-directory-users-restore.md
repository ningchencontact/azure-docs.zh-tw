---
title: 將最近刪除的使用者還原或永久移除- Azure Active Directory | Microsoft Docs
description: 如何使用 Azure Active Directory 檢視可還原的使用者、還原已刪除的使用者，或是永久刪除使用者。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d69bd931f2f8c72fd1e6fc79c16662ea367617d6
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802009"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>使用 Azure Active Directory 將最近刪除的使用者還原或移除
刪除使用者之後，其帳戶會維持在暫時停權狀態 30 天。 在這 30 天期間，可以還原該使用者帳戶及其所有屬性。 30 天期限過後，系統會自動將該使用者永久刪除。

您可以在 Azure 入口網站中使用 Azure Active Directory (Azure AD)，檢視可還原的使用者、還原已刪除的使用者，或永久刪除使用者。

>[!Important]
>您和 Microsoft 客戶支援服務都無法還原已永久刪除的使用者。

## <a name="required-permissions"></a>所需的權限
您必須具備下列其中一個角色，才能還原及永久刪除使用者。

- 全域管理員

- 合作夥伴第 1 層支援

- 合作夥伴第 2 層支援

- 使用者管理員

## <a name="view-your-restorable-users"></a>檢視可還原的使用者
您可以查看刪除 30 天以內的所有使用者。 這些使用者均可還原。

### <a name="to-view-your-restorable-users"></a>若要檢視可還原的使用者
1. 登入[Azure 入口網站](https://portal.azure.com/)使用全域管理員帳戶的組織使用。

2. 選取 [Azure Active Directory] 並選取 [使用者]，然後選取 [已刪除的使用者]。

    檢閱可還原的使用者清單。

    ![[使用者 - 已刪除的使用者] 頁面上顯示仍可還原的使用者](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>還原最近刪除的使用者

刪除使用者帳戶時，從組織，在帳戶處於暫停狀態，並保留所有相關的組織資訊。 當您還原的使用者時，同時也會還原此組織資訊。

> [!Note]
> 一旦還原使用者之後，在刪除時指派給使用者的授權會也會還原，即使有可供這些授權的任何基座。 如果您要使用多個超過您購買授權，您的組織可能暫時不符合授權使用情形。

### <a name="to-restore-a-user"></a>若要還原使用者
1. 在 [使用者 - 已刪除的使用者] 頁面上，搜尋並選取其中一個可用的使用者。 例如 _Mary Parker_。

2. 選取 [還原使用者]。

    ![[使用者 - 已刪除的使用者] 頁面上顯示反白的 [還原使用者] 選項](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>永久刪除使用者
您可以永久刪除使用者從您的組織不需等到自動刪除 30 天。 無論是您、其他系統管理員還是 Microsoft 客戶支援服務，均無法還原已永久刪除的使用者。

>[!Note]
>如果您意外永久刪除使用者，則必須建立新的使用者，並以手動方式輸入所有先前的資訊。 如需如何建立使用者的詳細資訊，請參閱[新增或刪除使用者](add-users-azure-active-directory.md)。

### <a name="to-permanently-delete-a-user"></a>若要永久刪除使用者

1. 在 [使用者 - 已刪除的使用者] 頁面上，搜尋並選取其中一個可用的使用者。 例如 _Rae Huff_。

2. 選取 [永久刪除]。

    ![[使用者 - 已刪除的使用者] 頁面上顯示反白的 [還原使用者] 選項](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>後續步驟
還原或刪除使用者之後，您可以執行下列基本程序：

- [新增或刪除使用者](add-users-azure-active-directory.md)

- [將角色指派給使用者](active-directory-users-assign-role-azure-portal.md)

- [新增或變更設定檔資訊](active-directory-users-profile-azure-portal.md)

- [從另一個組織中新增來賓使用者](../b2b/what-is-b2b.md)

如需有關其他可用的使用者管理工作， [Azure AD 使用者管理文件](../users-groups-roles/index.yml)。
