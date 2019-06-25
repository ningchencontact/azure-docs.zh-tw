---
title: Azure Service Fabric CLI- sfctl mesh volume | Microsoft Docs
description: 描述 Service Fabric CLI sfctl mesh volume 命令。
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
ms.openlocfilehash: 740aec6ccb9b20cbcb8f55b2518c2b2539ef82ac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60836904"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
取得和刪除磁碟區資源。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| delete | 可刪除磁碟區資源。 |
| list | 可列出所有磁碟區資源。 |
| 顯示 | 可取得指定名稱的磁碟區資源。 |

## <a name="sfctl-mesh-volume-delete"></a>sfctl mesh volume delete
可刪除磁碟區資源。

可刪除透過名稱識別的磁碟區資源。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --name -n [必要] | 磁碟區的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。 |

## <a name="sfctl-mesh-volume-list"></a>sfctl mesh volume list
可列出所有磁碟區資源。

可取得指定資源群組中所有磁碟區資源的相關資訊。 資訊包括磁碟區的描述與其他屬性。

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。 |

## <a name="sfctl-mesh-volume-show"></a>sfctl mesh volume show
可取得指定名稱的磁碟區資源。

可取得指定名稱磁碟區資源的相關資訊。 資訊包括磁碟區的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --name -n [必要] | 磁碟區的名稱。 |

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