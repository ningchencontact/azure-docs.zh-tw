---
title: 使用 PowerShell 管理 Azure Stack 中的金鑰保存庫 | Microsoft Docs
description: 了解如何使用 PowerShell 管理 Azure Stack 中的金鑰保存庫
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.openlocfilehash: db68d3ac626d80361e456a251b93d847a73afb8c
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192611"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>使用 PowerShell 管理 Azure Stack 中的金鑰保存庫

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

您可以使用 PowerShell 管理 Azure Stack 中的金鑰保存庫。 本文會說明如何使用 Key Vault PowerShell Cmdlet 來：

* 建立金鑰保存庫。
* 儲存及管理密碼編譯金鑰和密碼。
* 授權使用者或應用程式叫用保存庫中的作業。

>[!NOTE]
>本文中所述的金鑰保存庫 PowerShell Cmdlet 會隨附於 Azure PowerShell SDK 中。

## <a name="prerequisites"></a>必要條件

* 您必須訂閱包含 Azure Key Vault 服務的供應項目。
* [安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。
* [設定 Azure Stack 使用者的 PowerShell 環境](azure-stack-powershell-configure-user.md)。

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>針對金鑰保存庫作業啟用您的租用戶訂用帳戶

在您可以對金鑰保存庫執行任何作業之前，必須先確保已針對保存庫作業啟用您的租用戶訂用帳戶。 若要確認保存庫作業已啟用，執行以下命令：

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**輸出**

若已針對保存庫作業啟用訂用帳戶，在針對金鑰保存庫之所有資源類型的輸出中，[RegistrationState] 都會顯示為 [Registered]。

![金鑰保存庫註冊狀態](media/azure-stack-key-vault-manage-powershell/image1.png)

如果尚未啟用保存庫作業，請執行下列命令以在訂用帳戶中註冊 Key Vault 服務：

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**輸出**

如果註冊成功，會傳回下列輸出：

![註冊](media/azure-stack-key-vault-manage-powershell/image2.png)

當您叫用 Key Vault 命令時，可能會遇到錯誤，例如「未註冊訂用帳戶，無法使用命名空間 'Microsoft.KeyVault'」。如果您遇到錯誤，請確認您已依照先前所述的指示[啟用 Key Vault 資源提供者](#enable-your-tenant-subscription-for-vault-operations)。

## <a name="create-a-key-vault"></a>建立金鑰保存庫

建立金鑰保存庫之前，先建立資源群組，使所有與金鑰保存庫相關的資源都存在於資源群組中。 請使用下列命令以建立新的資源群組：

```PowerShell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force
```

**輸出**

![新增資源群組](media/azure-stack-key-vault-manage-powershell/image3.png)

現在，使用 **New-AzureRMKeyVault** Cmdlet 以在稍早所建立的資源群組中建立金鑰保存庫。 這個命令會讀取三個必要參數：資源群組名稱、金鑰保存庫名稱、地理位置。

請執行下列命令以建立金鑰保存庫：

```PowerShell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

**輸出**

![新的金鑰保存庫](media/azure-stack-key-vault-manage-powershell/image4.png)

此命令的輸出會顯示您所建立的金鑰保存庫屬性。 當應用程式存取此保存庫時，其必須使用 `Vault URI` 屬性，在此範例中為 `https://vault01.vault.local.azurestack.external`。

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Active Directory 同盟服務 (AD FS) 部署

在 AD FS 部署中，您可能會收到這個警告：「未設定存取原則。 使用者或應用程式沒有使用此保存庫的存取權」。 若要解決此問題，請使用 [Set-AzureRmKeyVaultAccessPolicy](azure-stack-key-vault-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) Cmdlet 設定保存庫的存取原則：

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>管理金鑰和祕密

在您建立金鑰保存庫之後，請使用下列步驟來建立並管理保存庫中的金鑰和祕密。

### <a name="create-a-key"></a>建立金鑰

使用 **Add-AzureKeyVaultKey** Cmdlet，在金鑰保存庫中建立或匯入受到軟體保護的金鑰。

```PowerShell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

`Destination` 參數是用來指定金鑰已受到軟體保護。 成功建立金鑰時，命令會輸出新建立金鑰的詳細資料。

**輸出**

![新金鑰](media/azure-stack-key-vault-manage-powershell/image5.png)

您現在可以使用新建立金鑰的 URI 來參考它。 如果您建立或匯入與現有金鑰名稱相同的金鑰，就會以新金鑰中指定的值來更新原始金鑰。 您可以使用金鑰的版本特定 URI 來存取先前的版本。 例如︰

* 使用 `https://vault10.vault.local.azurestack.external:443/keys/key01` 以一律可取得最新版本。
* 使用 `https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a` 以取得此特定版本。

### <a name="get-a-key"></a>取得金鑰

使用 **Get AzureKeyVaultKey** Cmdlet 以讀取金鑰及其詳細資料。

```PowerShell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>建立祕密

使用 **Set-AzureKeyVaultSecret** Cmdlet 以建立或更新保存庫中的祕密。 如果祕密尚不存在，系統便會建立一個。 如果已經存在，則會建立新版本的密碼。

```PowerShell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

**輸出**

![建立祕密](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>取得祕密

使用 **Get-AzureKeyVaultSecret** Cmdlet 以讀取金鑰保存庫中的祕密。 此命令可傳回所有或特定版本的祕密。

```PowerShell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

建立金鑰和密碼之後，可以授權讓外部應用程式使用它們。

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>授權應用程式使用金鑰或祕密

使用 **Set-AzureRmKeyVaultAccessPolicy** Cmdlet 以授權讓應用程式存取金鑰保存庫中的金鑰或祕密。 在以下範例中，保存庫名稱是 `ContosoKeyVault`，而您想要授權之應用程式的用戶端識別碼是 `8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed`。 若要，執行下列命令。 您也可以指定 `PermissionsToKeys` 參數以設定使用者、應用程式或安全性群組的權限。

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

如果您想要授權該相同的應用程式讀取您保存庫中的祕密，請執行以下 Cmdlet：

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>後續步驟

* [使用存放在金鑰保存庫中的密碼來部署 VM](azure-stack-key-vault-deploy-vm-with-secret.md)
* [使用存放在金鑰保存庫中的憑證來部署 VM](azure-stack-key-vault-push-secret-into-vm.md)
