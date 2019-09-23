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
ms.openlocfilehash: 4412bc9ce8d78b5810b25b60724575af66774127
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146265"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Azure Active Directory 中的大量匯入群組成員（預覽）

使用 Azure Active Directory （Azure AD）入口網站，您可以使用逗號分隔值（CSV）檔案來大量匯入群組成員，以將大量成員新增至群組。

> [!NOTE]
> Azure AD 大量作業是 Azure AD 的公開預覽功能，適用于任何付費 Azure AD 授權方案。 如需預覽使用詞彙的詳細資訊，請參閱[Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

## <a name="to-bulk-import-group-members"></a>大量匯入群組成員

1. 使用組織中的使用者系統管理員帳戶登入[Azure 入口網站](https://portal.azure.com)。 群組擁有者也可以大量匯入他們所擁有之群組的成員。
1. 在 Azure AD 中，選取 [**群組** > ] [**所有群組**]。
1. 開啟您要新增成員的群組，然後選取 [**成員**]。
1. 在 [**成員**] 頁面上，選取 [匯**入成員**]。
1. 在 [**大量匯入群組成員（預覽）** ] 頁面上，選取 [**下載**] 以取得具有必要群組成員屬性的 CSV 檔案範本。

    ![[匯入成員] 命令位於群組的 [設定檔] 頁面](./media/groups-bulk-import-members/import-panel.png)

1. 開啟 CSV 檔案，並為您要匯入至群組的每個群組成員新增一行（[必要值] 為 [**成員物件識別碼**] 或 [**使用者主體名稱**]）。 然後儲存檔案。

   ![CSV 檔案包含要匯入之成員的名稱和識別碼](./media/groups-bulk-import-members/csv-file.png)

1. 在 [**大量匯入群組成員（預覽）** ] 頁面上，于 [**上傳您的 csv**檔案] 下，流覽至檔案。 當您選取檔案時，CSV 檔案的驗證就會啟動。
1. 驗證檔案內容時，[大量匯入] 頁面會顯示 [**已成功上傳**檔案]。 如果發生錯誤，您必須修正這些錯誤，才能提交工作。
1. 當您的檔案通過驗證時，請選取 [**提交**] 來啟動 Azure 大量作業，以將群組成員匯入群組。
1. 當匯入作業完成時，您會看到大量作業成功的通知。

## <a name="check-import-status"></a>檢查匯入狀態

您可以在 [**大量作業結果（預覽）** ] 頁面中查看所有擱置大量要求的狀態。

   ![[大量作業結果] 頁面會顯示大量要求狀態](./media/groups-bulk-import-members/bulk-center.png)

如需大量作業中每個明細專案的詳細資料，請選取 [ **# Success**]、[**失敗**] 或 [**要求總數**] 資料行底下的值。 如果發生失敗，將會列出失敗的原因。

## <a name="bulk-import-service-limits"></a>大量匯入服務限制

每個要匯入群組成員清單的大量活動，最多可執行一小時。 這可匯入至少40000個成員的清單。

## <a name="next-steps"></a>後續步驟

- [大容量移除群組成員](groups-bulk-remove-members.md)
- [下載群組的成員](groups-bulk-download-members.md)
- [下載所有群組的清單](groups-bulk-download.md)
