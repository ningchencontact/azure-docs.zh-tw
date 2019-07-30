---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: a2994602f857c2c8ff9f935b649a8d3e94c10dca
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444131"
---
## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源 (如函式應用程式、資料庫和儲存體帳戶) 的邏輯容器。

下列範例會建立名為 `myResourceGroup` 的資源群組。  
如果您未使用 Cloud Shell，請先使用 `az login` 登入。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

您通常會在附近的[區域](https://azure.microsoft.com/global-infrastructure/regions/)中建立資源群組和資源。 
