---
title: 什麼是混合式 Azure AD 已加入裝置？
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
ms.openlocfilehash: 5c57180ba10322cb790c05b3f8f48043ca08b545
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462742"
---
# <a name="hybrid-azure-ad-joined-devices"></a>混合式 Azure AD 已加入裝置

十多年來，許多組織已對他們的內部部署 Active Directory 使用網域加入以讓：

- IT 部門可從中央位置管理工作用的裝置。
- 使用者可使用使用其 Active Directory 公司或學校帳戶登入他們的裝置。

通常，具有內部部署使用量的組織依賴映像處理方法來佈建裝置，且通常使用 **System Center Configuration Manager (SCCM)** 或**群組原則 (GP)** 加以管理。

如果您的環境具有內部部署 AD 使用量，而且您也想要從 Azure Active Directory 所提供的功能受益，您可以實作混合式 Azure AD 已加入裝置。 這些裝置是已加入您的內部部署 Active Directory，且與 Azure Active Directory 註冊的裝置。

|   | 混合式 Azure AD Join |
| --- | --- |
| **定義** | 加入內部部署 AD 與 Azure AD 需要組織帳戶登入裝置 |
| **主要對象** | 適用與現有的混合式組織的內部部署 AD 基礎結構 |
|   | 適用於組織中的所有使用者 |
| **裝置擁有權** | 組織 |
| **作業系統** | Windows 10、windows 8.1 和 7 |
|   | Windows Server 2008/R2，2012年/R2，2016年和 2019年 |
| **佈建** | Windows 10，Windows Server 2016/2019 |
|   | 加入網域的 IT 並自動連接功能，透過 Azure AD Connect 或 ADFS 組態 |
|   | 加入網域的 Windows Autopilot 和自動連接功能，透過 Azure AD Connect 或 ADFS 組態 |
|   | Windows 8.1、 Windows 7、 Windows Server 2012 R2、 Windows Server 2012 和 Windows Server 2008 R2-需要 MSI |
| **裝置登入選項** | 使用組織帳戶： |
|   | 密碼 |
|   | Windows Hello for Business for Win10 |
| **裝置管理** | 群組原則 |
|   | System Center Configuration Manager 獨立版或使用 Microsoft Intune 的共同管理 |
| **主要功能** | 雲端和內部部署資源的 SSO |
|   | 條件式存取透過網域聯結或 Intune 如果共同管理裝置 |
|   | 自助式密碼重設 Windows Hello 的 pin 碼重設和鎖定畫面上 |
|   | 在裝置上的企業狀態漫遊 |

![混合式 Azure AD 已加入裝置](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>案例

使用 Azure AD 已加入混合式裝置如果：

- 您已將 Win32 應用程式部署至這些倚賴 Active Directory 電腦驗證的裝置。
- 您想要繼續使用群組原則管理裝置設定。
- 您想要繼續使用現有的映像處理解決方案來部署和設定裝置。
- 您必須支援舊版 Windows 7 和 8.1 的裝置，以及 Windows 10

## <a name="next-steps"></a>後續步驟

- [規劃混合式 Azure AD 聯結實作](hybrid-azuread-join-plan.md)
- [管理裝置身分識別使用 Azure 入口網站](device-management-azure-portal.md)
- [管理 Azure AD 中的過時裝置](manage-stale-devices.md)
