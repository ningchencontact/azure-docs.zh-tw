---
title: 為您的組織啟用多重要素驗證-Azure Active Directory
description: 根據您的授權為您的組織啟用 Azure MFA
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98bf62dff3e6c642f5de775f2d5bf8682ecea169
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164922"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>為您的組織啟用多重要素驗證

有多種方式可以根據貴組織所擁有的授權，為您的 Azure Active Directory （AD）使用者啟用 Azure 多重要素驗證（MFA）。 

![調查信號並視需要強制執行 MFA](./media/concept-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

根據我們的研究，當您使用 MFA 時，您的帳戶會比99.9% 更不可能遭到入侵。

那麼，您的組織在成為統計資料之前，如何開啟多重要素驗證（即使是免費的）呢？

## <a name="free-option"></a>免費選項

利用 Azure AD 免費權益的客戶可以使用[安全性預設值](../conditional-access/concept-conditional-access-security-defaults.md)，在其環境中啟用多重要素驗證。

## <a name="office-365"></a>Office 365

針對具有 Office 365 的客戶，有兩個選項：

- 您可以透過 Azure AD 啟用[安全性預設值](../conditional-access/concept-conditional-access-security-defaults.md)，以使用 Azure 多因素驗證來保護您的所有使用者。
- 如果您的組織在提供多重要素驗證時需要更細微的程度，您的 Office 授權會包含[每個使用者的 MFA](../authentication/howto-mfa-userstates.md)功能。 系統管理員會個別針對每個使用者啟用並強制執行每個使用者的 MFA。

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

適用于具有 Azure AD Premium P1 或類似授權的客戶，其中包括 Enterprise Mobility + Security E3、Microsoft 365 F1 或 Microsoft 365 E3 等這類功能： 

建議使用[條件式存取原則](../conditional-access/concept-conditional-access-policy-common.md)，以獲得最佳的使用者體驗。

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

適用于具有 Azure AD Premium P2 或類似授權的客戶，其中包含此功能，例如 Enterprise Mobility + Security E5 或 Microsoft 365 E5： 

建議使用[條件式存取原則](../conditional-access/concept-conditional-access-policy-common.md)以及身分[識別保護](../identity-protection/overview-v2.md)風險原則，以獲得最佳的使用者體驗和強制執行彈性。

## <a name="authentication-methods"></a>驗證方法

|   | 安全性預設值 | 所有其他方法 |
| --- | --- | --- |
| 行動應用程式的通知 | X | X |
| 來自行動應用程式或硬體 Token 的驗證碼 |   | X |
| 電話簡訊 |   | X |
| 電話通話 |   | X |
| 應用程式密碼 |   | X * * |

\* * 只有在系統管理員啟用時，應用程式密碼才會在具有傳統驗證案例的每一使用者 MFA 中提供。

## <a name="next-steps"></a>後續步驟

[Azure AD 定價頁面](https://azure.microsoft.com/pricing/details/active-directory/)
