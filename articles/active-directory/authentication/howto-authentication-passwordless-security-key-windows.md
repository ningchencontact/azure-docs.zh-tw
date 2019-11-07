---
title: 啟用 Azure AD 的無密碼安全性金鑰登入（預覽）-Azure Active Directory
description: 使用 FIDO2 安全性金鑰（預覽）啟用無密碼安全性金鑰登入 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b57fe9241a6a29e6f5ce12b7a1412455df4a001
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603482"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>啟用無密碼安全性金鑰登入 Windows 10 裝置（預覽）

本檔著重于啟用以 Windows 10 裝置為基礎的 FIDO2 安全性金鑰型無密碼驗證。 在本文結尾，您將能夠使用 FIDO2 安全性金鑰，以 Azure AD 帳戶登入 web 應用程式和您的 Azure AD 加入的 Windows 10 裝置。

|     |
| --- |
| FIDO2 安全性金鑰是 Azure Active Directory 的公開預覽功能。 如需預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>要求

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [結合的安全性資訊註冊預覽](concept-registration-mfa-sspr-combined.md)
- 相容的[FIDO2 安全性金鑰](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN 需要 Windows 10 1809 版或更高版本
- 已[加入 Azure AD 的裝置](../devices/concept-azure-ad-join.md)需要 Windows 10 1809 版或更高版本
- [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) （選擇性）
- 布建套件（選擇性）

### <a name="unsupported-scenarios"></a>不支援的情節

- Windows Server Active Directory Domain Services （AD DS）已加入網域（僅限內部部署裝置）部署**不受支援**。
- **不支援**使用安全性金鑰的 RDP、VDI 和 Citrix 案例。
- **不支援**使用安全性金鑰進行 S/MIME。
- 使用安全性金鑰**不支援**「執行身分」。
- **不支援**使用安全性金鑰登入伺服器。
- 如果您在線上時未使用您的安全性金鑰來登入您的裝置，將無法使用它來離線登入或解除鎖定。

## <a name="prepare-devices-for-preview"></a>準備裝置以供預覽

您要試驗的 Azure AD 聯結裝置必須執行 Windows 10 1809 版或更高版本。 最佳體驗是在 Windows 10 1903 版或更新版本上。

## <a name="enable-security-keys-for-windows-sign-in"></a>啟用 Windows 登入的安全性金鑰

組織可以選擇使用下列一或多種方法，根據組織的需求，使用安全性金鑰來進行 Windows 登入。

- [使用 Intune 啟用](#enable-with-intune)
   - [目標 Intune 部署](#targeted-intune-deployment)
- [使用布建套件啟用](#enable-with-a-provisioning-package)

### <a name="enable-with-intune"></a>使用 Intune 啟用

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至**Microsoft Intune** > **裝置註冊** > Windows**註冊** > **windows Hello 企業版** > **屬性**。
1. 在 **[設定] 下，** 將 [**使用安全性金鑰登入**] 設定為 [**已啟用**]。

設定用於登入的安全性金鑰，並不依存于設定 Windows Hello 企業版。

#### <a name="targeted-intune-deployment"></a>目標 Intune 部署

若要以特定裝置群組為目標以啟用認證提供者，請透過 Intune 使用下列自訂設定。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至**Microsoft Intune** > 的**裝置**設定 > **配置**檔 > **建立設定檔**。
1. 使用下列設定來設定新的設定檔
   1. 名稱： Windows 登入的安全性金鑰
   1. 描述：啟用在 Windows 登入期間使用的 FIDO 安全性金鑰
   1. 平臺： Windows 10 和更新版本
   1. 配置檔案類型：自訂
   1. 自訂 OMA URI 設定：
      1. 名稱：開啟 Windows 登入的 FIDO 安全性金鑰
      1. OMA-URI：./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. 資料類型：整數
      1. 值：1
1. 此原則可指派給特定的使用者、裝置或群組。 如需詳細資訊，請參閱在[Microsoft Intune 中指派使用者和裝置設定檔](https://docs.microsoft.com/intune/device-profile-assign)一文。

![建立 Intune 自訂裝置設定原則](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>使用布建套件啟用

對於不受 Intune 管理的裝置，可以安裝布建套件來啟用此功能。 您可以從[Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22)安裝 Windows 設定設計工具應用程式。

1. 啟動 Windows 設定設計工具。
1. 選取 **[** 檔案] > [**新增專案**]。
1. 為您的專案命名，並記下您的專案建立所在的路徑。
1. 選取 [下一步]。
1. 將布建**套件**保留為**選取的專案工作流程**，然後選取 **[下一步]** 。
1. 選取 **[選擇要查看和設定的設定**] 下的**所有 Windows 桌上出版本**，然後選取 **[下一步]** 。
1. 選取 [完成]。
1. 在新建立的專案中，流覽至 **執行時間設定** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**。
1. 將 [ **UseSecurityKeyForSignIn** ] 設定為 [**已啟用**]。
1. 選取**匯出** > 布建**套件**
1. 在 [描述布建**套件**] 底下的 [**組建**] 視窗中保留預設值，然後選取 **[下一步]**
1. 將預設值保留在 **組建** 視窗的 選取布建**套件的安全性詳細資料** 底下，然後選取**下一步**
1. 在 [**選取要儲存**布建套件的位置] 底下，記下或變更 [**組建**] 視窗中的路徑，然後選取 **[下一步]** 。
1. 選取 [建立布建**套件**] 頁面上的 [**組建**]。
1. 將建立的兩個檔案（ppkg 和 cat）儲存到您稍後可以將它們套用至電腦的位置。
1. 遵循套用布建[套件一](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)文中的指導方針，套用您所建立的布建套件。

> [!NOTE]
> 執行 Windows 10 1809 版的裝置也必須啟用共用電腦模式（EnableSharedPCMode）。 如需啟用此功能的相關資訊，請參閱[使用 Windows 10 設定共用或來賓電腦](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc)一文。

## <a name="sign-in-to-windows-with-a-fido2-security-key"></a>使用 FIDO2 安全性金鑰登入 Windows

在下列範例中，使用者 Bala Sandhu 已使用上一篇文章中的步驟來布建其 FIDO2 安全性金鑰，請[啟用無密碼安全性金鑰登入](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)。 Bala 可以從 Windows 10 鎖定畫面中選擇安全性金鑰認證提供者，並插入安全性金鑰以登入 Windows。

![安全性金鑰登入 Windows 10 鎖定畫面](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>管理安全性金鑰生物識別、PIN 或重設安全性金鑰

* Windows 10 1903 版或更高版本
   * 使用者可以在其裝置上開啟**Windows 設定**>**帳戶** > **安全性金鑰**
   * 使用者可以變更其 PIN、更新生物識別，或重設其安全性金鑰

## <a name="troubleshooting-and-feedback"></a>疑難排解與意見反應

如果您想要在預覽這項功能時分享意見反應或遇到問題，請透過 Windows 意見反應中樞應用程式共用。

1. 啟動**意見反應中樞**，並確認您已登入。
1. 在下列分類下提交意見反應：
   1. 類別：安全性和隱私權
   1. 子類別目錄： FIDO
1. 若要捕獲記錄，請使用 [**重新建立我的問題**] 選項。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-this-work-in-my-on-premises-environment"></a>這會在我的內部部署環境中運作嗎？

這項功能不適用於純粹的內部部署 Active Directory Domain Services （AD DS）環境。

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>我的組織需要雙因素驗證才能存取資源，我該怎麼做才能支援這項需求？

安全性金鑰有各種形式的規格。 請洽詢裝置製造商，以討論如何以 PIN 或生物特徵辨識作為第二個因素來啟用其裝置。

### <a name="can-admins-set-up-security-keys"></a>系統管理員可以設定安全性金鑰嗎？

我們正致力於此功能的公開上市（GA）功能。

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>哪裡可以找到相容的安全性金鑰？

[FIDO2 安全性金鑰](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>如果我遺失安全性金鑰，該怎麼辦？

您可以從 Azure 入口網站移除金鑰，方法是流覽至 [安全性資訊] 頁面，並移除安全性金鑰。

## <a name="next-steps"></a>後續步驟

[深入瞭解裝置註冊](../devices/overview.md)

[深入瞭解 Azure 多重要素驗證](../authentication/howto-mfa-getstarted.md)
