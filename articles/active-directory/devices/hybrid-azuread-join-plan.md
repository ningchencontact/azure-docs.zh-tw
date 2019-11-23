---
title: Plan hybrid Azure Active Directory join - Azure Active Directory
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
ms.openlocfilehash: 3a4f85aeaf2fb263ba2df8f34a51f9e25c212aff
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379326"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>How To: Plan your hybrid Azure Active Directory join implementation

以類似的使用方式，裝置是您想要保護的另一個核心身分識別，可用來隨時隨地保護您的資源。 您可以使用下列其中一種方法，將裝置身分識別導入 Azure AD 中進行管理，以達到此目標：

- Azure AD Join
- 混合式 Azure AD Join
- Azure AD 註冊

將您的裝置導入 Azure AD 中，您將可透過跨雲端和內部部署資源的單一登入 (SSO)，將使用者的生產力最大化。 At the same time, you can secure access to your cloud and on-premises resources with [Conditional Access](../active-directory-conditional-access-azure-portal.md).

If you have an on-premises Active Directory (AD) environment and you want to join your AD domain-joined computers to Azure AD, you can accomplish this by doing hybrid Azure AD join. 本文提供在您的環境中實作混合式 Azure AD Join 的相關步驟。 

## <a name="prerequisites"></a>必要條件

This article assumes that you are familiar with the [Introduction to device identity management in Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> The minimum required domain controller version for Windows 10 hybrid Azure AD join is Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>計劃您的實作

若要規劃您的混合式 Azure AD 實作，您應該熟悉：

|   |   |
| --- | --- |
| ![勾選][1] | 檢閱支援的裝置 |
| ![勾選][1] | 檢閱您應該知道的事情 |
| ![勾選][1] | Review controlled validation of hybrid Azure AD join |
| ![勾選][1] | Select your scenario based on your identity infrastructure |
| ![勾選][1] | Review on-premises AD UPN support for hybrid Azure AD join |

## <a name="review-supported-devices"></a>檢閱支援的裝置

混合式 Azure AD Join 支援廣泛的 Windows 裝置。 因為執行舊版 Windows 的裝置設定需要其他或不同的步驟，所以支援的裝置會分為兩類：

### <a name="windows-current-devices"></a>現行 Windows 裝置

- Windows 10
- Windows Server 2016
- Windows Server 2019

For devices running the Windows desktop operating system, supported version are listed in this article [Windows 10 release information](https://docs.microsoft.com/windows/release-information/). As a best practice, Microsoft recommends you upgrade to the latest version of Windows 10.

### <a name="windows-down-level-devices"></a>舊版 Windows 裝置

- Windows 8.1
- Windows 7. For support information on Windows 7, see [Support for Windows 7 is ending](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. For support information on Windows Server 2008 and 2008 R2, see [Prepare for Windows Server 2008 end of support](https://www.microsoft.com/cloud-platform/windows-server-2008).

在第一個規劃步驟中，您應該檢閱您的環境，並判斷是否需要支援 Windows 舊版裝置。

## <a name="review-things-you-should-know"></a>檢閱您應該知道的事情

Hybrid Azure AD join is currently not supported if your environment consists of a single AD forest synchronizing identity data to more than one Azure AD tenant.

If your environment uses virtual desktop infrastructure (VDI), see [Device identity and desktop virtualization](https://docs.microsoft.com/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

Hybrid Azure AD join is supported for FIPS-compliant TPM 2.0 and not supported for TPM 1.2. If your devices have FIPS-compliant TPM 1.2, you must disable them before proceeding with Hybrid Azure AD join. Microsoft does not provide any tools for disabling FIPS mode for TPMs as it is dependent on the TPM manufacturer. Please contact your hardware OEM for support. Starting from WIndows 10 1903 release, TPMs 1.2 are not used for hybrid Azure AD join and devices with those TPMs will be considered as if they don't have a TPM.

Hybrid Azure AD join is not supported for Windows Server running the Domain Controller (DC) role.

Hybrid Azure AD join is not supported on Windows down-level devices when using credential roaming or user profile roaming or mandatory profile.

If you are relying on the System Preparation Tool (Sysprep) and if you are using a **pre-Windows 10 1809** image for installation, make sure that image is not from a device that is already registered with Azure AD as Hybrid Azure AD join.

If you are relying on a Virtual Machine (VM) snapshot to create additional VMs, make sure that snapshot is not from a VM that is already registered with Azure AD as Hybrid Azure AD join.

If your Windows 10 domain joined devices are [Azure AD registered](overview.md#getting-devices-in-azure-ad) to your tenant, it could lead to a dual state of Hybrid Azure AD joined and Azure AD registered device. We recommend upgrading to Windows 10 1803 (with KB4489894 applied) or above to automatically address this scenario. In pre-1803 releases, you will need to remove the Azure AD registered state manually before enabling Hybrid Azure AD join. In 1803 and above releases, the following changes have been made to avoid this dual state:

- Any existing Azure AD registered state would be automatically removed <i>after the device is Hybrid Azure AD joined</i>.
- You can prevent your domain joined device from being Azure AD registered by adding this registry key - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001.
- In Windows 10 1803, if you have Windows Hello for Business configured, the user needs to re-setup Windows Hello for Business after the dual state clean up.This issue has been addressed with KB4512509

> [!NOTE]
> The Azure AD registered device will not be automatically removed if it is managed by Intune.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Review controlled validation of hybrid Azure AD join

When all of the pre-requisites are in place, Windows devices will automatically register as devices in your Azure AD tenant. The state of these device identities in Azure AD is referred as hybrid Azure AD join. More information about the concepts covered in this article can be found in the article [Introduction to device identity management in Azure Active Directory](overview.md).

Organizations may want to do a controlled validation of hybrid Azure AD join before enabling it across their entire organization all at once. Review the article [controlled validation of hybrid Azure AD join](hybrid-azuread-join-control.md) to understand how to accomplish it.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Select your scenario based on your identity infrastructure

Hybrid Azure AD join works with both, managed and federated environments depending on whether the UPN is routable or non-routable. See bottom of the page for table on supported scenarios.  

### <a name="managed-environment"></a>受控環境

受控環境可使用[無縫單一登入](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)透過[密碼雜湊同步 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) 或[傳遞驗證 (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) 進行部署。

在這些案例中，您不需要設定同盟伺服器以進行驗證。

### <a name="federated-environment"></a>同盟環境

同盟環境應具有支援下列需求的識別提供者。 如果您的同盟環境使用 Active Directory 同盟服務 (AD FS)，則已支援下列需求。

- **WIAORMULTIAUTHN claim:** This claim is required to do hybrid Azure AD join for Windows down-level devices.
- **WS-Trust protocol:** This protocol is required to authenticate Windows current hybrid Azure AD joined devices with Azure AD. 當您使用 AD FS 時，您必須啟用下列 WS-Trust 端點：`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** 或 **adfs/services/trust/13/windowstransport** 都只能啟用為內部網路對應端點，且不得透過 Web 應用程式 Proxy 公開為內部網路對應端點。 若要深入了解如何停用 WS-Trust Windows 端點，請參閱[在 Proxy上停用 WS-Trust Windows 端點](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)。 您可以在 AD FS 管理主控台的 [服務] > [端點] 下方查看已啟用的端點。

> [!NOTE]
> Azure AD 不支援受控網域中的智慧卡或憑證。

從 1.1.819.0 版開始，Azure AD Connect 即為您提供用來設定混合式 Azure AD Join 的精靈。 此精靈可讓您大幅簡化設定程序。 如果安裝必要的 Azure AD Connect 版本不適合您，請參閱[如何手動設定裝置註冊](hybrid-azuread-join-manual.md)。 

Based on the scenario that matches your identity infrastructure, see:

- [Configure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
- [Configure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Review on-premises AD UPN support for Hybrid Azure AD join

有時候，您的內部部署 AD UPN 可能與 Azure AD UPN 不同。 在此情況下，Windows 10 混合式 Azure AD Join 會根據[驗證方法](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)、網域類型和 Windows 10 版本，提供有限的內部部署 AD UPN 支援。 您的環境中可以有兩種類型的內部部署 AD UPN：

- Routable UPN: A routable UPN has a valid verified domain, that is registered with a domain registrar. 例如，如果 Azure AD 中的主要網域為 contoso.com，則 contoso.org 在 Contoso 所擁有且[已在 Azure AD 中驗證](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)的內部部署 AD 中是主要網域
- Non-routable UPN: A non-routable UPN does not have a verified domain. 它僅適用於組織的私人網路內。 例如，如果 Azure AD 中的主要網域為 contoso.com，則 contoso.local 是內部部署 AD 中的主要網域，但不是網際網路中可驗證的網域，而且只能在 Contoso 的網路內使用。

下表提供有關在 Windows 10 混合式 Azure AD Join 中支援這些內部部署 AD UPN 的詳細資料

| 內部部署 AD UPN 的類型 | 網域類型 | Windows 10 版本 | 描述 |
| ----- | ----- | ----- | ----- |
| 路由式 | 同盟 | 自 1703 版起 | 正式推出 |
| 非可路由傳送 | 同盟 | 自 1803 版起 | 正式推出 |
| 路由式 | 受控 | 自 1803 版起 | Generally available, Azure AD SSPR on Windows lockscreen is not supported |
| 非可路由傳送 | 受控 | 不支援 | |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Configure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
> [Configure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
