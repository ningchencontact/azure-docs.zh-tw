---
title: Azure Active Directory 條件式存取設定參考 | Microsoft Docs
description: 取得 Azure Active Directory 條件式存取原則中所支援設定的概觀。
services: active-directory.
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f92001e9d2fd322e858440ef64991b9de856735
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273034"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Azure Active Directory 條件式存取設定參考

您可以使用 [Azure Active Directory (Azure AD) 條件式存取](../active-directory-conditional-access-azure-portal.md)來控制已獲授權使用者存取您資源的方式。

本文會針對條件式存取原則中的下列設定選項，為您提供支援資訊：

- 雲端應用程式指派
- 裝置平台條件
- 雲端應用程式條件
- 已核准的用戶端應用程式需求

如果這不是您要尋找的資訊，請在本文結尾留言。

## <a name="cloud-apps-assignments"></a>雲端應用程式指派

使用條件式存取原則，您就能控制使用者存取您[雲端應用程式](conditions.md#cloud-apps)的方式。 當您設定條件式存取原則時，必須選取至少一個雲端應用程式。 

![選取您原則適用的雲端應用程式](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Microsoft 雲端應用程式

您可以將條件式存取原則指派給下列 Microsoft 的雲端應用程式：

- Azure Analysis Services
- Azure DevOps
- Azure SQL Database 和資料倉儲-[深入](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights 分析
- Microsoft Azure 資訊保護-[深入](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Microsoft Azure 管理-[深入](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Microsoft Azure RemoteApp
- Microsoft Azure 訂用帳戶管理
- Microsoft Cloud App Security
- Microsoft 商務工具的存取控制入口網站
- Microsoft Commerce 工具驗證服務
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Microsoft Intune 註冊
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerApps
- Microsoft Bing 搜尋
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Project Online
- 商務用 Skype Online
- 虛擬私人網路 (VPN)
- Visual Studio App Center
- Windows Defender ATP

### <a name="other-applications"></a>其他應用程式

除了 Microsoft 雲端應用程式之外，您還可以將條件式存取原則指派給下列類型的雲端應用程式：

- Azure AD 連線的應用程式
- 預先整合的同盟軟體即服務 (SaaS) 應用程式
- 使用密碼單一登入 (SSO) 的應用程式
- 企業營運應用程式
- 使用 Azure AD 應用程式 Proxy 的應用程式

## <a name="device-platform-condition"></a>裝置平台條件

在條件式存取原則中，您可以設定裝置平台條件，將原則繫結到用戶端上的作業系統。 Azure AD 條件式存取支援下列裝置平台：

- Android
- iOS
- Windows Phone
-  Windows
- macOS

![將存取原則繫結至用戶端作業系統](./media/technical-reference/41.png)

## <a name="client-apps-condition"></a>用戶端應用程式條件

在條件式存取原則中，您可以設定[用戶端應用程式](conditions.md#client-apps)條件，將原則繫結至已啟動存取嘗試的用戶端應用程式。 當有人從下列類型的用戶端應用程式嘗試存取時，將用戶端應用程式條件設定為授與或封鎖存取：

- [瀏覽器]
- 行動裝置應用程式和桌面應用程式

![控制對用戶端應用程式的存取](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>支援的瀏覽器

在您的條件式存取原則中，您可以選取 [瀏覽器] 作為用戶端應用程式。

![控制對支援之瀏覽器的存取](./media/technical-reference/05.png)

此設定適用於所有瀏覽器。 不過，為了滿足像是符合裝置需求規範的裝置原則，因此支援下列作業系統和瀏覽器：

| 作業系統                     | 瀏覽器                                      |
| :--                    | :--                                           |
| Windows 10             | Internet Explorer、Microsoft Edge、Chrome     |
| Windows 8 / 8.1        | Internet Explorer、Chrome                     |
| Windows 7              | Internet Explorer、Chrome                     |
| iOS                    | Microsoft Edge 的 safari Intune Managed 的 Browser |
| Android                | Chrome、 Microsoft Edge Intune Managed 的 Browser |
| Windows Phone          | Internet Explorer、Microsoft Edge             |
| Windows Server 2016    | Internet Explorer、Microsoft Edge             |
| Windows Server 2016    | Chrome                                        |
| Windows Server 2012 R2 | Internet Explorer、Chrome                     |
| Windows Server 2008 R2 | Internet Explorer、Chrome                     |
| macOS                  | Chrome、Safari                                |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>為什麼看到提示瀏覽器中的憑證

Windows 7、 iOS、 Android 及 macOS 上 Azure AD 會使用與 Azure AD 註冊裝置時，會佈建用戶端憑證將裝置識別。  當使用者第一次登入時透過瀏覽器會提示使用者選取的憑證。 使用者必須選取此憑證，才能使用瀏覽器。

#### <a name="chrome-support"></a>Chrome 支援

如需 **Windows 10 Creators Update (1703 版)** 或更新版本中的 Chrome 支援，請安裝[這個擴充功能](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。

若要自動將此擴充功能部署到 Chrome 瀏覽器，請建立下列登錄機碼：

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| 名稱 | 1 |
| 類型 | REG_SZ (字串) |
| 資料 | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx |

如需 **Windows 8.1 和 7** 中的 Chrome 支援，請建立下列登錄機碼：

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| 名稱 | 1 |
| 類型 | REG_SZ (字串) |
| 資料 | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

這些瀏覽器支援裝置驗證，因此可以根據原則來識別和驗證裝置。 如果瀏覽器在私用模式中執行，裝置檢查將會失敗。

### <a name="supported-mobile-applications-and-desktop-clients"></a>支援的行動裝置應用程式和桌面用戶端

在您的條件式存取原則中，您可以選取 [行動裝置應用程式和桌面用戶端] 作為用戶端應用程式。

![控制對支援之行動裝置應用程式或桌面用戶端的存取](./media/technical-reference/06.png)

這項設定會影響從下列行動裝置應用程式和桌面用戶端進行的存取嘗試：

| 用戶端應用程式 | 目標服務 | 平台 |
| --- | --- | --- |
| Azure 遠端應用程式 | Azure 遠端應用程式服務 | Windows 10、Windows 8.1、Windows 7、iOS、Android 及 macOS |
| Dynamics CRM 應用程式 | Dynamics CRM | Windows 10、Windows 8.1、iOS 和 Android |
| [電子郵件]/[行事曆]/[人員] 應用程式、Outlook 2016、Outlook 2013 (使用新式驗證)| Office 365 Exchange Online | Windows 10 |
| 應用程式的 MFA 和位置原則。 不支援裝置型原則。| 任何 My Apps 應用程式服務| Android 和 iOS |
| Microsoft Teams Services - 這會控制支援 Microsoft Teams 及其所有用戶端應用程式的所有服務 - Windows 桌面、iOS、Android、WP 和 Web 用戶端 | Microsoft Teams | Windows 10、Windows 8.1、Windows 7、iOS、Android 及 macOS |
| Office 2016 應用程式、Office 2013 (具備新式驗證)、OneDrive 同步處理用戶端 (請參閱[附註](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8.1、Windows 7 |
| Office 2016 應用程式、通用 Office 應用程式、Office 2013 (具備新式驗證)、OneDrive 同步處理用戶端 (請參閱[附註](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))、預計未來提供的 Office Groups 支援、預計未來提供的 SharePoint 應用程式支援 | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (僅限 Word、Excel、PowerPoint、OneNote)。 未來規劃支援商務用 OneDrive| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | Windows 10，macOS |
| Office 行動應用程式 | Office 365 SharePoint Online | Android、iOS |
| Office Yammer 應用程式 | Office 365 Yammer | Windows 10、iOS、Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10，macOS |
| Outlook 2016 (macOS 版 Office) | Office 365 Exchange Online | macOS |
| Outlook 2016、Outlook 2013 (已啟用新式驗證)、商務用 Skype (採用新式驗證) | Office 365 Exchange Online | Windows 8.1、Windows 7 |
| Outlook 行動應用程式 | Office 365 Exchange Online | Android、iOS |
| Power BI 應用程式 | Power BI 服務 | Windows 10、 Windows 8.1、 Windows 7、 Android 和 iOS |
| 商務用 Skype | Office 365 Exchange Online| Android、iOS |
| Visual Studio Team Services 應用程式 | Visual Studio Team Services | Windows 10、Windows 8.1、Windows 7、iOS 和 Android |

## <a name="support-for-legacy-authentication"></a>支援舊版驗證

藉由選取 [其他用戶端]，您可以指定會影響搭配使用基本驗證和郵件通訊協定 (如 IMAP、MAPI、POP、SMTP) 之應用程式的條件，這些條件也會影響不是使用新式驗證的舊版 Office 應用程式。  

![其他用戶端](./media/technical-reference/11.png)

如需詳細資訊，請參閱[用戶端應用程式](conditions.md#client-apps)。

## <a name="approved-client-app-requirement"></a>核准的用戶端應用程式需求

在條件式存取原則中，您可以要求從已核准用戶端應用程式對所選取雲端應用程式進行存取嘗試。 

![控制對經過核准之用戶端應用程式的存取](./media/technical-reference/21.png)

此設定適用於下列用戶端應用程式：

- Microsoft Azure 資訊保護
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune 受控瀏覽器
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft 商務用 Skype
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**備註**

- 經過核准的用戶端應用程式支援 Intune 行動應用程式管理功能。
- **需要經過核准的用戶端應用程式**需求：
   - 僅支援[裝置平台條件](#device-platform-condition)適用的 iOS 和 Android。

## <a name="app-protection-policy-requirement"></a>應用程式保護原則需求 

在條件式存取原則中，您可以要求應用程式保護原則會出現在用戶端應用程式，才能存取適用於選取的雲端應用程式。 

![使用應用程式保護原則來控制存取](./media/technical-reference/22.png)

此設定適用於下列用戶端應用程式：

- Microsoft OneDrive
- Microsoft Outlook

**備註**

- 應用程式保護原則的應用程式支援 Intune 行動應用程式管理功能，使用原則保護。
- **需要應用程式保護原則**需求：
    - 僅支援[裝置平台條件](#device-platform-condition)適用的 iOS 和 Android。

## <a name="next-steps"></a>後續步驟

- 如需條件式存取的概觀，請參閱[什麼是 Azure Active Directory 中的條件式存取？](../active-directory-conditional-access-azure-portal.md)
- 如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的建議做法](best-practices.md)。

<!--Image references-->
[1]: ./media/technical-reference/01.png
