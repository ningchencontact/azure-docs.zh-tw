---
title: 保護您的 Azure Active Directory Domain Services 受控網域 | Microsoft Docs
description: 保護受控網域
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 923ecae9dc649b8f5cdcfd447b78fdec0805927a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879156"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>保護您的 Azure AD Domain Services 受控網域
本文將協助您保護受控網域。 您可以關閉弱式加密套件的使用，並停用 NTLM 認證雜湊同步處理。

## <a name="install-the-required-powershell-modules"></a>安裝必要的 PowerShell 模組

### <a name="install-and-configure-azure-ad-powershell"></a>安裝並設定 Azure AD PowerShell
請遵循文章中的指示[安裝 Azure AD PowerShell 模組並連線至 Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。

### <a name="install-and-configure-azure-powershell"></a>安裝並設定 Azure PowerShell
請遵循文章中的指示[安裝 Azure PowerShell 模組並連線至 Azure 訂用帳戶](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>停用弱式加密套件和 NTLM 認證雜湊同步處理
使用下列 PowerShell 指令碼執行：

1. 停用受控網域上的 NTLM v1 支援。
2. 從您的內部部署 AD 停用 NTLM 密碼雜湊同步處理。
3. 在受控網域上停用 TLS v1。

如果您收到錯誤訊息, 指出`Get-AzResource` *Microsoft AAD/DomainServices*資源不存在, 請[提高您的存取權以管理所有 Azure 訂用帳戶和管理群組](../role-based-access-control/elevate-access-global-admin.md)。

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

> [!IMPORTANT]
> 如果您已在 Azure AD Domain Services 實例上停用 NTLM 密碼雜湊同步處理, 使用者 (和服務帳戶) 就無法執行 LDAP 簡單系結。  如需停用 NTLM 密碼雜湊同步處理的詳細資訊, 請參閱[保護您的 Azure AD 網域服務受控網域](secure-your-domain.md)。
>
>

## <a name="next-steps"></a>後續步驟
* [了解 Azure AD Domain Services 中的同步處理](synchronization.md)
