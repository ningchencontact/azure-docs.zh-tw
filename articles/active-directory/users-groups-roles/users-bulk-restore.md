---
title: 在 Azure Active Directory 入口網站中大量還原已刪除的使用者（預覽） |Microsoft Docs
description: 在 Azure AD 系統管理中心內，將已刪除的使用者大量還原 Azure Active Directory
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
ms.openlocfilehash: d392ae97a8325dd4a56acd807ebfb2b951216eae
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174281"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>在 Azure Active Directory 中大量還原已刪除的使用者（預覽）

Azure Active Directory （Azure AD）支援大量使用者建立和刪除作業、大量邀請來賓，以及支援下載使用者、群組和群組成員的清單。

## <a name="to-bulk-restore-users"></a>大量還原使用者

1. 使用 Azure AD 組織中使用者系統管理員的帳戶登[入您的 Azure AD 組織](https://aad.portal.azure.com)。
1. 在 Azure AD 中，選取 **已刪除**的**使用者** > 。
1. 在 [**刪除的使用者**] 頁面上，選取 [**大量還原**]，以上傳要還原之使用者屬性的有效 CSV 檔案。

   ![在 [刪除的使用者] 頁面上選取 [大量還原] 命令](./media/users-bulk-restore/bulk-restore.png)

1. 開啟 CSV 檔案，並為您要還原的每個使用者新增一行。 唯一必要的值是**ObjectID**。 然後儲存檔案。

   ![選取您要加入的本機 CSV 檔案，其中列出您要新增的使用者](./media/users-bulk-restore/upload-button.png)

1. 在 [**大量還原（預覽）** ] 頁面上，于 [**上傳您的 csv**檔案] 底下，流覽至檔案。 當您選取檔案並按一下 [**提交**] 時，CSV 檔案的驗證就會啟動。
1. 當檔案內容經過驗證後，您會看到「檔案已成功上傳」。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 當您的檔案通過驗證時，請選取 [**提交**] 來啟動還原使用者的 Azure 大量作業。
1. 當還原作業完成時，您會看到大量作業成功的通知。

如果發生錯誤，您可以在 [**大量作業結果**] 頁面上下載並查看結果檔案。 檔案包含每個錯誤的原因。

## <a name="check-status"></a>檢查狀態

您可以在 [**大量作業結果（預覽）** ] 頁面中查看所有擱置大量要求的狀態。

   ![在大量作業結果頁面中檢查上傳狀態](./media/users-bulk-restore/bulk-center.png)

接下來，您可以檢查您所還原的使用者是否存在於 Azure 入口網站中的 Azure AD 組織，或使用 PowerShell。

## <a name="view-restored-users-in-the-azure-portal"></a>在 Azure 入口網站中查看已還原的使用者

1. 使用組織中使用者系統管理員的帳戶登[入 Azure AD admin center](https://aad.portal.azure.com) 。
1. 在導覽窗格中，選取 [Azure Active Directory]。
1. 在 [管理] 底下選取 [使用者]。
1. 在 [**顯示**] 底下，選取 [**所有使用者**]，並確認已列出您所還原的使用者。

### <a name="view-users-with-powershell"></a>使用 PowerShell 來觀看使用者

執行下列命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

您應該會看到您所還原的使用者已列出。

## <a name="next-steps"></a>後續步驟

- [大量匯入使用者](users-bulk-add.md)
- [大量刪除使用者](users-bulk-delete.md)
- [下載使用者清單](users-bulk-download.md)
