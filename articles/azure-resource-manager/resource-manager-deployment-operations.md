---
title: 部署歷程記錄與 Azure Resource Manager |Microsoft Docs
description: 說明如何使用入口網站、PowerShell、Azure CLI 及 REST API 來檢視 Azure Resource Manager 部署作業。
tags: top-support-issue
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 58d22e3fcae5c30e5d7dcc39b317afeef4a693ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65605988"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>檢視部署記錄與 Azure Resource Manager

Azure Resource Manager 可讓您檢視您的部署歷程記錄，並檢查在過去的部署中的特定作業。 您可以看到已部署的資源，並取得任何錯誤的相關資訊。

如需帮助解决特定部署错误，请参阅[解决使用 Azure Resource Manager 将资源部署到 Azure 时的常见错误](resource-manager-common-deployment-errors.md)。

## <a name="portal"></a>入口網站

若要從部署歷程記錄中取得部署的相關詳細資料。

1. 選取您想要檢查的資源群組。

1. 選取下方的連結**部署**。

   ![選取部署歷程記錄](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. 您可以選取其中一個部署從部署歷程記錄。

   ![選取部署](./media/resource-manager-deployment-operations/select-details.png)

1. 部署的摘要隨即顯示，包括一份已部署的資源。

    ![部署摘要](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. 若要檢視部署所用的範本，請選取**範本**。 您可以下載範本來重複使用它。

    ![顯示範本](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. 如果您的部署失敗，您會看到一則錯誤訊息。 選取 更多詳細資料的錯誤訊息。

    ![檢視失敗的部署](./media/resource-manager-deployment-operations/show-error.png)

1. 會顯示詳細的錯誤訊息。

    ![檢視錯誤詳細資料](./media/resource-manager-deployment-operations/show-details.png)

1. 相互關聯識別碼用來追蹤相關的事件，並使用技術支援，針對部署進行疑難排解時很有幫助。

    ![取得相互關聯識別碼](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. 若要深入了解失敗的步驟，請選取**作業詳細資料**。

    ![選取的部署作業](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. 您會看到該部署的步驟詳細資料。

    ![顯示作業詳細資料](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要取得部署的整體狀態，請使用 **Get-AzResourceGroupDeployment** 命令。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

或者，您也可以篩選結果，只找出失敗的部署。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

相互關聯識別碼用來追蹤相關的事件，並使用技術支援，針對部署進行疑難排解時很有幫助。 若要取得相互關連識別碼，請使用：

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

每個部署都包括多個作業。 每個作業皆代表部署程序中的一個步驟。 若要探索部署有何問題，您通常需要查看有關部署作業的詳細資訊。 您可以利用 **Get-AzResourceGroupDeploymentOperation** 查看作業的狀態。

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

以下列格式傳回多項作業︰

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

若要取得有關失敗作業的詳細資訊，請擷取具有 [失敗]  狀態的作業屬性。

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

這會以下列格式傳回所有失敗的作業︰

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

請記下此作業的 serviceRequestId 和 trackingId。 與技術支援人員合作來排解部署問題時，serviceRequestId 會相當有用。 您將使用下一個步驟中的 trackingId 將重點放在特定的作業。

若要取得特定失敗作業的狀態訊息，請使用下列命令︰

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

返回：

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Azure 中的每個部署作業包含要求和回應內容。 在部署期間，您可以使用**DeploymentDebugLogLevel**參數來指定記錄要求和/或回應。

您會使用下列 PowerShell 命令從記錄檔取得該資訊，並將它儲存在本機︰

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure CLI

若要取得部署的整體狀態，請使用**azure 群組的部署顯示**命令。

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
相互關聯識別碼用來追蹤相關的事件，並使用技術支援，針對部署進行疑難排解時很有幫助。

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

若要查看某個部署的作業，請使用：

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

若要取得部署的相關資訊，請使用[取得範本部署的相關資訊](https://docs.microsoft.com/rest/api/resources/deployments)作業。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

在回應中，請特別注意 **provisioningState**、**correlationId** 和 **error** 元素。 **correlationId** 可用來追蹤相關的事件，並且在與技術支援人員合作來排解部署問題時會相當有用。

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

若要取得部署的相關資訊，請使用[列出所有範本部署作業](https://docs.microsoft.com/rest/api/resources/deployments)。 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
回應會根據您在部署期間於 **debugSetting** 屬性中指定的內容，來包含要求和 (或) 回應資訊。

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>後續步驟
* 如需帮助解决特定部署错误，请参阅[解决使用 Azure Resource Manager 将资源部署到 Azure 时的常见错误](resource-manager-common-deployment-errors.md)。
* 若要了解如何使用活動記錄來監視其他類型的動作，請參閱[檢視活動記錄以管理 Azure 資源](resource-group-audit.md)。
* 若要在执行部署之前验证部署，请参阅[使用 Azure Resource Manager 模板部署资源组](resource-group-template-deploy.md)。

