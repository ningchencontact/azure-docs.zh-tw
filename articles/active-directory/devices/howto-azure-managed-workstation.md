---
title: 部署 Azure 受管理的工作站-Azure Active Directory
description: 了解如何部署安全 Azure 受管理的工作站，以降低的漏洞，因為設定不正確或遭到入侵的風險
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
ms.openlocfilehash: 242926c0821e4951d2a2bd2f858f63691baf1017
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307223"
---
# <a name="deploy-a-secure-workstation"></a>部署安全的工作站

既然您了解[保護的工作站存取是重要的原因？](concept-azure-managed-workstation.md)就可以開始使用可用的工具進行部署的程序。 本指南將使用已定義的設定檔，來建立從更安全的工作站。

![安全的工作站的部署](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

在之前部署解決方案時，必須選取您要使用的設定檔。 請務必注意您可以用來套用所有選取的設定檔，並指派您的需求為基礎的 Intune 中的設定檔之間移動。 可以在部署中，同時使用多個設定檔，並指派使用標記之或群組指派。

| 設定檔 | 低 | 增強 | 高 | 特製化 | 保護 | 隔離 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 在 Azure AD 中的使用者 | 是 | 是 | 是 | 是 | 是 | 是 |
| 受 Intune 管理 | 是 | 是 | 是 | 是 | 是 | 是 |
| Azure AD 註冊的裝置 | 是 |  |  |  |  | |   |
| 已加入 Azure AD 的裝置 |   | 是 | 是 | 是 | 是 | 是 |
| Intune 套用的安全性基準 |   | 是 <br> （增強） | 是 <br> (HighSecurity) | 是 <br> (NCSC) | 是 <br> （受保護） |  NA |
| 硬體符合安全的 Windows 10 標準 |   | 是 | 是 | 是 | 是 | 是 |
| 啟用 Microsoft Defender ATP |   | 是  | 是 | 是 | 是 | 是 |
| 移除的系統管理員權限 |   |   | 是  | 是 | 是 | 是 |
| 使用 Microsoft Autopilot 部署 |   |   | 是  | 是 | 是 | 是 |
| 只有由 Intune 所安裝的應用程式 |   |   |   | 是 | 是 |是 |
| 限制為僅限核准清單的 Url |   |   |   | 是 | 是 |是 |
| 網際網路 （輸入/輸出封鎖） |   |   |   |  |  |是 |

## <a name="license-requirements"></a>授權需求

Microsoft 365 企業版 E5 」 或 「 對等的 SKU，則會假設本指南中所涵蓋的概念。 本指南中的建議事項的一些可以使用較低 Sku 來實作。 其他資訊都位於[Microsoft 365 企業版授權](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)。

您可能想要設定[群組型授權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign)為您自動佈建授權的使用者。

## <a name="azure-active-directory-configuration"></a>Azure Active Directory 組態

設定您的 Azure Active Directory (Azure AD) 目錄，將會管理您的使用者、 群組和您的系統管理員工作站的裝置，您必須啟用身分識別服務和功能[的系統管理員帳戶](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

當您建立安全的工作站系統管理員帳戶時，您將帳戶到您目前的工作站。 建議您此初始設定，並從已知的安全裝置的所有全域設定。 您可以考慮到的指導方針[防止惡意程式碼感染](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection)以降低暴露的第一次首次經驗，遭受攻擊的風險。

組織應該至少需要多重要素驗證，其系統管理員。 請參閱[部署雲端式 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)實作指引。

### <a name="azure-ad-users-and-groups"></a>Azure AD 使用者和群組

從 Azure 入口網站中，瀏覽至**Azure Active Directory** > **使用者** > **新使用者**。 [建立安全的工作站使用者](https://docs.microsoft.com/Intune/quickstart-create-user)，誰會是您的裝置系統管理員。

* **名稱**-安全的工作站系統管理員
* **使用者名稱**- secure-ws-admin@identityitpro.com
* **目錄角色** - **受限的管理員**，然後選取下列的系統管理角色
   * **Intune 系統管理員**
* **建立**

我們將建立兩個群組一個工作站的使用者，一個用於工作站本身。 從 Azure 入口網站中，瀏覽至**Azure Active Directory** > **群組** > **新群組**

第一個工作站的使用者群組。 您可能想要設定[群組型授權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign)中此群組來自動佈建授權給使用者的使用者。

* **群組類型**-安全性
* **群組名稱**-安全工作站的使用者
* **成員資格類型**-指派
* 將安全的工作站系統管理員使用者新增至群組
   * secure-ws-admin@identityitpro.com
* 您可以新增要管理安全的工作站，群組的任何其他使用者
* **建立**

第二個工作站裝置群組。

* **群組類型**-安全性
* **群組名稱**-安全的工作站
* **成員資格類型**-指派
* **建立**

### <a name="azure-ad-device-configuration"></a>Azure AD 裝置設定

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>指定誰可以將裝置加入 Azure AD

設定您的裝置設定 Active Directory 中，將您的系統管理安全性的群組將裝置加入您的網域。 若要設定此設定從 Azure 入口網站，瀏覽至**Azure Active Directory** > **裝置** > **裝置設定**。 選擇**Selected**下方**使用者可能會將裝置加入 Azure AD**並選取 「 安全的工作站使用者 」 群組。

#### <a name="removal-of-local-admin-rights"></a>移除的本機系統管理權限

首度發行過程工作站使用者 VIP、 DevOps 和安全性層級的工作站會在其電腦上有沒有系統管理員權限。 若要設定此設定從 Azure 入口網站，瀏覽至**Azure Active Directory** > **裝置** > **裝置設定**。 選取 **無**下方**其他本機系統管理員，在 Azure AD 已加入裝置**。

#### <a name="require-multi-factor-authentication-to-join-devices"></a>需要多重要素驗證，才能加入裝置

若要進一步加強將裝置加入 Azure AD 的程序，瀏覽至**Azure Active Directory** > **裝置** > **裝置設定**選擇 **[是]** 下方**需要 Multi-factor Auth 才能聯結裝置**然後選擇**儲存**。

#### <a name="configure-mdm"></a>設定 MDM

從 Azure 入口網站中，瀏覽至**Azure Active Directory** > **行動性 （MDM 與 MAM）**  > **Microsoft Intune**。 將設定變更**MDM 使用者範圍**要**所有**，然後選擇 **儲存**我們允許在此案例中，由 Intune 管理的任何裝置。 文件中，可以找到更多資訊[Intune 快速入門：設定適用於 Windows 10 裝置的自動註冊](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)。 我們將在未來的步驟中建立 Intune 設定和合規性原則。

#### <a name="azure-ad-conditional-access"></a>Azure AD 條件式存取

Azure AD 條件式存取可協助保持符合規範的裝置上的這些特殊權限的系統管理工作。 我們已定義為成員的使用者**安全的工作站使用者**群組都必須執行多重要素驗證登入雲端應用程式時。 我們將遵循的最佳做法指導，並從原則中排除我們緊急存取帳戶。 在本文中，可以找到其他資訊[在 Azure AD 中管理緊急存取帳戶](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

若要設定條件式存取，從 Azure 入口網站，瀏覽至**Azure Active Directory** > **條件式存取** > **新原則**。

* **名稱**-安全的裝置所需的原則
* 指派
   * **使用者和群組**
      * 包含-**使用者和群組**-選取**安全工作站使用者**稍早建立的群組
      * 排除**使用者和群組**-選取您的組織緊急存取帳戶
   * **雲端應用程式**
      * 包括-**所有雲端應用程式**
* 存取控制
   * **授與**-選取**授與存取權**選項按鈕
      * **需要多重要素驗證**
      * [裝置需要標記為合規] 
      * 為多個控制項-**需要所有選取的控制項**
* 啟用原則-**上**

組織可以選擇建立區塊國家/地區的使用者會存取公司資源的原則。 IP 位置型條件式存取原則的詳細資訊可在發行項[什麼是 Azure Active Directory 條件式存取中的位置條件？](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)

## <a name="intune-configuration"></a>Intune 設定

### <a name="configure-enrollment-status"></a>設定註冊狀態

供應鏈管理所述，確保安全的工作站信任乾淨的裝置，建議購買新的裝置，裝置會設為原廠[Windows 10 專業版在 S 模式](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode)，進而限制暴露和在供應鏈管理的弱點。 一旦您的供應商收到的裝置，裝置會移除 S 模式中使用 Autopilot。 下列指引提供詳細資料上套用轉換程序。

我們想要確保裝置已完整設定才能使用。 Intune 提供一個方法來**封鎖直到所有的應用程式和設定檔已安裝的裝置，請使用**。 

1. 從**Azure 入口網站**瀏覽至：
1. **Microsoft Intune** > **裝置註冊** > **Windows 註冊** > **註冊狀態頁面 （預覽）**  > **預設** > **設定**。
1. 設定**顯示應用程式設定檔安裝進度**要**是**。
1. 設定**封鎖裝置，請使用，直到所有的應用程式和設定檔會安裝**要**是**。

### <a name="create-an-autopilot-deployment-profile"></a>建立 Autopilot 部署設定檔

建立裝置群組之後，您必須建立的部署設定檔，以便您可以設定 Autopilot 裝置。

1. 在 Intune Azure 入口網站中，選擇**裝置註冊** > **Windows 註冊** > **部署設定檔** >  **建立設定檔**。
1. 針對名稱，輸入**保護工作站部署設定檔**。 如需說明，請輸入**安全的工作站的部署**。
1. 設為 [是] 的 Autopilot 轉換所有目標裝置。 此設定可確保所有裝置清單中的，以取得都向 Autopilot 部署服務。  允許 48 小時才能處理註冊。
1. 部署模式中，選擇**自我部署 （預覽）** 。 註冊裝置的使用者，使用此設定檔的裝置會產生關聯。 使用者認證，才能註冊裝置。
1. 在方塊中，為 Azure ad 聯結**已加入 Azure AD**應該選擇，並呈現灰色。
1. 選取的全新體驗 (OOBE)，設定下列選項和保留其他項目設為預設值，然後按**Ok**:
   1. 使用者帳戶類型：**標準**
1. 選取 [建立]  以建立設定檔。 現在可指派給裝置的 Autopilot 部署設定檔。
1. 選擇**指派** > **指派給** > **選取的群組**
   1. **選取要包含的群組**-安全工作站的使用者

### <a name="configure-windows-update"></a>設定 Windows Update

組織可以做最重要的事情之一是保持 Windows 10 最新狀態。 若要維護安全的狀態中的 Windows 10 中，我們將部署管理工作站來套用更新的進度更新通道。 此設定可在**Azure 入口網站** > **Microsoft Intune** > **軟體更新** >  **Windows 10 更新通道**。

我們會**建立**新的更新通道，使用下列設定變更的預設值。

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

按一下  **Create**然後在**指派**索引標籤上加入**安全的工作站**群組做為包含的群組。

Windows Update 原則的其他資訊可在[原則 CSP-更新](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune 整合

Windows Defender ATP 和 Microsoft Intune 一起工作，以協助防止安全性產生漏洞，並協助限制缺口影響。 功能會提供即時偵測。 ATP 也會提供我們的部署大量的稽核和記錄的端點裝置。

若要設定 Windows Defender ATP Intune 整合在 Azure 入口網站中，瀏覽至**Microsoft Intune** > **裝置合規性** > **Windows Defender ATP**.

1. 步驟 1 之下**設定 Windows Defender ATP**，按一下**Windows Defender 資訊安全中心的 Microsoft Intune 來連線 Windows Defender ATP**。
1. Windows Defender 資訊安全中心：
   1. 選取 **設定** > **進階功能**
   1. 針對**Microsoft Intune 連線**，選擇**上**
   1. 選取**儲存喜好設定**
1. 建立連線之後，傳回至 Intune，然後按一下**重新整理**頂端。
1. 設定**連線的 Windows 裝置版本 10.0.15063 和更新版本到 Windows Defender ATP**要**上**。
1. **儲存**

在本文中，可以找到其他資訊[Windows Defender 進階威脅防護](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)。

### <a name="completing-hardening-of-the-workstation-profile"></a>完成強化工作站的設定檔

若要成功完成解決方案的強化，下載並執行指令碼為所需的基礎**設定檔層級**從下列圖表。

| 設定檔 | 下載位置 | 檔案名稱 |
| --- | --- | --- |
| 低安全性 | N/A |  N/A |
| 增強的安全性 | https://aka.ms/securedworkstationgit | 增強式--windows 10-工作站 (1809).ps1 |
| 高安全性  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| 特製化 | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| 特製化的合規性 * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| 保護 | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

特製化的合規性 * 是強制執行 NCSC Windows10 SecurityBaseline 中提供的特殊的組態的指令碼。

一旦成功執行選取的指令碼，可以在 Microsoft Intune 中進行設定檔和原則的更新。 增強和安全的設定檔的指令碼會建立原則和設定檔的您，但您必須指派原則，以您**安全的工作站**群組。

* 指令碼所建立的 Intune 裝置組態設定檔可在**Azure 入口網站** > **Microsoft Intune** > **裝置組態** > **設定檔**。
* 在中，可以找到指令碼所建立的 Intune 裝置合規性政策**Azure 入口網站** > **Microsoft Intune** > **裝置合規性**  > **原則**。

您也可以檢閱所做的變更匯出設定檔，並將變更套用至匯出檔案，SECCON 中所述根據和其他強化的文件不需要。

執行 Intune 資料匯出指令碼`DeviceConfiguration_Export.ps1`從[DeviceConfiguration GiuHub 存放庫](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration)會提供所有現有的 Intune 設定檔的目前的匯出。

## <a name="additional-configurations-and-hardening-to-consider"></a>其他組態和強化考量

提供的指引，已啟用安全的工作站，其他的控制項也應該考慮，例如替代的瀏覽器存取，輸出 HTTP 允許和封鎖的網站和執行自訂 PowerShell 指令碼的能力。

### <a name="restrictive-inbound-and-outbound-rules-in-firewall-configuration-service-provider-csp"></a>防火牆組態服務提供者 (CSP) 的限制輸入及輸出規則

額外的輸入和輸出規則的管理可以更新以反映您允許和封鎖的端點。 隨著我們持續強化安全的工作站，我們會移動限制為 拒絕所有輸入和輸出為預設值，並新增允許的站台的輸出以反映一般和受信任的網站。 防火牆組態服務提供者的其他組態資訊可在發行項[防火牆 CSP](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp)。

預設限制建議事項為：

* 拒絕所有輸入
* 拒絕所有輸出

Spamhaus 專案有一份良好的組織是否有封鎖稱為站台時，可用做為起點[網域區塊清單 (DBL)](https://www.spamhaus.org/dbl/)。

### <a name="managing-local-applications"></a>管理本機應用程式

移除本機應用程式，包括移除的產能應用程式就會進行安全的工作站真正強化後的狀態。 在本例中，我們會將 Chrome 新增的預設瀏覽器，並限制能夠修改包括外掛程式的瀏覽器使用 Intune。

#### <a name="deploy-applications-using-intune"></a>使用 Intune 部署應用程式

在某些情況下，應用程式，例如 Google Chrome 瀏覽器需要在安全的工作站上。 下列範例會提供指示給安全性群組中的裝置安裝 Chrome**安全的工作站**稍早建立。

1. 下載離線安裝程式[Windows 64‑bit 的 Chrome 套件組合](https://cloud.google.com/chrome-enterprise/browser/download/)
1. 解壓縮檔案，並記下的位置`GoogleChromeStandaloneEnterprise64.msi`使用 Intune 來安裝
1. 在  **Azure 入口網站**瀏覽至**Microsoft Intune** > **用戶端應用程式** > **應用程式** > **新增**
1. 底下**應用程式類型**，選擇**的業務**
1. 底下**應用程式套件檔案**，選取`GoogleChromeStandaloneEnterprise64.msi`從已解壓縮的位置，然後按一下 **[確定]**
1. 底下**應用程式資訊**、 提供描述和 「 發行者 」，然後選擇 **[確定]**
1. 按一下 [新增] 
1. 在 **指派**選取**適用於已註冊的裝置**下**指派類型**
1. 底下**包含的群組**，新增**安全的工作站**稍早建立的群組
1. 按一下  **確定**然後**儲存**

在 Chrome 設定的其他指引，請參閱其技術支援文件[使用 Microsoft Intune 管理 Chrome 瀏覽器](https://support.google.com/chrome/a/answer/9102677)。

#### <a name="configuring-the-company-portal-for-custom-apps"></a>設定自訂應用程式在公司入口網站

在安全模式中，安裝應用程式將會限制為 Intune 公司入口網站。 不過，安裝入口網站需要存取 Microsoft Store。 在我們的安全解決方案中，我們將在入口網站提供給所有裝置使用公司入口網站的離線模式。

安裝 Intune 受管理的複本[公司入口網站](https://docs.microsoft.com/Intune/store-apps-company-portal-app)會允許將資料放入額外的工具，視受保護的工作站的使用者的能力。

某些組織可能需要安裝 Windows 32 位元應用程式或需要其他準備来部署的應用程式。 這些應用程式，如[Microsoft win32 的內容準備工具](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool)會提供已準備好使用`.intunewin`格式檔案以進行安裝。

### <a name="setting-up-custom-settings-using-powershell"></a>使用 PowerShell 的自訂設定的設定

我們也會使用提供的擴充性管理主應用程式中使用 PowerShell 的範例。 指令碼會設定在主機上的預設背景。 這項功能也會提供設定檔中，並只會用來說明此功能。

在方案中可能需要在安全的工作站上設定的某些自訂控制項和設定。 在本例中，我們將變更要能夠輕鬆地將裝置視為可供使用安全的工作站中使用 Powershell 的工作站的背景。 雖然我們的範例將使用 PowerShell 來完成這項工作，它也可在 Azure 入口網站中完成。

我們的範例會使用下列[免費的泛型背景影像](https://i.imgur.com/OAJ28zO.png)並[SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/)從 Microsoft 指令碼中心，以允許 Windows 將在啟動背景。

1. 指令碼下載到本機裝置
1. 更新 customerXXXX 和您想要以反映背景檔案和資料夾，您想要使用部署指令碼中使用的背景的下載位置。 在本例中，我們會取代 customerXXXX 至背景。  
1. 瀏覽至**Azure 入口網站** > **Microsoft Intune** > **裝置組態** > **PowerShell指令碼** > **新增**
1. 提供**名稱**指令碼，並指定**指令碼位置**您下載它的位置
1. 選取 [設定] 
   1. 設定**執行此指令碼，使用登入認證**至 **[是]**
   1. 按一下 **[確定]** 。
1. 按一下 [建立] 
1. 選取 **指派** > **選取群組**
   1. 新增安全性群組**安全的工作站**稍早建立，並按一下**儲存**

### <a name="using-the-preview-mdm-security-baseline-for-october-2018"></a>使用預覽：2018 年 10 月的 MDM 安全性基準

Microsoft Intune 導入了簡單的方式，來強制執行常見的基準安全性狀況提供系統管理員的安全性基準管理功能。 基準會提供類似的方法，以達到下加強設定檔工作站的鎖定。

安全的工作站，此基準不會為它的實作會與安全的組態部署發生衝突。

|   |   |   |
| :---: | :---: | :---: |
| 鎖定上 | 裝置安裝 | 遠端桌面服務問題 |
| 應用程式執行階段 | 裝置鎖定 | 遠端管理 |
| 應用程式管理 | 事件記錄服務 | 遠端程序呼叫 |
| 自動播放 | 體驗 | Search |
| BitLocker | 惡意探索防護 | Smartscreen 篩選工具 |
| [瀏覽器] | 檔案總管 | 系統需求|
| 連線能力 | Internet Explorer | Wi-Fi |
| 認證委派 | 本機原則安全性選項 | Windows 連線管理員 |
| 認證 UI | MS 安全性指南 | Windows Defender|
| 資料保護 | MSS 舊版 | Windows Ink 工作區 |
| Device Guard | 乘冪 | Windows PowerShell |

這項預覽功能的詳細資訊可在發行項[Intune 的 Windows 安全性基準設定](https://docs.microsoft.com/Intune/security-baseline-settings-windows)。

## <a name="enroll-and-validate-your-first-device"></a>註冊並驗證您的第一個裝置

1. 若要註冊您的裝置，您需要下列資訊：
   * **序號**-裝置底座上找到
   * **Windows 產品識別碼**-下找到**系統** > **關於**[Windows 設定] 功能表。
   * 執行[Get WindowsAutoPilotInfo](https://aka.ms/Autopilotshell)會提供所有必要的資訊的 CSV 雜湊檔案進行裝置註冊。 
      * 執行`Get-WindowsAutoPilotInfo – outputfile device1.csv`輸出為 CSV 檔案匯入至 Intune 的資訊。

   > [!NOTE]
   > 指令碼將會需要提高權的限，並執行做為遠端簽署。 您可以使用下列命令，以允許指令碼正確執行。 `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

   *  您可以收集此資訊來收集資訊，請登入到 Windows 10 版本 1809年或更高版本的裝置，或硬體轉銷商訂購新的裝置時，可以提供這項資訊。
1. 收集所需的資訊，並回到**Azure 入口網站**。 瀏覽至**Microsoft Intune** > **裝置註冊** > **Windows 註冊** > **裝置-管理 Windows Autopilot 裝置**，選取**匯入**，並選擇 CSV 檔案，您建立或提供。
1. 將現在已註冊的裝置新增到安全性群組**安全的工作站**稍早建立。
1. 從您想要設定 Windows 10 裝置，瀏覽至**Windows 設定** > **更新與安全性** > **復原**。 選擇**開始**下方**重設此 PC**並遵循提示來重設並重新設定使用的設定檔和合規性原則設定的裝置。

在設定裝置之後，完成檢閱，請檢查組態。 確認第一個裝置已正確設定再繼續您的部署。

## <a name="assignment-and-monitoring"></a>指派和監視

指派的裝置和使用者需要的對應[選取設定檔](https://docs.microsoft.com/intune/device-profile-assign)您安全性群組和所有新的使用者會獲得服務的權限必須加入至安全群組。

可以在監視設定檔，以使用監控[Microsoft Intune 設定檔](https://docs.microsoft.com/intune/device-profile-monitor)。

## <a name="next-steps"></a>後續步驟

[Microsoft Intune](https://docs.microsoft.com/intune/index)文件

[Azure AD](https://docs.microsoft.com/azure/active-directory/index)文件