---
title: 使用 KEDA 在 Kubernetes 上 Azure Functions
description: 瞭解如何使用 KEDA、Kubernetes 型事件驅動自動調整，在雲端或內部部署的 Kubernetes 中執行 Azure Functions。
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: azure 函式，函數，事件處理，動態計算，無伺服器架構，kubernetes
ms.service: azure-functions
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: 8e07032f84ead4bb003176af84cb4c731819ffa4
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900060"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>使用 KEDA 在 Kubernetes 上 Azure Functions

Azure Functions 執行時間可讓您彈性地裝載所需的位置和方式。  [KEDA](https://github.com/kedacore/kore) （以 Kubernetes 為基礎的事件驅動自動調整）可與 Azure Functions 執行時間和工具緊密結合，以在 Kubernetes 中提供事件驅動的規模。

## <a name="how-kubernetes-based-functions-work"></a>以 Kubernetes 為基礎的函式如何運作

Azure Functions 服務是由兩個主要元件所組成：執行時間和縮放控制器。  函式執行時間會執行並執行您的程式碼。  執行時間包含如何觸發、記錄和管理函數執行的邏輯。  另一個元件則是縮放控制器。  「縮放控制器」會監視以您的函式為目標的事件速率，並主動調整執行應用程式的實例數目。  若要深入了解，請參閱 [Azure Functions 規模調整和主控](functions-scale.md)。

以 Kubernetes 為基礎的函式會在[Docker 容器](functions-create-function-linux-custom-image.md)中提供函式執行時間，並透過 KEDA 進行事件驅動的調整。  KEDA 可以相應減少為0個實例（沒有發生任何事件時），以及多達*n*個實例。 其運作方式是公開 Kubernetes 自動調整程式的自訂計量（水準 Pod 自動調整程式）。  將函式容器與 KEDA 搭配使用，可讓您在任何 Kubernetes 叢集中複寫無伺服器函式功能。  這些函式也可以使用[Azure Kubernetes Services （AKS）虛擬節點](../aks/virtual-nodes-cli.md)功能進行部署，以進行無伺服器基礎結構。

## <a name="managing-keda-and-functions-in-kubernetes"></a>在 Kubernetes 中管理 KEDA 和函數

若要在 Kubernetes 叢集上執行函數，您必須安裝 KEDA 元件。 您可以使用[Azure Functions Core Tools](functions-run-local.md)安裝此元件。

### <a name="installing-with-the-azure-functions-core-tools"></a>使用 Azure Functions Core Tools 安裝

根據預設，核心工具會同時安裝 KEDA 和 Osiris 元件，分別支援事件驅動和 HTTP 調整。  安裝會使用在目前內容中執行 `kubectl`。

執行下列 install 命令，在您的叢集中安裝 KEDA：

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>將函數應用程式部署至 Kubernetes

您可以將任何函數應用程式部署到執行 KEDA 的 Kubernetes 叢集。  因為您的函式會在 Docker 容器中執行，所以您的專案需要 `Dockerfile`。  如果它還沒有，您可以在函式專案的根目錄執行下列命令來新增 Dockerfile：

```cli
func init --docker-only
```

若要建立映射，並將您的函式部署至 Kubernetes，請執行下列命令：

> [!NOTE]
> 核心工具會利用 docker CLI 來建立和發佈映射。 請確定已安裝 docker，並使用 `docker login`連接到您的帳戶。

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> 以函式應用程式的名稱取代 `<name-of-function-deployment>`。

這會建立 Kubernetes `Deployment` 資源、`ScaledObject` 資源和 `Secrets`，包括從您的 `local.settings.json` 檔案匯入的環境變數。

### <a name="deploying-a-function-app-from-a-private-registry"></a>從私用登錄部署函數應用程式

上述流程也適用于私用登錄。  如果您要從私人登錄提取容器映射，請在執行 `func kubernetes deploy`時，加入參照 Kubernetes 秘密的 `--pull-secret` 旗標，以保存私人登錄認證。

## <a name="removing-a-function-app-from-kubernetes"></a>從 Kubernetes 移除函式應用程式

部署之後，您可以藉由移除相關聯的 `Deployment`（`ScaledObject`）建立的 `Secrets` 來移除函式。

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>從 Kubernetes 卸載 KEDA

您可以執行下列 core tools 命令，從 Kubernetes 叢集中移除 KEDA：

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>KEDA 中支援的觸發程式

KEDA 目前處於搶鮮版（Beta），並支援下列 Azure 函數觸發程式：

* [Azure 儲存體佇列](functions-bindings-storage-queue.md)
* [Azure 服務匯流排佇列](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱下列資源：

* [使用自訂映射建立函式](functions-create-function-linux-custom-image.md)
* [撰寫 Azure Functions 並在本機進行測試](functions-develop-local.md)
* [Azure 函數耗用量方案的運作方式](functions-scale.md)