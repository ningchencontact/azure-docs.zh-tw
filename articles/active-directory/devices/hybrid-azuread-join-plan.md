---
title: 如何規劃 Azure Active Directory (Azure AD) 中的混合式 Azure Active Directory Join 實作 | Microsoft Docs
description: 了解如何設定混合式 Azure Active Directory 已加入的裝置。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: abb050eb527e65b4fd31f3251d37fef7d51e867e
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655967"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>如何：規劃混合式 Azure Active Directory Join 實作

以類似的使用方式，裝置是您想要保護的另一個核心身分識別，可用來隨時隨地保護您的資源。 您可以使用下列其中一種方法，將裝置身分識別導入 Azure AD 中進行管理，以達到此目標：

- Azure AD Join
- 混合式 Azure AD Join
- Azure AD 註冊

將您的裝置導入 Azure AD 中，您將可透過跨雲端和內部部署資源的單一登入 (SSO)，將使用者的生產力最大化。 在此同時，您可以安全存取您雲端和內部部署資源[條件式存取](../active-directory-conditional-access-azure-portal.md)。

如果您有內部部署 Active Directory (AD) 環境，而且您想要將您的 AD 網域的電腦加入 Azure AD，您可以藉由混合式 Azure AD join 完成。 本文提供在您的環境中實作混合式 Azure AD Join 的相關步驟。 

## <a name="prerequisites"></a>必要條件

本文假設您已熟悉[Azure Active Directory 中的裝置身分識別管理簡介](../device-management-introduction.md)。

> [!NOTE]
> 需要的最低網域功能和樹系功能等級為 Windows 10 的混合式 Azure AD join 是 Windows Server 2008 R2。

## <a name="plan-your-implementation"></a>計劃您的實作

若要規劃您的混合式 Azure AD 實作，您應該熟悉：

|   |   |
| --- | --- |
| ![勾選][1] | 檢閱支援的裝置 |
| ![勾選][1] | 檢閱您應該知道的事情 |
| ![勾選][1] | 檢閱受控制的混合式 Azure AD join 的驗證 |
| ![勾選][1] | 選取您根據您的身分識別基礎結構的案例 |
| ![勾選][1] | 檢閱內部部署 AD UPN 的支援將混合式 Azure AD join |

## <a name="review-supported-devices"></a>檢閱支援的裝置

混合式 Azure AD Join 支援廣泛的 Windows 裝置。 因為執行舊版 Windows 的裝置設定需要其他或不同的步驟，所以支援的裝置會分為兩類：

### <a name="windows-current-devices"></a>現行 Windows 裝置

- Windows 10
- Windows Server 2016
- Windows Server 2019

對於執行 Windows 桌面作業系統的裝置，這篇文章中列出支援的版本[Windows 10 版本資訊](https://docs.microsoft.com/windows/release-information/)。 最佳做法是，Microsoft 會建議您升級至 Windows 10 最新版本。

### <a name="windows-down-level-devices"></a>舊版 Windows 裝置

- Windows 8.1
- Windows 7. 如需 Windows 7 的支援資訊，請檢閱這篇文章[適用於 Windows 7 支援即將結束](https://www.microsoft.com/en-us/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

在第一個規劃步驟中，您應該檢閱您的環境，並判斷是否需要支援 Windows 舊版裝置。

## <a name="review-things-you-should-know"></a>檢閱您應該知道的事情

如果您的環境是由單一 AD 樹系同步處理多個 Azure AD 租用戶的身分識別資料所組成，混合式 Azure AD join 是目前不支援。

加入混合式 Azure AD 目前不支援使用虛擬桌面基礎結構 (VDI) 時。

加入混合式 Azure AD 不支援 FIPS 相容的 Tpm。 如果您的裝置有 FIPS 相容的 Tpm，您必須進行混合式 Azure AD 聯結之前，先停用它們。 Microsoft 不提供任何工具對 Tpm 中停用 FIPS 模式，因為它是取決於 TPM 製造商。 請如需支援，連絡您的硬體 OEM。

執行網域控制站 (DC) 角色的 Windows Server 不支援混合式 Azure AD join。

混合式 Azure AD 聯結不支援在 Windows 舊版裝置上使用認證漫遊，或使用者設定檔漫遊時。

如果您依賴的系統準備工具 (Sysprep)，如果您使用**pre-Windows 10 1809年**安裝映像，請確定該映像不是從已向 Azure AD 混合式 Azure AD 聯結裝置。

如果您依賴在虛擬機器 (VM) 快照集來建立其他 Vm，請確定該快照集不是來自已向 Azure AD 混合式 Azure AD join 的 VM。

如果您已加入網域的 Windows 10 裝置已向租用戶[註冊 Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview#getting-devices-in-azure-ad)，我們強烈建議您先移除該狀態，再啟用混合式 Azure AD Join。 自 Windows 10 1809 版起，我們已進行下列變更以避免這種雙重狀態：

- 在裝置加入混合式 Azure AD 之後，就會自動移除任何現有的 Azure AD 註冊狀態。
- 您可以防止您加入的網域的裝置 Azure AD 註冊加入此登錄機碼-HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin，「 BlockAADWorkplaceJoin"= dword: 00000001。
- 這項變更現在是適用於 Windows 10 1803年版 KB4489894 套用使用中。 不過，如果您有 Windows hello 企業版設定，使用者也需要重新設定 Windows hello 企業版的雙狀態之後清除。

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>檢閱受控制的混合式 Azure AD join 的驗證

所有必要的元件時就地，將自動將 Windows 裝置註冊為 Azure AD 租用戶中的裝置。 在 Azure AD 中的這些裝置身分識別狀態稱為 「 混合式 Azure AD 聯結 」。 可以在文章中找到這篇文章所涵蓋的概念的詳細資訊[Azure Active Directory 中的裝置身分識別管理簡介](overview.md)和[規劃您的混合式 Azure Active Directory join實作](hybrid-azuread-join-plan.md)。

組織可能想要執行混合式 Azure AD join 控制的驗證，再讓它全部一次其整個組織。 檢閱文件[控制的混合式 Azure AD join 驗證](hybrid-azuread-join-control.md)來了解如何完成這項工作。

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>選取您根據您的身分識別基礎結構的案例

混合式 Azure AD join 搭配，受控和同盟的環境。  

### <a name="managed-environment"></a>受控環境

受控環境可使用[無縫單一登入](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)透過[密碼雜湊同步 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) 或[傳遞驗證 (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) 進行部署。

在這些案例中，您不需要設定同盟伺服器以進行驗證。

### <a name="federated-environment"></a>同盟環境

同盟的環境中應該有識別提供者支援下列需求：

- **Ws-trust 通訊協定：** 此通訊協定，才能驗證 Windows 目前的混合式 Azure AD 已加入 Azure ad 的裝置。
- **WIAORMULTIAUTHN 宣告：** 此宣告，才能執行混合式 Azure AD join 適用於 Windows 舊版裝置。

如果您有使用 Active Directory Federation Services (AD FS) 同盟的環境中，都已經支援上述的需求。

> [!NOTE]
> Azure AD 不支援受控網域中的智慧卡或憑證。

從 1.1.819.0 版開始，Azure AD Connect 即為您提供用來設定混合式 Azure AD Join 的精靈。 此精靈可讓您大幅簡化設定程序。 如果安裝必要的 Azure AD Connect 版本不適合您，請參閱[如何手動設定裝置註冊](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual)。 

根據符合您的身分識別基礎結構的案例，請參閱：

- [設定同盟環境中的混合式 Azure Active Directory join](hybrid-azuread-join-federated-domains.md)
- [設定受管理環境的混合式 Azure Active Directory join](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>檢閱內部部署 AD UPN 支援混合式 Azure AD 加入

有時候，您的內部部署 AD UPN 可能與 Azure AD UPN 不同。 在此情況下，Windows 10 混合式 Azure AD Join 會根據[驗證方法](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)、網域類型和 Windows 10 版本，提供有限的內部部署 AD UPN 支援。 您的環境中可以有兩種類型的內部部署 AD UPN：

- 可路由的 UPN：可路由的 UPN 具有有效的已驗證網域，該網域已向網域註冊機構註冊。 例如，如果 Azure AD 中的主要網域為 contoso.com，則 contoso.org 在 Contoso 所擁有且[已在 Azure AD 中驗證](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)的內部部署 AD 中是主要網域
- 不可路由的 UPN：不可路由的 UPN 沒有已驗證的網域。 它僅適用於組織的私人網路內。 例如，如果 Azure AD 中的主要網域為 contoso.com，則 contoso.local 是內部部署 AD 中的主要網域，但不是網際網路中可驗證的網域，而且只能在 Contoso 的網路內使用。

下表提供有關在 Windows 10 混合式 Azure AD Join 中支援這些內部部署 AD UPN 的詳細資料

| 內部部署 AD UPN 的類型 | 網域類型 | Windows 10 版本 | 描述 |
| ----- | ----- | ----- | ----- |
| 路由式 | 同盟 | 自 1703 版起 | 正式推出 |
| 非可路由傳送 | 同盟 | 自 1803 版起 | 正式推出 |
| 路由式 | 受控 | 不支援 | |
| 非可路由傳送 | 受控 | 不支援 | |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [針對同盟的環境設定混合式 Azure Active Directory join](hybrid-azuread-join-federated-domains.md)
> [設定混合式 Azure Active Directory join 的受管理的環境](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
