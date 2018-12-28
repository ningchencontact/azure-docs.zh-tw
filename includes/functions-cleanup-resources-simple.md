---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f953c7ae1bba774a69bffddb148c93609c9a85c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52854628"
---
## <a name="clean-up-resources"></a>清除資源

以此發行項建立的資源使用完畢之後，請使用下列命令，從您的訂用帳戶中刪除這些資源：

```azurecli-interactive
az group delete --name myResourceGroup
```

在出現提示時輸入 `y`。 此命令會移除 `myResourceGroup` 資源群組和屬於該群組的所有資源。