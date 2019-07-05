---
title: 部署 Azure 受管理的工作站-Azure Active Directory
description: 了解如何部署安全、 受 Azure 管理的工作站，以降低風險，因為設定不正確或遭到入侵的缺口。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51d8bbc8b8be9679fbf024d7c51de53c430dc493
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550477"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>部署安全、 受 Azure 管理的工作站

現在您[了解安全的工作站](concept-azure-managed-workstation.md)，就可以開始部署程序。 本指南中，使用中，您可以使用定義的設定檔來建立從更安全的工作站。

![安全的工作站的部署](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

您可以部署方案之前，您必須選取設定檔。 您可以在部署中同時使用多個設定檔，並將其指派給標籤或群組。
> [!NOTE]
> 視您的需求，請套用任何設定檔。 您可以在 Intune 中指派給它，就可移至另一個設定檔。

| 設定檔 | 低 | 增強 | 高 | 特製化 | 保護 | 隔離 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 在 Azure AD 中的使用者 | 是 | 是 | 是 | 是 | 是 | 是 |
| 受 Intune 管理 | 是 | 是 | 是 | 是 | 是 | 是 |
| Azure AD 註冊裝置 | 是 |  |  |  |  | |   |
| 裝置-Azure AD 已加入 |   | 是 | 是 | 是 | 是 | 是 |
| Intune 套用的安全性基準 |   | 是 <br> （增強） | 是 <br> (HighSecurity) | 是 <br> (NCSC) | 是 <br> （受保護） |  NA |
| 硬體符合安全的 Windows 10 標準 |   | 是 | 是 | 是 | 是 | 是 |
| 啟用 Microsoft Defender ATP |   | 是  | 是 | 是 | 是 | 是 |
| 移除的系統管理員權限 |   |   | 是  | 是 | 是 | 是 |
| 使用 Microsoft Autopilot 部署 |   |   | 是  | 是 | 是 | 是 |
| 只有由 Intune 所安裝的應用程式 |   |   |   | 是 | 是 |是 |
| 限制為核准清單的 Url |   |   |   | 是 | 是 |是 |
| 網際網路封鎖 （輸入/輸出） |   |   |   |  |  |是 |

## <a name="license-requirements"></a>授權需求

本指南中所涵蓋的概念，假設您有 Microsoft 365 企業版 E5 」 或 「 對等的 SKU。 本指南中的建議事項的一些可以使用較低 Sku 來實作。 如需詳細資訊，請參閱 < [Microsoft 365 企業版授權](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)。

若要自動化佈建授權，請考慮[群組型授權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign)為您的使用者。

## <a name="azure-active-directory-configuration"></a>Azure Active Directory 組態

Azure Active Directory (Azure AD) 可管理使用者、 群組和您的系統管理員工作站的裝置。 您必須啟用身分識別服務和功能[系統管理員帳戶](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。

當您建立安全的工作站系統管理員帳戶時，您會公開目前工作站的帳戶。 請確定您使用已知的安全裝置來執行這項初始設定和所有的全域組態。 若要降低攻擊風險，第一次體驗，請考慮下列[指南，以防止惡意程式碼感染](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection)。

您也應該為系統管理員至少要求多重要素驗證。 請參閱[部署雲端式 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)實作指引。

### <a name="azure-ad-users-and-groups"></a>Azure AD 使用者和群組

1. 從 Azure 入口網站中，瀏覽至**Azure Active Directory** > **使用者** > **新使用者**。
1. 請依照下列中的步驟建立您的裝置系統管理員[建立使用者教學課程](https://docs.microsoft.com/Intune/quickstart-create-user)。
1. 輸入：
   * **名稱**-安全的工作站系統管理員
   * **使用者名稱** - `secure-ws-admin@identityitpro.com`
   * **目錄角色** - **有限的系統管理員**，然後選取**Intune 系統管理員**角色。
1. 選取 [建立]  。

接下來，您會建立兩個群組： 工作站的使用者和工作站的裝置。

從 Azure 入口網站中，瀏覽至**Azure Active Directory** > **群組** > **新群組**。

1. 工作站 [使用者] 群組中，您可能想要設定[群組型授權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign)自動佈建授權給使用者。
1. 工作站的使用者群組，請輸入：
   * **群組類型**-安全性
   * **群組名稱**-安全工作站的使用者
   * **成員資格類型**-指派
1. 將安全的工作站的系統管理員使用者： `secure-ws-admin@identityitpro.com`
1. 您可以新增任何其他要管理安全的工作站的使用者。
1. 選取 [建立]  。

1. 工作站的裝置群組，請輸入：
   * **群組類型**-安全性
   * **群組名稱**-安全的工作站
   * **成員資格類型**-指派
1. 選取 [建立]  。

### <a name="azure-ad-device-configuration"></a>Azure AD 裝置設定

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>指定誰可以將裝置加入 Azure AD

設定您在 Active Directory 中設定可讓您的系統管理安全性的群組將裝置加入您的網域的裝置。 若要設定此設定從 Azure 入口網站：

1. 移至**Azure Active Directory** > **裝置** > **裝置設定**。
1. 選擇**Selected**下方**使用者可能會將裝置加入 Azure AD**，然後選取 「 安全的工作站使用者 」 群組。

#### <a name="removal-of-local-admin-rights"></a>移除的本機系統管理權限

此方法需要 VIP、 DevOps 和保護層級工作站的使用者，有其電腦上沒有系統管理員權限。 若要設定此設定從 Azure 入口網站：

1. 移至**Azure Active Directory** > **裝置** > **裝置設定**。
1. 選取 **無**下方**其他本機系統管理員，在 Azure AD 已加入裝置**。

#### <a name="require-multi-factor-authentication-to-join-devices"></a>需要多重要素驗證，才能加入裝置

若要進一步加強將裝置加入 Azure AD 的程序：

1. 移至**Azure Active Directory** > **裝置** > **裝置設定**。
1. 選取  **是**下方**需要 Multi-factor Auth 才能聯結裝置**。
1. 選取 [ **儲存**]。

#### <a name="configure-mdm"></a>設定 MDM

從 Azure 入口網站：

1. 瀏覽至**Azure Active Directory** > **行動性 （MDM 與 MAM）**  > **Microsoft Intune**。
1. 變更**MDM 使用者範圍**設為**所有**。
1. 選取 [ **儲存**]。

這些步驟可讓您管理使用 Intune 的任何裝置。 如需詳細資訊，請參閱[Intune 快速入門：設定適用於 Windows 10 裝置的自動註冊](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)。 您在未來的步驟中建立 Intune 設定和合規性原則。

#### <a name="azure-ad-conditional-access"></a>Azure AD 條件式存取

Azure AD 條件式存取可協助符合規範的裝置限制特殊權限的系統管理工作。 預先定義的成員**安全的工作站使用者**執行多重要素驗證，登入雲端應用程式時所需的群組。 最佳的作法是從原則中排除緊急存取帳戶。 如需詳細資訊，請參閱 <<c0> [ 在 Azure AD 中管理緊急存取帳戶](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)。

若要從 Azure 入口網站中設定條件式存取：

1. 移至**Azure Active Directory** > **條件式存取** > **新原則**。
1. 輸入：
   * **名稱**-安全的裝置所需的原則
   * 指派
     * **使用者和群組**
       * 包含-**使用者和群組**-選取**安全工作站使用者**稍早建立的群組。
       * 排除**使用者和群組**-選取您的組織緊急存取帳戶。
     * **雲端應用程式**-包含**所有雲端應用程式**。
    * 存取控制
      * **授與**-選取**授與存取權**選項按鈕。
        * **需要多重要素驗證**。
        * **標記為合規的裝置需要**。
        * 為多個控制項-**需要所有選取的控制項**。
    * 啟用原則-**上**。

您可以建立原則，以阻止使用者會存取公司資源的國家/地區。 如需 IP 位置型條件式存取原則的詳細資訊，請參閱[在 Azure Active Directory 條件式存取的位置條件](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)。

## <a name="intune-configuration"></a>Intune 設定

### <a name="configure-enrollment-status"></a>設定註冊狀態

請務必確保安全的工作站信任乾淨的裝置。 購買新的裝置，您可以堅持它們原廠設定，為[Windows 10 專業版在 S 模式](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode)，進而限制期間供應鏈管理的暴露的弱點。 從您的供應商收到裝置之後，您可以使用 Autopilot 從 S 模式變更它。 下列指引提供詳細資料上套用轉換程序。

若要確保裝置已完整設定中使用之前，Intune 會提供一個方法來**封鎖直到所有的應用程式和設定檔已安裝的裝置，請使用**。

從 **Azure 入口網站**：
1. 移至**Microsoft Intune** > **裝置註冊** > **Windows 註冊** > **註冊狀態頁面** > **預設** > **設定**。
1. 設定**顯示應用程式設定檔安裝進度**要**是**。
1. 設定**封鎖裝置，請使用，直到所有的應用程式和設定檔會安裝**要**是**。

### <a name="create-an-autopilot-deployment-profile"></a>建立 Autopilot 部署設定檔

建立裝置群組之後，您必須建立的部署設定檔，來設定 Autopilot 裝置。

在 Azure 入口網站中 Intune:

1. 選取 **裝置註冊** > **Windows 註冊** > **部署設定檔** > **建立設定檔**.
1. 輸入：
   * 名稱-**保護工作站部署設定檔**。
   * 描述-**安全的工作站的部署**。
   * 設定**將所有目標的裝置轉換為 Autopilot**要**是**。 此設定可確保所有裝置清單中的，以取得都向 Autopilot 部署服務。 允許 48 小時才能處理註冊。
1. 選取 [下一步]  。
   * 針對**部署模式**，選擇**自我部署 （預覽）** 。 使用此設定檔的裝置會註冊裝置的使用者相關聯。 使用者認證，才能註冊裝置。
   * **加入 Azure AD**應該會顯示方塊**已加入 Azure AD**和呈現灰色。
   * 選取您 Langugage （「 區域 」），使用者帳戶類型**標準**。 
1. 選取 [下一步]  。
   * 如果您已預先設定的其中一個，請選取範圍標記。
1. 選取 [下一步]  。
1. 選擇**指派** > **指派給** > **選取的群組**。 在 **選取要包含的群組**，選擇**安全工作站使用者**。
1. 選取 [下一步]  。
1. 選取 [建立]  以建立設定檔。 現在可指派給裝置的 Autopilot 部署設定檔。

### <a name="configure-windows-update"></a>設定 Windows Update

讓 Windows 10 最新狀態是其中一個最重要的工作，您可以執行。 若要維護 Windows 處於安全狀態，您將部署更新通道來管理的步調，更新會套用到工作站。 

本指南建議您建立新的更新通道，並變更下列預設設定：

在 Azure 入口網站中：

1. 移至**Microsoft Intune** > **軟體更新** > **Windows 10 更新通道**。
1. 輸入：
   * 名稱- **Azure 受管理的工作站的更新**
   * 維護通道- **Windows 測試人員-快**
   * 品質更新延遲 （天）- **3**
   * 功能更新延遲期間 （天數）- **3**
   * 自動更新行為-**自動安裝，並沒有使用者控制重新開機**
   * 封鎖使用者，從暫停的 Windows 更新-**區塊**
   * 需要使用者核准，才可重新啟動外部工作時數-**所需**
   * 允許使用者重新啟動 （參與重新啟動）-**所需**
   * 轉換使用者參與的重新啟動之後自動重新啟動 （天）- **3**
   * 延遲參與的重新啟動提醒 （天數）- **3**
   * 設定期限暫止重新啟動 （天）- **3**

1. 選取 [建立]  。
1. 在 **指派**索引標籤上，新增**安全的工作站**群組。

如需有關 Windows Update 原則的詳細資訊，請參閱[原則 CSP-更新](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)。

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune 整合

Windows Defender ATP 和 Microsoft Intune 一起運作，可協助防止安全性產生漏洞。 它們也可以限制缺口影響。 ATP 功能提供即時的威脅偵測，以及啟用豐富的稽核和記錄的端點裝置。

若要設定 Windows Defender ATP 與 Intune 的整合，請前往 Azure 入口網站。

1. 瀏覽至**Microsoft Intune** > **裝置合規性** > **Windows Defender ATP**。
1. 步驟 1 之下**設定 Windows Defender ATP**，選取**Windows Defender 資訊安全中心的 Microsoft Intune 來連線 Windows Defender ATP**。
1. Windows Defender 資訊安全中心：
   1. 選取 **設定** > **進階功能**。
   1. 針對**Microsoft Intune 連線**，選擇**上**。
   1. 選取 **儲存喜好設定**。
1. 建立連線之後，返回 Intune，然後選取**重新整理**頂端。
1. 設定**連線的 Windows 裝置版本 10.0.15063 和更新版本到 Windows Defender ATP**要**上**。
1. 選取 [ **儲存**]。

如需詳細資訊，請參閱 < [Windows Defender 進階威脅防護](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)。

### <a name="finish-workstation-profile-hardening"></a>完成強化工作站設定檔

若要成功完成解決方案的強化，下載並執行適當的指令碼。 尋找您想要的下載連結**設定檔層級**:

| 設定檔 | 下載位置 | 檔案名稱 |
| --- | --- | --- |
| 低安全性 | N/A |  N/A |
| 增強的安全性 | https://aka.ms/securedworkstationgit | 增強式--windows 10-工作站 (1809).ps1 |
| 高安全性  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| 特製化 | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| 特製化的合規性 * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| 保護 | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\* 特製化的合規性是強制執行 NCSC Windows10 SecurityBaseline 中提供的特殊的組態的指令碼。

已成功執行指令碼之後，您可以對設定檔和原則在 Intune 中的進行更新。 增強和安全的設定檔的指令碼建立原則和設定檔，但您必須指派原則，以您**安全的工作站**群組。

* 以下是您可以在其中找到指令碼所建立的 Intune 裝置組態設定檔：**Azure 入口網站** > **Microsoft Intune** > **裝置組態** > **設定檔**。
* 以下是您可以在何處 Intune 裝置合規性政策的指令碼所建立：**Azure 入口網站** > **Microsoft Intune** > **裝置合規性** > **原則**。

若要檢閱指令碼所做的變更，您可以匯出設定檔。 如此一來您就可以判斷可能需要 SECCON 文件中所述的其他強化。

執行 Intune 的資料匯出指令碼`DeviceConfiguration_Export.ps1`從[DeviceConfiguration GiuHub 存放庫](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration)匯出所有目前的 Intune 設定檔。

## <a name="additional-configurations-and-hardening-to-consider"></a>其他組態和強化考量

只要遵循這裡的指引，您已部署安全的工作站。 不過，您也應該考慮其他控制項。 例如:

* 替代的瀏覽器的限制存取
* 允許輸出 HTTP
* 封鎖選取的網站
* 設定執行自訂 PowerShell 指令碼的權限

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>在 防火牆設定服務提供者 (CSP) 中設定規則

您可以輸入和輸出規則的管理進行其他變更，視您允許和封鎖的端點。 隨著您繼續強化安全的工作站，您可以放鬆限制會拒絕所有輸入和輸出流量。 您可以新增允許輸出的站台，以包含一般和受信任的網站。 如需詳細資訊，請參閱 <<c0> [ 防火牆設定服務](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp)。

預設限制建議事項為：

* 拒絕所有輸入
* 拒絕所有輸出

Spamhaus 專案會維持[網域區塊清單 (DBL)](https://www.spamhaus.org/dbl/)： 做為起點，用於封鎖站台的絕佳資源。

### <a name="manage-local-applications"></a>管理本機應用程式

移除本機應用程式，包括產能應用程式時，安全的工作站將移到真正的強化後的狀態。 在這裡，您將 Chrome 新增的預設瀏覽器，並使用 Intune 來限制使用者能夠修改瀏覽器和其外掛程式。

#### <a name="deploy-applications-using-intune"></a>使用 Intune 部署應用程式

在某些情況下，應用程式，例如 Google Chrome 瀏覽器需要在安全的工作站上。 下列範例會提供指示給安全性群組中的裝置安裝 Chrome**安全的工作站**。

1. 下載離線安裝程式[Chrome 組合 Windows 64‑bit](https://cloud.google.com/chrome-enterprise/browser/download/)。
1. 解壓縮檔案，並記下的位置`GoogleChromeStandaloneEnterprise64.msi`檔案。
1. 在  **Azure 入口網站**瀏覽至**Microsoft Intune** > **用戶端應用程式** > **應用程式** > **新增**。
1. 底下**應用程式類型**，選擇**特定業務**。
1. 底下**應用程式套件檔案**，選取`GoogleChromeStandaloneEnterprise64.msi`檔案從已解壓縮的位置，然後選取**確定**。
1. 底下**應用程式資訊**，提供發行者和描述。 選取 [確定]  。
1. 選取 [新增]  。
1. 在 **指派**索引標籤上，選取**適用於已註冊的裝置**之下**指派類型**。
1. 底下**包含的群組**，新增**安全的工作站**群組。
1. 選取 [確定]  ，然後選取 [儲存]  。

如需詳細指引 Chrome 設定的詳細資訊，請參閱[使用 Microsoft Intune 管理 Chrome 瀏覽器](https://support.google.com/chrome/a/answer/9102677)。

#### <a name="configuring-the-company-portal-for-custom-apps"></a>設定自訂應用程式在公司入口網站

在安全模式中，應用程式安裝限於 Intune 公司入口網站。 不過，安裝入口網站需要存取 Microsoft Store。 在安全的解決方案中，您可以提供公司入口網站透過離線模式的所有裝置。

將受 Intune 管理的複本[公司入口網站](https://docs.microsoft.com/Intune/store-apps-company-portal-app)可讓您視需要存取其他工具，可以向下推送到受保護的工作站的使用者。

您可能需要安裝 Windows 32 位元應用程式或其他應用程式的部署需要特殊的準備工作。 在此情況下， [Microsoft win32 的內容準備工具](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool)可以提供已準備好使用`.intunewin`格式檔案以進行安裝。

### <a name="use-powershell-to-create-custom-settings"></a>使用 PowerShell 建立自訂設定

您也可以使用 PowerShell 來擴充主應用程式管理功能。 此範例指令碼會設定在主機上的預設背景。 這項功能也可透過 Azure 入口網站和設定檔。 範例指令碼只用來說明 PowerShell 功能。

您可能需要在安全的工作站上設定的某些自訂控制項和設定。 這個範例會變更工作站的背景，更容易找出裝置為已準備好使用且安全的工作站中使用 Powershell 的功能。

[SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/)從 Microsoft 指令碼中心指令碼可讓 Windows 載入這[免費的泛型背景影像](https://i.imgur.com/OAJ28zO.png)上啟動。

1. 下載指令碼至本機裝置。
1. 更新 customerXXXX 和背景影像的下載位置。 在本例中，我們會取代 customerXXXX 至背景。  
1. 瀏覽至**Azure 入口網站** > **Microsoft Intune** > **裝置組態** > **PowerShell指令碼** > **新增**。
1. 提供**名稱**指令碼，並指定**指令碼位置**。
1. 選取 [設定]  。
   1. 設定**執行此指令碼，使用登入認證**要**是**。
   1. 選取 [確定]  。
1. 選取 [建立]  。
1. 選取 **指派** > **選取群組**。
   1. 新增安全性群組**安全的工作站**。
   1. 選取 [ **儲存**]。

## <a name="enroll-and-validate-your-first-device"></a>註冊並驗證您的第一個裝置

1. 若要註冊您的裝置，您需要下列資訊：
   * **序號**-裝置底座上找到。
   * **Windows 產品識別碼**-下找到**系統** > **關於**[Windows 設定] 功能表。
   * 您可以執行[Get WindowsAutoPilotInfo](https://aka.ms/Autopilotshell)取得裝置註冊的所有必要資訊的 CSV 雜湊檔案。
   
     執行`Get-WindowsAutoPilotInfo – outputfile device1.csv`輸出為 CSV 檔案，您可以匯入至 Intune 的資訊。

     > [!NOTE]
     > 指令碼需要提高權的限。 它會執行做為遠端簽署。 `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`命令可讓指令碼正確執行。

   * 您可以登入 Windows 10 版本 1809年或更高版本的裝置來收集這項資訊。 硬體轉銷商也可以提供這項資訊。
1. 在  **Azure 入口網站**，請前往**Microsoft Intune** > **裝置註冊** > **Windows 註冊** > **裝置-管理 Windows Autopilot 裝置**。
1. 選取 **匯入**並選擇 CSV 檔案。
1. 將裝置新增到**安全的工作站**安全性群組。
1. 在您想要設定 Windows 10 裝置，請移至**Windows 設定** > **更新與安全性** > **復原**。
   1. 選擇**開始**下方**重設此 PC**。
   1. 遵循提示來重設並重新設定與設定的設定檔和合規性原則的裝置。

設定裝置之後，完成檢閱，請檢查組態。 確認第一個裝置已正確設定，然後再繼續您的部署。

## <a name="assign-and-monitor"></a>指派和監視

若要指派的裝置和使用者，您需要將對應[選取設定檔](https://docs.microsoft.com/intune/device-profile-assign)到您的安全性群組。 所有新的使用者需要服務的權限必須加入安全群組。

您可以監視的設定檔[Intune 設定檔監視](https://docs.microsoft.com/intune/device-profile-monitor)。

## <a name="next-steps"></a>後續步驟

* 深入了解[Microsoft Intune](https://docs.microsoft.com/intune/index)。
* 了解[Azure AD](https://docs.microsoft.com/azure/active-directory/index)。
