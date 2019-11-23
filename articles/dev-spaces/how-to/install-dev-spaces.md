---
title: Install Azure Dev Spaces on AKS & the client-side tooling
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Learn how to install Azure Dev Spaces on an AKS cluster and install the client-side tooling.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由, kubectl, k8s
ms.openlocfilehash: 2649b36c96313d4a7d878a1c72c3b175ad0f4d30
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325787"
---
# <a name="install-azure-dev-spaces-on-aks-and-the-client-side-tooling"></a>Install Azure Dev Spaces on AKS and the client-side tooling

This article shows you several ways to install Azure Dev Spaces on an AKS cluster as well as install the client-side tooling.

## <a name="install-azure-dev-spaces-using-the-cli"></a>Install Azure Dev Spaces using the CLI

Before you can install Dev Spaces using the CLI, you need:
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶][az-portal-create-account]。
* [The Azure CLI installed][install-cli].
* [An AKS cluster][create-aks-cli] in a [supported region][supported-regions].

使用 `use-dev-spaces` 命令在 AKS 叢集上啟用 Dev Spaces，並遵循提示來進行。

```cmd
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

The above command enables Dev Spaces on the *myAKSCluster* cluster in the *myResourceGroup* group and creates a *default* dev space.

```cmd
$ az aks use-dev-spaces -g myResourceGroup -n myAKSCluster

'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

The `use-dev-spaces` command also installs the Azure Dev Spaces CLI.

## <a name="install-azure-dev-spaces-using-the-azure-portal"></a>Install Azure Dev Spaces using the Azure portal

Before you can install Dev Spaces using the Azure portal, you need:
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶][az-portal-create-account]。
* [An AKS cluster][create-aks-portal] in a [supported region][supported-regions].

To install Azure Dev Spaces using the Azure portal:
1. 登入 [Azure 入口網站][az-portal]。
1. Navigate to your AKS cluster.
1. Click *Dev Spaces*.
1. 將 [啟用 Dev Spaces] 變更為 [是]，然後按一下 [儲存]。

![在 Azure 入口網站中啟用 Dev Spaces](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Installing Azure Dev Spaces using the Azure portal **does not** install any client-side tooling for Azure Dev Spaces.

## <a name="install-the-client-side-tooling"></a>Install the client-side tooling

You can use the Azure Dev Spaces client-side tooling to interact with dev spaces on an AKS cluster from your local machine. There are several ways to install the client-side tooling:

* In [Visual Studio Code][vscode], install the [Azure Dev Spaces extension][vscode-extension].
* In [Visual Studio 2019][visual-studio], install the Azure Development workload.
* In Visual Studio 2017, install the Web Development workload and [Visual Studio Tools for Kubernetes][visual-studio-k8s-tools].
* Download and install the [Windows][cli-win], [Mac][cli-mac], or [Linux][cli-linux] CLI.

## <a name="next-steps"></a>後續步驟

了解 Azure Dev Spaces 如何協助您跨多個容器開發更複雜的應用程式，以及如何藉由在不同的空間中使用不同的程式碼版本或分支，來簡化共同開發。

> [!div class="nextstepaction"]
> [在 Azure Dev Spaces 中進行小組開發][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
