---
title: Azure Service Fabric CLI- sfctl mesh network | Microsoft Docs
description: 描述 Service Fabric CLI sfctl mesh network 命令。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 8828605241d55ac6f9ce789ccc55c30f2cd36434
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283494"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
可取得和刪除 mesh 網路資源。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
| delete | 可刪除網路資源。 |
| list | 可列出所有網路資源。 |
| 顯示 | 可取得指定名稱的網路資源。 |

## <a name="sfctl-mesh-network-delete"></a>sfctl mesh network delete
可刪除網路資源。

可刪除透過名稱識別的網路資源。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --name -n [必要] | 網路的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。 |

## <a name="sfctl-mesh-network-list"></a>sfctl mesh network list
可列出所有網路資源。

可取得指定資源群組中所有網路資源的相關資訊。 資訊包括網路的描述與其他屬性。

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。 |

## <a name="sfctl-mesh-network-show"></a>sfctl mesh network show
可取得指定名稱的網路資源。

可取得指定名稱網路資源的相關資訊。 資訊包括網路的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --name -n [必要] | 網路的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。 |


## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。