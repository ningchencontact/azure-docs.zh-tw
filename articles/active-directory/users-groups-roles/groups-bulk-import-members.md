---
title: 大量匯入上傳以將成員新增至群組-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 系統管理中心內大量新增群組成員。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0335522b398c22fb395305b33bac5a56ba565ee2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911083"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Azure Active Directory 中的大量匯入群組成員（預覽）

使用 Azure Active Directory （Azure AD）入口網站，您可以使用逗號分隔值（CSV）檔案來大量匯入群組成員，以將大量成員新增至群組。

> [!NOTE]
> Azure AD 大量作業是 Azure AD 的公開預覽功能，適用于任何付費 Azure AD 授權方案。 如需預覽使用詞彙的詳細資訊，請參閱[Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

## <a name="bulk-import-service-limits"></a>大量匯入服務限制

每個要匯入群組成員清單的大量活動，最多可執行一小時。 這可匯入至少40000個成員的清單。

## <a name="to-bulk-import-group-members"></a>大量匯入群組成員

1. 使用組織中的使用者系統管理員帳戶登入[Azure 入口網站](https://portal.azure.com)。 群組擁有者也可以大量匯入他們所擁有之群組的成員。
1. 在 Azure AD 中，選取 [**群組** > ] [**所有群組**]。
1. 開啟您要新增成員的群組，然後選取 [**成員**]。
1. 在 [**成員**] 頁面上，選取 [匯**入成員**] 以下載、更新及上傳 CSV 檔案，其中列出您要匯入至群組的成員。

   ![[匯入成員] 命令位於群組的 [設定檔] 頁面](./media/groups-bulk-import-members/import-panel.png)

## <a name="check-import-status"></a>檢查匯入狀態

您可以在 [**大量作業結果（預覽）** ] 頁面中查看所有擱置大量要求的狀態。

   ![[大量作業結果] 頁面會顯示大量要求狀態](./media/groups-bulk-import-members/bulk-center.png)

## <a name="next-steps"></a>後續步驟

- [大容量移除群組成員](groups-bulk-remove-members.md)
- [下載群組的成員](groups-bulk-download-members.md)
