---
title: Azure Container Registry Webhook 結構描述參考
description: Webhook 會向 JSON 承載參考要求 Azure Container Registry。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/05/2019
ms.author: danlep
ms.openlocfilehash: 4c0845b9cf5194ecbd0ab813997e17e070840f44
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099894"
---
# <a name="azure-container-registry-webhook-reference"></a>Azure Container Registry Webhook 參考

當某些動作對產生事件的容器登錄執行時，您可以針對產生事件的容器登錄[設定 Webhook](container-registry-webhook.md)。 例如，啟用容器映像或 Helm 圖表會推送至登錄，或刪除時觸發的 webhook。 當 Webhook 觸發時，Azure Container Registry 就會發出 HTTP 或 HTTPS 要求，當中包含對您所指定端點之事件的相關資訊。 然後，您的端點就可以據此處理 webhook 並採取行動。

下列各節詳細說明受支援事件所產生的 webhook 要求之結構描述。 事件區段包含事件類型的承載結構描述、範例要求承載，以及一或多個會觸發 webhook 的範例命令。

如需設定 Azure Container Registry 的 Webhook 相關資訊，請參閱[使用 Azure Container Registry Webhook](container-registry-webhook.md)。

## <a name="webhook-requests"></a>Webhook 要求

### <a name="http-request"></a>HTTP 要求

當您設定 webhook 時，觸發的 Webhook 會使 HTTP `POST` 要求您指定的 URL 端點。

### <a name="http-headers"></a>HTTP 標頭

如果您尚未指定 webhook 的 `Content-Type` 自訂標頭，Webhook 要求就會包含 `application/json` 的 `Content-Type`。

不會有其他標頭新增至超出您可能已針對 webhook 指定的這些自訂標頭。

## <a name="push-event"></a>推送事件

當容器映像推送到存放庫時，會觸發 Webhook。

### <a name="push-event-payload"></a>推送事件裝載

|元素|類型|描述|
|-------------|----------|-----------|
|`id`|字串|Webhook 事件的識別碼。|
|`timestamp`|DateTime|Webhook 事件觸發的時間。|
|`action`|字串|觸發 webhook 事件的動作。|
|[目標](#target)|複雜類型|觸發 webhook 事件的事件目標。|
|[要求](#request)|複雜類型|產生 webhook 事件的要求。|

### <a name="target"></a>target

|元素|類型|描述|
|------------------|----------|-----------|
|`mediaType`|字串|參考物件的 MIME 類型。|
|`size`|Int32|內容的位元組數目。 與長度欄位相同。|
|`digest`|字串|如 Registry V2 HTTP API 規格所定義的內容摘要。|
|`length`|Int32|內容的位元組數目。 [與大小相同] 欄位。|
|`repository`|字串|存放庫名稱。|
|`tag`|字串|映像標記名稱。|

### <a name="request"></a>request

|元素|類型|描述|
|------------------|----------|-----------|
|`id`|字串|起始事件之要求的識別碼。|
|`host`|字串|登錄執行個體的外部可存取主機名稱，如內送要求上的 HTTP 主機標頭所指定。|
|`method`|字串|產生事件的要求方法。|
|`useragent`|字串|要求的使用者代理程式標頭。|

### <a name="payload-example-image-push-event"></a>承載範例： 映像推送事件

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

範例[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)觸發程序映像的命令**推播**webhook 事件：

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>圖表推送事件

Helm 圖表會推送至儲存機制時觸發的 Webhook。

### <a name="chart-push-event-payload"></a>圖表推送事件裝載

|元素|類型|描述|
|-------------|----------|-----------|
|`id`|字串|Webhook 事件的識別碼。|
|`timestamp`|DateTime|Webhook 事件觸發的時間。|
|`action`|字串|觸發 webhook 事件的動作。|
|[目標](#helm_target)|複雜類型|觸發 webhook 事件的事件目標。|

### <a name="helm_target"></a>target

|元素|類型|描述|
|------------------|----------|-----------|
|`mediaType`|字串|參考物件的 MIME 類型。|
|`size`|Int32|內容的位元組數目。|
|`digest`|字串|如 Registry V2 HTTP API 規格所定義的內容摘要。|
|`repository`|字串|存放庫名稱。|
|`tag`|字串|圖表的標記名稱。|
|`name`|字串|圖表名稱。|
|`version`|字串|圖表的版本。|

### <a name="payload-example-chart-push-event"></a>承載範例： 圖表推送事件

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

範例[Azure CLI](/cli/azure/acr)觸發程序的命令**chart_push** webhook 事件：

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>刪除事件

Webhook 觸發時的映像儲存機制，或刪除資訊清單。 刪除標記時不會觸發。

### <a name="delete-event-payload"></a>刪除事件裝載

|元素|類型|描述|
|-------------|----------|-----------|
|`id`|字串|Webhook 事件的識別碼。|
|`timestamp`|DateTime|Webhook 事件觸發的時間。|
|`action`|字串|觸發 webhook 事件的動作。|
|[目標](#delete_target)|複雜類型|觸發 webhook 事件的事件目標。|
|[要求](#delete_request)|複雜類型|產生 webhook 事件的要求。|

### <a name="delete_target"></a> 目標

|元素|類型|描述|
|------------------|----------|-----------|
|`mediaType`|字串|參考物件的 MIME 類型。|
|`digest`|字串|如 Registry V2 HTTP API 規格所定義的內容摘要。|
|`repository`|字串|存放庫名稱。|

### <a name="delete_request"></a> 要求

|元素|類型|描述|
|------------------|----------|-----------|
|`id`|字串|起始事件之要求的識別碼。|
|`host`|字串|登錄執行個體的外部可存取主機名稱，如內送要求上的 HTTP 主機標頭所指定。|
|`method`|字串|產生事件的要求方法。|
|`useragent`|字串|要求的使用者代理程式標頭。|

### <a name="payload-example-image-delete-event"></a>承載範例： 映像刪除事件

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

會觸發**刪除** 事件 webhook 的範例 [Azure CLI](/cli/azure/acr) 命令：

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>圖表刪除事件

Helm 圖表或儲存機制刪除時觸發的 Webhook。 

### <a name="chart-delete-event-payload"></a>圖表刪除事件裝載

|元素|類型|描述|
|-------------|----------|-----------|
|`id`|字串|Webhook 事件的識別碼。|
|`timestamp`|DateTime|Webhook 事件觸發的時間。|
|`action`|字串|觸發 webhook 事件的動作。|
|[目標](#chart_delete_target)|複雜類型|觸發 webhook 事件的事件目標。|

### <a name="chart_delete_target"></a> 目標

|元素|類型|描述|
|------------------|----------|-----------|
|`mediaType`|字串|參考物件的 MIME 類型。|
|`size`|Int32|內容的位元組數目。|
|`digest`|字串|如 Registry V2 HTTP API 規格所定義的內容摘要。|
|`repository`|字串|存放庫名稱。|
|`tag`|字串|圖表的標記名稱。|
|`name`|字串|圖表名稱。|
|`version`|字串|圖表的版本。|

### <a name="payload-example-chart-delete-event"></a>承載範例： 圖表刪除事件

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

範例[Azure CLI](/cli/azure/acr)觸發程序的命令**chart_delete** webhook 事件：

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>後續步驟

[使用 Azure Container Registry Webhook](container-registry-webhook.md)