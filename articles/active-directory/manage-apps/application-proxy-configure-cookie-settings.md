---
title: 應用程式 Proxy Cookie 設定 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) 有可透過「應用程式 Proxy」存取內部部署應用程式的存取和工作階段 Cookie。 在本文中，您將了解如何使用及設定 Cookie 設定。
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06fd83ee815e9e207c1fa5a1c6767280122c4d0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440525"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Azure Active Directory 中用於存取內部部署網站的 Cookie 設定

Azure Active Directory (Azure AD) 有可透過「應用程式 Proxy」存取內部部署應用程式的存取和工作階段 Cookie。 了解如何使用「應用程式 Proxy」Cookie 設定。 

## <a name="what-are-the-cookie-settings"></a>什麼是 Cookie 設定？

[應用程式 Proxy](application-proxy.md) 會使用下列存取和工作階段 Cookie 設定。

| Cookie 設定 | 預設值 | 描述 | 建議 |
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

## <a name="view-current-cookie-settings---powershell"></a>檢視目前的 cookie 設定-PowerShell

若要查看目前的 cookie 設定，應用程式，請使用此 PowerShell 命令：  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>設定 cookie 設定-PowerShell

在下列 PowerShell 命令中，```<ObjectId>```應用程式的 objectid。 

**僅限 http Cookie** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Secure Cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**永續性 Cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
