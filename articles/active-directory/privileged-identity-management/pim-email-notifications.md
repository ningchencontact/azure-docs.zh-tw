---
title: 電子郵件通知在 PIM-Azure Active Directory |Microsoft Docs
description: 說明 Azure AD Privileged Identity Management (PIM) 中的電子郵件通知。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa9da83a7a6e97f5b721dad550831fe2645ffd3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60289140"
---
# <a name="email-notifications-in-pim"></a>PIM 中的電子郵件通知

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 可讓您知道重要事件的發生時，例如角色指派或啟用時。 PIM 會將電子郵件通知傳送給您和其他參與者，讓您持續掌握資訊。 這些電子郵件也可能包含相關工作 (例如啟用或更新角色) 的連結。 本文說明這些電子郵件的外觀、傳送時間及收件者。

## <a name="sender-email-address-and-subject-line"></a>寄件者電子郵件地址和主旨列

PIM 針對 Azure AD 和 Azure 資源角色傳送的電子郵件具有下列寄件者電子郵件地址：

- 電子郵件地址： **azure noreply\@microsoft.com**
- 顯示名稱：Microsoft Azure

這些電子郵件的主旨行中包含 **PIM** 前置詞。 以下是範例：

- PIM：Alain Charon 已獲永久指派「備份讀者」角色

## <a name="pim-emails-for-azure-ad-roles"></a>Azure AD 角色的 PIM 電子郵件

當 Azure AD 角色發生以下事件時，PIM 就會傳送電子郵件：

- 當特殊權限角色啟用正在等待核准時
- 當特殊權限角色啟用要求已完成時
- 當 Azure AD PIM 啟用時

收到 Azure AD 角色的這些電子郵件的對象取決於您的角色、事件和通知設定：

| 使用者 | 角色啟用正在等待核准 | 角色啟用要求已完成 | RDP 已啟用 |
| --- | --- | --- | --- |
| 特殊權限角色管理員</br>(已啟用/合格) | 是</br>(唯有未指定任何明確核准者時) | 是* | 是 |
| 安全性系統管理員</br>(已啟用/合格) | 否 | 是* | 是 |
| 全域管理員</br>(已啟用/合格) | 否 | 是* | 是 |

\* 如果 [ **[通知]** 設定](pim-how-to-change-default-settings.md#notifications)設為 [啟用]  時。

以下所顯示的範例電子郵件，會在使用者啟用虛構 Contoso 組織的 Azure AD 角色時傳送。

![Azure AD 角色的新 PIM 電子郵件](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Azure AD 角色的每週 PIM 摘要電子郵件

Azure AD 角色的每週 PIM 摘要電子郵件會傳送給特殊權限角色管理員、安全性管理員，以及已啟用 PIM 的全域管理員。 此每週電子郵件會針對每週的 PIM 活動，提供快照集以及特殊權限角色指派。 它只適用於公用雲端上的租用戶。 以下是範例電子郵件：

![Azure AD 角色的每週 PIM 摘要電子郵件](./media/pim-email-notifications/email-directory-weekly.png)

電子郵件包含四個圖格：

| 圖格 | 描述 |
| --- | --- |
| **已啟用的使用者** | 使用者在租用戶內啟用其合格角色的次數。 |
| **成為永久狀態的使用者** | 具有合格指派的使用者成為永久狀態的次數。 |
| **PIM 中的角色指派** | 使用者在 PIM 內獲派合格角色的次數。 |
| **PIM 外部的角色指派** | 使用者在 PIM 外部 (Azure AD 內部) 被指派永久角色的次數。 |

[頂端的角色概觀]  區段會根據每個角色的永久與合格管理員總數，列出您租用戶中的前五個角色。 [採取動作]  連結會開啟 [PIM 精靈](pim-security-wizard.md)，您可以在其中將永久管理員分批轉換成合格的管理員。

## <a name="pim-emails-for-azure-resource-roles"></a>Azure 資源角色的 PIM 電子郵件

當 Azure 資源角色發生以下事件時，PIM 就會傳送電子郵件給擁有者和使用者存取管理員：

- 當角色指派等待核准時
- 當角色已指派時
- 當角色即將到期時
- 當角色有資格擴充時
- 當使用者正在更新角色時
- 當角色啟用要求已完成時

當 Azure 資源角色發生以下事件時，PIM 就會傳送電子郵件給使用者：

- 當角色已指派給使用者時
- 當使用者的角色已過期時
- 當使用者的角色已擴充時
- 當使用者的角色啟用要求已完成時

以下所顯示的範例電子郵件，會在指派虛構 Contoso 組織的 Azure 資源角色給使用者時傳送。

![Azure 資源角色的新 PIM 電子郵件](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>後續步驟

- [在 PIM 中設定 Azure AD 角色設定](pim-how-to-change-default-settings.md)
- [核准或拒絕要求的 Azure AD PIM 中的角色](azure-ad-pim-approval-workflow.md)
