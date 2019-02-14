---
title: 金鑰保存庫參考 - Azure App Service | Microsoft Docs
description: Azure App Service 和 Azure Functions 中 Azure Key Vault 參考的概念參考和設定指南
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 662260c3cf37f8f8a675c522f3d3dea41153e485
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663558"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>使用 App Service 和 Azure Functions 的 Key Vault 參考 (預覽)

> [!NOTE] 
> Key Vault 參考目前處於預覽狀態。

本主題示範如何在 App Service 或 Azure Functions 應用程式中使用來自 Azure Key Vault 的祕密，而不需要變更任何程式碼。 [Azure Key Vault](../key-vault/key-vault-overview.md) 是提供集中式祕密管理的服務，可完整控制存取原則和稽核歷程記錄。

## <a name="granting-your-app-access-to-key-vault"></a>將您的應用程式存取權授與 Key Vault

若要從 Key Vault 讀取祕密，您需要建立保存庫，並提供您的應用程式權限來存取它。

1. 依照 [Key Vault 快速入門](../key-vault/quick-create-cli.md)來建立金鑰保存庫。

1. 為您的應用程式建立[系統指派的受控識別](overview-managed-identity.md)。

   > [!NOTE] 
   > Key Vault 參考目前只支援系統指派的受控識別。 您無法使用使用者指派的識別。

1. 針對您稍早建立的應用程式識別碼，建立 [Key Vault 中的存取原則](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies)。 在此原則上啟用 "Get" 祕密權限。 請勿設定「授權的應用程式」或 `appliationId` 設定，因為這與受控識別不相容。

## <a name="reference-syntax"></a>參考語法

Key Vault 參考格式為 `@Microsoft.KeyVault({referenceString})`，其中 `{referenceString}` 會由下列其中一個選項來取代：

> [!div class="mx-tdBreakAll"]
> | 參考字串                                                            | 說明                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** 應該是 Key Vault 中祕密的完整資料平面 URI (包括版本在內)，例如 https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** 應該是您 Key Vault 資源的名稱。 **SecretName** 應該是目標祕密的名稱。 **SecretVersion** 應該是要使用的祕密版本。 |

> [!NOTE] 
> 在目前的預覽中，版本為必要項目。 輪替祕密時，您必須在應用程式設定中更新版本。

例如，完整的參考可能看起來如下：

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

或者：

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>來自 Key Vault 的來源應用程式設定

Key Vault 參考可用來作為[應用程式設定](web-sites-configure.md#app-settings)的值，可讓您將祕密保留於 Key Vault 中，而不是網站設定內。應用程式設定會以靜止形式安全地加密，但如果您需要祕密管理功能，就應該將它們移至 Key Vault。

若要使用應用程式設定的 Key Vault 參考，請將參考設為設定的值。 您的應用程式可以正常方式透過它的金鑰來參考祕密。 不需要變更程式碼。

> [!TIP]
> 使用 Key Vault 參考的大部分應用程式設定都應該標示為位置設定，因為每個環境都應該有不同的保存庫。

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 部署

透過 Azure Resource Manager 範本將資源部署自動化時，您可能需要以特定順序來排序相依性，才能使此功能運作。 請注意，您必須將應用程式設定定義為它們自己的資源，而不是使用網站定義中的 `siteConfig` 屬性。 這是因為必須先定義網站，才能使用它來建立系統指派的識別，並且可在存取原則中使用。

函式應用程式的範例虛擬範本看起來可能如下：

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> 在此範例中，原始檔控制部署取決於應用程式設定。 這通常是不安全的行為，因為應用程式設定更新會以非同步方式運作。 不過，因為我們已包含 `WEBSITE_ENABLE_SYNC_UPDATE_SITE` 應用程式設定，所以更新會同步。 這表示，原始檔控制部署將只會在應用程式設定已完全更新之後開始。
