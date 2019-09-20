---
title: Azure Active Directory 入口網站中的大量刪除使用者（預覽） |Microsoft Docs
description: 在 Azure Active Directory 的 Azure 系統管理中心內大量刪除使用者
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb01b46d61b6ba99c3ec9c537dccc350074f5e05
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146447"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Azure Active Directory 中的大量刪除使用者（預覽）

使用 Azure Active Directory （Azure AD）入口網站，您可以使用逗號分隔值（CSV）檔案來大量刪除使用者，藉以移除群組中大量的成員。

## <a name="to-bulk-delete-users"></a>大量刪除使用者

1. 使用組織中使用者系統管理員的帳戶登入您的 Azure AD 組織。
1. 在 Azure AD 中，選取 [**使用者** > ] [**大量刪除**]。
1. 在 [**大量刪除使用者**] 頁面上，選取 [**下載**] 以接收有效的使用者屬性 CSV 檔案，然後新增您想要刪除的使用者。

   ![CSV 檔案包含要刪除之使用者的名稱和識別碼](./media/users-bulk-delete/delete-csv-file.png)

1. 完成 CSV 檔案的編輯後，請選取 [**上傳您的 csv**檔案以進行驗證] 底下的檔案。

   ![選取您要刪除的使用者所在的本機 CSV 檔案](./media/users-bulk-delete/bulk-delete.png)

1. 當檔案內容經過驗證之後，您必須在提交作業之前修正任何錯誤。
1. 當您的檔案通過驗證時，請選取 [**提交**] 來啟動刪除使用者的 Azure batch 作業。 如果發生錯誤，您可以在 [大量作業結果] 頁面上下載並查看結果檔案。 檔案包含每個錯誤的原因。

## <a name="check-status"></a>檢查狀態

您可以在 [**大量作業結果（預覽）** ] 頁面中查看所有擱置大量要求的狀態。

   ![在大量作業結果頁面中檢查上傳狀態](./media/users-bulk-delete/bulk-center.png)

接下來，您可以檢查您刪除的使用者是否存在於 Azure 入口網站中的 Azure AD 組織，或使用 PowerShell。

## <a name="verify-deleted-users-in-the-azure-portal"></a>確認 Azure 入口網站中已刪除的使用者

1. 使用組織中使用者系統管理員的帳戶登入 Azure 入口網站。
1. 在導覽窗格中，選取 [Azure Active Directory]。
1. 在 [管理] 底下選取 [使用者]。
1. 在 [**顯示**] 底下，選取 [僅限**所有使用者**]，並確認您刪除的使用者已不再列出。

### <a name="verify-deleted-users-with-powershell"></a>使用 PowerShell 驗證已刪除的使用者

執行下列命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

確認您刪除的使用者已不再列出。

## <a name="next-steps"></a>後續步驟

- [大量新增使用者](users-bulk-add.md)
- [下載使用者清單](users-bulk-download.md)
- [大量還原使用者](users-bulk-restore.md)
