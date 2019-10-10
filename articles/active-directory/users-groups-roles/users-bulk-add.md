---
title: Azure Active Directory 入口網站中的大量建立使用者（預覽） |Microsoft Docs
description: 在 Azure AD 系統管理中心的 Azure Active Directory 中加入大量使用者
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10dfffa69652ee2b75053c04b97f6492c46811e
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174332"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Azure Active Directory 中的大量建立使用者（預覽）

Azure Active Directory （Azure AD）支援大量使用者建立和刪除作業、大量邀請來賓，以及支援下載使用者、群組和群組成員的清單。

## <a name="required-permissions"></a>所需的權限

若要在系統管理入口網站中大量建立使用者，您必須以全域管理員或使用者系統管理員的身分登入。

## <a name="to-create-users-in-bulk"></a>若要大量建立使用者

1. 使用組織中使用者系統管理員的帳戶登[入您的 Azure AD 組織](https://aad.portal.azure.com)。
1. 在 Azure AD 中，選取 **使用者**  >  **大量建立**。
1. 在 [**大量建立使用者**] 頁面上，選取 [**下載**] 以接收使用者屬性的有效逗號分隔值（CSV）檔案，然後新增您想要建立的 [新增使用者]。

   ![選取您要加入的本機 CSV 檔案，其中列出您要新增的使用者](./media/users-bulk-add/upload-button.png)

1. 開啟 CSV 檔案，並為您想要建立的每個使用者新增一行。 唯一必要的值為 [**名稱**]、[**使用者主體名稱**]、[**初始密碼**] 和 [**封鎖登入] （是/否）** 。 然後儲存檔案。

   ![CSV 檔案包含要建立之使用者的名稱和識別碼](./media/users-bulk-add/add-csv-file.png)

1. 在 [**大量建立使用者（預覽）** ] 頁面上，于 [上傳您的 CSV 檔案] 底下，流覽至檔案。 當您選取檔案並按一下 [**提交**] 時，CSV 檔案的驗證就會啟動。
1. 檔案內容經過驗證之後，您就會看到檔案**已成功上傳**。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 當您的檔案通過驗證時，請選取 [**提交**] 來啟動匯入新使用者的 Azure 大量作業。
1. 當匯入作業完成時，您會看到大量操作工作狀態的通知。

如果發生錯誤，您可以在 [**大量作業結果**] 頁面上下載並查看結果檔案。 檔案包含每個錯誤的原因。

## <a name="check-status"></a>檢查狀態

您可以在 [**大量作業結果（預覽）** ] 頁面中查看所有擱置大量要求的狀態。

   ![在大量作業結果頁面中檢查上傳狀態](./media/users-bulk-add/bulk-center.png)

接下來，您可以檢查您所建立的使用者是否存在於 Azure 入口網站中的 Azure AD 組織，或使用 PowerShell。

## <a name="verify-users-in-the-azure-portal"></a>驗證 Azure 入口網站中的使用者

1. 使用組織中使用者系統管理員的帳戶登[入 Azure AD admin center](https://aad.portal.azure.com) 。
1. 在導覽窗格中，選取 [Azure Active Directory]。
1. 在 [管理] 底下選取 [使用者]。
1. 在 [**顯示**] 底下，選取 [**所有使用者**]，並確認已列出您所建立的使用者。

### <a name="verify-users-with-powershell"></a>使用 PowerShell 驗證使用者

執行下列命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

您應該會看到列出您所建立的使用者。

## <a name="bulk-import-service-limits"></a>大量匯入服務限制

建立使用者的每個大量活動最多隻能執行一小時。 這可讓您大量建立至少50000個使用者。

## <a name="next-steps"></a>後續步驟

- [大量刪除使用者](users-bulk-delete.md)
- [下載使用者清單](users-bulk-download.md)
- [大量還原使用者](users-bulk-restore.md)
