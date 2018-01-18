---
title: "在 Azure Active Directory 中還原已刪除的 使用者 | Microsoft Docs"
description: "如何還原已刪除的使用者、檢視可還原的使用者，以及永久刪除 Azure Active Directory 中的使用者"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: c3b7550c2aea0e8bcb7998e0e8c732894b500403
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2018
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>在 Azure Active Directory 中還原已刪除的 使用者

本文包含還原或永久刪除先前刪除之使用者的指示。 當您在 Azure Active Directory (Azure AD) 中刪除使用者時，從刪除日起算，仍會保留已刪除的使用者 30 天。 在這段時間內，可以還原使用者和其屬性。 

## <a name="required-permissions"></a>所需的權限
下列權限已足夠還原使用者。

角色  | 權限 
--------- | ---------
公司系統管理員<p>合作夥伴第 1 層支援<p>合作夥伴第 2 層支援<p>使用者帳戶管理員 | 可還原已刪除的使用者 
公司系統管理員<p>合作夥伴第 1 層支援<p>合作夥伴第 2 層支援<p>使用者帳戶管理員 | 可永久刪除使用者

## <a name="how-to-restore-a-deleted-user"></a>如何還原已刪除的使用者

在 Azure 入口網站中，您可以還原已刪除的使用者和永久刪除已刪除的使用者。

1. 在 [Azure AD 系統管理中心](https://aad.portal.azure.com)內，選取 [使用者和群組]&gt;[所有使用者]。 
2. 在 [顯示] 下方，篩選頁面以顯示 [最近刪除的使用者]。 
3. 選取一或多個最近刪除的使用者。
4. 選取 [還原使用者] 或 [永久刪除]。

## <a name="next-steps"></a>後續步驟
這些文章提供有關 Azure Active Directory 使用者管理的其他資訊。

* [快速入門：在 Azure Active Directory 中新增或刪除使用者](add-users-azure-active-directory.md)
* [管理使用者設定檔](active-directory-users-profile-azure-portal.md)
* [從另一個目錄中新增來賓使用者](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [在 Azure AD 中將使用者指派給角色](active-directory-users-assign-role-azure-portal.md)
