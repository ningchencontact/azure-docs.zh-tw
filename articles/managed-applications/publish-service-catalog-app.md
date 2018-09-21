---
title: 建立及發佈 Azure 受服務類別目錄管理的應用程式 | Microsoft Docs
description: 示範如何建立 Azure 受控應用程式，以供組織成員使用。
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 06/08/2018
ms.author: tomfitz
ms.openlocfilehash: 3b1da6e9068be3c96cce5973f29344fe7e4b4872
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2018
ms.locfileid: "35762870"
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>發佈受控應用程式，以供內部使用

您可以建立及發佈 Azure [受控應用程式](overview.md)，以供組織成員使用。 例如，IT 部門可以發佈符合組織標準的受控應用程式。 這些受控應用程式可透過服務類別目錄取得，而非 Azure Marketplace。

若要發佈受服務類別目錄管理的應用程式，您必須：

* 建立範本，該範本會定義要與受控應用程式一起部署的資源。
* 部署受控應用程式時，定義入口網站的使用者介面元素。
* 建立包含必要範本檔案的 .zip 套件。
* 決定需要存取使用者訂用帳戶中的資源群組之使用者、群組或應用程式。
* 建立指向 .zip 套件並要求存取身分識別的受控應用程式定義。

在本文中，受控應用程式只有一個儲存體帳戶。 它是用來說明發佈受控應用程式的步驟。 如需完整範例，請參閱[適用於 Azure 受控應用程式之範例專案](sample-projects.md)。

本文中的 PowerShell 範例需要 Azure PowerShell 6.2 或更新版本。 如有需要，[請更新您的版本](/powershell/azure/install-azurerm-ps)。

## <a name="create-the-resource-template"></a>建立資源範本

每個受控應用程式定義都包含名為 **mainTemplate.json** 的檔案。 您可以在其中定義要部署的 Azure 資源。 此範本與一般 Resource Manager 範本不同。

建立名為 **mainTemplate.json** 的檔案。 名稱有區分大小寫。

將下列 JSON 新增至檔案。 它會定義用來建立儲存體帳戶的參數，並且指定儲存體帳戶的屬性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

儲存 mainTemplate.json 檔案。

## <a name="create-the-user-interface-definition"></a>建立使用者介面定義

Azure 入口網站會使用 **createUiDefinition.json** 檔案，為建立受控應用程式的使用者產生使用者介面。 您可以定義使用者提供每個參數輸入的方式。 您可以使用諸如下拉式清單、文字方塊、密碼方塊和其他輸入工具等選項。 若要了解如何建立受控應用程式的 UI 定義檔案，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。

建立名為 **createUiDefinition.json** 的檔案。 名稱有區分大小寫。

將下列 JSON 新增至檔案。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

儲存 createUiDefinition.json 檔案。

## <a name="package-the-files"></a>封裝檔案

將兩個檔案新增至名為 app.zip 的 .zip 檔案。 這兩個檔案必須位於 .zip 檔案的根層級。 如果您將這些檔案放在資料夾中，當建立受控應用程式時，您會收到錯誤，指出必要的檔案不存在。 

將套件上傳至可從中取用它的可存取位置。 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>建立受控應用程式定義

### <a name="create-an-azure-active-directory-user-group-or-application"></a>建立 Azure Active Directory 使用者群組或應用程式

下一個步驟是選取要代表客戶管理資源的使用者群組或應用程式。 此使用者群組或應用程式會根據指派的角色，取得受控資源群組的權限。 角色可以是任何內建的角色型存取控制 (RBAC) 角色，例如擁有者或參與者。 您也可以將管理資源的權限提供給個別使用者，但您通常要將此權限指派給使用者群組。 若要建立新的 Active Directory 使用者群組，請參閱[在 Azure Active Directory 中建立群組和新增成員](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

您需要使用者群組的物件識別碼，以便用於管理資源。 

```powershell
$groupID=(Get-AzureRmADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>取得角色定義識別碼

接下來，您需要的角色定義識別碼，是您想要授與使用者、使用者群組或應用程式存取權的 RBAC 內建角色。 您通常會使用「擁有者」或「參與者」或「讀取者」角色。 下列命令會顯示如何取得「擁有者」角色的角色定義識別碼：

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>建立受控應用程式定義

如果您尚未有可儲存受控應用程式定義的資源群組，請立即建立一個：

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

現在，建立受控應用程式定義資源。

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

### <a name="make-sure-users-can-see-your-definition"></a>確定使用者可以看到您的定義

您可以存取受控應用程式定義，但您想確保您組織中的其他使用者可以存取它。 至少在定義上將讀者角色授與給他們。 他們可能已從訂用帳戶或資源群組繼承此存取層級。 若要檢查有誰可以存取定義及新增使用者或群組的，請參閱[使用角色型存取控制來管理 Azure 訂用帳戶資源的存取權](../role-based-access-control/role-assignments-portal.md)。

## <a name="create-the-managed-application"></a>建立受控應用程式

您可以透過入口網站、PowerShell 或 Azure CLI 來部署受控應用程式。

### <a name="powershell"></a>PowerShell

首先，我們要使用 PowerShell 來部署受控應用程式。

```powershell
# Create resource group
New-AzureRmResourceGroup -Name applicationGroup -Location westcentralus

# Get ID of managed application definition
$appid=(Get-AzureRmManagedApplicationDefinition -ResourceGroupName appDefinitionGroup -Name ManagedStorage).ManagedApplicationDefinitionId

# Create the managed application
New-AzureRmManagedApplication `
  -Name storageApp `
  -Location westcentralus `
  -Kind ServiceCatalog `
  -ResourceGroupName applicationGroup `
  -ManagedApplicationDefinitionId $appid `
  -ManagedResourceGroupName "InfrastructureGroup" `
  -Parameter "{`"storageAccountNamePrefix`": {`"value`": `"demostorage`"}, `"storageAccountType`": {`"value`": `"Standard_LRS`"}}"
```

您的受控應用程式和受控基礎結構現已存在於訂用帳戶中。

### <a name="portal"></a>入口網站

現在，讓我們使用入口網站來部署受控應用程式。 您會在套件中看到您建立的使用者介面。

1. 移至 Azure 入口網站。 選取 [+ 建立資源]，並搜尋**服務目錄**。

   ![服務類別目錄](./media/publish-service-catalog-app/create-new.png)

1. 選取 [服務類別目錄受控應用程式]。

   ![選取服務類別目錄](./media/publish-service-catalog-app/select-service-catalog-managed-app.png)

1. 選取 [建立] 。

   ![選取 [建立]](./media/publish-service-catalog-app/select-create.png)

1. 從可用的解決方案清單中，尋找並選取您想要建立的受控應用程式。 選取 [建立] 。

   ![尋找受控應用程式](./media/publish-service-catalog-app/find-application.png)

   如果您無法透過入口網站查看受控應用程式定義，則可能需要變更您的入口網站設定。 選取 [目錄和訂用帳戶篩選條件]。

   ![選取訂用帳戶篩選條件](./media/publish-service-catalog-app/select-filter.png)

   檢查全域訂用帳戶篩選條件包括含有受控應用程式定義的訂用帳戶。

   ![檢查訂用帳戶篩選條件](./media/publish-service-catalog-app/check-global-filter.png)

   在選取訂用帳戶之後，以建立服務目錄受控應用程式重新開始。 您現在應會看見該應用程式。

1. 提供受控應用程式所需的基本資訊。 指定要包含受控應用程式的訂用帳戶和新資源群組。 選取 [美國中西部] 為位置。 完成時，選取 [確認]。

   ![提供受控應用程式參數](./media/publish-service-catalog-app/add-basics.png)

1. 提供專屬於受控應用程式中資源的值。 完成時，選取 [確認]。

   ![提供資源參數](./media/publish-service-catalog-app/add-storage-settings.png)

1. 範本會驗證您所提供的值。 如果驗證成功，請選取 [確定] 以開始部署。

   ![驗證受控應用程式](./media/publish-service-catalog-app/view-summary.png)

在部署完成之後，受控應用程式會存在名為 applicationGroup 的資源群組中。 儲存體帳戶會存在名為 applicationGroup (加上雜湊字串值) 的資源群組中。

## <a name="next-steps"></a>後續步驟

* 如需受控應用程式的簡介，請參閱[受控應用程式概觀](overview.md)。
* 如需範例專案，請參閱[適用於 Azure 受控應用程式之範例專案](sample-projects.md)。
* 若要了解如何建立受控應用程式的 UI 定義檔案，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
