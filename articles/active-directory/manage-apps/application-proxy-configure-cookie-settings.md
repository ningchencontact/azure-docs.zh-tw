---
title: 應用程式 Proxy Cookie 設定 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) 有可透過「應用程式 Proxy」存取內部部署應用程式的存取和工作階段 Cookie。 在本文中，您將了解如何使用及設定 Cookie 設定。
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: concept
ms.date: 01/16/2019
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 9c5979357532bb29f8e3545db57aa32603763dc1
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54855596"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Azure Active Directory 中用於存取內部部署網站的 Cookie 設定

Azure Active Directory (Azure AD) 有可透過「應用程式 Proxy」存取內部部署應用程式的存取和工作階段 Cookie。 了解如何使用「應用程式 Proxy」Cookie 設定。 

## <a name="what-are-the-cookie-settings"></a>什麼是 Cookie 設定？

[應用程式 Proxy](application-proxy.md) 會使用下列存取和工作階段 Cookie 設定。

| Cookie 設定 | 預設值 | 說明 | 建議 |
| -------------- | ------- | ----------- | --------------- |
| 使用僅限 HTTP Cookie | **否** | 使用 [是] 時，可讓「應用程式 Proxy」在 HTTP 回應標頭中包含 HTTPOnly 旗標。 此旗標提供額外的安全性優點，例如可防止用戶端指令碼處理 (CSS) 複製或修改 Cookie。<br></br><br></br>在我們支援「僅限 HTTP」設定之前，「應用程式 Proxy」是透過受保護的 SSL 通道來加密和傳輸 Cookie，以防止修改。 | 為了獲得額外的安全性優點，請使用 [是]。<br></br><br></br>針對不需要存取工作階段 Cookie 的用戶端或使用者代理程式，請使用 [否]。 例如，針對透過「應用程式 Proxy」連線到「遠端桌面閘道伺服器」地 RDP 或 MTSC 用戶端，請使用 [否]。|
| 使用安全的 Cookie | **否** | 使用 [是] 時，可讓「應用程式 Proxy」在 HTTP 回應標頭中包含 Secure 旗標。 安全 Cookie 可藉由透過 TLS 安全防護通道 (例如 HTTPS) 傳輸 Cookie 來提升安全性。 這可防止因以純文字傳輸 Cookie 而讓未經授權的對象得以看見 Cookie。 | 為了獲得額外的安全性優點，請使用 [是]。|
| 使用永續性 Cookie | **否** | 使用 [是] 時，可讓「應用程式 Proxy」將其存取 Cookie 設定為在網頁瀏覽器關閉時不過期。 永續性會持續到存取權杖到期為止，或直到使用者手動刪除永續性 Cookie 為止。 | 由於讓使用者保持在已獲授權狀態會帶來安全性風險，因此請使用 [否]。<br></br><br></br>建議只有針對無法在處理序之間共用 Cookie 的舊版應用程式，才使用 [是]。 最好是更新您的應用程式以處理處理序之間的 Cookie 共用，而不要使用永續性 Cookie。 例如，您可能需要使用永續性 Cookie，才能允許使用者從 SharePoint 網站以總管檢視開啟 Office 文件。 在沒有永續性 Cookie 的情況下，如果未在瀏覽器、總管處理序及 Office 處理序之間共用存取 Cookie，此作業就會失敗。 |

## <a name="set-the-cookie-settings---azure-portal"></a>設定 Cookie 設定 - Azure 入口網站
使用 Azure 入口網站來設定 Cookie 設定：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 瀏覽至 [Azure Active Directory] > [企業應用程式] **** > [所有應用程式] ****。
3. 選取您要啟用 Cookie 設定的應用程式。
4. 按一下 [應用程式 Proxy]。
5. 在 [其他設定] 底下，將 Cookie 設定設為 [是] 或 [否]。
6. 按一下 [儲存] 套用變更。 

<!---

## View current cookie settings - PowerShell

To see the current cookie settings for the application, use this PowerShell command:  

```PowerShell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## Set cookie settings - PowerShell

In the following PowerShell commands, ```<ObjectId>``` is the ObjectId of the application. 

**Http-Only Cookie** 

```PowerShell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Secure Cookie**

```PowerShell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Persistent Cookies**

```PowerShell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```

-->
