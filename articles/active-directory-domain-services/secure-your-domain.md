---
title: 保護您的 Azure Active Directory Domain Services 受控網域 | Microsoft Docs
description: 保護受控網域
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: ab371553a96f3a8d393c8b773c4024d04fd171a1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246730"
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

## <a name="next-steps"></a>後續步驟
* [了解 Azure AD Domain Services 中的同步處理](synchronization.md)
