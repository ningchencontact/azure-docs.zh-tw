---
title: 上傳 csv 檔案以大容量移除群組成員-Azure Active Directory |Microsoft Docs
description: 在 Azure 系統管理中心內大量新增使用者。
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
ms.openlocfilehash: 9d384ea4749e2d0bc7edf8df7ac0508566f2f76b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517101"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Azure Active Directory 中的大量移除群組成員（預覽）

使用 Azure Active Directory （Azure AD）入口網站，您可以使用逗號分隔值（CSV）檔案，從群組中移除大量成員，以大容量移除群組成員。

## <a name="to-bulk-remove-group-members"></a>大容量移除群組成員

1. 使用組織中的使用者系統管理員帳戶登入[Azure 入口網站](https://portal.azure.com)。 群組擁有者也可以大量移除他們所擁有之群組的成員。
1. 在 Azure AD 中，選取 **群組**  >  **所有群組**。
1. 開啟您要移除其成員的群組，然後選取 [**成員**]。
1. 在 [**成員**] 頁面上，選取 [**移除成員**]。
1. 在 [**大量移除群組成員（預覽）** ] 頁面上，選取 [**下載**] 以取得具有必要群組成員屬性的 CSV 檔案範本。

   ![[移除成員] 命令位於群組的 [設定檔] 頁面上](./media/groups-bulk-remove-members/remove-panel.png)

1. 開啟 CSV 檔案，並為您要從群組中移除的每個群組成員新增一行（必要的值為 [成員物件識別碼] 或 [使用者主體名稱]）。 然後儲存檔案。

   ![CSV 檔案包含要移除之成員的名稱和識別碼](./media/groups-bulk-remove-members/csv-file.png)

1. 在 [**大量移除群組成員（預覽）** ] 頁面上，于 [**上傳您的 csv**檔案] 底下，流覽至檔案。 當您選取檔案時，就會開始驗證 .csv 檔案。
1. 驗證檔案內容時，[大量匯入] 頁面會顯示 [**已成功上傳**檔案]。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
1. 當您的檔案通過驗證時，請選取 [**提交**] 來啟動 Azure 大量作業，以從群組中移除群組成員。
1. 當移除作業完成時，您會看到大量作業成功的通知。

## <a name="check-removal-status"></a>檢查移除狀態

您可以在 [**大量作業結果（預覽）** ] 頁面中查看所有擱置大量要求的狀態。

   ![[大量作業結果] 頁面會顯示大量要求狀態](./media/groups-bulk-remove-members/bulk-center.png)

如需大量作業中每個明細專案的詳細資料，請選取 [ **# Success**]、[**失敗**] 或 [**要求總數**] 資料行底下的值。 如果發生失敗，將會列出失敗的原因。

## <a name="bulk-removal-service-limits"></a>大量移除服務限制

從移除群組成員清單的每個大量活動，最多可執行一小時。 這可移除至少40000個成員的清單。

## <a name="next-steps"></a>後續步驟

- [大量匯入群組成員](groups-bulk-import-members.md)
- [下載群組的成員](groups-bulk-download-members.md)
- [下載所有群組的清單](groups-bulk-download.md)
