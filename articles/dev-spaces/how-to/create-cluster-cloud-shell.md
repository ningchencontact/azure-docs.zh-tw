---
title: 如何建立 Kubernetes 叢集，讓您使用 Azure Cloud Shell 的 Azure 開發人員分享空間
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: 了解如何在不安裝任何項目的情況下，直接從瀏覽器快速建立針對 Azure Dev Spaces 所啟用的 Kubernetes 叢集。
keywords: Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器，Helm，服務網格，服務網格路由、 kubectl，k8s
ms.openlocfilehash: bfde055c99a1109a8f71c838b44ed1742e3f06aa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895561"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>使用 Azure Cloud Shell 建立 Kubernetes 叢集

您可以使用 [Azure Cloud Shell](/azure/cloud-shell)，透過此頁面的 [試試看] 按鈕建立適用於 Azure Dev Spaces 的叢集。 如果您未登入，請遵循提示使用 Azure 帳戶來登入，然後在 Azure Cloud Shell 提示字元出現時，於其中輸入命令。

## <a name="create-the-cluster"></a>建立叢集

首先，在其中建立資源群組[支援 Azure 開發空間的區域](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams)。

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

使用下列命令來建立 Kubernetes 叢集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region>
```

建立叢集需要幾分鐘的時間。  完成時，輸出會以 JSON 格式顯示。 尋找 `provisioningState` 並確認它 `Succeeded`。

## <a name="next-steps"></a>後續步驟

如需完整教學課程的連結，請參閱 [Azure Dev Spaces](/azure/dev-spaces/)。
