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
ms.openlocfilehash: d7c47887c12c8bf9be7a0c5b11dfb3f099965cb7
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174357"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Azure Active Directory 中的大量刪除使用者（預覽）

使用 Azure Active Directory （Azure AD）入口網站，您可以使用逗號分隔值（CSV）檔案來大量刪除使用者，藉以移除群組中大量的成員。

## <a name="to-bulk-delete-users"></a>大量刪除使用者

1. 使用組織中使用者系統管理員的帳戶登[入您的 Azure AD 組織](https://aad.portal.azure.com)。
1. 在 Azure AD 中，選取 **使用者**  >  **大量刪除**。
1. 在 [**大量刪除使用者**] 頁面上，選取 [**下載**] 以接收使用者屬性的有效 CSV 檔案。

   ![選取您要刪除的使用者所在的本機 CSV 檔案](./media/users-bulk-delete/bulk-delete.png)

1. 開啟 CSV 檔案，並為您要刪除的每個使用者新增一行。 唯一必要的值是 [**使用者主體名稱**]。 然後儲存檔案。

   ![CSV 檔案包含要刪除之使用者的名稱和識別碼](./media/users-bulk-delete/delete-csv-file.png)

1. 在 [**大量刪除使用者（預覽）** ] 頁面上，于 [**上傳您的 csv**檔案] 底下，流覽至檔案。 當您選取檔案並按一下 [提交] 時，CSV 檔案的驗證就會啟動。
1. 當檔案內容經過驗證後，您會看到「檔案已成功上傳」。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 當您的檔案通過驗證時，請選取 [**提交**] 來啟動刪除使用者的 Azure 大量操作。
1. 當刪除作業完成時，您會看到大量作業成功的通知。

如果發生錯誤，您可以在 [**大量作業結果**] 頁面上下載並查看結果檔案。 檔案包含每個錯誤的原因。

## <a name="check-status"></a>檢查狀態

您可以在 [**大量作業結果（預覽）** ] 頁面中查看所有擱置大量要求的狀態。

   ![在大量作業結果頁面中檢查上傳狀態](./media/users-bulk-delete/bulk-center.png)

接下來，您可以檢查您刪除的使用者是否存在於 Azure 入口網站中的 Azure AD 組織，或使用 PowerShell。

## <a name="verify-deleted-users-in-the-azure-portal"></a>確認 Azure 入口網站中已刪除的使用者

1. 使用組織中的「使用者管理員」帳戶來登入 Azure 入口網站。
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
