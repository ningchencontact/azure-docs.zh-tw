---
title: 使用租用戶限制來管理對 SaaS 雲端應用程式-Azure |Microsoft Docs
description: 如何使用租用戶限制來管理哪些使用者可以存取其 Azure AD 租用戶為基礎的應用程式。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a340663a1ec4ddf748c6dc2bc3a4e2ce0c4228e
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824389"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>使用租用戶限制來管理對 SaaS 雲端應用程式的存取權

注重安全性的大型組織想要移到 Office 365 之類的雲端服務，但需要確知其使用者只能存取獲得核准的資源。 傳統上，當公司想要管理存取時，會限制網域名稱或 IP 位址。 在公用雲端，在共用的網域名稱，例如軟體即服務 （簡稱 SaaS） 應用程式的裝載位置的世界中，這個方法行不通[outlook.office.com](https://outlook.office.com/)並[login.microsoftonline.com](https://login.microsoftonline.com/)。 封鎖這些位址會讓使用者無法存取整個網路上的 Outlook，而不僅是限制他們只能存取已核准的身分識別和資源。

這項挑戰的 Azure Active Directory (Azure AD) 解決方案是稱為租用戶限制 」 的功能。 租用戶限制，組織可以控制對 SaaS 雲端應用程式，根據應用程式使用單一登入 Azure AD 租用戶的存取。 例如，您可能想要允許使用者存取您組織的 Office 365 應用程式，但又防止他們存取其他組織的這些相同應用程式執行個體。  

租用戶限制，組織可以指定允許使用者存取的租用戶的清單。 Azure AD 接著便可只授與對這些已允許之租用戶的存取權。

本文著重於 Office 365 租用戶限制，但此功能應使用任何與 Azure AD 單一登入使用新式驗證通訊協定的 SaaS 雲端應用程式。 如果您使用 SaaS 應用程式搭配與 Office 365 所用租用戶不同的 Azure AD 租用戶，請務必核准所有必要的租用戶。 如需有關 SaaS 雲端應用程式的詳細資訊，請參閱 [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)。

## <a name="how-it-works"></a>運作方式

整體解決方案包含下列元件：

1. **Azure AD**：如果`Restrict-Access-To-Tenants: <permitted tenant list>`為允許的租用戶的存在，Azure AD 只發出安全性權杖。

2. **在內部部署 proxy 伺服器基礎結構**:此基礎結構是支援 Secure Sockets Layer (SSL) 檢查的 proxy 裝置。 您必須設定要插入到目的地為 Azure AD 的流量包含允許的租用戶清單的標頭的 proxy。

3. **用戶端軟體**:若要支援租用戶限制，用戶端軟體必須要求權杖直接從 Azure AD 中，以便 proxy 基礎結構可以攔截流量。 瀏覽器為基礎的 Office 365 應用程式目前支援租用戶限制，一樣是使用新式驗證 （例如 OAuth 2.0) 的 Office 用戶端。

4. **新式驗證**:雲端服務必須使用新式驗證來使用租用戶限制，並封鎖對所有非允許之租用戶存取。 您必須設定為預設使用新式驗證通訊協定的 Office 365 雲端服務。 如需有關 Office 365 對新式驗證之支援的最新資訊，請參閱[更新的 Office 365 新式驗證 (英文)](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。

下圖說明概略的流量流程。 租用戶限制需要只在資料傳輸至 Azure AD，而非 Office 365 雲端服務上的 SSL 檢查。 這項區別很重要，，因為向 Azure AD 進行驗證的流量通常遠比對 SaaS 應用程式，例如 Exchange Online 和 SharePoint Online 的流量。

![租用戶限制流量流程-圖表](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>設定租用戶限制

有兩個步驟，若要開始使用租用戶限制。 首先，請確定您的用戶端可以連線到正確的位址。 第二，設定您的 proxy 基礎結構。

### <a name="urls-and-ip-addresses"></a>URL 和 IP 位址

若要使用租用戶限制，您的用戶端必須能夠連線到下列的 Azure AD Url 來進行驗證： [login.microsoftonline.com](https://login.microsoftonline.com/)， [login.microsoft.com](https://login.microsoft.com/)，和[login.windows.net](https://login.windows.net/)。 此外，若要存取 Office 365，您的用戶端也必須能夠連線到完整的網域名稱 (Fqdn) Url，並定義中的 IP 位址[Office 365 Url 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)。 

### <a name="proxy-configuration-and-requirements"></a>Proxy 組態和需求

下列組態，才能啟用 透過 proxy 基礎結構的租用戶限制。 本指導方針是通用的，因此如需了解特定的實作步驟，您應該參考您 Proxy 廠商的文件。

#### <a name="prerequisites"></a>必要條件

- Proxy 必須要能夠執行 SSL 攔截、HTTP 標頭插入，以及使用 FQDN/URL 來篩選目的地。

- 用戶端必須信任 Proxy 針對 SSL 通訊所出示的憑證鏈結。 比方說，如果從內部憑證[公開金鑰基礎結構 (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure)會使用內部發行的根憑證授權單位憑證必須受到信任。

- 這項功能納入 Office 365 訂用帳戶，但如果您想要使用的租用戶限制來控制對其他 SaaS 應用程式的存取，然後 Azure AD Premium 1 所需授權。

#### <a name="configuration"></a>組態

針對每個傳送到 login.microsoftonline.com、login.microsoft.com 及 login.windows.net 的連入要求，請插入兩個 HTTP 標頭：*Restrict-Access-To-Tenants* 和 *Restrict-Access-Context*。

這些標頭應該包含下列元素︰

- 針對*限制存取-至-租用戶*，使用值\<允許租用戶清單\>，這是您想要允許使用者存取的租用戶的逗號分隔清單。 與租用戶一起註冊的任何網域都可用來在此清單中識別該租用戶。 例如，若要允許存取 Contoso 和 Fabrikam 租用戶，名稱/值組會顯示如下： `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- 針對*限制存取內容*，使用單一目錄識別碼的值，宣告所在的租用戶會設定租用戶限制。 例如，若要宣告 Contoso 作為設定 「 租用戶限制 」 原則的租用戶，名稱/值組看起來像： `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> 您可以找到您的目錄識別碼，在[Azure Active Directory 入口網站](https://aad.portal.azure.com/)。 請以系統管理員身分登入，選取 [Azure Active Directory]，然後選取 [屬性]。

若要避免使用者插入自己的 HTTP 標頭與非核准租用戶，proxy 必須取代*限制存取-至-租用戶*如果已經存在連入要求中的標頭。

必須強制用戶端針對所有傳送到 login.microsoftonline.com、login.microsoft.com 及 login.windows.net 的要求使用 Proxy。 比方說，如果將使用 proxy 的用戶端導向使用 PAC 檔案，使用者應該不能編輯或停用 PAC 檔案。

## <a name="the-user-experience"></a>使用者體驗

本節說明使用者和系統管理員的體驗。

### <a name="end-user-experience"></a>使用者體驗

範例使用者位於 Contoso 網路上，但正在嘗試存取 Fabrikam 的共用 SaaS 應用程式執行個體 (例如 Outlook Online)。 如果 Fabrikam Contoso 執行個體的非允許租用戶，則使用者會看到存取拒絕訊息，指出您嘗試存取屬於組織 IT 部門所未核准的資源。

### <a name="admin-experience"></a>管理員體驗

雖然租用戶限制的設定會對公司 proxy 基礎結構，系統管理員就可以直接存取 Azure 入口網站中的租用戶限制 」 報告。 若要檢視報表：

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)。 **Azure Active Directory 系統管理中心**儀表板隨即出現。

2. 在左窗格中，選取 [Azure Active Directory]。 Azure Active Directory 的 [概觀] 頁面隨即出現。

3. 在 **其他功能**標題之下，選取**租用戶限制**。

指定為受限制存取內容的租用戶可以使用此報告以查看登入遭到封鎖，因為 「 租用戶限制 」 原則，包括使用的身分識別租用戶系統管理員和目標目錄識別碼。 如果設定限制的租用戶為登入的使用者租用戶或資源租用戶，則會包含登入。

與 Azure 入口網站中的其他報告相同，您可以使用篩選來指定報告的範圍。 您可以篩選特定的時間間隔、 使用者、 應用程式、 用戶端或狀態。 如果您選取**資料行** 按鈕，您可以選擇顯示下列欄位的任何組合的資料：

- **使用者**
- **應用程式**
- **狀態**
- **Date**
- **日期 (UTC)** （UTC 是國際標準時間）
- **MFA 驗證方法**（多重要素驗證方法）
- **MFA 驗證詳細資料**（多重要素驗證詳細資料）
- **MFA 結果**
- **IP 位址**
- **用戶端**
- **使用者名稱**
- **位置**
- **目標租用戶識別碼**

## <a name="office-365-support"></a>Office 365 支援

Office 365 應用程式必須符合租用戶限制的完整支援的兩個準則：

1. 使用用戶端支援新式驗證。
2. 啟用新式驗證作為雲端服務的預設驗證通訊協定。

如需有關哪些 Office 用戶端目前支援新式驗證的最新資訊，請參閱[更新的 Office 365 新式驗證 (英文)](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。 該頁面也包含在特定 Exchange Online 和「商務用 Skype Online」租用戶上啟用新式驗證的指示連結。 SharePoint Online 已啟用新式驗證預設。

Office 365 瀏覽器為基礎的應用程式 （Office Portal、 Yammer、 SharePoint 網站、 Outlook Web 等等） 目前支援 「 租用戶限制。 豐富型用戶端 （Outlook、 Skype for Business、 Word、 Excel、 PowerPoint 和更多功能） 只有在使用新式驗證時，可以強制執行租用戶限制。  

Outlook 及 Skype for Business 支援新式驗證的用戶端可能仍然能夠使用舊版的通訊協定，對租用戶未啟用新式驗證的位置，有效地略過租用戶限制。 租用戶限制可能會封鎖使用傳統通訊協定，如果它們在驗證期間連絡 login.microsoftonline.com、 login.microsoft.com 或 login.windows.net 的應用程式。

針對 Windows 上的 Outlook，客戶可以選擇實作可防止使用者將非已核准郵件帳戶新增至其設定檔的限制。 例如，請參閱[防止新增非預設 Exchange 帳戶](https://gpsearch.azurewebsites.net/default.aspx?ref=1)群組原則設定。

## <a name="testing"></a>測試

如果您想要試用租用戶限制為整個組織實作之前，您會有兩個選項： 主控件為基礎的方法，使用 Fiddler 或分段的推出 proxy 設定之類的工具。

### <a name="fiddler-for-a-host-based-approach"></a>適用於主機型方法的 Fiddler

Fiddler 是一個免費的 Web 偵錯 Proxy，可用來擷取和修改 HTTP/HTTPS 流量，包括插入 HTTP 標頭。 若要設定 Fiddler 以測試 「 租用戶限制，請執行下列步驟：

1. [下載並安裝 Fiddler](https://www.telerik.com/fiddler)。

2. 依照 [Fiddler 的說明文件 (英文)](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS) 操作，設定 Fiddler 以將 HTTPS 流量解密。

3. 設定 Fiddler 以使用自訂規則來插入 *Restrict-Access-To-Tenants* 和 *Restrict-Access-Context* 標頭：

   1. 在「Fiddler Web 偵錯工具」中，選取 [Rules] \(規則) 功能表，然後選取 [Customize Rules] \(自訂規則) 以開啟 CustomRules 檔案。

   2. 在開頭新增下列行`OnBeforeRequest`函式。 取代\<租用戶網域\>與 註冊您的租用戶網域 (例如`contoso.onmicrosoft.com`)。 使用您租用戶的 Azure AD GUID 識別碼來取代 \<directory ID\>。

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      如果您需要允許多個租用戶，請使用逗號來分隔租用戶名稱。 例如：

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. 儲存並關閉 CustomRules 檔案。

設定 Fiddler 之後，您可以移至 [File] \(檔案) 功能表並選取 [Capture Traffic] \(擷取流量)，來擷取流量。

### <a name="staged-rollout-of-proxy-settings"></a>分段推出 Proxy 設定

視您 Proxy 基礎結構的功能而定，您可能可以向使用者分段推出設定。 以下是一些可供考量的概略選項：

1. 使用 PAC 檔案將測試使用者指向測試 Proxy 基礎結構，同時又讓一般使用者繼續使用生產環境 Proxy 基礎結構。
2. 有些 Proxy 伺服器可以使用群組來支援不同的組態。

特定的詳細資訊，請參閱您的 proxy 伺服器文件。

## <a name="next-steps"></a>後續步驟

- 閱讀[更新的 Office 365 新式驗證 (英文)](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- 檢閱 [Office 365 URL 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
