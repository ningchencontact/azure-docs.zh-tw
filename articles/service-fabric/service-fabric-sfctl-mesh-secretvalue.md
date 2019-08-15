---
title: Azure Service Fabric CLI - sfctl mesh secretvalue | Microsoft Docs
description: 介紹 Service Fabric CLI 的 sfctl mesh secretvalue 命令。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 5200c40afe62ce3a236a3a16b59b64deb8d24ced
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035926"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
取得和刪除網狀結構 mesh secretvalue 資源。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| 刪除 | 刪除選定祕密資源的指定值。 |
| 清單 | 列出指定祕密資源所有值的名稱。 |
| 顯示 | 擷取祕密資源指定版本的值。 |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete
刪除選定祕密資源的指定值。

刪除以名稱識別的祕密值資源。 資源的名稱一般是與該值相關聯的版本。 如果指定值正在使用中，則會刪除失敗。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --secret-name -n [必要] | 祕密資源的名稱。 |
| --version -v     [必要] | 祕密版本的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。 |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue list
列出指定祕密資源所有值的名稱。

取得指定祕密資源所有祕密值資源的相關資訊。 資訊包含祕密值資源的名稱，但並不包含實際值。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --secret-name -n [必要] | 祕密資源的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。 |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
擷取祕密資源指定版本的值。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --secret-name -n [必要] | 祕密資源的名稱。 |
| --version -v     [必要] | 祕密版本的名稱。 |
| --show-value | 顯示祕密版本的實際值。 |

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