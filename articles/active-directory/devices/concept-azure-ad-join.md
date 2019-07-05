---
title: 什麼是 Azure AD 已加入裝置？
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
ms.openlocfilehash: 4af3aea7218ea8792bb66188e8df7baf9f460b0b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462807"
---
# <a name="azure-ad-joined-devices"></a>Azure AD 加入裝置

Azure AD join 適用於想要為雲端優先或僅限雲端的組織。 任何組織可部署 Azure AD 加入裝置，無論何種大小或產業。 Azure AD join 適用於甚至在混合式環境中，啟用雲端和內部部署的應用程式和資源的存取。

|   | Azure AD Join |
| --- | --- |
| **定義** | 只有已加入 Azure AD 需要組織帳戶登入裝置 |
| **主要對象** | 適用於兩者僅限雲端和混合式組織。 |
|   | 適用於組織中的所有使用者 |
| **裝置擁有權** | 組織 |
| **作業系統** | 所有 Windows 10 裝置 |
| **佈建** | 自助：Windows OOBE 或設定值 |
|   | 大量註冊 |
|   | Windows Autopilot |
| **裝置登入選項** | 使用組織帳戶： |
|   | 密碼 |
|   | Windows Hello 企業版 |
|   | FIDO2.0 安全性金鑰 （預覽） |
| **裝置管理** | 行動裝置管理 (範例：Microsoft Intune) |
|   | 使用 Microsoft Intune 和 System Center Configuration Manager 共同管理 |
| **主要功能** | 雲端和內部部署資源的 SSO |
|   | 透過 MDM 註冊和 MDM 合規性評估的條件式存取 |
|   | 自助式密碼重設 Windows Hello 的 pin 碼重設和鎖定畫面上 |
|   | 在裝置上的企業狀態漫遊 |

Azure AD 聯結裝置使用組織登入 Azure AD 帳戶。 組織中資源的存取權可進一步限制根據該 Azure AD 帳戶及[條件式存取原則](../conditional-access/overview.md)套用至裝置身分識別。

系統管理員可以保護並進一步控制 Azure AD 已加入使用行動裝置管理 (MDM) 工具，例如 Microsoft Intune，或使用 System Center Configuration Manager 的共同管理案例中的裝置。 這些工具能用來強制執行組織所需的設定，例如要求加密的儲存體、 密碼複雜性、 軟體安裝和軟體更新。 系統管理員可以將組織應用程式提供給使用 Azure AD 已加入裝置[System Center Configuration Manager 和 Microsoft Store for Business](https://docs.microsoft.com/sccm/apps/deploy-use/manage-apps-from-the-windows-store-for-business)。

使用自助式選項等體驗 (OOBE)，大量註冊，就可以完成 azure AD join 或[Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot)。

當它們位於組織的網路，則 azure AD 已加入裝置可以維持在內部部署資源的單一登入存取。 已加入 Azure ad 的裝置仍可以向內部部署伺服器，例如檔案、 列印及其他應用程式。

## <a name="scenarios"></a>案例

雖然 Azure AD Join 主要適用於沒有內部部署 Windows Server Active Directory 基礎結構的組織，但您也可以在下列情況下使用它：

- 您想要使用 Azure AD 和 MDM (例如 Intune) 來轉換成雲端式基礎結構。
- 例如，如果您需要取得行動裝置 (例如控制下的平板電腦和電話)，您無法使用內部部署網域加入。
- 您的使用者主要需要存取 Office 365 或與 Azure AD 整合的其他 SaaS 應用程序。
- 您想要管理 Azure AD 中的使用者群組，而不是 Active Directory 中的使用者群組。 此案例適用於，例如季節性工作者、 承包商或學生。
- 您要為內部部署基礎結構受到限制的遠端分公司工作者提供加入功能。

您可以設定適用於 Windows 10 裝置的 Azure AD 已加入裝置。

Azure AD 加入裝置的目標是簡化：

- Windows 部署工作用的裝置
- 從任何 Windows 裝置存取組織應用程式與資源
- 工作用裝置的雲端式管理
- 他們使用其 Azure AD 的裝置登入的使用者或同步處理 Active Directory 工作或學校帳戶。

![Azure AD 加入裝置](./media/concept-azure-ad-join/azure-ad-joined-device.png)

您可以使用下列任何一種方法來部署 Azure AD Join：

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [大量部署](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [自助體驗](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>後續步驟

- [規劃您的 Azure AD 聯結實作](azureadjoin-plan.md)
- [如何在 Azure AD 中管理的本機 administrators 群組已加入裝置](assign-local-admin.md)
- [管理裝置身分識別使用 Azure 入口網站](device-management-azure-portal.md)
- [管理 Azure AD 中的過時裝置](manage-stale-devices.md)
