---
title: 刪除群組 - Azure Active Directory | Microsoft Docs
description: 關於如何使用 Azure Active Directory 刪除群組的指示。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.openlocfilehash: 0e50abb4c7aa5641d92bf0a9f5c4516fc29125e2
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452560"
---
# <a name="delete-a-group-using-azure-active-directory"></a>使用 Azure Active Directory 刪除群組
您有可能基於任何原因而刪除 Azure Active Directory (Azure AD) 群組，但通常是因為您：

- 不正確地將 [群組類型] 設定為錯誤的選項。

- 誤建錯誤或重複的群組。 

- 不再需要該群組。

## <a name="to-delete-a-group"></a>刪除群組
1. 使用目錄的全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [Azure Active Directory]，然後選取 [群組]。

3. 從 [群組 - 所有群組] 頁面，搜尋並選取您要刪除的群組。 針對這些步驟，我們將使用 [MDM 原則 - 東部]。

    ![群組-所有群組頁面，已反白顯示群組名稱](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. 在 [MDM 原則 - 東部概觀] 頁面上，選取 [刪除]。

    將從您的 Azure Active Directory 租用戶刪除該群組。

    ![MDM 原則 - 東部概觀頁面，已反白顯示刪除選項](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>後續步驟

- 若您不慎將群組刪除，您可以再次建立。 如需詳細資訊，請參閱[如何建立基本的群組及新增成員](active-directory-groups-create-azure-portal.md)。

- 若不慎刪除 Office 365 群組，您或許可以還原它。 如需詳細資訊，請參閱[還原已刪除的 Office 365 群組](../users-groups-roles/groups-restore-deleted.md)。
