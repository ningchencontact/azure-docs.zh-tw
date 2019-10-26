---
title: Azure Service Fabric CLI- sfctl mesh app | Microsoft Docs
description: 描述 Service Fabric CLI sfctl mesh app 命令。
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
ms.openlocfilehash: 18cf1107ed86915f96dbc92d588a6ebedab530b4
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901467"
---
# <a name="sfctl-mesh-app"></a>sfctl mesh app
取得和刪除應用程式資源。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| delete | 可刪除應用程式資源。 |
| list | 可列出所有應用程式資源。 |
| 顯示 | 可取得指定名稱的應用程式資源。 |

## <a name="sfctl-mesh-app-delete"></a>sfctl mesh app delete
可刪除應用程式資源。

可刪除透過名稱識別的應用程式資源。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --name -n [必要] | 應用程式的名稱。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。 |

## <a name="sfctl-mesh-app-list"></a>sfctl mesh app list
可列出所有應用程式資源。

可取得指定資源群組中所有應用程式資源的相關資訊。 資訊包括應用程式的描述與其他屬性。

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。 |

## <a name="sfctl-mesh-app-show"></a>sfctl mesh app show
可取得指定名稱的應用程式資源。

可取得指定名稱應用程式資源的相關資訊。 資訊包括應用程式的描述與其他屬性。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --name -n [必要] | 應用程式的名稱。 |

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