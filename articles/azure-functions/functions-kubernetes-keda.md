---
title: KEDA 的 Kubernetes 上的 azure 函式
description: 了解如何在雲端中的 Kubernetes 中執行 Azure Functions 或在內部使用 KEDA，Kubernetes 為基礎的事件驅動型自動調整。
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: azure functions，函式、 事件處理，動態計算，無伺服器架構，kubernetes
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: c82ed7aa841f53f5c81f3281ed1b09926e565e75
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077616"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>KEDA 的 Kubernetes 上的 azure 函式

Azure Functions 執行階段提供位置和方式想在裝載的彈性。  [KEDA](https://github.com/kedacore/kore) （Kubernetes 型事件驅動型自動調整） 配對順暢地與 Azure Functions 執行階段和工具，可提供在 Kubernetes 中的事件驅動的規模。

## <a name="how-kubernetes-based-functions-work"></a>工作如何以 Kubernetes 為基礎的函式

Azure Functions 服務組成的兩個主要元件： 執行階段 」 和 「 縮放控制器。  Functions 執行階段執行，並執行您的程式碼。  執行階段會包含有關如何觸發程序、 記錄檔，以及管理函式執行的邏輯。  另一個元件是縮放控制器。  縮放控制器會監視的目標您的函式的事件的速率，並主動調整執行您的應用程式的執行個體數目。  若要進一步了解，請參閱[Azure Functions 的級別和裝載](functions-scale.md)。

Kubernetes 型的函式提供中的函式執行階段[Docker 容器](functions-create-function-linux-custom-image.md)KEDA 透過事件導向調整。  KEDA 可以相應減少至 0 的執行個體 （時沒有發生任何事件） 和最多*n*執行個體。 它會藉由公開 Kubernetes autoscaler （水平 Pod 自動調整程式） 的自訂計量。  使用具有 KEDA 函式容器讓您更能夠複寫任何 Kubernetes 叢集中的無伺服器函式功能。  這些函式也可以部署使用[Azure Kubernetes Service (AKS) 的虛擬節點](../aks/virtual-nodes-cli.md)無伺服器的基礎結構的功能。

## <a name="managing-keda-and-functions-in-kubernetes"></a>管理 KEDA 和在 Kubernetes 中的函式

若要在 Kubernetes 叢集上執行函式，您必須安裝 KEDA 元件。 您可以安裝此元件使用[Azure Functions Core Tools](functions-run-local.md)。

### <a name="installing-with-the-azure-functions-core-tools"></a>安裝 Azure Functions Core Tools

根據預設，Core Tools 會安裝 KEDA 和奧西元件，支援事件驅動和 HTTP 縮放比例，分別。  安裝會使用`kubectl`目前內容中執行。

執行下列安裝命令，在叢集中安裝 KEDA:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>將函式應用程式部署至 Kubernetes

您可以將任何函式應用程式部署到執行 KEDA 的 Kubernetes 叢集。  因為您的函式執行的 Docker 容器中，您的專案需要`Dockerfile`。  如果它還沒有其中一個，您可以在函式專案的根目錄執行下列命令來新增 Dockerfile:

```cli
func init --docker-only
```

若要建置的映像，並將您的函式部署至 Kubernetes，執行下列命令：

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> 以函式應用程式的名稱取代 `<name-of-function-deployment>`。

這會建立 Kubernetes`Deployment`資源，`ScaledObject`資源，並`Secrets`，其中包含環境變數從匯入您`local.settings.json`檔案。

## <a name="removing-a-function-app-from-kubernetes"></a>移除 Kubernetes 中的函式應用程式

部署您可以藉由移除相關聯移除函式之後`Deployment`， `ScaledObject`、`Secrets`建立。

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>解除安裝 KEDA 從 Kubernetes

您可以執行下列的核心工具命令，以從 Kubernetes 叢集中移除 KEDA:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>支援的觸發程序中 KEDA

KEDA 目前為 beta 版並支援下列的 Azure 函式觸發程序：

* [Azure 儲存體佇列](functions-bindings-storage-queue.md)
* [Azure 服務匯流排佇列](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱下列資源：

* [建立使用自訂映像的函式](functions-create-function-linux-custom-image.md)
* [撰寫 Azure Functions 並在本機進行測試](functions-develop-local.md)
* [Azure Functions 取用方案的運作方式](functions-scale.md)