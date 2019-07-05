---
title: 受控制的混合式 Azure AD 聯結 Azure AD 的驗證
description: 了解如何受控制的驗證的混合式 Azure AD join 之前啟用它全部一次在整個組織
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
ms.openlocfilehash: d59104bf9c7675fdac2c245fff89ab1483b96b67
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481728"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>混合式 Azure AD 聯結的受控驗證

所有必要的元件時就地，將自動將 Windows 裝置註冊為 Azure AD 租用戶中的裝置。 在 Azure AD 中的這些裝置身分識別狀態稱為 「 混合式 Azure AD 聯結 」。 可以在文章中找到這篇文章所涵蓋的概念的詳細資訊[Azure Active Directory 中的裝置管理簡介](overview.md)和[規劃混合式 Azure Active Directory join 實作](hybrid-azuread-join-plan.md).

組織可能想要執行混合式 Azure AD join 控制的驗證，再讓它全部一次其整個組織。 這篇文章將說明如何完成混合式 Azure AD join 的受控制的驗證。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Windows 目前裝置上的混合式 Azure AD join 的受控制的驗證

對於執行 Windows 桌面作業系統的裝置，支援的版本為 Windows 10 年度更新版 (版本 1607) 或更新版本。 最佳做法是升級至最新版的 Windows 10。

若要執行混合式 Azure AD 聯結在目前的 Windows 裝置上的受控制的驗證，您需要：

1. 如果存在的話，請清除的服務連接點 (SCP) 項目從 Active Directory (AD)
1. 使用群組原則物件 (GPO) 已加入網域的電腦上設定 SCP 用戶端登錄設定
1. 如果您使用 AD FS，您必須在您使用 GPO 的 AD FS 伺服器上，也設定 SCP 用戶端登錄設定  



### <a name="clear-the-scp-from-ad"></a>清除 從 AD SCP

若要修改的 SCP 物件在 AD 中使用 Active Directory 服務介面編輯器 (Adsi)。

1. 啟動**Adsi**從桌面應用程式和系統管理工作站或網域控制站做為企業系統管理員。
1. 連接到**組態命名內容**您的網域。
1. 瀏覽至**CN = Configuration，DC = contoso，DC = com** > **CN = Services** > **CN = Device Registration Configuration**
1. 以滑鼠右鍵按一下底下的分葉物件**CN = Device Registration Configuration** ，然後選取**屬性**
   1. 選取 **關鍵字**從**屬性編輯器**視窗，然後按一下**編輯**
   1. 選取的值**azureADId**並**azureADName** （一次一個），按一下 **移除**
1. 關閉**Adsi 編輯器**


### <a name="configure-client-side-registry-setting-for-scp"></a>設定 SCP 用戶端登錄設定

您可以使用下列範例來建立的 「 群組原則物件 (GPO) 」，部署登錄設定，您的裝置登錄中設定 SCP 項目。

1. 開啟群組原則管理主控台，並在網域中建立新的群組原則物件。
   1. 提供您新建立的 GPO 的名稱 (例如 ClientSideSCP)。
1. 編輯 GPO，並找出下列路徑：**電腦設定** > **喜好設定** > **Windows 設定** > **登錄**
1. 以滑鼠右鍵按一下登錄，然後選取**的新** > **登錄項目**
   1. 在 **一般**索引標籤上，設定下列各項
      1. 動作：**更新**
      1. 登錄區：**HKEY_LOCAL_MACHINE**
      1. 機碼路徑：**SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 值名稱：**TenantId**
      1. 實值型別：**REG_SZ**
      1. 數值資料：GUID 或**Directory 識別碼**您的 Azure AD 執行個體 (此值可在**Azure 入口網站** > **Azure Active Directory**  >  **屬性** > **Directory 識別碼**)
   1. 按一下 **[確定]** 。
1. 以滑鼠右鍵按一下登錄，然後選取**的新** > **登錄項目**
   1. 在 **一般**索引標籤上，設定下列各項
      1. 動作：**更新**
      1. 登錄區：**HKEY_LOCAL_MACHINE**
      1. 機碼路徑：**SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 值名稱：**TenantName**
      1. 實值型別：**REG_SZ**
      1. 數值資料：您已驗證**網域名稱**在 Azure AD 中 (例如`contoso.onmicrosoft.com`或您的目錄中的任何其他已驗證的網域名稱)
   1. 按一下 **[確定]** 。
1. 關閉新建立的 gpo 編輯器
1. 新建立的 GPO 連結到想要的 OU 包含已加入網域的電腦隸屬於您控制導入母體擴展

### <a name="configure-ad-fs-settings"></a>設定 AD FS 設定

如果您使用 AD FS，必須先設定用戶端 SCP 使用前面所提到的但將 GPO 連結到您的 AD FS 伺服器的指示。 此組態所需的 AD FS 與 Azure AD 中建立裝置身分識別的來源。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>在 Windows 舊版裝置上的混合式 Azure AD join 的受控制的驗證

若要註冊舊版 Windows 裝置，組織必須安裝可在 Microsoft 下載中心取得的[適用於非 Windows 10 電腦的 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)。

您可以使用軟體發佈系統 (例如  [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager)) 來部署此套件。 此套件支援使用 quiet 參數的標準無訊息安裝選項。 組態管理員的目前分支會提供勝過舊版的好處，例如能夠追蹤已完成的註冊。

安裝程式會在系統上建立排定的工作，此工作是在使用者內容中執行。 此工作會在使用者登入 Windows 時觸發。 此工作會在向 Azure AD 進行驗證後，透過使用者認證以無訊息方式向 Azure AD 加入裝置。

若要控制的裝置註冊，您應該將的 Windows Installer 套件部署到您選取 Windows 舊版裝置的群組。

> [!NOTE]
> 如果未在 AD 中，設定 SCP，則您應該遵循相同的方法，請依照若要[設定用戶端登錄設定 SCP](#configure-client-side-registry-setting-for-scp)) 使用群組原則物件 (GPO) 已加入網域的電腦上。


確認一切運作正常之後，您可以與 Azure AD 自動註冊其餘 Windows 目前和舊版裝置[設定使用 Azure AD Connect 的 SCP](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)。

## <a name="next-steps"></a>後續步驟

[規劃混合式 Azure Active Directory Join 實作](hybrid-azuread-join-plan.md)
