---
title: "Azure Active Directory Domain Services：啟用 Kerberos 限制委派 | Microsoft Docs"
description: "在 Azure Active Directory Domain Services 受控網域上啟用 Kerberos 限制委派"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: maheshu
ms.openlocfilehash: b09c725609fe866b0c9ba2f5b5789e00f808b1ab
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>在受管理的網域上設定 Kerberos 限制委派 (KCD)
許多應用程式都需要以使用者的登入身分存取資源。 Active Directory 支援稱為 Kerberos 委派的機制，可用於此使用案例。 再者，您可以限制委派，讓使用者的登入身分只能用來存取特定資源。 Azure AD Domain Services 受控網域與傳統Active Directory 網域不同，因為前者的權限更為嚴格且更為安全。

本文示範如何在 Azure AD 網域服務受管理的網域上設定 Kerberos 限制委派。

## <a name="kerberos-constrained-delegation-kcd"></a>Kerberos 限制委派 (KCD)
Kerberos 委派可讓帳戶模擬另一個安全性主體 (例如使用者) 以存取資源。 考慮一個可以使用者登入身分存取後端 Wb API 的 Web 應用程式。 在此案例中，Web 應用程式 (以服務帳戶身分或電腦/機器帳戶身分執行) 在存取資源 (後端 Web API) 時會模擬使用者。 Kerberos 委派不安全，因為它不會限制使用者登入身分中的模擬帳戶可存取的資源。

Kerberos 限制委派 (KCD) 會限制指定的伺服器可代表使用者存取的服務/資源。 傳統 KCD 需要網域系統管理員權限才能設定服務的網域帳戶，而且會將該帳戶限制在單一網域。

傳統 KCD 也有一些問題。 在舊版的作業系統，如果網域系統管理員設定以帳戶為基礎的 KCD 的服務，服務系統管理員將必須沒有實用的方式可以得知哪些前端服務委派給他們所擁有的資源服務。 此外，任何可委派給資源服務的前端服務都會暴露一個潛在的受攻擊點。 若裝載前端服務的伺服器被入侵，而且它是設定為委派給資源服務，則資源服務可能也會被入侵。

> [!NOTE]
> 在 Azure AD Domain Services 受控網域上，您沒有網域系統管理員權限。 因此，**無法在受控網域上設定傳統 KCD**。 使用此文章中所述的資源型 KCD。 此機制也比較安全。
>
>

## <a name="resource-based-kcd"></a>以資源為基礎的 KCD
服務系統管理員能夠從 Windows Server 2012 及更新版本設定其務的限制委派。 在此模型中，後端服務系統管理員可以允許或拒絕特定的前端服務使用 KCD。 這個模式又稱為**資源型 KCD**。

資源型 KCD 是使用 PowerShell 所設定的。 您使用`Set-ADComputer`或`Set-ADUser`cmdlet，根據模擬的帳戶是電腦帳戶或使用者帳戶/服務帳戶。

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>為受控網域上的電腦帳戶設定資源型 KCD
假設您有在電腦 'contoso100-webapp.contoso100.com' 上執行的 Web 應用程式。 它需要以網域使用者的登入身分存取資源 ('contoso100-api.contoso100.com' 上執行的 Web API)。 以下是針對此案例設定資源型 KCD 的方式。

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>為受控網域上的使用者帳戶設定資源型 KCD
假設您有以服務帳戶 'appsvc' 執行的 Web 應用程式，且它需要以網域使用者登入身分存取資源 (以服務帳戶 - 'backendsvc' 執行的 Web API)。 以下是針對此案例設定資源型 KCD 的方式。

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

## <a name="related-content"></a>相關內容
* [Azure AD Domain Services - 入門指南](active-directory-ds-getting-started.md)
* [Kerberos 限制委派概觀](https://technet.microsoft.com/library/jj553400.aspx)
