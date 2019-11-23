---
title: Approved client apps with Conditional Access - Azure Active Directory
description: Learn how to require approved client apps for cloud app access with Conditional Access in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: c173d0e17166911e28fea3d1c5820879d17af4a8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381128"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>How To: Require approved client apps for cloud app access with Conditional Access 

您的員工使用行動裝置來處理個人和工作事務。 在維護員工的生產力時，您也希望能預防資料遺失。 With Azure Active Directory (Azure AD) Conditional Access, you can restrict access to your cloud apps to approved client apps that can protect your corporate data.  

本主題說明如何設定需要已核准用戶端應用程式的條件式存取原則。

## <a name="overview"></a>概觀

With [Azure AD Conditional Access](overview.md), you can fine-tune how authorized users can access your resources. 例如，您可以將雲端應用程式的存取限制為受信任的裝置。

您可以使用 [Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)來協助保護公司資料。 Intune 應用程式保護原則不需要行動裝置管理 (MDM) 解決方案，因此不論您是否在裝置管理解決方案中註冊裝置，都可以保護公司的資料。

Azure Active Directory Conditional Access enables you to limit access to your cloud apps to client apps that support Intune app protection policies. 例如，您可以限制唯有 Outlook 應用程式能存取 Exchange Online。

In the Conditional Access terminology, these client apps are known as **approved client apps**.  

![條件式存取](./media/app-based-conditional-access/05.png)

如需核准的用戶端應用程式清單，請參閱[核准的用戶端應用程式需求](technical-reference.md#approved-client-app-requirement)。

You can combine app-based Conditional Access policies with other policies such as [device-based Conditional Access policies](require-managed-devices.md) to provide flexibility in how to protect data for both personal and corporate devices.

## <a name="before-you-begin"></a>開始之前

本主題假設您已熟悉：

- [核准的用戶端應用程式需求](technical-reference.md#approved-client-app-requirement)技術參考。
- The basic concepts of [Conditional Access in Azure Active Directory](overview.md).
- How to [configure a Conditional Access policy](app-based-mfa.md).
- The [migration of Conditional Access policies](best-practices.md#policy-migration).

## <a name="prerequisites"></a>必要條件

To create an app-based Conditional Access policy, you must have an Enterprise Mobility + Security or an Azure Active Directory premium subscription, and the users must be licensed for EMS or Azure AD. 

## <a name="exchange-online-policy"></a>Exchange Online 原則 

This scenario consists of an app-based Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：

- 將使用 iOS 或 Android 原生郵件應用程式的電子郵件設定為連線到 Exchange
- 收到一封電子郵件，郵件顯示僅能使用 Outlook 應用程式存取
- 透過連結下載應用程式
- 開啟 Outlook 應用程式並使用 Azure AD 認證登入
- 系統提示安裝 Authenticator (iOS) 或公司入口網站 (Android) 以繼續進行
- 安裝應用程式，而且可以返回 Outlook 應用程式繼續進行
- 系統提示註冊裝置
- 能夠存取電子郵件

Any Intune app protection policies are activated at the time the access corporate data and may prompt the user to restart the application, use an additional PIN etc. (if configured for the application and platform).

### <a name="configuration"></a>組態 

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。
1. **條件：** [條件] 需要設定 [裝置平台] 和 [用戶端應用程式]：
   1. [裝置平台] 請選取 [Android] 和 [iOS]。
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式與傳統型應用程式] 和 [新式驗證用戶端]。
1. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。

   ![條件式存取](./media/app-based-conditional-access/05.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。
1. **條件：** [條件] 需要設定 [用戶端應用程式 (預覽)]。 
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [Exchange ActiveSync 用戶端]。
   1. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。

      ![條件式存取](./media/app-based-conditional-access/05.png)

**步驟 3 - 設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online 和 SharePoint Online 原則

This scenario consists of a Conditional Access with mobile app management policy for access to Exchange Online and SharePoint Online with approved apps.

### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：

- 嘗試使用 SharePoint 應用程式連接還有檢視其公司網站
- Attempts to sign in with the same credentials as the Outlook app credentials
- 不需要重新註冊，即可取得資源的存取權

### <a name="configuration"></a>組態

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 和 [Office 365 SharePoint Online] 做為雲端應用程式。 
1. **條件：** [條件] 需要設定 [裝置平台] 和 [用戶端應用程式]：
   1. [裝置平台] 請選取 [Android] 和 [iOS]。
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [新式驗證用戶端]。
1. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。

   ![條件式存取](./media/app-based-conditional-access/05.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。 線上 
1. **條件：** [條件] 需要設定 [用戶端應用程式]：
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [Exchange ActiveSync 用戶端]。
   1. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。

      ![條件式存取](./media/app-based-conditional-access/05.png)

**步驟 3 - 設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**

![條件式存取](./media/app-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>適用於 Exchange Online 和 SharePoint Online 的應用程式型或相容裝置原則

This scenario consists of an app-based or compliant device Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>情節腳本

此情節假設：
 
- Some users are already enrolled (with or without corporate devices)
- 未使用應用程式保護的應用程式向 Azure AD 申請和註冊的使用者需要註冊裝置，才能存取資源
- 已使用應用程式保護的應用程式完成申請的使用者不需要重新註冊裝置

### <a name="configuration"></a>組態

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 和 [Office 365 SharePoint Online] 做為雲端應用程式。 
1. **條件：** [條件] 需要設定 [裝置平台] 和 [用戶端應用程式]。 
   1. [裝置平台] 請選取 [Android] 和 [iOS]。
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [新式驗證用戶端]。
1. [存取控制項] 需要選取下列項目：
   - [裝置需要標記為合規]
   - [需要經過核准的用戶端應用程式 \(預覽\)\]
   - [需要其中一個選取的控制項]   
 
      ![條件式存取](./media/app-based-conditional-access/11.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。 
1. **條件：** [條件] 需要設定 [用戶端應用程式]。 
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [Exchange ActiveSync 用戶端]。
1. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。
 
   ![條件式存取](./media/app-based-conditional-access/11.png)

**步驟 3 - 設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**

![條件式存取](./media/app-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>適用於 Exchange Online 和 SharePoint Online 的應用程式型和相容裝置原則

This scenario consists of an app-based and compliant device Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：
 
- 將使用 iOS 或 Android 原生郵件應用程式的電子郵件設定為連線到 Exchange
- 接收指出存取權要求註冊您裝置的電子郵件
- 下載公司入口網站並登入公司入口網站
- 檢查電子郵件，並且被要求使用 Outlook 應用程式
- 下載 Outlook 應用程式
- 開啟 Outlook 應用程式，並輸入申請時所用的認證
- 使用者即可存取電子郵件

任何 Intune 應用程式防護原則都會在存取公司資料時啟動，而且可能會提示使用者重新啟動應用程式、使用其他 PIN 碼等 (若有針對應用程式和平台設定)。

### <a name="configuration"></a>組態

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 和 [Office 365 SharePoint Online] 做為雲端應用程式。 
1. **條件：** [條件] 需要設定 [裝置平台] 和 [用戶端應用程式]。 
   1. [裝置平台] 請選取 [Android] 和 [iOS]。
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式與傳統型應用程式] 和 [新式驗證用戶端]。
1. [存取控制項] 需要選取下列項目：
   - [裝置需要標記為合規]
   - [需要經過核准的用戶端應用程式 \(預覽\)\]
   - [需要所有選取的控制項]   
 
      ![條件式存取](./media/app-based-conditional-access/13.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。 
1. **條件：** [條件] 需要設定 [用戶端應用程式 (預覽)]。 
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [Exchange ActiveSync 用戶端]。

   ![條件式存取](./media/app-based-conditional-access/92.png)

1. [存取控制項] 需要選取下列項目：
   - [裝置需要標記為合規]
   - [需要經過核准的用戶端應用程式 \(預覽\)\]
   - [需要所有選取的控制項]   

**步驟 3 - 設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**

![條件式存取](./media/app-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="next-steps"></a>後續步驟

如果您想要知道如何設定條件式存取原則，請參閱[利用 Azure Active Directory 條件式存取來取得特定應用程式的 MFA](app-based-mfa.md)。

如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](best-practices.md)。 
