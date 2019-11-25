---
title: Conditional Access require managed device - Azure Active Directory
description: Learn how to configure Azure Active Directory (Azure AD) device-based Conditional Access policies that require managed devices for cloud app access.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0caf8e8d5e18efc0a7332f97acccc394051ed360
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452390"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>How To: Require managed devices for cloud app access with Conditional Access

在行動第一、雲端第一的世界中，Azure Active Directory (Azure AD) 可讓您從任何地方單一登入至應用程式和服務。 授權使用者可以從多種裝置 (包括行動裝置和個人裝置) 存取您的雲端應用程式。 不過，許多環境都至少會有些許應用程式只應由符合您的安全性與相容性標準的裝置存取。 這些裝置也稱為受控裝置。 

This article explains how you can configure Conditional Access policies that require managed devices to access certain cloud apps in your environment. 

## <a name="prerequisites"></a>必要條件

Requiring managed devices for cloud app access ties **Azure AD Conditional Access** and **Azure AD device management** together. 如果您還不熟悉上述其中一種領域，您應該先閱讀下列主題：

- **[Conditional Access in Azure Active Directory](../active-directory-conditional-access-azure-portal.md)** - This article provides you with a conceptual overview of Conditional Access and the related terminology.
- **[Azure Active Directory 中的裝置管理簡介](../devices/overview.md)** - 本文概略說明在組織的控制下可用來連接裝置的各種選項。 

## <a name="scenario-description"></a>案例描述

要兼顧安全性與生產力並不容易。 在可存取雲端資源的裝置種類激增的情況下，使用者的生產力得以提升。 而另一方面，您應該不希望環境中的特定資源在保護層級不確定的情況下受到裝置存取。 對於受影響的資源，您應要求使用者只能使用受控裝置加以存取。 

With Azure AD Conditional Access, you can address this requirement with a single policy that grants access:

- 將存取權授與所選的雲端應用程式
- 將存取權授與所選使用者和群組
- 必須使用受控裝置

## <a name="managed-devices"></a>受控裝置  

簡單來說，受控裝置是受到*某種*組織性控制的裝置。 在 Azure AD 中，受控裝置的必要條件是已向 Azure AD 註冊。 註冊裝置時，系統會以裝置物件的形式為裝置建立身分識別。 Azure 使用此物件來追蹤裝置的狀態資訊。 身為 Azure AD 管理員的您已經可以使用此物件來切換 (啟用/停用) 裝置的狀態。
  
![裝置型條件](./media/require-managed-devices/32.png)

若要向 Azure AD 註冊裝置，您有三個選項： 

- **Azure AD registered devices** - to get a personal device registered with Azure AD
- **Azure AD joined devices** - to get an organizational Windows 10 device that is not joined to an on-premises AD registered with Azure AD. 
- **Hybrid Azure AD joined devices** - to get a Windows 10 or supported down-level device that is joined to an on-premises AD registered with Azure AD.

These three options are discussed in the article [What is a device identity?](../devices/overview.md)

若要成為受控裝置，已註冊的裝置必須是**已加入混合式 Azure AD 的裝置**，或是**標示為符合規範的裝置**。  

![裝置型條件](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>需要已加入混合式 Azure AD 的裝置

In your Conditional Access policy, you can select **Require Hybrid Azure AD joined device** to state that the selected cloud apps can only be accessed using a managed device. 

![裝置型條件](./media/require-managed-devices/10.png)

此設定僅適用於要加入內部部署 AD 的 Windows 10 或下層裝置 (例如 Windows 7 或 Windows 8)。 您只可以使用「加入混合式 Azure AD」向 Azure AD 註冊這些裝置，而這是註冊 Windows 10 裝置的[自動化程序](../devices/hybrid-azuread-join-plan.md)。 

![裝置型條件](./media/require-managed-devices/45.png)

如何讓已加入混合式 Azure AD 的裝置成為受控裝置？  若為已加入內部部署 AD 的裝置，則會假設已使用管理解決方案 (例如 **System Center Configuration Manager (SCCM)** 或**群組原則 (GP)** 強制控制這些裝置，進而管理它們。 因為 Azure AD 沒有任何方法可判斷裝置是否已套用任何方法，所以需要已加入混合式 Azure AD 的裝置是需要受控裝置的相對較弱機制。 如果這類裝置也是已加入混合式 Azure AD 的裝置，則由系統管理員判斷已加入網域的內部部署裝置所套用的方法是否足以造就受控裝置。

## <a name="require-device-to-be-marked-as-compliant"></a>裝置需要標記為符合規範

「裝置需要標記為符合規範」選項是要求受控裝置的最強形式。

![裝置型條件](./media/require-managed-devices/11.png)

此選項要求向 Azure AD 註冊裝置，而且裝置要由下列項目標示為符合規範：
         
- Intune
- 一個第三方行動裝置管理 (MDM) 系統，可透過 Azure AD 整合來管理 Windows 10 裝置。 不支援針對 Windows 10 以外的裝置 OS 類型使用的第三方 MDM 系統。
 
![裝置型條件](./media/require-managed-devices/46.png)

對於標示為符合規範的裝置，您可以假設： 

- 您的員工用來存取公司資料的行動裝置受到控管
- 您的員工使用的行動裝置應用程式受到控管
- 協助控制您的員工存取及共用公司資訊的方式，進而保護公司資訊
- 裝置與其應用程式都符合公司安全性需求的規範

> [!NOTE]
> If you configure a policy to require compliant devices users may be prompted on Mac, iOS, and Android to select a device certificate during policy evaluation. This is a known behavior.

## <a name="next-steps"></a>後續步驟

Before configuring a device-based Conditional Access policy in your environment, you should take a look at the [best practices for Conditional Access in Azure Active Directory](best-practices.md).
