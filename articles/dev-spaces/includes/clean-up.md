---
title: 包含檔案
description: 包含檔案
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: b8b8b1a593328197c61f8edba9f8f849518dd2e5
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129077"
---
## <a name="clean-up"></a>清除
若要完全刪除叢集上的 Azure Dev Spaces 執行個體，包括所有開發人員空間及其中正在執行的服務，請使用 `az aks remove-dev-spaces` 命令。 請記住此動作無法復原。 您可以在叢集上再次新增 Azure Dev Spaces 的支援，但會如同您再次開始執行。 將不會還原您的舊服務和空間。

下列範例列出您使用中訂用帳戶中的 Azure Dev Spaces 控制項，然後刪除資源群組 'myaks-rg' 中與 AKS 叢集 'myaks' 相關聯的 Azure Dev Spaces 控制項。

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```

