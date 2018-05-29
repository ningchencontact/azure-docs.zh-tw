---
title: 設定 Azure Active Directory 裝置型條件式存取原則 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 裝置型條件式存取原則。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 07957d5ec843c414813d69b7084915bcd70a5a61
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930852"
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>設定 Azure Active Directory 裝置型條件式存取原則

透過 [Azure Active Directory (Azure AD) 條件式存取](active-directory-conditional-access-azure-portal.md)，您可以控制授權使用者如何存取您的資源。 例如，您可以將特定資源的存取限制為受控裝置。 需要受控裝置的條件式存取原則，也稱為裝置型條件式存取原則。

本主題說明如何為 Azure AD 連線應用程式設定裝置型條件式存取原則。 


## <a name="before-you-begin"></a>開始之前

裝置型條件式存取會將 **Azure AD 條件式存取**和 **Azure AD 裝置管理**繫結在一起。 如果您還不熟悉上述其中一種領域，您應該先閱讀下列主題：

- **[Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal.md)** - 本主題提供條件式存取和相關術語的概念性概觀。

- **[Azure Active Directory 中的裝置管理簡介](device-management-introduction.md)** - 本主題提供您可用來連線裝置與 Azure AD 之各種選項的概觀。 



## <a name="managed-devices"></a>受控裝置  

在行動第一、雲端第一的世界中，Azure Active Directory 可讓您從任何地方單一登入至裝置、應用程式和服務。 對於您環境中的某些資源，授與存取權給適當的使用者可能還不夠好。 除了適當的使用者，您也可能要求只能使用受控裝置執行存取嘗試。

受控裝置是符合您的安全性與合規性標準的裝置。 簡單來說，受控裝置是受到「某種」組織性控制的裝置。 在 Azure AD 中，受控裝置的必要條件是已向 Azure AD 註冊。 註冊裝置時，系統會以裝置物件的形式為裝置建立身分識別。 Azure 使用此物件來追蹤裝置的狀態資訊。 身為 Azure AD 管理員的您已經可以使用此物件來切換 (啟用/停用) 裝置的狀態。
  
![裝置型條件](./media/active-directory-conditional-access-policy-connected-applications/32.png)

若要向 Azure AD 註冊裝置，您有三個選項：

- **[Azure AD 註冊裝置](device-management-introduction.md#azure-ad-registered-devices)** - 向 Azure AD 註冊個人裝置

- **[已加入 Azure AD 的裝置](device-management-introduction.md#azure-ad-joined-devices)** - 向 Azure AD 註冊未加入內部部署 AD 的組織 Windows 10 裝置。 

- **[已加入混合式 Azure AD 的裝置](device-management-introduction.md#hybrid-azure-ad-joined-devices)** - 向 Azure AD 註冊已加入內部部署 AD 的 Windows 10 裝置。

若要成為受控裝置，註冊的裝置可以是已加入混合式 Azure AD 的裝置，或是標示為符合規範的裝置。  

![裝置型條件](./media/active-directory-conditional-access-policy-connected-applications/47.png)


 
## <a name="require-hybrid-azure-ad-joined-devices"></a>需要已加入混合式 Azure AD 的裝置

在條件式存取原則中，您可以選取 [需要已加入混合式 Azure AD 的裝置]，表示只能使用受控裝置來存取所選取的雲端應用程式。 

![裝置型條件](./media/active-directory-conditional-access-policy-connected-applications/10.png)

此設定只適用於已加入內部部署 Azure AD 的 Windows 10 裝置。 您只可以使用「加入混合式 Azure AD」向 Azure AD 註冊這些裝置，而這是註冊 Windows 10 裝置的[自動化程序](device-management-hybrid-azuread-joined-devices-setup.md)。 

![裝置型條件](./media/active-directory-conditional-access-policy-connected-applications/45.png)

如何讓已加入混合式 Azure AD 的裝置成為受控裝置？  若為已加入內部部署 AD 的裝置，則會假設已使用管理解決方案 (例如 **System Center Configuration Manager (SCCM)** 或**群組原則 (GP)** 強制控制這些裝置，進而管理它們。 因為 Azure AD 沒有任何方法可判斷裝置是否已套用任何方法，所以需要已加入混合式 Azure AD 的裝置是需要受控裝置的相對較弱機制。 如果這類裝置也是已加入混合式 Azure AD 的裝置，則由系統管理員判斷已加入網域的內部部署裝置所套用的方法是否足以造就受控裝置。


## <a name="require-device-to-be-marked-as-compliant"></a>裝置需要標記為符合規範

「裝置需要標記為符合規範」選項是要求受控裝置的最強形式。

![裝置型條件](./media/active-directory-conditional-access-policy-connected-applications/11.png)

此選項要求向 Azure AD 註冊裝置，而且裝置要由下列項目標示為符合規範：
         
- Intune 
- 協力廠商行動裝置受控系統，可透過 Azure AD 整合來管理 Windows 10 裝置 
 
![裝置型條件](./media/active-directory-conditional-access-policy-connected-applications/46.png)



對於標示為符合規範的裝置，您可以假設： 

- 您的員工用來存取公司資料的行動裝置受到控管
- 您的員工使用的行動裝置應用程式受到控管
- 協助控制您的員工存取及共用公司資訊的方式，進而保護公司資訊
- 裝置與其應用程式都符合公司安全性需求的規範




## <a name="next-steps"></a>後續步驟

在您的環境中設定裝置型條件式存取原則之前，您應該閱讀 [Azure Active Directory 中條件式存取的最佳做法](active-directory-conditional-access-best-practices.md)。

