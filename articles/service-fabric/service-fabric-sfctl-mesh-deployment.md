---
title: Azure Service Fabric CLI- sfctl mesh deployment | Microsoft Docs
description: 描述 Service Fabric CLI sfctl mesh deployment 命令。
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: dbecf8e96c6cb5d0d6d873309d848304fe33eaaa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901260"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
建立 Service Fabric Mesh 資源。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| create | 建立 Service Fabric Mesh 資源的部署。 |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
建立 Service Fabric Mesh 資源的部署。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --input-yaml-files [必要] | 以逗號分隔的所有 yaml 檔案的相對/絕對檔案路徑，或包含 yaml 檔案之目錄的相對/絕對路徑（遞迴）。 |
| --parameters | Yaml 檔案或 json 物件的相對/絕對路徑，其中包含需要覆寫的參數。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。 |

### <a name="examples"></a>範例

可透過覆寫 yaml 檔案中提到的參數，將所有資源合併並部署到叢集
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

可透過覆寫 yaml 檔案中提到的參數，將目錄中的所有資源合併並部署到叢集

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

藉由覆寫直接傳遞為 json 物件的參數，將目錄中的所有資源合併並部署到叢集
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。