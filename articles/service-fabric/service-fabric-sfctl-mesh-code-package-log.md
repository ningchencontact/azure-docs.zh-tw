---
title: Azure Service Fabric CLI- sfctl mesh code-package-log | Microsoft Docs
description: 描述 Service Fabric CLI sfctl mesh code-package-log 命令。
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
ms.openlocfilehash: d1f0e34389a48b79c049f26e8b04c870f0f1a9a7
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901272"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
針對指定服務複本，取得指定程式碼套件的容器記錄。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| get | 取得容器的記錄。 |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
取得容器的記錄。

取得服務複本指定程式碼套件的容器記錄。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --app-name --application-name [必要] | 應用程式的名稱。 |
| --code-package-name           [必要] | 服務程式碼套件的名稱。 |
| --replica-name                [必要] | Service Fabric 複本名稱。 |
| --service-name                [必要] | 服務的名稱。 |
| --tail | 要從記錄結尾顯示的行數。 預設值為 100。 'all' 表示顯示完整記錄。 |

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