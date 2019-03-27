---
title: Azure 快速入門 - 使用 Azure Resource Manager 範本建立 Azure Key Vault 和祕密 |Microsoft Docs
description: 說明如何建立 Azure Key Vault，並使用 Azure Resource Manager 範本將祕密新增至保存庫的快速入門。
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/04/2019
ms.author: jgao
ms.openlocfilehash: 4b774e5b0a5c64a9af9a5a54ba264c6226558a24
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880014"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>快速入門：使用 Resource Manager 範本從 Azure Key Vault 設定及擷取祕密

[Azure Key Vault](./key-vault-overview.md) 是雲端服務，可安全儲存祕密，例如金鑰、密碼、憑證和其他祕密。 本快速入門著重於部署 Resource Manager 範本來建立金鑰保存庫和秘密的程序。 如需開發 Resource Manager 範本的詳細資訊，請參閱 [Resource Manager 文件](/azure/azure-resource-manager/)和[範本參考](/azure/templates/microsoft.keyvault/allversions)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要：

* 範本需要您的 Azure AD 使用者物件識別碼來設定權限。 下列程序會取得物件識別碼 (GUID)。

    1. 透過選取 [試試看] 來執行下列 Azure PowerShell 或 Azure CLI 命令，然後將指令碼貼到 Shell 窗格中。 若要貼上指令碼，請以滑鼠右鍵按一下 Shell，然後選取 [貼上]。 

        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --upn-or-object-id $upn --query "objectId" 
        ```

        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        ```

    2. 請記下物件識別碼。 您會在本快速入門中的下一節用到它。

## <a name="create-a-vault-and-a-secret"></a>建立保存庫和秘密

本快速入門中使用的範本是來自[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-key-vault-create/)。 更多 Azure Key Vault 範本範例可在[此處](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)找到。

1. 選取以下影像來登入 Azure 並開啟範本。 此範本會建立金鑰保存庫和祕密。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. 選取或輸入下列值。  

    ![Resource Manager 範本 Key Vault 整合部署入口網站](./media/quick-create-template/create-key-vault-using-template-portal.png)

    除非有指定，否則就使用預設值來建立金鑰保存庫和密碼。

    * **訂用帳戶**：選取 Azure 訂用帳戶。
    * [資源群組]選取 [新建]，輸入資源群組的唯一名稱，然後按一下 [確認]。 
    * **位置**：選取位置。  例如，**美國中部**。
    * **Key Vault 名稱**：輸入的金鑰保存庫的唯一名稱。  
    * **租用戶識別碼**：範本功能會自動擷取您的租用戶識別碼。請勿變更預設值
    * **AD 使用者識別碼**：輸入您從[必要條件](#prerequisites)中擷取的 Azure AD 使用者物件識別碼。
    * **祕密名稱**：輸入您在金鑰保存庫中儲存的祕密的名稱。  例如，**adminpassword**
    * **祕密值**：輸入祕密值。  如果您儲存密碼，建議使用您在必要條件中建立而產生的密碼。
    * **我同意上方所述的條款及條件**：選取。
3. 選取 [購買]。

## <a name="validate-the-deployment"></a>驗證部署

您可以使用 Azure 入口網站來檢查金鑰保存庫和秘密，或使用下列 Azure CLI 或 Azure PowerShell 指令碼列出所建立的祕密。

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName
```

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
```

## <a name="clean-up-resources"></a>清除資源

其他 Key Vault 快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門和教學課程，您可以讓這些資源留在原處。
如果不再需要，請刪除資源群組，這會刪除 Key Vault 和相關資源。 若要使用 Azure CLI 或 Azure Powershell 刪除資源群組：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```
```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>後續步驟

* [Azure Key Vault 首頁](https://azure.microsoft.com/services/key-vault/)
* [Azure Key Vault 文件](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK For Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Azure REST API 參考](https://docs.microsoft.com/rest/api/keyvault/)
