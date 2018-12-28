---
title: Azure Service Fabric CLI- sfctl mesh secret | Microsoft Docs
description: 描述 Service Fabric CLI sfctl mesh secret 命令。
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
ms.openlocfilehash: e2dc9f815fc578bbe1f01b6b64327ccf23a1aebb
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283384"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
取得和刪除 mesh 秘密資源。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
| delete | 刪除秘密資源。 |
| list | 列出所有秘密資源。 |
| 顯示 | 取得指定名稱的秘密資源。 |

## <a name="sfctl-mesh-secret-delete"></a>sfctl mesh secret delete
刪除秘密資源。

刪除指定的秘密資源和其所有具名值。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --name -n [必要] | 秘密的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。 |

## <a name="sfctl-mesh-secret-list"></a>sfctl mesh secret list
列出所有秘密資源。

取得指定資源群組中所有秘密資源的相關資訊。 資訊包括秘密的描述與其他屬性。

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。 |

## <a name="sfctl-mesh-secret-show"></a>sfctl mesh secret show
取得指定名稱的秘密資源。

取得指定名稱秘密資源的相關資訊。 資訊包括秘密的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --name -n [必要] | 秘密的名稱。 |

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