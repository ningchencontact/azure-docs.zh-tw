---
title: 如何使用 Azure Cloud Shell 建立針對 Azure Dev Spaces 所啟用的 Kubernetes 叢集 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: iainfoulds
ms.author: iainfou
ms.date: 10/04/2018
ms.topic: article
description: 了解如何在不安裝任何項目的情況下，直接從瀏覽器快速建立針對 Azure Dev Spaces 所啟用的 Kubernetes 叢集。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器
ms.openlocfilehash: 47c467e020a7a9253daa636352352d9a57dddf28
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978140"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>使用 Azure Cloud Shell 建立 Kubernetes 叢集

您可以使用 [Azure Cloud Shell](/azure/cloud-shell)，透過此頁面的 [試試看] 按鈕建立適用於 Azure Dev Spaces 的叢集。 如果您未登入，請遵循提示使用 Azure 帳戶來登入，然後在 Azure Cloud Shell 提示字元出現時，於其中輸入命令。

## <a name="create-the-cluster"></a>建立叢集

首先，建立資源群組。 使用目前支援的其中一個區域 (EastUS、EastUS2、CentralUS、WestUS2、WestEurope、SoutheastAsia、CanadaCentral 或 CanadaEast)。

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

使用下列命令來建立 Kubernetes 叢集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.11.3 --enable-addons http_application_routing
```

建立叢集需要幾分鐘的時間。  完成時，輸出會以 JSON 格式顯示。 尋找 `provisioningState` 並確認它 `Succeeded`。

## <a name="next-steps"></a>後續步驟

如需完整教學課程的連結，請參閱 [Azure Dev Spaces](/azure/dev-spaces/)。
