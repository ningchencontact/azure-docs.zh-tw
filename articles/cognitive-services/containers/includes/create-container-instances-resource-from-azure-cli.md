---
title: 容器支援
titleSuffix: Azure Cognitive Services
description: 了解如何從 Azure CLI 建立 Azure 容器執行個體資源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 5e7a3d849f726ae4dbbd559d541464404e427775
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717062"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>從 Azure CLI 建立 Azure 容器執行個體資源

下列 YAML 定義 Azure 容器執行個體的資源。 複製並貼到新的檔案內容，名為`my-aci.yaml`並取代您自己加上註解的值。 請參閱 [範本 format] [範本-格式化] 的有效 YAML。 請參閱[容器儲存機制和映像][repositories-and-images]可用映像名稱和其對應的儲存機制。

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> 並非所有位置都有相同的 CPU 和記憶體可用性。 請參閱[位置和資源][location-to-resource]資料表的清單中可用的資源，適用於每個位置和 OS 的容器。

我們針對所建立的 YAML 檔案會依賴[ `az container create` ][azure-container-create]命令。 從 Azure CLI，執行`az container create`命令取代`<resource-group>`以您自己。 此外，來保護內 YAML 值部署請參閱[保護值][secure-values]。

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

此命令的輸出是`Running...`如果有效，稍待片刻後輸出變更為 JSON 字串，表示新建立的 ACI 資源。 容器映像為多個可能無法使用一段時間，但現在已部署資源。

> [!TIP]
> 如果視 YAML 會據以調整為符合的位置，請密切注意的公開預覽 Azure 認知服務供應項目位置。

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values