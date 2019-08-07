---
title: 啟用 Azure AD 的無密碼安全性金鑰登入 (預覽)-Azure Active Directory
description: 啟用無密碼安全性金鑰使用 FIDO2 安全性金鑰登入 Azure AD (預覽)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: a063891339a46366490447b7c7a7a1a14fd81be6
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828913"
---
# <a name="enable-passwordless-security-key-sign-in-for-azure-ad-preview"></a>啟用 Azure AD 的無密碼安全性金鑰登入 (預覽)

## <a name="requirements"></a>需求

* Azure Multi-Factor Authentication
* 結合的註冊預覽與已啟用 SSPR 的使用者
* FIDO2 安全性金鑰預覽需要相容的 FIDO2 安全性金鑰
* WebAuthN 需要 Windows 10 1809 版或更高版本上的 Microsoft Edge
* 以 FIDO2 為基礎的 Windows 登入需要 Azure AD 聯結的 Windows 10 1809 版或更高版本

## <a name="prepare-devices-for-preview"></a>準備裝置以供預覽

您將試驗的裝置必須執行 Windows 10 1809 版或更高版本。 最佳體驗是在 Windows 10 1903 版或更新版本上。

## <a name="enable-security-keys-for-windows-sign-in"></a>啟用 Windows 登入的安全性金鑰

組織可能會選擇使用下列一或多個方法來啟用 Windows 登入的安全性金鑰使用。

### <a name="enable-credential-provider-via-intune"></a>透過 Intune 啟用認證提供者

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至 **[Microsoft Intune**  > **裝置註冊** > ] [**windows 註冊** > ] [windows**Hello 企業** > 版]**屬性**。
1. 在 [設定] 下, 將 [**使用安全性金鑰登入**] 設定為 [**已啟用**]。

設定登入的安全性金鑰, 並不依存于設定 Windows Hello 企業版。

#### <a name="enable-targeted-intune-deployment"></a>啟用目標 Intune 部署

若要以特定裝置群組為目標以啟用認證提供者, 請透過 Intune 使用下列自訂設定。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至**Microsoft Intune**  > **裝置配置** > 檔 > **建立設定檔**。
1. 使用下列設定來設定新的設定檔
   1. 名稱:Windows 登入的安全性金鑰
   1. 描述:啟用 FIDO 安全性金鑰以在 Windows 登入期間使用
   1. 平台:Windows 10 和更新版本
   1. 平臺類型:自訂
   1. 自訂 OMA URI 設定:
      1. 名稱:開啟 Windows 登入的 FIDO 安全性金鑰
      1. OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. 資料類型:整數
      1. 值:1 
1. 此原則可指派給特定的使用者、裝置或群組。 如需詳細資訊, 請參閱在[Microsoft Intune 中指派使用者和裝置設定檔](https://docs.microsoft.com/intune/device-profile-assign)一文。

![建立 Intune 自訂裝置設定原則](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>透過布建套件啟用認證提供者

對於不受 Intune 管理的裝置, 可以安裝布建套件來啟用此功能。 您可以從[Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22)安裝 Windows 設定設計工具應用程式。

1. 啟動 Windows 設定設計工具。
1. 選取 [檔案] [**新增專案**]。  > 
1. 為您的專案命名, 並記下您的專案建立所在的路徑。
1. 選取 [下一步]。
1. 將布建**套件**保留為**選取的專案工作流程**, 然後選取 **[下一步]** 。
1. 選取 **[選擇要查看和設定的設定**] 下的**所有 Windows 桌上出版本**, 然後選取 **[下一步]** 。
1. 選取 [完成]。
1. 在新建立的專案中, 流覽至 [**執行時間設定** > ] [**WindowsHelloForBusiness**  >  **SecurityKeys**  >  **UseSecurityKeyForSignIn**]。
1. 將 [ **UseSecurityKeyForSignIn** ] 設定為 [**已啟用**]。
1. 選取**匯出** > 布建**套件**
1. 在 [描述布建**套件**] 底下的 [**組建**] 視窗中保留預設值, 然後選取 **[下一步]**
1. 將預設值保留在 **組建** 視窗的 選取布建**套件的安全性詳細資料** 底下, 然後選取**下一步**
1. 在 [**選取要儲存**布建套件的位置] 底下, 記下或變更 [**組建**] 視窗中的路徑, 然後選取 **[下一步]** 。
1. 選取 [建立布建**套件**] 頁面上的 [**組建**]。
1. 將建立的兩個檔案 (ppkg 和 cat) 儲存到您稍後可以將它們套用至電腦的位置。
1. 遵循套用布建[套件一](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)文中的指導方針, 套用您所建立的布建套件。

## <a name="obtain-fido2-security-keys"></a>取得 FIDO2 安全性金鑰

如需有關支援的金鑰和製造商的詳細資訊, 請參閱 <<c0>什麼是無密碼？一節中的 < FIDO2 安全性金鑰。

> [!NOTE]
> 如果您購買並計畫使用 NFC 型安全性金鑰, 您將需要支援的 NFC 讀取器。

## <a name="enable-passwordless-authentication-method"></a>啟用無密碼 authentication 方法

### <a name="enable-the-combined-registration-experience"></a>啟用合併的註冊體驗

無密碼驗證方法的註冊功能依賴結合的註冊預覽。 遵循[啟用結合的安全性資訊註冊 (預覽)](howto-registration-mfa-sspr-combined.md)一文中的步驟, 以啟用合併的註冊預覽。

### <a name="enable-new-passwordless-authentication-method"></a>啟用新的無密碼驗證方法

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 流覽至**Azure Active Directory**  > **安全性** > **驗證**方法驗證 > **方法原則 (預覽)**
1. 在每個**方法**底下, 選擇下列選項
   1. **啟用**-是或否
   1. **目標**-所有使用者或選取使用者
1. **儲存**每個方法

> [!WARNING]
> FIDO2 「金鑰限制原則」尚未生效。 這項功能將在正式運作之前提供, 請不要變更這些原則的預設值。

> [!NOTE]
> 您不需要加入這兩個無密碼方法 (如果只想要預覽一個無密碼方法, 您只能啟用該方法)。 我們鼓勵您試用這兩種方法, 因為它們都有自己的優點。

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 安全性金鑰的使用者註冊與管理

1. 瀏覽至 [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. 登入 (如果尚未登入)
1. 按一下 [**安全性資訊**]
   1. 如果使用者已註冊至少一個 Azure 多因素驗證方法, 他們可以立即註冊 FIDO2 的安全性金鑰。
   1. 如果未註冊至少一個 Azure 多因素驗證方法, 則必須新增一個。
1. 按一下 [**新增方法**], 然後選擇 [**安全性金鑰**], 以新增 FIDO2 安全性金鑰
1. 選擇**USB 裝置**或**NFC 裝置**
1. 備妥您的金鑰, 然後選擇 **[下一步]**
1. 隨即會出現一個方塊, 並要求您建立/輸入您的安全性金鑰的 PIN, 然後針對您的金鑰執行必要的手勢來進行生物識別或觸控。
1. 您將會回到合併的註冊體驗, 並要求您為權杖提供有意義的名稱, 以便您可以在有多個時識別哪一個。 按一下 [下一步]。
1. 按一下 [**完成**] 以完成程式

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>管理安全性金鑰生物識別、PIN 或重設安全性金鑰

* Windows 10 版本1809
   * 需要安全性金鑰廠商隨附的軟體
* Windows 10 1903 版或更高版本
   * 使用者可以在其裝置上開啟**Windows 設定**>**帳戶** > **安全性金鑰**
   * 使用者可以變更其 PIN、更新生物識別, 或重設其安全性金鑰

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式的使用者註冊與管理

若要設定 Microsoft Authenticator 應用程式以進行手機登入, 請遵循[使用 Microsoft Authenticator 應用程式登入您的帳戶](../user-help/user-help-auth-app-sign-in.md)一文中的指導方針。

## <a name="sign-in-with-passwordless-credential"></a>使用無密碼認證登入

### <a name="sign-in-at-the-lock-screen"></a>在鎖定畫面登入

在下列範例中, 使用者 Bala Sandhu 已布建其 FIDO2 安全性金鑰。 Bala 可以從 Windows 10 鎖定畫面中選擇安全性金鑰認證提供者, 並插入安全性金鑰以登入 Windows。

![安全性金鑰登入 Windows 10 鎖定畫面](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>在 web 上登入

在下列範例中, 使用者已布建其 FIDO2 安全性金鑰。 使用者可以選擇使用 Windows 10 1809 版或更高版本上 Microsoft Edge 瀏覽器中的 FIDO2 安全性金鑰來登入 web。

![Microsoft Edge 中的安全性金鑰登入](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="known-issues"></a>已知問題

### <a name="security-key-provisioning"></a>安全性金鑰提供

公開預覽中不提供安全性金鑰的系統管理員布建和取消布建。

### <a name="hybrid-azure-ad-join"></a>加入混合式 Azure AD

依賴使用受控認證 (例如 FIDO2 安全性金鑰或無密碼登入) Microsoft Authenticator 應用程式的使用者, 必須要有 Windows 10 的混合式聯結, 才能獲得 SSO 的優勢。 不過, 安全性金鑰僅適用于目前已加入 Azure Active Directory 的電腦。 我們建議您只在已加入純 Azure Active Directory 的電腦上, 嘗試 FIDO2 Windows 鎖定畫面的安全性金鑰。 這項限制不適用於 web。

### <a name="upn-changes"></a>UPN 變更

我們正努力支援可在混合式 AADJ 和 AADJ 裝置上進行 UPN 變更的功能。 如果使用者的 UPN 變更, 您就無法再修改 FIDO2 安全性金鑰來說明該帳戶。 因此, 唯一的方法是重設裝置, 而使用者必須重新註冊。

## <a name="next-steps"></a>後續步驟

[深入了解裝置註冊](../devices/overview.md)

[深入了解 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
