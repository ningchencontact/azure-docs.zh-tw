---
title: Access controls in Azure Active Directory Conditional Access
description: Learn how access controls in Azure Active Directory Conditional Access work.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cac92da744b3d5b7aeaa325c7cc564a3d7e2abdd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380801"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>What are access controls in Azure Active Directory Conditional Access?

With [Azure Active Directory (Azure AD) Conditional Access](../active-directory-conditional-access-azure-portal.md), you can control how authorized users access your cloud apps. In a Conditional Access policy, you define the response ("do this") to the reason for triggering your policy ("when this happens").

![控制](./media/controls/10.png)

In the context of Conditional Access,

- "**When this happens**" 稱為**條件**
- "**Then do this**" 稱為**存取控制**

The combination of a condition statement with your controls represents a Conditional Access policy.

![控制](./media/controls/61.png)

每種控制項都是必須由登入之使用者或系統履行的要求，或是規範使用者在登入後之行為的限制。

控制項分為兩種：

- **授與控制項** - 限制存取權限
- **工作階段控制項** - 限制工作階段內的存取權限

This topic explains the various controls that are available in Azure AD Conditional Access. 

## <a name="grant-controls"></a>授與控制

透過授與控制項，您可以藉由選取需要的控制項來全面封鎖存取權限，或允許在滿足其他要求的情況下授與存取權限。 若要實施多個控制項，您可以要求：

- 滿足所有選取的控制項 (*AND*)
- 滿足一項選取的控制項 (*OR*)

![控制](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>多因素驗證

您可以使用這個控制項來要求使用者通過 Multi-Factor Authentication 後，才能存取指定的雲端應用程式。 這個控制項支援以下多重要素提供者：

- Azure Multi-Factor Authentication
- 結合 Active Directory Federation Services (AD FS) 的內部部署 Multi-Factor Authentication 提供者。

對於未獲授權但已取得有效使用者之主要認證存取權的使用者，使用 Multi-Factor Authentication 有助於防止其存取資源。

### <a name="compliant-device"></a>符合規範的裝置

You can configure Conditional Access policies that are device-based. The objective of a device-based Conditional Access policy is to only grant access to the selected cloud apps from [managed devices](require-managed-devices.md). 您必須限制對受控裝置的存取的其中一個選項，是要求將裝置標示為符合規範。 Intune (適用於任何裝置作業系統) 或您的協力廠商 MDM 系統 (適用於 Windows 10 裝置) 可以將裝置標示為符合規範。 不支援針對 Windows 10 以外的裝置 OS 類型使用的第三方 MDM 系統。 

您的裝置必須向 Azure AD 註冊，才能標示為符合規範。 若要註冊裝置，您會有三個選項： 

- Azure AD 註冊裝置
- Azure AD 加入裝置  
- 混合式 Azure AD 已加入裝置

These three options are discussed in the article [What is a device identity?](../devices/overview.md)

For more information, see [how to require managed devices for cloud app access with Conditional Access](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>已加入混合式 Azure AD 的裝置

Requiring a Hybrid Azure AD joined device is another option you have to configure device-based Conditional Access policies. 這項需求是指加入內部部署 Active Directory 的 Windows 桌上型電腦、膝上型電腦和企業平板電腦。 If this option is selected, your Conditional Access policy grants access to access attempts made with devices that are joined to your on-premises Active Directory and your Azure Active Directory.  

For more information, see [set up Azure Active Directory device-based Conditional Access policies](require-managed-devices.md).

### <a name="approved-client-app"></a>已核准的用戶端應用程式

由於員工使用行動裝置來處理個人和工作事務，因此即使您不負責管理公司資料，可能也會想要在員工使用裝置存取公司資料時保護資料。
您可以使用 [Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)來保護公司的資料，並且不受任何行動裝置管理 (MDM) 解決方案影響。

藉由核准的用戶端應用程式，您可以要求嘗試存取雲端應用程式的用戶端應用程式支援 [Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)。 例如，您可以限制唯有 Outlook 應用程式能存取 Exchange Online。 A Conditional Access policy that requires approved client apps is  also known as [app-based Conditional Access policy](app-based-conditional-access.md). 如需支援的核准用戶端應用程式清單，請參閱[核准的用戶端應用程式需求](technical-reference.md#approved-client-app-requirement)。

### <a name="app-protection-policy-preview"></a>App protection policy (preview)

由於員工使用行動裝置來處理個人和工作事務，因此即使您不負責管理公司資料，可能也會想要在員工使用裝置存取公司資料時保護資料。
您可以使用 [Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)來保護公司的資料，並且不受任何行動裝置管理 (MDM) 解決方案影響。

With app protection policy, you can limit access to client applications that have reported to Azure AD has having received [Intune app protection policies](https://docs.microsoft.com/intune/app-protection-policy). For example, you can restrict access to Exchange Online to the Outlook app that has an Intune app protection policy. A Conditional Access policy that requires app protection policy is also known as [app protection-based Conditional Access policy](app-protection-based-conditional-access.md). 

Your device must be registered to Azure AD before an application can be marked as policy protected.

For a list of supported policy protected client apps, see [app protection policy requirement](technical-reference.md#app-protection-policy-requirement).

### <a name="terms-of-use"></a>使用規定

您可以要求租用戶中的使用者先同意使用條款，再授與他們存取資源的權利。 身為系統管理員，您可以上傳 PDF 文件來設定及自訂使用條款。 使用者如果落在此控制項的控制範圍內，就必須同意使用條款才會獲得應用程式的存取權。

## <a name="custom-controls-preview"></a>自訂控制項 (預覽)

自訂控制項是 Azure Active Directory Premium P1 版本的一項功能。 使用自訂控制項時，系統會將使用者重新導向到相容的服務，以滿足 Azure Active Directory 之外的進一步需求。 為了滿足此控制項，系統會將使用者的瀏覽器重新導向至外部服務，執行任何必要的驗證或確認活動，再將瀏覽器重新導向回到 Azure Active Directory。 Azure Active Directory verifies the response and, if the user was successfully authenticated or validated, the user continues in the Conditional Access flow.

These controls allow the use of certain external or custom services as Conditional Access controls, and generally extend the capabilities of Conditional Access.

目前提供相容服務的提供者包括：

- [Duo Security](https://duo.com/docs/azure-ca)
- [委託 Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping 身分識別](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

如需這些服務的詳細資訊，請直接連絡相關提供者。

### <a name="creating-custom-controls"></a>建立自訂控制項

若要建立自訂控制項，請先連絡您想要使用的提供者。 Each non-Microsoft provider has its own process and requirements to sign up, subscribe, or otherwise become a part of the service, and to indicate that you wish to integrate with Conditional Access. 屆時，提供者會提供您 JSON 格式的資料區塊。 This data allows the provider and Conditional Access to work together for your tenant, creates the new control and defines how Conditional Access can tell if your users have successfully performed verification with the provider.

Custom controls cannot be used with Identity Protection's automation requiring multi-factor authentication or to elevate roles in Privileged Identity Manager (PIM).

複製 JSON 資料並貼到相關文字方塊中。 除非您明確地了解您要進行的變更，否則請勿變更 JSON。 進行變更可能會讓提供者與 Microsoft 之間的連線中斷，進而可能將您的帳戶鎖定，讓您和您的使用者無法使用。

The option to create a custom control is in the **Manage** section of the **Conditional Access** page.

![控制](./media/controls/82.png)

按一下 [新增自訂控制項] 隨即會開啟刀鋒視窗，裡面有文字方塊可供輸入控制項的 JSON 資料。  

![控制](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>刪除自訂控制項

To delete a custom control, you must first ensure that it isn’t being used in any Conditional Access policy. 完成之後：

1. 移至 [自訂控制項] 清單
1. 按一下 [...]  
1. 選取 [刪除]。

### <a name="editing-custom-controls"></a>編輯自訂控制項

若要編輯自訂控制項，您必須先刪除目前的控制項，然後使用更新後的資訊建立新的控制項。

## <a name="session-controls"></a>工作階段控制項

工作階段控制項可讓您限制雲端應用程式內的體驗。 工作階段控制項是由雲端應用程式強制執行，並依賴 Azure AD 對應用程式提供有關工作階段的其他資訊。

![控制](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>使用應用程式強制執行限制

您可以使用這個控制項，要求 Azure AD 將裝置資訊傳遞至所選的雲端應用程式。 裝置資訊可讓雲端應用程式知道連線是否從符合規範或已加入網域的裝置起始。 此控制項僅支援 SharePoint Online 和 Exchange Online 作為選取的雲端應用程式。 選取後，雲端應用程式會使用裝置資訊來提供使用者有限或完整的體驗 (視裝置狀態而定)。

若要深入了解，請參閱：

- [啟用 SharePoint Online 的有限存取](https://aka.ms/spolimitedaccessdocs)
- [啟用 Exchange Online 的有限存取](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>後續步驟

- 如果您想要知道如何設定條件式存取原則，請參閱[利用 Azure Active Directory 條件式存取來取得特定應用程式的 MFA](app-based-mfa.md)。
- 如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](best-practices.md)。
