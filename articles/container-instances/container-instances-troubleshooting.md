---
title: 針對 Azure Container Instances 進行疑難排解
description: 了解如何使用 Azure Container Instances 進行問題的疑難排解
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a527939d6bc73e3dee5701bc53ef8312e68d2953
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>使用 Azure Container Instances 進行部署問題的疑難排解

本文說明如何在將容器部署至 Azure Container Instances 時進行問題的疑難排解。 此外，也會說明一些您可能會碰到的常見問題。

## <a name="view-logs-and-stream-output"></a>檢視記錄和串流輸出

如果您的容器發生異常，可先透過 [az container logs][az-container-logs] 檢查其記錄，然後使用 [az container attach][az-container-attach] 串流其標準輸出和標準錯誤。

### <a name="view-logs"></a>檢視記錄檔

若要在容器內檢視應用程式程式碼中的記錄，您可以使用 [az container logs][az-container-logs] 命令。

以下記錄輸出是來自[在 ACI 中執行容器化工作](container-instances-restart-policy.md)中的工作型容器範例，這是讓容器處理無效 URL 之後的記錄輸出：

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

### <a name="attach-output-streams"></a>附加輸出資料流

[az container attach][az-container-attach] 命令會提供容器啟動期間的診斷資訊。 容器啟動之後，會將 STDOUT 和 STDERR 串流至您的本機主控台。

例如，以下輸出是來自[在 ACI 中執行容器化工作](container-instances-restart-policy.md)的工作型容器，這是處理有效大型文字檔 URL 之後的輸出：

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-09 23:21:33+00:00) pulling image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Successfully pulled image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Created container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Started container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>取得診斷事件

如果容器的部署並未成功，您就需要檢閱由 Azure Container Instances 資源提供者所提供的診斷資訊。 若要檢視容器的事件，請執行 [az container show][az-container-show]：

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

輸出中會包含容器的核心屬性以及部署事件 (此處顯示已截斷)：

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```

## <a name="common-deployment-issues"></a>常見部署問題

下列幾節將說明造成容器部署中大部分錯誤的常見問題：

* [不支援的映像版本](#image-version-not-supported)
* [無法提取映像](#unable-to-pull-image)
* [容器不斷結束又重新啟動](#container-continually-exits-and-restarts)
* [容器要等很久才會啟動](#container-takes-a-long-time-to-start)
* [「資源無法使用」錯誤](#resource-not-available-error)

## <a name="image-version-not-supported"></a>不支援映像版本

如果您指定 Azure 容器執行個體無法支援的映像，則會傳回 `ImageVersionNotSupported` 錯誤。 錯誤的值是 `The version of image '{0}' is not supported.`，且目前會發生於 Windows 1709 映像。 若要解決這個問題，請使用 LTS Windows 映像。 Windows 1709 映像支援正在進行中。

## <a name="unable-to-pull-image"></a>無法提取映像

如果 Azure Container Instances 一開始無法提取您的映像，它會先重試一段時間，最後才會失敗。 如果它無法提取映像，系統便會顯示如 [az container show][az-container-show] 輸出中所示的事件：

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

若要解決，請刪除容器並重試部署，特別注意您所輸入的映像名稱是否正確。

## <a name="container-continually-exits-and-restarts"></a>容器不斷結束又重新啟動

如果您的容器執行到完成又自動重新啟動，可能需要設定 **OnFailure** 或 **Never** 的 [restart policy](container-instances-restart-policy.md) (重新啟動原則)。 如果指定 **OnFailure** 後仍持續重新啟動，可能是容器中執行的應用程式或指令碼的問題。

容器執行個體 API 有一個 `restartCount` 屬性。 若要檢查容器的重新啟動次數，可以在 Azure CLI 2.0 中使用 [az container show][az-container-show] 命令。 在下列範例輸出中 (為簡潔起見已截斷畫面)，您可以在輸出的結尾看到 `restartCount` 屬性。

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Linux 散發套件的大部分容器映像都會設定殼層 (例如 bash) 來作為預設命令。 因為殼層本身不是長時間執行的服務，當設定為預設的 **Always** 重新啟動原則時，這些容器會立即結束並落入重新啟動迴圈。

## <a name="container-takes-a-long-time-to-start"></a>容器要等很久才會啟動

在 Azure 容器執行個體中，影響容器啟動時間的兩個主要因素如下：

* [映像大小](#image-size)
* [映像位置](#image-location)

Windows 映像會有[其他考量](#use-recent-windows-images)。

### <a name="image-size"></a>映像大小

如果您的容器要等很久才會啟動，但最終還是會啟動成功，請先看看您的容器映像大小。 因為 Azure Container Instances 會視需要來提取您的容器映像，因此啟動時間的長短會與其大小直接相關。

您可以在 Docker CLI 中使用 `docker images` 命令來檢視容器映像的大小：

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

讓映像不會變得太大的關鍵在於，確保最終的映像不會包含執行階段所不需要的任何項目。 若要做到這一點，有一種方式是使用[多階段建置][docker-multi-stage-builds]。 多階段建置可讓您輕鬆地確保最終映像只包含應用程式所需的構件，而不會包含建置階段所需的任何額外內容。

### <a name="image-location"></a>映像位置

另一種可在容器啟動階段降低對於映像提取作業影響的方式，是在您想要部署容器執行個體的相同區域中，將容器映像裝載在 [Azure Container Registry](/azure/container-registry/) 中。 這種方式會縮短容器映像需要經過的網路路徑，從而大幅縮短下載時間。

### <a name="use-recent-windows-images"></a>使用新的 Windows 映像

針對以特定 Windows 映像為基礎的映像，Azure 容器執行個體使用的快取機制有助於加快容器啟動時間。

若要確保擁有最快速的 Windows 容器啟動時間，請在以下**兩個映像**的**最新三個**版本中選一個作為基礎映像：

* [Windows Server 2016][docker-hub-windows-core] (僅限 LTS)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

## <a name="resource-not-available-error"></a>資源無法使用錯誤

Azure 中有各種不同的地區資源負載，因此您在嘗試部署容器執行個體時，可能會收到下列錯誤訊息：

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

此錯誤訊息表示您嘗試執行部署產品的地區負載過重，因此當時無法配置您為容器指定的資源。 執行下列一或多個風險降低步驟有助於解決問題。

* 確認容器部署設定是否位於 [Azure Container Instances 的配額與地區可用性](container-instances-quotas.md#region-availability)所定義的參數範圍內
* 為容器指定較低階的 CPU 和記憶體設定
* 部署至其他 Azure 地區
* 過一段時間再部署

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show