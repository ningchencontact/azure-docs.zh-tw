---
title: 使用 Azure CLI 與範本部署資源 | Microsoft Docs
description: 使用 Azure Resource Manager 和 Azure CLI, 將資源部署至 Azure。 資源會定義在 Resource Manager 範本中。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: tomfitz
ms.openlocfilehash: bd43e919cc0b2bcf1d130c7e616b7da064abcc65
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971020"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>使用 Resource Manager 範本與 Azure CLI 部署資源

本文說明如何使用 Azure CLI 與 Resource Manager 範本，將您的資源部署至 Azure。 如果您不熟悉部署和管理 Azure 解決方案的相關概念，請參閱 [Azure Resource Manager 概觀](resource-group-overview.md)。  

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

如果您沒有安裝 Azure CLI，可以使用 [Cloud Shell](#deploy-template-from-cloud-shell)。

## <a name="deployment-scope"></a>部署範圍

您可以將部署的目標設為 Azure 訂用帳戶或訂用帳戶內的資源群組。 在大部分情況下, 您會將部署目標設為資源群組。 使用訂用帳戶部署, 在訂用帳戶之間套用原則和角色指派。 您也可以使用「訂用帳戶」部署來建立資源群組, 並將資源部署到其中。 視部署的範圍而定, 您可以使用不同的命令。

若要部署至**資源群組**, 請使用[az group deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

若要部署至**訂**用帳戶, 請使用[az deployment create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

目前, 只有透過 REST API 支援管理群組部署。 請參閱[使用 Resource Manager 範本部署資源和 Resource Manager REST API](resource-group-template-deploy-rest.md)。

本文中的範例會使用資源群組部署。 如需訂用帳戶部署的詳細資訊, 請參閱在訂用帳戶[層級建立資源群組和資源](deploy-to-subscription.md)。

## <a name="deploy-local-template"></a>部署本機範本

將資源部署至 Azure 時，您應該：

1. 登入您的 Azure 帳戶
2. 建立資源群組，作為已部署資源的容器。 資源群組的名稱只能包含英數字元、句點 (.)、底線、連字號及括弧。 最多可有 90 個字元。 不能以句點結束。
3. 將範本部署至資源群組，範本中定義要建立的資源

範本可以包含讓您自訂部署的參數。 例如，您可以提供針對特定環境 (例如開發、測試和生產) 量身訂做的值。 範例範本會定義儲存體帳戶 SKU 的參數。 

下列範例會建立資源群組，並從您的本機電腦部署範本：

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

部署需要幾分鐘的時間才能完成。 完成後，您會看到包含結果的訊息：

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>部署遠端範本

您可能希望將 Resource Manager 範本儲存在外部位置，而不是儲存在您的本機電腦。 您可以將範本儲存在原始檔控制存放庫 (例如 GitHub) 中。 或者，您可以將它們儲存在 Azure 儲存體帳戶中，以在組織內共用存取。

若要部署外部範本，請使用 **template-uri** 參數。 在範例中使用 URI 以部署來自 GitHub 的範例範本。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

上述範例針對範本需要可公開存取 URI，這適用於大部分的案例，因為您的範本不應該包含機密資料。 如果您需要指定機密資料 (例如系統管理員密碼)，請將該值以安全參數傳遞。 不過，如果不希望將範本公開存取，您可以將它儲存在私人儲存體容器中加以保護。 如需部署需要共用存取簽章 (SAS) 權杖之範本的相關資訊，請參閱[使用 SAS 權杖部署私人範本](resource-manager-cli-sas-token.md)。

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

在 Cloud Shell 中使用下列命令︰

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="redeploy-when-deployment-fails"></a>部署失敗時重新部署

這項功能也稱為「*發生錯誤時復原*」。 當部署失敗時，您可以從部署記錄自動重新部署先前成功的部署。 若要指定重新部署，請在部署命令中使用 `--rollback-on-error` 參數。 如果您的基礎結構部署有已知的良好狀態, 而且想要還原為此狀態, 此功能就很有用。 有一些警告和限制:

- 重新部署的執行方式與先前使用相同參數執行的完全相同。 您無法變更參數。
- 先前的部署是使用[完整模式](./deployment-modes.md#complete-mode)來執行。 先前部署中未包含的任何資源都會刪除, 而且任何資源設定都會設為先前的狀態。 請確定您完全瞭解[部署模式](./deployment-modes.md)。
- 重新部署只會影響資源, 任何資料變更都不會受到影響。
- 只有資源群組部署支援這項功能, 而不是訂用帳戶層級部署。 如需訂用帳戶層級部署的詳細資訊, 請參閱在訂用帳戶[層級建立資源群組和資源](./deploy-to-subscription.md)。

若要使用這個選項，您的部署必須有唯一的名稱，以便在歷程記錄中進行識別。 如果您沒有唯一的名稱，則目前失敗的部署可能會覆寫歷程記錄中先前成功的部署。 您只可以使用此選項搭配根層級部署。 從巢狀範本部署不適用於重新部署。

若要重新部署最後一個成功的部署，請新增 `--rollback-on-error` 參數作為旗標。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

若要重新部署特定部署，請使用 `--rollback-on-error` 參數，並提供部署的名稱。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

指定的部署必須成功。

## <a name="parameters"></a>參數

若要傳遞參數值，您可以使用內嵌參數或參數檔案。

### <a name="inline-parameters"></a>內嵌參數

若要傳遞內嵌參數，請提供 `parameters` 中的值。 例如，若要透過 Bash 殼層將字串和陣列傳遞至範本，請使用：

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

如果您要使用 Azure CLI 搭配 Windows 命令提示字元 (CMD) 或 PowerShell, 請以下列格式傳遞陣列`exampleArray="['value1','value2']"`:。

您也可以取得檔案內容，並提供該內容作為內嵌參數。

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

當您需要提供組態值時，從檔案取得參數值會很有幫助。 例如，您可以提供 [Linux 虛擬機器的 cloud-init 值](../virtual-machines/linux/using-cloud-init.md)。

arrayContent.json 的格式為：

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>參數檔案

相對於在您的指令碼中將參數做為內嵌值傳遞，使用包含該參數值的 JSON 檔案可能較為容易。 參數檔案必須是本機檔案。 Azure CLI 不支援外部參數檔案。

如需參數檔案的詳細資訊, 請參閱[建立 Resource Manager 參數](resource-manager-parameter-files.md)檔案。

若要傳遞本機參數檔案，請使用 `@` 來指定名為 storage.parameters.json 的本機檔案。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>測試範本部署

若要測試您的範本和參數值而不實際部署任何資源，請使用 [az group deployment validate](/cli/azure/group/deployment#az-group-deployment-validate)。 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

如果沒有偵測到任何錯誤，命令會傳回有關測試部署的資訊。 請特別注意到 **error** 值為 null。

```azurecli
{
  "error": null,
  "properties": {
      ...
```

如果偵測到錯誤，命令會傳回錯誤訊息。 例如，針對儲存體帳戶 SKU 傳遞不正確的值，則會傳回下列錯誤：

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

如果您的範本有語法錯誤，命令會傳回錯誤，指出無法剖析範本。 訊息會指出剖析錯誤的行號和位置。

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>後續步驟

- 本主題中的範例會將資源部署到您預設訂用帳戶中的資源群組。 若要使用不同的訂用帳戶，請參閱[管理多個 Azure 訂用帳戶](/cli/azure/manage-azure-subscriptions-azure-cli)。
- 若要指定如何處理存在於資源群組中、但尚未定義於範本中的資源，請參閱 [Azure Resource Manager 部署模式](deployment-modes.md)。
- 若要了解如何在您的範本中定義參數，請參閱[了解 Azure Resource Manager 範本的結構和語法](resource-group-authoring-templates.md)。
- 如需解決常見部署錯誤的秘訣，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](resource-manager-common-deployment-errors.md)。
- 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](resource-manager-cli-sas-token.md)。
- 若要安全地在多個區域推出您的服務，請參閱 [Azure 部署管理員](deployment-manager-overview.md)。
