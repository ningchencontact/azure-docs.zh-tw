---
title: Webhooks to respond to registry actions
description: Learn how to use webhooks to trigger events when push or pull actions occur in your registry repositories.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5e6fd2d9f4c7727365a8e2fe3893aafebfeb7bd4
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454368"
---
# <a name="using-azure-container-registry-webhooks"></a>使用 Azure Container Registry Webhook

Azure Container Registry 會儲存和管理私人 Docker 容器映像，其方式類似於 Docker 中樞儲存公用 Docker 映像。 It can also host repositories for [Helm charts](container-registry-helm-repos.md) (preview), a packaging format to deploy applications to Kubernetes. 在其中一個登錄存放庫發生特定動作時，您可以使用 Webhook 來觸發事件。 Webhook 可以回應登錄層級的事件，或可將範圍縮小至特定的存放庫標記。 With a  [geo-replicated](container-registry-geo-replication.md) registry, you configure each webhook to respond to events in a specific regional replica.

如需 Webhook 要求的詳細資訊，請參閱 [Azure Container Registry Webhook 結構描述參考](container-registry-webhook-reference.md)。

## <a name="prerequisites"></a>必要條件

* Azure 容器登錄 - 在 Azure 訂用帳戶中建立容器登錄。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。 The [Azure Container Registry SKUs](container-registry-skus.md) have different webhooks quotas.
* Docker CLI - 若要將您的本機電腦設定為 Docker 主機並存取 Docker CLI 命令，請安裝 [Docker 引擎](https://docs.docker.com/engine/installation/)。

## <a name="create-webhook---azure-portal"></a>Create webhook - Azure portal

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至您要在其中建立 Webhook 的容器登錄。
1. Under **Services**, select **Webhooks**.
1. 選取 [Webhook] 工具列中的 [新增]。
1. 填入下列資訊以完成 [建立 Webhook] 表單：

| Value | 描述 |
|---|---|
| Webhook name | 您想要提供給 Webhook 的名稱。 It may contain only letters and numbers, and must be 5-50 characters in length. |
| Location | For a [geo-replicated](container-registry-geo-replication.md) registry, specify the Azure region of the registry replica. 
| 服務 URI | Webhook 需在當中傳送 POST 通知的 URI。 |
| 自訂標頭 | 您想要與 POST 要求一起傳遞的標頭。 它們應該為「金鑰：值」的格式。 |
| 觸發程序動作 | 觸發 Webhook 的動作。 Actions include image push, image delete, Helm chart push, Helm chart delete, and image quarantine. You can choose one or more actions to trigger the webhook. |
| 狀態 | Webhook 建立之後的狀態。 此選項預設為啟用狀態。 |
| Scope | Webhook 的運作範圍。 If not specified, the scope is for all events in the registry. It can be specified for a repository or a tag by using the format "repository:tag", or "repository:*" for all tags under a repository. |

範例 Webhook 表單：

![在 Azure 入口網站中建立 ACR Webhook](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Create webhook - Azure CLI

若要使用 Azure CLI 建立 Webhook，請使用 [az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create) 命令。 The following command creates a webhook for all image delete events in the registry *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>測試 Webhook

### <a name="azure-portal"></a>Azure Portal

Prior to using the webhook, you can test it with the **Ping** button. Ping 會將泛型 POST 要求傳送至指定的端點，並記錄回應。 使用 Ping 功能可協助您確認您已正確設定 Webhook。

1. 選取您想要測試的 Webhook。
2. 在頂端工具列中，選取 [Ping]。
3. 檢查 HTTP STATUS 資料行中的端點回應。

![在 Azure 入口網站中建立 ACR Webhook](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 測試 ACR Webhook，請使用 [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) 命令。

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

若要查看結果，請使用 [az acr webhook list-events](/cli/azure/acr/webhook) 命令。

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>刪除 Webhook

### <a name="azure-portal"></a>Azure Portal

依序選取 Azure 入口網站上的 [Webhook] 和 [刪除] 按鈕，就可以將每個 Webhook 刪除。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>後續步驟

### <a name="webhook-schema-reference"></a>Webhook 結構描述參考

若要深入了解 Azure Container Registry 所發出的 JSON 事件裝載格式與內容，請參閱 Webhook 結構描述參考：

[Azure Container Registry Webhook 結構描述參考](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>事件方格的事件

除了本文所討論的原生登錄 Webhook 事件，Azure Container Registry 可將事件發送至事件方格：

[快速入門：將容器登錄事件傳送至事件方格](container-registry-event-grid-quickstart.md)
