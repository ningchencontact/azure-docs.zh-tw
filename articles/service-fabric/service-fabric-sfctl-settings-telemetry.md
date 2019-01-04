---
title: Azure Service Fabric CLI- sfctl settings telemetry | Microsoft Docs
description: 描述 Service Fabric CLI sfctl settings telemetry 命令。
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
ms.openlocfilehash: e0e229f0edb078fe9ce289d0089f34d7cbf9dbf8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283764"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
在 sfctl 的此執行個體設定遙測設定。

Sfctl telemetry 會收集命令名稱 (不提供參數或其名稱)、sfctl 版本、OS 類型、python 版本、命令成功或失敗，以及傳回的錯誤訊息。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
| set-telemetry | 開啟或關閉遙測。 |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
開啟或關閉遙測。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --off | 關閉遙測。 |
| --on | 開啟遙測。 這是預設值。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。 |

### <a name="examples"></a>範例

關閉遙測。

```
sfctl settings telemetry set_telemetry --off
```

開啟遙測。

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。