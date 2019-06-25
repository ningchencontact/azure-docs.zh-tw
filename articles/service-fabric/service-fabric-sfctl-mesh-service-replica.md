---
title: Azure Service Fabric CLI- sfctl mesh service-replica | Microsoft Docs
description: 描述 Service Fabric CLI sfctl mesh service-replica 命令。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 1e0955ef2a52a6313f0449b956229e03f0a5b5e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61038443"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
取得複本服務詳細資料和應用程式資源中指定服務的清單複本。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| list | 列出服務的所有複本。 |
| 顯示 | 取得應用程式服務的指定複本。 |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh service-replica list
列出服務的所有複本。

取服務所有複本的相關資訊。 資訊包括服務複本的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --app-name --application-name [必要] | 應用程式的名稱。 |
| --service-name                [必要] | 服務的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。 |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replica show
取得應用程式服務的指定複本。

取得指定名稱服務複本的相關資訊。 資訊包括服務複本的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --app-name --application-name [必要] | 應用程式的名稱。 |
| --name -n                     [必要] | 服務複本的名稱。 |
| --service-name                [必要] | 服務的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。 |


## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。