---
title: 保護您的 Azure Active Directory Domain Services 受控網域 | Microsoft Docs
description: 保護受控網域
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: maheshu
ms.openlocfilehash: 20579f7abd6cd815377c3e97d820a3e5490e0f95
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902514"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>保護您的 Azure AD Domain Services 受控網域
本文將協助您保護受控網域。 您可以關閉弱式加密套件的使用，並停用 NTLM 認證雜湊同步處理。

## <a name="install-the-required-powershell-modules"></a>安裝必要的 PowerShell 模組

### <a name="install-and-configure-azure-ad-powershell"></a>安裝並設定 Azure AD PowerShell
請遵循文章中的指示[安裝 Azure AD PowerShell 模組並連線至 Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。

### <a name="install-and-configure-azure-powershell"></a>安裝並設定 Azure PowerShell
請遵循文章中的指示[安裝 Azure PowerShell 模組並連線至 Azure 訂用帳戶](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>停用弱式加密套件和 NTLM 認證雜湊同步處理
使用下列 PowerShell 指令碼執行：
1. 停用受控網域上的 NTLM v1 支援。
2. 從您的內部部署 AD 停用 NTLM 密碼雜湊同步處理。
3. 在受控網域上停用 TLS v1。

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

## <a name="next-steps"></a>後續步驟
* [了解 Azure AD Domain Services 中的同步處理](active-directory-ds-synchronization.md)
