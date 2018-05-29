---
title: 無邀請將 B2B 共同作業使用者新增至 Azure Active Directory | Microsoft Docs
description: 您可以讓來賓使用者將其他來賓使用者加入您的 Azure AD，而不需在 Azure Active Directory B2B 共同作業中兌換邀請。
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 589d9a417dae5c40d24d25c4ef864ce903fbfbe3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259513"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>在沒有邀請的情況下新增 B2B 共同作業來賓使用者

> [!NOTE]
> 現在，除了某些特殊情況，來賓使用者已不再需要邀請電子郵件。 如需詳細資訊，請參閱 [B2B 共同作業邀請兌換](redemption-experience.md)。  

您可以允許使用者 (例如合作夥伴代表) 將來自合作夥伴的使用者加入您的組織，而不需要兌換邀請。 您必須做的是將合作夥伴組織在目錄中的列舉權限授與該使用者。 

授與這些權限的時機︰

1. 主組織 (例如，WoodGrove) 中的使用者邀請合作夥伴組織 (例如，Sam@litware.com) 的使用者做為「來賓」。
2. 主組織中的管理員會[設定原則](delegate-invitations.md)，以允許 Sam 識別及新增來自合作夥伴組織 (Litware) 的其他使用者。
3. 現在 Sam 可以將來自 Litware 的其他使用者新增到 WoodGrove 目錄、群組或應用程式，而不需要兌換邀請。 若 Sam 具有 Litware 中的適當列舉權限，這會自動發生。

### <a name="next-steps"></a>後續步驟

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [資訊工作者如何新增 B2B 共同作業使用者？](add-users-information-worker.md)
- [B2B 共同作業邀請兌換](redemption-experience.md)
- [委派 Azure Active Directory B2B 共同作業邀請](delegate-invitations.md)

