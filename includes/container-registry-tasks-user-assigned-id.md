---
title: 包含檔案
description: 包含檔案
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0e2acb346fad87e0c1c7fd7de1389d8fc86206d0
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642107"
---
### <a name="create-a-user-assigned-identity"></a>建立使用者指派的身分識別

使用[az identity create][az-identity-create]命令, 在您的訂用帳戶中建立名為*myACRTasksId*的身分識別。 您可以使用先前用來建立容器登錄或不同的資源群組。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

若要在下列步驟中設定使用者指派的身分識別, 請使用[az identity show][az-identity-show]命令, 將身分識別的資源識別碼、服務主體識別碼和用戶端識別碼儲存在變數中。

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show