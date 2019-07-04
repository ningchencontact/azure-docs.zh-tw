---
title: 什麼是 Azure AD 已註冊的裝置？
description: 了解裝置身分識別管理如何協助您管理存取您環境中資源的裝置。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462755"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 註冊裝置

Azure AD 註冊裝置的目標是為使用者提供支援攜帶您自己的裝置 (BYOD) 或行動裝置案例。 在這些情況下，使用者可以存取您組織的 Azure Active Directory 受控資源使用個人裝置。

|   | Azure AD 註冊 |
| --- | --- |
| **定義** | 向 Azure AD 註冊，而不需要登入裝置的組織帳戶 |
| **主要對象** | 適用於所有的使用者，使用下列準則： |
|   | 攜帶您自己的裝置 (BYOD) |
|   | 行動裝置 |
| **裝置擁有權** | 使用者或組織 |
| **作業系統** | Windows 10、 iOS、 Android 及 MacOS |
| **佈建** | Windows 10-設定 |
|   | iOS/Android – 公司入口網站或 Microsoft Authenticator 應用程式 |
|   | MacOS-公司入口網站 |
| **裝置登入選項** | 使用者的本機認證 |
|   | 密碼 |
|   | Windows Hello |
|   | PIN 碼 |
|   | 生物識別技術或其他裝置模式 |
| **裝置管理** | 行動裝置管理 (範例：Microsoft Intune) |
|   | 行動應用程式管理 |
| **主要功能** | 雲端資源的 SSO |
|   | 當註冊到 Intune 的條件式存取 |
|   | 透過應用程式保護原則的條件式存取 |
|   | 啟用 Microsoft Authenticator 應用程式使用手機登入。 |

![Azure AD 註冊裝置](./media/concept-azure-ad-register/azure-ad-registered-device.png)

在 Windows 10 裝置上，使用如 Microsoft 帳戶的本機帳戶登入 azure AD 註冊裝置，但另外有 Azure AD 帳戶以存取連結到組織的資源。 根據該 Azure AD 帳戶的進一步限制和條件式存取原則套用至裝置身分識別，可以是組織中資源的存取權。

系統管理員可以安全，並進一步控制使用 Microsoft Intune 等行動裝置管理 (MDM) 工具，這些 Azure AD 註冊裝置。 MDM 提供強化組織所需的設定，例如要求加密的儲存體、 密碼複雜性，並保持更新的安全性軟體的方法。 

存取工作應用程式第一次，或以手動方式使用 Windows 10 設定功能表時，就可以完成 azure AD 註冊。 

## <a name="scenarios"></a>案例

您的組織中的使用者想要存取電子郵件，工具報告時間關閉，並從家用電腦的優點註冊。 您的組織有後置條件式存取原則，要求從 Intune 相容裝置存取這些工具。 使用者將其組織帳戶，並向 Azure AD 註冊他們的家用電腦和所需的 Intune 原則會強制執行，讓使用者存取其資源。

另一位使用者想要存取其組織的電子郵件，已進行 root 破解其個人的 Android 手機上。 您的公司需要符合規範的裝置，並且已建立的 Intune 合規性政策，來封鎖任何 root 破解的裝置。 員工會停止，直到存取此裝置上的組織資源。

## <a name="next-steps"></a>後續步驟

- [管理裝置身分識別使用 Azure 入口網站](device-management-azure-portal.md)
- [管理 Azure AD 中的過時裝置](manage-stale-devices.md)
