---
title: 使用群組存取管理的後續步驟 - Azure AD | Microsoft Docs
description: 管理安全性群組，以及如何使用這些群組管理資源的存取權的進階說明。
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/12/2017
ms.author: lizross
ms.custom: it-pro
ms.openlocfilehash: bdc8754253ce2567d957b4d6240fe52242aea2ea
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448816"
---
# <a name="managing-owners-for-a-group"></a>管理群組的擁有者
一旦資源擁有者將資源的存取權指派給 Azure AD 群組後，群組擁有者就會管理群組的成員資格。 資源擁有者實際上是委派權限給群組的擁有者，以指派使用者存取資源。

## <a name="add-an-owner-to-a-group"></a>將擁有者新增至群組

1. 在 [Azure AD 系統管理中心](https://aad.portal.azure.com)內，選取 [使用者和群組]。
2. 選取 [所有群組] ，然後開啟您想要新增擁有者的目標群組。
3. 選取 [新增擁有者] 。
4. 在 [新增擁有者] 頁面上，選取您想要新增為此群組擁有者的使用者，並確定這個名稱新增至 [已選取] 窗格中。

## <a name="remove-an-owner-from-a-group"></a>移除群組中的擁有者

1. 在 [Azure AD 系統管理中心](https://aad.portal.azure.com)內，選取 [使用者和群組]。
2. 選取 [所有群組]，然後開啟要移除擁有者的群組。
3. 選取 [擁有者]  索引標籤。
4. 選取您要從這個群組中移除的擁有者，然後選取 [移除] 。

## <a name="additional-information"></a>其他資訊
這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組來管理資源的存取權](active-directory-manage-groups.md)
* [設定群組設定的 Azure Active Directory Cmdlet](../users-groups-roles/groups-settings-cmdlets.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](../active-directory-apps-index.md)
* [什麼是 Azure Active Directory？](active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](../connect/active-directory-aadconnect.md)
