---
title: 新增應用程式時如何選擇要使用的應用程式類型 | Microsoft Docs
description: 了解支援與 Azure AD 整合的應用程式類型及其相關的設定選項
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e5e7ae02cfdc921d593d204151ac501c8c8ee80
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807664"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>在 Azure Active Directory 中新增應用程式時，選擇應用程式類型

了解您可以對 Azure Active Directory (Azure AD) 新增的 4 個應用程式類型。 當您在 Azure Active Directory 中新增應用程式時，系統會提示您選擇四個應用程式類型中的其中一個。

## <a name="what-are-the-types-of-applications"></a>應用程式類型是什麼？

Azure AD 支援四種主要的應用程式類型，您可以使用 [企業應用程式]  下的 [新增]  功能新增這幾種類型。 它們包括：

- **Azure AD 資源庫應用程式** – 與 Azure AD 預先整合以啟用單一登入的應用程式。

- **應用程式 Proxy** – 您想要提供從對外進行安全單一登入的應用程式 (在內部部署環境中執行)。

- **自訂開發的應用程式** – 您的組織想要在 Azure AD 應用程式開發平台上開發的應用程式，但可能尚不存在。

- **不在資源庫內的應用程式** – 引進您自己的應用程式！ 您想要的任何網頁連結，或任何呈現使用者名稱和密碼欄位的應用程式，都支援 SAML 或 OpenID Connect 通訊協定，或支援您想要與 Azure AD 整合以啟用單一登入的 SCIM。

## <a name="features-and-capabilities-supported-by-the-application-types"></a>應用程式類型所支援的功能

在 Azure AD 中，上述四種應用程式類型都支援下列功能︰

- **快速啟動** – 依照[簡單部署步驟](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)快速開始使用應用程式

- **一般屬性管理** – 取得應用程式的[直接深層連結](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)、為應用程式[自訂商標](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal)或對所有使用者[停用應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)。

- **使用者和群組管理** – 針對應用程式[指派](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)或[移除](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)使用者和群組，並選擇性地指派這些使用者和群組可存取的特定應用程式角色

- **自助應用程式存取** – 可讓您的使用者從應用程式存取面板直接新增應用程式，或[加入自助啟用的群組](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) (過程中可選擇需要商務核准)，以要求應用程式的[自助應用程式存取](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

- **單一登入** – 請參閱[應用程式的所有登入](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)(或您的所有應用程式)

- **稽核記錄** – 請參閱[有關應用程式修改的詳細稽核記錄](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) (或您的所有應用程式)

- **條件式和以風險為基礎的存取** – 設定強大的[條件式存取規則](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access)，在使用者嘗試登入特定的應用程式時強制執行

- **權限檢視** – 從單一位置檢視應用程式在您的目錄中可存取的任何 [OAuth2 權限](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>特定應用程式類型所支援的單一登入和佈建模式

下表說明前述每個應用程式類型所支援的不同單一登入和佈建模式。 請利用此表格，協助您了解需要新增哪個應用程式以支援特定目標。

  ![表格：不同的 SSO 和佈建每個應用程式類型所支援的模式](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>如何選擇單一登入模式

以下列出 Azure AD 應用程式支援的**單一登入**模式。

- **Azure AD 單一登入已停用** – 如果您還沒有準備好整合此應用程式與 Azure AD 的單一登入，或只是要測試，請選擇 Azure AD 單一登入已停用**單一登入模式**

- **連結的登入** – 如果您的應用程式已連線至現有的單一登入解決方案，或您只是想要在使用者的[應用程式存取面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)或 [Office 365 應用程式啟動程式](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)中發佈簡單連結，請選擇[連結的登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)**單一登入模式**

- **以密碼為基礎的登入** – 如果您的應用程式呈現 HTML 使用者名稱和密碼欄位，而且您想要安全地儲存該使用者名稱和密碼，以便稍後重播給應用程式，請選擇[以密碼為基礎的登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)**單一登入模式**

- **以 SAML 為基礎的登入** – 如果您的應用程式支援 SAML 或 OpenID Connect 通訊協定，或想要根據您在 SAML 宣告中定義的規則，將使用者對應至特定的應用程式角色，請選擇[以 SAML 為基礎的登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)單一登入模式 *

  >[!NOTE]
  >已設定應用程式的應用程式 Proxy 時，無法使用此選項。

- **以標頭為基礎的登入** – 如果有您想要執行單一登入的應用程式，且它使用的 PingAccess 支援以 HTTP 標頭為基礎的驗證 ，請選擇這個[以標頭為基礎的登入](application-proxy-configure-single-sign-on-with-ping-access.md)單一登入模式

  >[!NOTE]
  >只有在已設定應用程式的應用程式 Proxy 和 PingAccess 時，才能使用此選項。

- **整合式 Windows 驗證** – 當公開您想要執行單一登入的內部部署 WIA 應用程式時，請選擇[整合式 Windows 驗證](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)單一登入模式

  >[!NOTE]
  >只有在已設定應用程式的應用程式 Proxy 時，才能使用此選項。

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>自訂開發之應用程式的單一登入模式

您透過自訂開發的應用程式經驗所自訂開發的應用程式，也支援前面未列出的其他單一登入模式，包括：

- 以 [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) 為基礎的登入

- 以 [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 為基礎的登入

- 以 [WS-同盟 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) 為基礎的登入

- 以 [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) 為基礎的登入

請參閱 [Azure Active Directory 開發人員手冊](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)，以深入了解如何建立支援這些單一登入模式的自訂開發應用程式。

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>如何設定應用程式的單一登入模式

若要設定應用程式的單一登入模式，請遵循下列指示：

1. 開啟 [Azure 入口網站  ](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。
1. 按一下左側主導覽功能表底部的 [所有服務]  ，以開啟 [Azure Active Directory 延伸模組]  。
1. 在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory]  項目。
1. 在 Azure Active Directory 左側導覽功能表中，按一下 [企業應用程式]  。
1. 按一下 [所有應用程式]  ，以檢視所有應用程式的清單。

   * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單]  頂端的 [篩選]  控制項，並將 [顯示]  選項設定為 [所有應用程式]  。

1. 選取您要設定單一登入的應用程式。
1. 應用程式載入後，按一下應用程式的左側導覽功能表中的 [單一登入]  。

## <a name="how-to-choose-a-provisioning-mode"></a>如何選擇佈建模式

- **手動佈建** – 如果您有現有的帳戶，或想要在 Azure AD 外部管理這個應用程式的帳戶，請選擇[手動](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes)佈建模式。

- **自動佈建** – 如果您想要啟用以 API 為基礎自動佈建和/或取消佈建此應用程式的使用者帳戶，請選擇[自動](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning)**佈建模式** 

  >[!NOTE]
  >此功能僅適用於 [Azure AD 應用程式庫](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal)的**精選**類別內的應用程式。

- **以 SCIM 為基礎的自動佈建** – 如果您的應用程式支援 SCIM 通訊協定來偵測使用者和群組的變更 (與 Azure AD 整合的任何應用程式變更時會自動發出)，請使用[以 SCIM 為基礎的自動佈建](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) 

  >[!NOTE]
  >此選項未列為特定的佈建模式，但對於所有與 Azure AD 整合的應用程式，依預設會啟用。

## <a name="how-to-set-an-applications-provisioning-mode"></a>如何設定應用程式的佈建模式

若要設定應用程式的**佈建**模式，請遵循下列指示：

1. 開啟 [Azure 入口網站  ](https://portal.azure.com/)，然後以**全域管理員**或**共同管理員**身分登入。
1. 按一下左側主導覽功能表底部的 [所有服務]  ，以開啟 [Azure Active Directory 延伸模組]  。
1. 在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory]  項目。
1. 在 Azure Active Directory 左側導覽功能表中，按一下 [企業應用程式]  。
1. 按一下 [所有應用程式]  ，以檢視所有應用程式的清單。

   * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單]  頂端的 [篩選]  控制項，並將 [顯示]  選項設定為 [所有應用程式]  。

1. 選取您要設定佈建的應用程式。
1. 應用程式載入之後，按一下應用程式左側導覽功能表中的 [佈建]  。

## <a name="next-steps"></a>後續步驟

[使用 Azure Active Directory 管理應用程式](what-is-application-management.md)
