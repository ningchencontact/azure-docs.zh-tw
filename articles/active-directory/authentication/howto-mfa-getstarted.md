---
title: Deploy Azure Multi-Factor Authentication - Azure Active Directory
description: Microsoft Azure Multi-Factor Authentication deployment planning
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cab4b57513f90a5cbe80724d4937f8d0f87b5517
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381789"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>Planning a cloud-based Azure Multi-Factor Authentication deployment

People are connecting to organizational resources in increasingly complicated scenarios. People connect from organization-owned, personal, and public devices on and off the corporate network using smart phones, tablets, PCs, and laptops, often on multiple platforms. In this always-connected, multi-device and multi-platform world, the security of user accounts is more important than ever. Passwords, no matter their complexity, used across devices, networks, and platforms are no longer sufficient to ensure the security of the user account, especially when users tend to reuse passwords across accounts. Sophisticated phishing and other social engineering attacks can result in usernames and passwords being posted and sold across the dark web.

[Azure Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) helps safeguard access to data and applications. It provides an additional layer of security using a second form of authentication. Organizations can use [Conditional Access](../conditional-access/overview.md) to make the solution fit their specific needs.

## <a name="prerequisites"></a>必要條件

Before starting a deployment of Azure Multi-Factor Authentication, there are prerequisite items that should be considered.

| 案例 | 先決條件 |
| --- | --- |
| **Cloud-only** identity environment with modern authentication | **No additional prerequisite tasks** |
| **Hybrid** identity scenarios | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) is deployed and user identities are synchronized or federated with the on-premises Active Directory Domain Services with Azure Active Directory. |
| On-premises legacy applications published for cloud access | Azure AD [Application Proxy](../manage-apps/application-proxy.md) is deployed. |
| Using Azure MFA with RADIUS Authentication | A [Network Policy Server (NPS)](howto-mfa-nps-extension.md) is deployed. |
| Users have Microsoft Office 2010 or earlier, or Apple Mail for iOS 11 or earlier | Upgrade to [Microsoft Office 2013 or later](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) and Apple mail for iOS 12 or later. Conditional Access is not supported by legacy authentication protocols. |

## <a name="plan-user-rollout"></a>Plan user rollout

Your MFA rollout plan should include a pilot deployment followed by deployment waves that are within your support capacity. Begin your rollout by applying your Conditional Access policies to a small group of pilot users. After evaluating the effect on the pilot users, process used, and registration behaviors, you can either add more groups to the policy or add more users to the existing groups.

### <a name="user-communications"></a>User communications

It is critical to inform users, in planned communications, about upcoming changes, Azure MFA registration requirements, and any necessary user actions. We recommend communications are developed in concert with representatives from within your organization, such as a Communications, Change Management, or Human Resources departments.

Microsoft provides [communication templates](https://aka.ms/mfatemplates) and [end-user documentation](../user-help/security-info-setup-signin.md) to help draft your communications. You can send users to [https://myprofile.microsoft.com](https://myprofile.microsoft.com) to register directly by selecting the **Security Info** links on that page.

## <a name="deployment-considerations"></a>部署考量

Azure Multi-factor Authentication is deployed by enforcing policies with Conditional Access. A [Conditional Access policy](../conditional-access/overview.md) can require users to perform multi-factor authentication when certain criteria are met such as:

* All users, a specific user, member of a group, or assigned role
* Specific cloud application being accessed
* 裝置平台
* State of device
* Network location or geo-located IP address
* 用戶端應用程式
* Sign-in risk (Requires Identity Protection)
* 符合規範的裝置
* 已加入混合式 Azure AD 的裝置
* Approved client application

Use the customizable posters and email templates in [multi-factor authentication rollout materials](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) to roll out multi-factor authentication to your organization.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>透過條件式存取啟用 Multi-Factor Authentication

Conditional Access policies enforce registration, requiring unregistered users to complete registration at first sign-in, an important security consideration.

[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) contributes both a registration policy for and automated risk detection and remediation policies to the Azure Multi-Factor Authentication story. Policies can be created to force password changes when there is a threat of compromised identity or require MFA when a sign-in is deemed risky by the following [events](../reports-monitoring/concept-risk-events.md):

* 認證外洩
* 從匿名 IP 位址登入
* 不可能到達非典型位置的移動
* 從不熟悉的位置登入
* 從受感染的裝置登入
* Sign-ins from IP addresses with suspicious activities

Some of the risk detections detected by Azure Active Directory Identity Protection occur in real time and some require offline processing. Administrators can choose to block users who exhibit risky behaviors and remediate manually, require a password change, or require a multi-factor authentication as part of their Conditional Access policies.

## <a name="define-network-locations"></a>Define network locations

We recommended that organizations use Conditional Access to define their network using [named locations](../conditional-access/location-condition.md#named-locations). If your organization is using Identity Protection, consider using risk-based policies instead of named locations.

### <a name="configuring-a-named-location"></a>Configuring a named location

1. Open **Azure Active Directory** in the Azure portal
2. Click **Conditional Access**
3. Click **Named Locations**
4. Click **New Location**
5. In the **Name** field, provide a meaningful name
6. Select whether you are defining the location using IP ranges or Countries/Regions
   1. If using IP Ranges
      1. Decide whether to mark the location as Trusted. 從受信任位置登入可降低使用者的登入風險。 Only mark this location as trusted if you know the IP ranges entered are established and credible in your organization.
      2. Specify the IP Ranges
   2. If using Countries/Regions
      1. Expand the drop-down menu and select the countries or regions you wish to define for this named location.
      2. Decide whether to Include unknown areas. Unknown areas are IP addresses that can't be mapped to a country/region.
7. 按一下 [建立]

## <a name="plan-authentication-methods"></a>Plan authentication methods

Administrators can choose the [authentication methods](../authentication/concept-authentication-methods.md) that they want to make available for users. It is important to allow more than a single authentication method so that users have a backup method available in case their primary method is unavailable. The following methods are available for administrators to enable:

### <a name="notification-through-mobile-app"></a>行動應用程式的通知

A push notification is sent to the Microsoft Authenticator app on your mobile device. The user views the notification and selects **Approve** to complete verification. Push notifications through a mobile app provide the least intrusive option for users. They are also the most reliable and secure option because they use a data connection rather than telephony.

> [!NOTE]
> If your organization has staff working in or traveling to China, the **Notification through mobile app** method on **Android devices** does not work in that country. Alternate methods should be made available for those users.

### <a name="verification-code-from-mobile-app"></a>行動應用程式傳回的驗證碼

A mobile app like the Microsoft Authenticator app generates a new OATH verification code every 30 seconds. 使用者會在登入介面中輸入此驗證碼。 The mobile app option can be used whether or not the phone has a data or cellular signal.

### <a name="call-to-phone"></a>電話通話

An automated voice call is placed to the user. The user answers the call and presses **#** on the phone keypad to approve their authentication. Call to phone is a great backup method for notification or verification code from a mobile app.

### <a name="text-message-to-phone"></a>電話簡訊

A text message that contains a verification code is sent to the user, the user is prompted to enter the verification code into the sign-in interface.

### <a name="choose-verification-options"></a>選擇驗證選項

1. 瀏覽至 **Azure Active Directory**、**使用者**、**Multi-Factor Authentication**。

   ![從 Azure 入口網站中的 [Azure AD 使用者] 刀鋒視窗存取 Multi-Factor Authentication 入口網站](media/howto-mfa-getstarted/users-mfa.png)

1. 在開啟的新索引標籤中，瀏覽至 [服務設定]。
1. 在 [驗證選項] 底下，核取使用者可用方法的所有方塊。

   ![在 [Multi-Factor Authentication 服務設定] 索引標籤中設定驗證方法](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. 按一下 [儲存]。
1. 關閉 [服務設定] 索引標籤。

## <a name="plan-registration-policy"></a>Plan registration policy

Administrators must determine how users will register their methods. Organizations should [enable the new combined registration experience](howto-registration-mfa-sspr-combined.md) for Azure MFA and self-service password reset (SSPR). SSPR allows users to reset their password in a secure way using the same methods they use for multi-factor authentication. We recommend this combined registration, currently in public preview, because it’s a great experience for users, with the ability to register once for both services. Enabling the same methods for SSPR and Azure MFA will allow your users to be registered to use both features.

### <a name="registration-with-identity-protection"></a>Registration with Identity Protection

If your organization is using Azure Active Directory Identity Protection, [configure the MFA registration policy](../identity-protection/howto-mfa-policy.md) to prompt your users to register the next time they sign in interactively.

### <a name="registration-without-identity-protection"></a>Registration without Identity Protection

If your organization does not have licenses that enable Identity Protection, users are prompted to register the next time that MFA is required at sign-in. Users may not be registered for MFA if they don't use applications protected with MFA. It's important to get all users registered so that bad actors cannot guess the password of a user and register for MFA on their behalf, effectively taking control of the account.

#### <a name="enforcing-registration"></a>Enforcing registration

Using the following steps a Conditional Access policy can force users to register for Multi-Factor Authentication

1. Create a group, add all users not currently registered.
2. Using Conditional Access, enforce multi-factor authentication for this group for access to all resources.
3. Periodically, reevaluate the group membership, and remove users who have registered from the group.

You may identify registered and non-registered Azure MFA users with PowerShell commands that rely on the [MSOnline PowerShell module](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identify registered users

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identify non-registered users

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Convert users from per-user MFA to Conditional Access based MFA

If your users were enabled using per-user enabled and enforced Azure Multi-Factor Authentication the following PowerShell can assist you in making the conversion to Conditional Access based Azure Multi-Factor Authentication.

Run this PowerShell in an ISE window or save as a .PS1 file to run locally.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> We recently changed the behavior and PowerShell script above accordingly. Previously, the script saved off the MFA methods, disabled MFA, and restored the methods. This is no longer necessary now that the default behavior for disable doesn't clear the methods.

## <a name="plan-conditional-access-policies"></a>Plan Conditional Access policies

To plan your Conditional Access policy strategy, which will determine when MFA and other controls are required, refer to [What is Conditional Access in Azure Active Directory?](../conditional-access/overview.md).

It is important that you prevent being inadvertently locked out of your Azure AD tenant. You can mitigate the impact of this inadvertent lack of administrative access by [creating two or more emergency access accounts in your tenant](../users-groups-roles/directory-emergency-access.md) and excluding them from your Conditional Access policy.

### <a name="create-conditional-access-policy"></a>Create Conditional Access policy

1. 使用全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至 [Azure Active Directory]、[條件式存取]。
1. 選取 [新增原則]。
   ![Create a Conditional Access policy to enable MFA for Azure portal users in pilot group](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. 為原則提供有意義的名稱。
1. 在 [使用者和群組] 底下：
   * 在 [包含] 索引標籤上，選取 [所有使用者] 選項按鈕
   * On the **Exclude** tab, check the box for **Users and groups** and choose your emergency access accounts.
   * 按一下 [完成]。
1. 在 [雲端應用程式] 下方，選取 [所有雲端應用程式] 選項按鈕。
   * 選擇性：在 [排除] 索引標籤上，選擇組織不要要求使用 MFA 的雲端應用程式。
   * 按一下 [完成]。
1. 在 [條件] 區段底下：
   * 選擇性：如果您已啟用 Azure Identity Protection，您可以選擇要在原則中評估登入風險。
   * 選擇性：如果您已設定信任的位置或具名位置，您可以指定要在原則中包含或排除這些位置。
1. 在 [授與] 下方，確定已選取 [授與存取權] 選項按鈕。
    * 核取 [需要多重要素驗證] 的方塊。
    * 按一下 [選取]。
1. 略過 [工作階段] 區段。
1. 將 [啟用原則] 切換為 [開啟]。
1. 按一下 [建立]。

## <a name="plan-integration-with-on-premises-systems"></a>Plan integration with on-premises systems

Some legacy and on-premises applications that do not authenticate directly against Azure AD require additional steps to use MFA including:

* Legacy on-premises applications, which will need to use Application proxy.
* On-premises RADIUS applications, which will need to use MFA adapter with NPS server.
* On-premises AD FS applications, which will need to use MFA adapter with AD FS 2016 or newer.

Applications that authenticate directly with Azure AD and have modern authentication (WS-Fed, SAML, OAuth, OpenID Connect) can make use of Conditional Access policies directly.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Use Azure MFA with Azure AD Application Proxy

Applications residing on-premises can be published to your Azure AD tenant via [Azure AD Application Proxy](../manage-apps/application-proxy.md) and can take advantage of Azure Multi-Factor Authentication if they are configured to use Azure AD pre-authentication.

These applications are subject to Conditional Access policies that enforce Azure Multi-Factor Authentication, just like any other Azure AD-integrated application.

Likewise, if Azure Multi-Factor Authentication is enforced for all user sign-ins, on-premises applications published with Azure AD Application Proxy will be protected.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integrating Azure Multi-Factor Authentication with Network Policy Server

Azure MFA 的網路原則伺服器 (NPS) 擴充功能可使用現有伺服器將雲端式 MFA 功能新增至驗證基礎結構。 With the NPS extension, you can add phone call, text message, or phone app verification to your existing authentication flow. This integration has the following limitations:

* With the CHAPv2 protocol, only authenticator app push notifications and voice call are supported.
* Conditional Access policies cannot be applied.

The NPS extension acts as an adapter between RADIUS and cloud-based Azure MFA to provide a second factor of authentication to protect [VPN](howto-mfa-nps-extension-vpn.md), [Remote Desktop Gateway connections](howto-mfa-nps-extension-rdg.md), or other RADIUS capable applications. Users that register for Azure MFA in this environment will be challenged for all authentication attempts, the lack of Conditional Access policies means MFA is always required.

#### <a name="implementing-your-nps-server"></a>Implementing your NPS server

If you have an NPS instance deployed and in use already, reference [Integrate your existing NPS Infrastructure with Azure Multi-Factor Authentication](howto-mfa-nps-extension.md). If you are setting up NPS for the first time, refer to [Network Policy Server (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) for instructions. Troubleshooting guidance can be found in the article [Resolve error messages from the NPS extension for Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Prepare NPS for users that aren't enrolled for MFA

Choose what happens when users that aren’t enrolled with MFA try to authenticate. Use the registry setting `REQUIRE_USER_MATCH` in the registry path `HKLM\Software\Microsoft\AzureMFA` to control the feature behavior. This setting has a single configuration option.

| 索引鍵 | Value | 預設值 |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE / FALSE | 未設定 (相當於 TRUE) |

此設定的目的是要決定當使用者未註冊 MFA 時的行為。 The effects of changing this setting are listed in the table below.

| 設定 | User MFA Status | Effects |
| --- | --- | --- |
| Key does not exist | Not enrolled | MFA challenge is unsuccessful |
| Value set to True / not set | Not enrolled | MFA challenge is unsuccessful |
| Key set to False | Not enrolled | Authentication without MFA |
| Key set to False or True | Enrolled | Must authenticate with MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integrate with Active Directory Federation Services

If your organization is federated with Azure AD, you can use [Azure Multi-Factor Authentication to secure AD FS resources](multi-factor-authentication-get-started-adfs.md), both on-premises and in the cloud. Azure MFA enables you to reduce passwords and provide a more secure way to authenticate. Starting with Windows Server 2016, you can now configure Azure MFA for primary authentication.

Unlike with AD FS in Windows Server 2012 R2, the AD FS 2016 Azure MFA adapter integrates directly with Azure AD and does not require an on-premises Azure MFA server. The Azure MFA adapter is built into Windows Server 2016, and there is no need for an additional installation.

When using Azure MFA with AD FS 2016 and the target application is subject to Conditional Access policy, there are additional considerations:

* Conditional Access is available when the application is a relying party to Azure AD, federated with AD FS 2016 or newer.
* Conditional Access is not available when the application is a relying party to AD FS 2016 or AD FS 2019 and is managed or federated with AD FS 2016 or AD FS 2019.
* Conditional Access is also not available when AD FS 2016 or AD FS 2019 is configured to use Azure MFA as the primary authentication method.

#### <a name="ad-fs-logging"></a>AD FS logging

Standard AD FS 2016 and 2019 logging in both the Windows Security Log and the AD FS Admin log, contains information about authentication requests and their success or failure. Event log data within these events will indicate whether Azure MFA was used. For example, an AD FS Auditing Event ID 1200 may contain:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Renew and manage certificates

On each AD FS server, in the local computer My Store, there will be a self-signed Azure MFA certificate titled OU=Microsoft AD FS Azure MFA, which contains the certificate expiration date. Check the validity period of this certificate on each AD FS server to determine the expiration date.

If the validity period of your certificates is nearing expiration, [generate and verify a new MFA certificate on each AD FS server](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

The following guidance details how to manage the Azure MFA certificates on your AD FS servers. When you configure AD FS with Azure MFA, the certificates generated via the `New-AdfsAzureMfaTenantCertificate` PowerShell cmdlet are valid for 2 years. Renew and install the renewed certificates prior to expiration to ovoid disruptions in MFA service.

## <a name="implement-your-plan"></a>Implement your plan

Now that you have planned your solution, you can implement by following the steps below:

1. Meet any necessary prerequisites
   1. Deploy [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) for any hybrid scenarios
   1. Deploy [Azure AD Application Proxy](../manage-apps/application-proxy.md) for on any on-premises apps published for cloud access
   1. Deploy [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) for any RADIUS authentication
   1. Ensure users have upgraded to supported versions of Microsoft Office with modern authentication enabled
1. Configure chosen [authentication methods](#choose-verification-options)
1. Define your [named network locations](../conditional-access/location-condition.md#named-locations)
1. Select groups to begin rolling out MFA.
1. Configure your [Conditional Access policies](#create-conditional-access-policy)
1. Configure your MFA registration policy
   1. [Combined MFA and SSPR](howto-registration-mfa-sspr-combined.md)
   1. With [Identity Protection](../identity-protection/howto-mfa-policy.md)
1. Send user communications and get users to enroll at [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Keep track of who’s enrolled](#identify-non-registered-users)

> [!TIP]
> Government cloud users can enroll at [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>管理您的解決方案

Reports for Azure MFA

Azure Multi-Factor Authentication provides reports through the Azure portal:

| 報告 | Location | 描述 |
| --- | --- | --- |
| 使用方式和詐騙警示 | Azure AD > 登入 | 提供整體使用量、使用者摘要和使用者詳細資料的相關資訊；以及在指定的日期範圍期間所提交的詐騙警示歷程記錄。 |

## <a name="troubleshoot-mfa-issues"></a>Troubleshoot MFA issues

Find solutions for common issues with Azure MFA at the [Troubleshooting Azure Multi-Factor Authentication article](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) on the Microsoft Support Center.

## <a name="next-steps"></a>後續步驟

* [驗證方法有哪些？](concept-authentication-methods.md)
* [啟用 Azure Multi-Factor Authentication 的聚合式註冊和 Azure AD 自助密碼重設](concept-registration-mfa-sspr-converged.md)
* 系統會還是不會提示使用者執行 MFA 的原因？ 請參閱＜Azure Multi-Factor Authentication 中的報告＞文件中的 [Azure AD 登入報告](howto-mfa-reporting.md#azure-ad-sign-ins-report)一節。
