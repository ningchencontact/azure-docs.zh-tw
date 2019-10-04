---
title: 在 Azure Kubernetes Service 中使用受控識別
description: 瞭解如何在 Azure Kubernetes Service 中使用受控識別（AKS）
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: a5717d8ee44e4d2e086a6e7bc1b7c3d0deb614c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827549"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>預覽-在 Azure Kubernetes Service 中使用受控識別

目前，使用者必須提供服務主體，否則 AKS 會代表您建立一個，讓 AKS 叢集（特別是 Kubernetes 雲端提供者）在 Azure 中建立額外的資源，例如負載平衡器和受控磁片。 通常會使用到期日來建立服務主體。 叢集最後會到達需要更新服務主體的狀態，否則叢集將無法正常執行。 管理服務主體會增加複雜度。 受控識別基本上是服務主體的包裝函式，讓其管理變得更簡單。 深入瞭解[受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別一文。

AKS 會以系統指派的受控識別，以及另一個使用者指派的身分識別，來建立兩個受控識別。 Kubernetes 雲端提供者會使用系統指派的受控識別，代表使用者建立 Azure 資源。 此系統指派的受控識別的生命週期會系結至叢集叢集，並在刪除叢集時刪除。 AKS 也會建立使用者指派的受控識別，以用於授權 AKS 存取 Acr、kubelet 以取得 Azure 的中繼資料等等。

在此預覽期間，仍然需要服務主體。 它將用來授權附加元件，例如監視、虛擬節點、azure 原則和 HTTP 應用程式路由。 目前正在進行工作，以移除 SPN 上附加元件的相依性，而且最終將完全移除 AKS 中的 SPN 需求。

> [!IMPORTANT]
> AKS 預覽功能是自助加入宣告。 預覽會以「原樣」和「可用」的方式提供，並從服務等級協定中排除，並享有有限擔保。 AKS 預覽的部分是由客戶支援，以最大的方式來涵蓋。 因此，這些功能並不適用于生產環境使用。 如需其他資訊，請參閱下列支援文章：
>
> * [AKS 支援原則](support-policies.md)
> * [Azure 支援常見問題集](faq.md)

## <a name="before-you-begin"></a>開始之前

您必須滿足以下條件：

* 您也需要 Azure CLI version 2.0.70 或更新版本，以及 aks-preview 0.4.14 擴充功能

## <a name="install-latest-aks-cli-preview-extension"></a>安裝最新的 AKS CLI preview 擴充功能

您需要**aks-preview 0.4.14**延伸模組或更新版本。

```azurecli
az extension update --name aks-preview 
az extension list
```

> [!CAUTION]
> 當您在訂用帳戶上註冊功能時, 目前無法取消註冊該功能。 啟用一些預覽功能之後, 預設值可能會用於在訂用帳戶中建立的所有 AKS 叢集。 請勿在生產訂用帳戶上啟用預覽功能。 使用個別的訂用帳戶來測試預覽功能並收集意見反應。

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間，狀態才會顯示為 [*已註冊*]。 您可以使用 [az feature list] [az-feature-list] 命令來檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

註冊狀態時，請使用 [az provider register] [az-provider-register] 命令重新整理*microsoft.containerservice*資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identity"></a>建立具有受控識別的 AKS 叢集

您現在可以使用下列 CLI 命令，建立具有受控識別的 AKS 叢集
```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集
```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

## <a name="get-credentials-to-access-the-cluster"></a>取得用來存取叢集的認證
```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
在幾分鐘內建立叢集之後，您就可以部署應用程式工作負載並與之互動，就像是使用以服務主體為基礎的 AKS 叢集一樣。 

> [!IMPORTANT]
> * 只有在叢集建立期間，才可以啟用具有受控識別的 AKS 叢集
> * 無法更新/升級現有的 AKS 叢集以啟用受控識別