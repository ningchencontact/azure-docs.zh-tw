---
title: Azure AD PIM 中的電子郵件通知 | Microsoft Docs
description: 說明 Azure AD Privileged Identity Management (PIM) 中的電子郵件通知
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 07/24/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: ffac7768fefece24cb69789558ce500e1ff64d98
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257889"
---
# <a name="email-notifications-in-azure-ad-pim"></a>Azure AD PIM 中的電子郵件通知

當 Azure AD Privileged Identity Management (PIM) 中發生重要事件時，電子郵件通知就會傳送給相關的系統管理員或使用者。 例如，PIM 會傳送下列事件的電子郵件：

- 當特殊權限角色啟用正在等待核准時
- 當特殊權限角色啟用要求已核准時
- 當啟動特殊權限角色時
- 當指派特殊權限角色時
- 當 Azure AD PIM 啟用時

從 2018 年 7 月底開始，透過 PIM 傳送的電子郵件通知會有新的寄件者電子郵件地址，以及新的視覺化設計。 此更新會影響 Azure AD 的 PIM 和 Azure 資源的 PIM。 先前已觸發電子郵件通知的所有事件，都會繼續傳送電子郵件。 某些電子郵件將會有更新的內容，提供更精準的資訊。

## <a name="sender-email-address"></a>寄件者電子郵件地址

從 2018 年 7 月底開始，電子郵件通知具有下列位址：

- 電子郵件地址：**azure-noreply@microsoft.com**
- 顯示名稱：Microsoft Azure

先前，電子郵件通知具有下列位址：

- 電子郵件地址：**azureadnotifications@microsoft.com**
- 顯示名稱：Microsoft Azure AD 通知服務

## <a name="email-subject-line"></a>電子郵件主旨列

從 2018 年 7 月底開始，Azure AD 和 Azure 資源角色的電子郵件通知將會在主旨列上加上 **PIM** 前置詞。 以下是範例：

- PIM：Alain Charon 已獲永久指派「備份讀者」角色。

## <a name="pim-emails-for-azure-ad-roles"></a>Azure AD 角色的 PIM 電子郵件

從 2018 年 7 月底開始，Azure AD 角色的 PIM 電子郵件通知會有經過更新的設計。 以下所顯示的範例電子郵件，會在使用者啟動虛構 Contoso 組織的特殊權限角色時傳送。

![Azure AD 角色的新 PIM 電子郵件](./media/pim-email-notifications/email-directory-new.png)

先前，當使用者啟動特殊權限角色時，電子郵件看起來如下所示。

![Azure AD 角色的舊 PIM 電子郵件](./media/pim-email-notifications/email-directory-old.png)

## <a name="pim-emails-for-azure-resource-roles"></a>Azure 資源角色的 PIM 電子郵件

從 2018 年 7 月底開始，Azure 資源角色的 PIM 電子郵件通知會有經過更新的設計。 以下所顯示的範例電子郵件，會在指派虛構 Contoso 組織的特殊權限角色給使用者時傳送。

![Azure 資源角色的新 PIM 電子郵件](./media/pim-email-notifications/email-resources-new.png)

先前，當特殊權限角色指派給使用者時，電子郵件看起來如下所示。

![Azure 資源角色的舊 PIM 電子郵件](./media/pim-email-notifications/email-resources-old.png)

## <a name="next-steps"></a>後續步驟

- [如何管理 Azure AD PIM 中的角色啟用設定](pim-how-to-change-default-settings.md)
- [Azure AD PIM 中的核准](azure-ad-pim-approval-workflow.md)
