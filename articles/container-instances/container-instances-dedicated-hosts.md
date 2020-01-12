---
title: 在專用主機上部署
description: 使用專用主機，為您的工作負載達成真正的主機層級隔離
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 619a39f4d08a4308cb0f566bc50860e9562bf9e4
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903752"
---
# <a name="deploy-on-dedicated-hosts"></a>在專用主機上部署

「專用」是一種 Azure 容器實例（ACI） sku，可提供隔離且專用的計算環境，以安全地執行容器。 使用專用 sku 會導致在 Azure 資料中心具有專用實體伺服器的每個容器群組中，確保完整的工作負載隔離，以協助符合您組織的安全性和合規性需求。 

專用 sku 適用于需要從實體伺服器觀點來隔離工作負載的容器工作負載。

## <a name="using-the-dedicated-sku"></a>使用專用 sku

> [!IMPORTANT]
> 使用專用 sku 僅適用于目前推出的最新 API 版本（2019-12-01）。在您的部署範本中指定此 API 版本。 此外，使用專用 sku 之任何訂用帳戶的預設限制為0。 如果您想要將此 sku 用於生產容器部署，請建立[Azure 支援要求][azure-support]

從 API 版本2019-12-01 開始，部署範本的 [容器群組屬性] 區段底下會有一個「sku」屬性，這是 ACI 部署所需的內容。 目前，您可以使用此屬性做為 ACI Azure Resource Manager 部署範本的一部分。 您可以在[教學課程：使用 Resource Manager 範本部署多容器群組](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)中，深入瞭解如何使用範本部署 ACI 資源。 

Sku 屬性可以有下列其中一個值：
* 標準-標準 ACI 部署選擇，其仍可保證程式管理層級安全性 
* 專用-用於具有容器群組專用實體主機的工作負載層級隔離

## <a name="modify-your-json-deployment-template"></a>修改您的 JSON 部署範本

在您的部署範本中，指定容器群組資源的位置，確定 `"apiVersion": "2019-12-01",`。 在容器群組資源的 [屬性] 區段中，設定 [`"sku": "Dedicated",`]。

以下是使用專用 sku 的容器群組部署範本之 resources 區段的範例程式碼片段：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux",
            },
            "location": "eastus2euap",
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>部署您的容器群組

如果您已在桌面上建立並編輯部署範本檔案，您可以將檔案拖曳到您的 Cloud Shell 目錄中，將檔案上傳到該檔案。 

使用 [az group create][az-group-create] 命令來建立資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az group deployment create][az-group-deployment-create] 命令部署範本。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

在幾秒內，您應該會從 Azure 收到首次回應。 部署完成之後，ACI 服務所保存的所有相關資料都會以您提供的金鑰進行加密。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
