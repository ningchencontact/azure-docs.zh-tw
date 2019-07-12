---
title: 設定 Azure Active Directory 無密碼登 （預覽）
description: 啟用無密碼登入 Azure AD 使用 FIDO2 安全性金鑰或 Microsoft Authenticator 應用程式 （預覽）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ba2545467ebfbd032408aeee25b82b92a628f2a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712068"
---
# <a name="enable-passwordless-sign-in-for-azure-ad-preview"></a>啟用無密碼登入 Azure ad （預覽）

## <a name="requirements"></a>需求

* Azure Multi-Factor Authentication
* 結合註冊預覽
* FIDO2 安全性金鑰預覽需要相容的 FIDO2 安全性金鑰
* WebAuthN 需要 Windows 10 版本 1809年或更高版本上的 Microsoft Edge
* FIDO2 型的 Windows 登入需要 Azure AD 已加入 Windows 10 版本 1809年或更高版本

## <a name="prepare-devices-for-preview"></a>準備裝置以進行預覽

1809 或更高版本，您將使用試驗的裝置必須執行 Windows 10 版本。 在 Windows 10 版本 1903年或更高版本上，是最佳的體驗。

## <a name="enable-security-keys-for-windows-sign-in"></a>啟用 Windows 登入的安全性金鑰

組織可以選擇使用其中一個或多個下列方法來啟用 Windows 的安全性金鑰的登入。

### <a name="enable-credential-provider-via-intune"></a>啟用透過 Intune 認證提供者

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至**Microsoft Intune** > **裝置註冊** > **Windows 註冊** > **Windows Hello商務** > **屬性**。
1. 底下**設定**設定**使用的登入的安全性金鑰**來**已啟用**。

組態的安全性金鑰進行登入，不是取決於設定 Windows hello 企業版。

#### <a name="enable-targeted-intune-deployment"></a>啟用有目標的 Intune 部署

若要啟用認證提供者的特定裝置群組為目標，使用下列自訂透過 Intune 設定。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至**Microsoft Intune** > **裝置組態** > **設定檔** > **建立設定檔**.
1. 使用下列設定來設定新的設定檔
   1. 名稱：Windows 登入的安全性金鑰
   1. Description:讓 Windows 登入期間所要使用 FIDO 安全性金鑰
   1. 平台：Windows 10 及更新版本
   1. 平台類型：自訂
   1. 自訂 OMA-URI 設定：
      1. 名稱：FIDO 安全性金鑰開啟的 Windows 登入
      1. OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. 資料類型：整數
      1. 值：1 
1. 此原則可以指派給特定使用者、 裝置或群組中。 文件中，可以找到更多資訊[指派使用者和裝置的設定檔，在 Microsoft Intune 中](https://docs.microsoft.com/intune/device-profile-assign)。

![建立 Intune 自訂裝置設定原則](./media/howto-authentication-passwordless-enable/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>啟用透過佈建套件的認證提供者

對於不受 Intune 管理的裝置，可安裝佈建套件啟用此功能。 您可以從安裝 Windows Configuration Designer 應用程式[Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22)。

1. 啟動 Windows 設定設計工具。
1. 選取 **檔案** > **新專案**。
1. 為您的專案命名並記下的路徑建立您的專案的位置。
1. 選取 [下一步]  。
1. 離開**佈建套件**選取作為**已選取專案的工作流程**，然後選取**下一步**。
1. 選取 [**所有的 Windows 桌面版本**下方**選擇要檢視和設定的設定**，然後選取**下一步]** 。
1. 選取 [完成]  。
1. 在您新建立的專案中，瀏覽至**執行階段設定** > **WindowsHelloForBusiness** > **SecurityKeys**  > **UseSecurityKeyForSignIn**。
1. 設定**UseSecurityKeyForSignIn**要**啟用**。
1. 選取 **匯出** > **佈建套件**
1. 保留中的預設值**建置**下方視窗**描述佈建套件**，然後選取**下一步**。
1. 保留中的預設值**建置**下方視窗**選取 [佈建套件的安全性詳細資料**，然後選取**下一步]** 。
1. 記下或變更中的路徑**建置**下方的 windows**選取儲存佈建套件位置**，然後選取**下一步**。
1. 選取 **建置**上**建置佈建套件**頁面。
1. 將建立兩個檔案 （ppkg 和 cat） 儲存，您可以套用至電腦之後的位置。
1. 請遵循本文中的指導方針[套用佈建套件](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)以套用您所建立的佈建套件。

## <a name="obtain-fido2-security-keys"></a>取得 FIDO2 安全性金鑰

請參閱本文的區段 FIDO2 安全性金鑰[什麼是無密碼？](concept-authentication-passwordless.md)如需有關支援的索引鍵和製造商。

> [!NOTE]
> 如果您購買，並計劃使用 NFC 基礎安全性金鑰必須支援的 NFC 讀取器。

## <a name="enable-passwordless-authentication-methods"></a>啟用無密碼驗證方法

### <a name="enable-the-combined-registration-experience"></a>可讓您合併的註冊體驗

FIDO2 安全性金鑰的註冊功能依賴結合的註冊預覽。 請遵循下列步驟來啟用合併的註冊預覽。

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 瀏覽至**Azure Active Directory** > **使用者設定**
   1. 按一下**管理設定的存取面板的預覽功能**
   1. 底下**使用者可以使用預覽功能註冊和管理安全性資訊-強化**。
      1. 選擇**Selected** ，然後選擇 在預覽中的 將參與的使用者群組。
      1. 或選擇**所有**以啟用您的目錄中的每個人。
1. 按一下 [儲存] 

### <a name="enable-new-passwordless-authentication-methods"></a>啟用新的無密碼驗證方法

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 瀏覽至**Azure Active Directory** > **驗證方法** > **驗證方法原則 （預覽）**
1. 在每個**方法**，選擇下列選項
   1. **啟用**-Yes 或 No
   1. **目標**-所有使用者或選取使用者
1. **儲存**每一種方法

> [!WARNING]
> 「 金鑰限制原則 」 無法運作尚未 FIDO2。 這項功能會提供正式運作之前，請不要變更這些原則從預設值。

> [!NOTE]
> 您不需要選擇加入這兩個無密碼的方法 （如果您想要預覽只能有一個無密碼的方法，您可以啟用只有該方法）。 我們鼓勵您試用這兩種方法，因為它們都有自己的優點。

## <a name="user-registration-and-management-of-fido2-security-keys"></a>使用者註冊和管理 FIDO2 安全性金鑰

1. 瀏覽至 [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. 在沒有已登入
1. 按一下 **安全性資訊**
   1. 如果使用者已經有至少一個已註冊的 Azure Multi-factor Authentication 方法，他們可以立即註冊 FIDO2 安全性金鑰。
   1. 如果沒有至少一個已註冊的 Azure Multi-factor Authentication 方法，它們必須新增一個。
1. 按一下 新增 FIDO2 安全性金鑰**將方法加入**，然後選擇**安全性金鑰**
1. 選擇**USB 裝置**或**NFC 裝置**
1. 擁有金鑰做好準備，然後選擇 [**下一步]**
1. 方塊將會出現並要求您建立或輸入 PIN 的安全性金鑰，然後針對您的金鑰執行必要的動作時，也將是生物識別技術或觸控。
1. 您將會傳回給合併的註冊體驗，要求您提供有意義的名稱，為您的權杖，因此如果您有多個識別哪一個。 按一下 [下一步]  。
1. 按一下 **完成**完成程序

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>管理安全性金鑰生物特徵辨識、 PIN，或重設的安全性金鑰

* Windows 10 版本 1809
   * 則需要安全性金鑰廠商所提供的隨附軟體
* Windows 10 版本 1903 或更高版本
   * 使用者可以開啟**Windows 設定**在其裝置上 >**帳戶** > **安全性金鑰**
   * 使用者可以變更 PIN、 更新生物識別技術，或重設他們的安全性金鑰

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>使用者註冊和管理的 Microsoft Authenticator 應用程式

若要設定手機登入的 Microsoft Authenticator 應用程式，請依照下列文章中的指導方針[給您使用 Microsoft Authenticator 應用程式的帳戶登入](../user-help/user-help-auth-app-sign-in.md)。

## <a name="sign-in-with-passwordless-credentials"></a>使用無密碼的認證登入

### <a name="sign-in-at-the-lock-screen"></a>在鎖定畫面上登入

在下面的使用者範例 Bala Sandhu 具有已佈建其 FIDO2 安全性金鑰。 Bala 可以選擇從 Windows 10 的鎖定畫面的安全性金鑰的認證提供者，並插入安全性金鑰，來登入 Windows。

![在 Windows 10 鎖定畫面上登入的安全性金鑰](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>在網站上登入

在範例中，下列使用者已佈建其 FIDO2 安全性金鑰。 使用者可以選擇使用其 FIDO2 安全性金鑰，在 Windows 10 版本 1809年或更高版本的 Microsoft Edge 瀏覽器登入在網站上。

![Microsoft Edge 中的安全性金鑰簽署](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-edge-sign-in.png)

如需有關使用 Microsoft Authenticator 應用程式登入資訊，請參閱文章[給您使用 Microsoft Authenticator 應用程式的帳戶登入](../user-help/user-help-auth-app-sign-in.md)。

## <a name="known-issues"></a>已知問題

### <a name="fido2-security-keys"></a>FIDO2 安全性金鑰

#### <a name="security-key-provisioning"></a>安全性金鑰佈建

佈建和取消佈建的安全性金鑰的系統管理員不提供公開預覽。

#### <a name="hybrid-azure-ad-join"></a>混合式 Azure AD Join

依賴 WIA SSO 的使用者使用受管理的認證會喜歡 FIDO2 安全性金鑰，或使用 Microsoft Authenticator 應用程式的無密碼登入需要混合式加入 Windows 10 上取得 SSO 的優點。 不過，安全性金鑰僅適用於 Azure Active Directory 已加入電腦，現在。 我們建議您僅試用 FIDO2 Windows 鎖定畫面上純 Azure Active Directory 已加入電腦的安全性金鑰。 這項限制不適用於 web。

#### <a name="upn-changes"></a>UPN 變更

我們正努力支援可讓混合式 AADJ UPN 變更與 AADJ 裝置的功能。 如果使用者的 UPN 變更，您也無法再修改 FIDO2 至該帳戶的安全性金鑰。 因此的唯一方法是將裝置重設，而且使用者必須重新註冊。

### <a name="authenticator-app"></a>驗證器應用程式

#### <a name="ad-fs-integration"></a>AD FS 整合

當使用者已啟用 Microsoft Authenticator 無密碼認證時，該使用者的驗證將一律預設為傳送通知以供核准。 此邏輯可防止在混合式租用戶中的使用者導向至 ADFS 的登入驗證而不需要採取額外的步驟，使用者按一下 「 改為： 使用您的密碼。 」 此程序也會略過任何內部部署條件式存取原則和傳遞驗證流程。 此程序的例外狀況是 login_hint 是否指定，使用者將會自動轉寄至 AD FS，並略過使用無認證的選項。

#### <a name="azure-mfa-server"></a>Azure MFA Server

透過組織的內部部署 Azure MFA server 啟用 MFA 的使用者仍然可以建立和使用認證中的單一無密碼的電話登。 如果使用者嘗試使用認證升級 Microsoft Authenticator 的多個安裝 (5+)，這項變更可能會導致錯誤。  

#### <a name="device-registration"></a>裝置註冊

建立這個新的強式認證的其中一個必要條件，就是它所在的裝置已在 Azure AD 租用戶內對個別使用者註冊。 由於裝置註冊限制，一個裝置只能在單一租用戶中註冊。 此限制表示，可以啟用電話登入的 Microsoft Authenticator 應用程式中的只有一個工作或學校帳戶。

## <a name="next-steps"></a>後續步驟

[深入了解裝置註冊](../devices/overview.md)

[深入了解 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
