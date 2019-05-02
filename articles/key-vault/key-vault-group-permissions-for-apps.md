---
title: 對許多應用程式授與 Azure 金鑰保存庫的存取權限 - Azure Key Vault | Microsoft Docs
description: 了解如何對許多應用程式授與金鑰保存庫的存取權限
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 39624595b4dd4e72ab1d34082a6ba44a1b3636d0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721988"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>對數個應用程式授與金鑰保存庫的存取權

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

存取控制原則可用來對數個應用程式授與金鑰保存庫的存取權。 存取控制原則最多可支援 1024 個應用程式，並已設定如下：

1. 建立 Azure Active Directory 安全性群組。 
2. 在安全性群組中新增應用程式所有的相關聯服務主體。
3. 對安全性群組授與金鑰保存庫的存取權。

## <a name="prerequisites"></a>必要條件

以下是必要條件：
* [安裝 Azure PowerShell](/powershell/azure/overview)。
* [安裝 Azure Active Directory V2 PowerShell 模組](https://www.powershellgallery.com/packages/AzureAD)。
* 在 Azure Active Directory 租用戶建立／編輯群組的權限。 如果您沒有權限，則可能需要連絡 Azure Active Directory 系統管理員。 如需 Key Vault 存取原則權限的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、祕密和憑證](about-keys-secrets-and-certificates.md)。

## <a name="granting-key-vault-access-to-applications"></a>授與應用程式的金鑰保存庫存取權

在 PowerShell 中執行下列命令：

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

如果您需要對一組應用程式授與一組不同的權限，請為這些應用程式另外建立一個 Azure Active Directory 安全性群組。

## <a name="next-steps"></a>後續步驟

深入了解如何[保護您的金鑰保存庫](key-vault-secure-your-key-vault.md)。
