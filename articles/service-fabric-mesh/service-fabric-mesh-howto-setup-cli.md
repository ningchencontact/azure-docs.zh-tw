---
title: 設定 Azure Service Fabric Mesh CLI | Microsoft Docs
description: 了解如何設定 Azure Service Fabric Mesh CLI。
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: cf899e236b7fa21a90a0784e1e2b4fce0feae055
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213430"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>設定 Service Fabric Mesh CLI
您需要 Service Fabric Mesh CLI，才能在 Service Fabric 網格中部署及管理資源。 

針對預覽版本，Azure Service Fabric Mesh CLI 是以 Azure CLI 擴充功能的形式撰寫。 您可以將其安裝在 Azure Cloud Shell 或 Azure CLI 的本機安裝上。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果您選擇在本機安裝和使用 CLI，則必須安裝 Azure CLI 2.0.35 版或更新版本。 執行 `az --version` 以尋找版本。 若要安裝或升級至最新版本的 CLI，請參閱[安裝 Azure CLI 2.0][azure-cli-install]。

請移除任何先前安裝的 Azure Service Fabric Mesh CLI 模組。

```azurecli-interactive
az extension remove --name mesh
```

請使用下列命令安裝 Azure Service Fabric Mesh CLI 擴充功能模組。 

```azurecli-interactive
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

您也可以設定 [Windows 開發環境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)。

[azure-cli-install]: /cli/azure/install-azure-cli