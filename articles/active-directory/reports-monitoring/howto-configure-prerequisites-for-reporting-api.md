---
title: 存取 Azure Active Directory 報告 API 的必要條件 | Microsoft Docs
description: 了解存取 Azure AD 報告 API 的必要條件
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ab30dfeccc4ae1c7b09a8f48846ffb9e71cfc23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438945"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>存取 Azure Active Directory 報告 API 的必要條件

[Azure Active Directory (Azure AD) 報告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 透過一組以 REST 為基礎的 API 為您提供資料的程式設計方式存取。 您可以從各種程式設計語言和工具呼叫這些 API。

報告 API 會使用 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 授權存取 Web API。

若要準備存取報告 API，您必須︰

1. [指派角色](#assign-roles)
2. [註冊應用程式](#register-an-application)
3. [授與權限](#grant-permissions)
4. [收集組態設定](#gather-configuration-settings)

## <a name="assign-roles"></a>指派角色

若要透過 API 來存取報告資料，您必須已具備下列其中一個指派的角色︰

- 安全性讀取者

- 安全性系統管理員

- 全域管理員


## <a name="register-an-application"></a>註冊應用程式

即使您使用指令碼來存取報告 API，也必須註冊應用程式。 註冊可讓您取得**應用程式識別碼**，您必須有此識別碼才能進行授權呼叫，以便讓程式碼獲得權杖。

若要設定您的目錄以存取 Azure AD 報告 API，您必須以 Azure 管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)，而且該帳戶同時也是 Azure AD 租用戶**全域管理員**目錄角色的成員。

> [!IMPORTANT]
> 在具有管理員權限的認證之下執行的應用程式會非常強大，請務必將應用程式的識別碼和祕密認證保存在安全的位置。
> 

**若要註冊 Azure AD 應用程式：**

1. 在 [Azure 入口網站](https://portal.azure.com) 的左導覽窗格中，選取 [Azure Active Directory]  。
   
    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 在 [Azure Active Directory]  頁面中，選取 [應用程式註冊]  。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. 從 [應用程式註冊]  頁面，選取 [新增應用程式註冊]  。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. 在 [建立]  頁面中，執行下列步驟：

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. 在 [名稱]  文字方塊中，輸入 `Reporting API application`。

    b. 對於 [應用程式類型]  ，選取 [Web 應用程式/API]  。

    c. 在 [登入 URL]  文字方塊中，輸入 `https://localhost`。

    d. 選取 [建立]  。 


## <a name="grant-permissions"></a>授與權限 

根據您想要存取的 API，您需要授與應用程式下列權限：  

| API | 權限 |
| --- | --- |
| Windows Azure Active Directory | 讀取目錄資料 |
| Microsoft Graph | 讀取所有稽核記錄資料 |


![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/36.png)

下節列出適用於這兩個 API 的步驟。 如果您不想存取其中一個 API，可以跳過相關步驟。

**若要對應用程式授與 API 使用權限：**

1. 從 [應用程式註冊]  頁面中選取您的應用程式，然後選取 [設定]  。 

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. 在 [設定]  頁面上，選取 [必要權限]  。 

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. 在 [必要權限]  頁面中，按一下 [API]  清單中的 [Windows Azure Active Directory]  。 

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. 在 [啟用存取]  頁面中，選取 [讀取目錄資料]  ，並取消選取 [登入及讀取使用者設定檔]  。 

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. 在頂端工具列中，按一下 [儲存]  。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. 在 [必要權限]  頁面頂端的工具列中，按一下 [新增]  。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. 在 [新增 API 存取權]  頁面中，按一下 [選取 API]  。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. 在 [選取 API]  頁面中，按一下 [Microsoft Graph]  ，然後按一下 [選取]  。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. 在 [啟用存取]  頁面中，選取 [讀取所有稽核記錄資料]  ，然後按一下 [選取]  。  

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. 在 [新增 API 存取權]  頁面中，按一下 [完成]  。  

11. 在 [必要權限]  頁面頂端的工具列中， 按一下 [授與權限]  ，然後按一下 [是]  。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>收集組態設定 

本節說明如何從您的目錄取得下列設定︰

- 網域名稱
- 用戶端識別碼
- 用戶端密碼

您在設定報告 API 的呼叫時需要這些值。 

### <a name="get-your-domain-name"></a>取得您的網域名稱

**若要取得網域名稱：**

1. 在 [Azure 入口網站](https://portal.azure.com) 的左導覽窗格上，選取 [Azure Active Directory]  。
   
    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 在 [Azure Active Directory]  頁面中，選取 [自訂網域名稱]  。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. 從網域清單中複製網域名稱。


### <a name="get-your-applications-client-id"></a>取得應用程式的用戶端識別碼

**若要取得應用程式的用戶端識別碼：**

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，按一下 [Azure Active Directory]  。
   
    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 從 [應用程式註冊]  頁面中選取您的應用程式。

3. 從應用程式頁面，瀏覽至 [應用程式識別碼]  並選取 [按一下以複製]  。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>取得應用程式的用戶端祕密
若要取得應用程式的用戶端密碼，您需要建立新的金鑰並在儲存新金鑰時儲存其值，因為稍後不可能再擷取此值。

**若要取得應用程式的用戶端祕密：**

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，按一下 [Azure Active Directory]  。
   
    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  從 [應用程式註冊]  頁面中選取您的應用程式。

3. 在應用程式頁面頂端的工具列中，選取 [設定]  。 

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. 在 [設定]  頁面的 [API 存取]  區段中，按一下 [金鑰]  。 

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. 在 [金鑰]  頁面中，執行下列步驟︰

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. 在 [描述]  文字方塊中，輸入 `Reporting API`。

    b. 選取 [2 年後]  來作為 [到期]  。

    c. 按一下 [儲存]  。

    d. 複製金鑰值。

## <a name="troubleshoot-errors-in-the-reporting-api"></a>針對報告 API 中的錯誤進行疑難排解

這一節列出當您使用 MS Graph API 存取活動報告時，可能會遇到的常見錯誤訊息，以及其解決方式的步驟。

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>存取 Microsoft Graph V2 端點時發生 500 HTTP 內部伺服器錯誤

我們目前不支援 Microsoft Graph v2 端點 - 請務必使用 Microsoft Graph v1 端點來存取活動記錄。

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Error:無法從 AD Graph 取得使用者角色

當嘗試使用 Graph 總管存取登入時，您可能會遇到這個錯誤訊息。 請確定您同時使用 Graph 總管 UI 中的兩個登入按鈕來登入帳戶，如下圖所示。 

![Graph 總管](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Error:無法從 AD Graph 進行進階授權檢查 

如果您在嘗試使用 Graph 總管存取登入時，遇到這個錯誤訊息，請在您帳戶左側導覽列底下選擇 [修改權限]  ，然後選取 [Tasks.ReadWrite]  和 [Directory.Read.All]  。 

![修改權限 UI](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Error:沒有任何 B2C 租用戶，或租用戶沒有進階授權

存取登入報表需要 Azure Active Directory 進階 1 (P1) 授權。 如果您在存取登入時看到這個錯誤訊息，請確定您的租用戶已獲得 Azure AD P1 授權。

### <a name="error-user-is-not-in-the-allowed-roles"></a>Error:使用者不是允許的角色 

如果您在嘗試使用 API 存取稽核記錄或登入時，看到這個錯誤訊息，請確定您的帳戶屬於 Azure Active Directory 租用戶中的**安全性讀者**或**報告讀者**角色。 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Error:應用程式遺漏 AAD「讀取目錄資料」權限 

請遵循[存取 Azure Active Directory 報告 API 的必要條件](howto-configure-prerequisites-for-reporting-api.md)中的步驟，以確保應用程式是以正確的權限集執行。 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Error:應用程式遺漏 MS Graph API「讀取所有稽核記錄資料」權限

請遵循[存取 Azure Active Directory 報告 API 的必要條件](howto-configure-prerequisites-for-reporting-api.md)中的步驟，以確保應用程式是以正確的權限集執行。 

## <a name="next-steps"></a>後續步驟

* [使用 Azure Active Directory 報告 API 搭配憑證來取得資料](tutorial-access-api-with-certificates.md)
* [稽核 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [登入活動報告 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
