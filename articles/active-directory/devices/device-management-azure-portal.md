---
title: 如何使用 Azure 入口網站來管理裝置 | Microsoft Docs
description: 了解如何使用 Azure 入口網站來管理裝置。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c8e94a1b15ad8cd55019f9351c82f58130e472d
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420615"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Manage device identities using the Azure portal

With device identity management in Azure Active Directory (Azure AD), you can ensure that your users are accessing your resources from devices that meet your standards for security and compliance.

本文：

- Assumes that you are familiar with the [introduction to device identity management in Azure Active Directory](overview.md)
- Provides you with information about managing your device identities using the Azure AD portal

## <a name="manage-device-identities"></a>管理裝置識別

The Azure AD portal provides you with a central place to manage your device identities. 您可以使用[直接連結](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)或依照下列手動步驟，來前往該位置：

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. Search for and select **Azure Active Directory** or select it from the **Home** page.

3. 在 [管理] 區段中，按一下 [裝置]。

   ![設定裝置設定](./media/device-management-azure-portal/74.png)

[裝置] 頁面可讓您：

- 設定您的裝置設定
- 尋找裝置
- Perform device identity management tasks
- Review device-related audit logs  
  
## <a name="configure-device-settings"></a>設定裝置設定

To manage your device identities using the Azure AD portal, your devices need to be either [registered or joined](overview.md) to Azure AD. 身為系統管理員，您可以透過設定裝置設定，來微調註冊和加入裝置的程序。

![設定裝置設定](./media/device-management-azure-portal/22.png)

[裝置設定] 頁面可讓您設定：

![管理 Intune 裝置](./media/device-management-azure-portal/21.png)

- **Users may join devices to Azure AD** - This setting enables you to select the users who can register their devices as Azure AD joined devices. 預設值是 [全部]。

> [!NOTE]
> **Users may join devices to Azure AD** setting is only applicable to Azure AD join on Windows 10.

- **加入 Azure AD 的裝置上其他本機系統管理員** - 您可以選取哪些使用者會授與裝置的本機系統管理員權限。 新增至此處的使用者將會新增至 Azure AD 中的「裝置系統管理員」角色。 Azure AD 中的全域管理員和裝置擁有者預設會授與本機系統管理員權限。 此選項是可透過 Azure AD Premium 或 Enterprise Mobility Suite (EMS) 等產品使用的進階編輯功能。
- **Users may register their devices with Azure AD** - You need to configure this setting to allow Windows 10 personal, iOS, Android, and macOs devices to be registered with Azure AD. If you select **None**, devices are not allowed to register with Azure AD. 需要先註冊 (registration)，才可註冊 (enrollment) Microsoft Intune 或適用於 Office 365 的行動裝置管理 (MDM)。 如果您已設定任一服務，則會選取 [全部] 且無法使用 [無]。
- **Require Multi-Factor Auth to join devices** - You can choose whether users are required to provide an additional authentication factor to join their device to Azure AD. 預設值為 [ **否**]。 建議在註冊裝置時要求 Multi-Factor Authentication。 啟用此服務的 Multi-Factor Authentication 之前，您必須確定已為註冊其裝置的使用者設定 Multi-Factor Authentication。 如需不同 Azure Multi-Factor Authentication 服務的詳細資訊，請參閱[開始使用 Azure Multi-Factor Authentication](../authentication/concept-mfa-whichversion.md)。 

> [!NOTE]
> **Require Multi-Factor Auth to join devices** setting applies to devices that are either Azure AD joined or Azure AD registered. This setting does not apply to hybrid Azure AD joined devices.

- **Maximum number of devices** - This setting enables you to select the maximum number of Azure AD joined or Azure AD registered devices that a user can have in Azure AD. 如果使用者達到此配額，則在移除一或多個現有的裝置之前，將無法新增其他裝置。 預設值為 **20**。

> [!NOTE]
> **Maximum number of devices** setting applies to devices that are either Azure AD joined or Azure AD registered. This setting does not apply to hybrid Azure AD joined devices.

- **使用者可以在裝置間同步設定及應用程式資料** - 根據預設，這項設定會設定為 [無]。 選取特定使用者或群組，或是選取 [全部]，以便在使用者的 Windows 10 裝置間同步其設定及應用程式資料。 深入了解同步在 Windows 10 中的運作方式。
此選項是可透過 Azure AD Premium 或 Enterprise Mobility Suite (EMS) 等產品使用的進階功能。

## <a name="locate-devices"></a>尋找裝置

您有兩個選項可用來尋找已註冊及已加入的裝置：

- [裝置] 頁面之 [管理] 區段中的 [所有裝置]  

   ![所有裝置](./media/device-management-azure-portal/41.png)

- [使用者] 頁面之 [管理] 區段中的 [裝置]

   ![所有裝置](./media/device-management-azure-portal/43.png)

您可以透過這兩個選項移至檢視，該檢視：

- Enables you to search for devices using the display name or device ID as filter.
- 為您提供已註冊和已加入裝置的詳細概觀
- 可讓您執行一般裝置管理工作

![所有裝置](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* If you see a device that is "Hybrid Azure AD joined" with a state "Pending" under the REGISTERED column, it indicates that the device has been synchronized from Azure AD connect and is waiting to complete registration from the client. Read more on how to [plan your Hybrid Azure AD join implementation](hybrid-azuread-join-plan.md). Additional information can be found in the article, [Devices frequently asked questions](faq.md).
>
>   ![Pending devices](./media/device-management-azure-portal/75.png)
>
>* 就某些 iOS 裝置而言，包含單引號的裝置名稱可能會使用看起來像單引號的不同字元。 So searching for such devices is a little tricky - if you are not seeing search results correctly, ensure that the search string contains matching apostrophe character.

## <a name="device-identity-management-tasks"></a>Device identity management tasks

As a global administrator or cloud device administrator, you can manage the registered or joined devices. Intune 服務管理員可以：

- 更新裝置 – 範例為啟用/停用裝置等每日作業
- 刪除裝置 – 當裝置已淘汰且應在 Azure AD 中刪除時

This section provides you with information about common device identity management tasks.

### <a name="manage-an-intune-device"></a>管理 Intune 裝置

如果您是 Intune 管理員，您可以管理標示為 **Microsoft Intune** 的裝置。 If the device is not enrolled with Microsoft Intune the "Manage" option will be greyed out.

![管理 Intune 裝置](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>啟用/停用 Azure AD 裝置

若要啟用/停用裝置，您有兩個選項：

- [所有裝置] 頁面上的 [工作] 功能表 ("...")

   ![管理 Intune 裝置](./media/device-management-azure-portal/71.png)

- [裝置] 頁面上的工具列

   ![管理 Intune 裝置](./media/device-management-azure-portal/32.png)

**備註：**

- You need to be a global administrator or cloud device administrator in Azure AD to enable / disable a device. 
- Disabling a device prevents a device from successfully authenticating with Azure AD, thereby preventing the device from accessing your Azure AD resources that are guarded by device CA or using your WH4B credentials.
- Disabling the device will revoke both the Primary Refresh Token (PRT) and any Refresh Tokens (RT) on the device.

### <a name="delete-an-azure-ad-device"></a>刪除 Azure AD 裝置

若要刪除裝置，您有兩個選項：

- [所有裝置] 頁面上的 [工作] 功能表 ("...")

   ![管理 Intune 裝置](./media/device-management-azure-portal/72.png)

- [裝置] 頁面上的工具列

   ![刪除裝置](./media/device-management-azure-portal/34.png)

**備註：**

- 您必須是 Azure AD 中的全域管理員或 Intune 系統管理員，才能刪除裝置。
- 刪除裝置：
   - 防止裝置存取您的 Azure AD 資源。
   - 移除所有附加至裝置的詳細資料，例如適用於 Windows 裝置的 BitLocker 金鑰。  
   - 代表無法復原的活動，除非必要，否則不建議使用。

If a device is managed by another management authority (for example, Microsoft Intune), make sure that the device has been wiped / retired before deleting the device in Azure AD. Review how to [manage stale devices](device-management-azure-portal.md) before deleting any devices.

### <a name="view-or-copy-device-id"></a>檢視或複製裝置識別碼

您可以使用裝置識別碼來確認裝置上的裝置識別碼詳細資料，或在疑難排解期間使用 PowerShell。 若要存取複製選項，請按一下裝置。

![檢視裝置識別碼](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>檢視或複製 BitLocker 金鑰

您可以檢視和複製 BitLocker 金鑰，以協助使用者復原他們所加密的磁碟機。 這些金鑰只適用於已加密並將其金鑰儲存在 Azure AD 中的 Windows 裝置。 您可以在存取裝置的詳細資料時複製這些金鑰。

![檢視 BitLocker 金鑰](./media/device-management-azure-portal/36.png)

若要檢視或複製 BitLocker 金鑰，您必須是裝置的擁有者，或是至少已獲指派下列其中一個角色的使用者：

- 雲端裝置管理員
- 全域管理員
- 服務台系統管理員
- Intune 服務管理員
- 安全性系統管理員
- 安全性讀取者

> [!NOTE]
> 已聯結混合式 Azure AD 的 Windows 10 裝置沒有擁有者。 因此，如果您依擁有者尋找裝置卻未找到，請依裝置識別碼來搜尋。

## <a name="audit-logs"></a>稽核記錄

您可以透過活動記錄來取得裝置活動。 These logs include activities triggered by the device registration service and by users:

- 建立裝置，以及在裝置上新增擁有者/使用者
- 變更裝置設定
- 刪除或更新裝置等裝置作業

稽核資料的進入點是 [裝置] 頁面之 [活動] 區段中的 [稽核記錄]。

![稽核記錄](./media/device-management-azure-portal/61.png)

稽核記錄的預設清單檢視顯示︰

- 發生時間與日期
- 目標
- 活動的啟動者/執行者 (對象)
- 活動 (項目)

![稽核記錄](./media/device-management-azure-portal/63.png)

您可以按一下工具列中的 [資料行] 來自訂清單檢視。

![稽核記錄](./media/device-management-azure-portal/64.png)

若要將報告的資料縮小至您適用的層級，您可以使用下列欄位篩選稽核資料︰

- 類別
- 活動資源類型
- 活動
- 日期範圍
- 確定目標
- 啟動者 (執行者)

除了篩選，您還可以搜尋特定項目。

![稽核記錄](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>後續步驟

[How to manage stale devices in Azure AD](manage-stale-devices.md)
