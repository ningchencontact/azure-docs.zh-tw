---
title: Azure Service Fabric CLI- sfctl mesh service | Microsoft Docs
description: 描述 Service Fabric CLI sfctl mesh service 命令。
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
ms.openlocfilehash: d2ddc34f8b298a4037a1c8286eceb9ef13b1f416
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901159"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
取得服務詳細資料和應用程式資源的清單服務。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| list | 可列出所有服務資源。 |
| 顯示 | 可取得指定名稱的服務資源。 |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
可列出所有服務資源。

可取得應用程式資源所有服務的相關資訊。 資訊包括服務的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --app-name --application-name [必要] | 應用程式的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。 |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
可取得指定名稱的服務資源。

可取得指定名稱服務資源的相關資訊。 資訊包括服務的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --app-name --application-name [必要] | 應用程式的名稱。 |
| --name -n                     [必要] | 服務的名稱。 |

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