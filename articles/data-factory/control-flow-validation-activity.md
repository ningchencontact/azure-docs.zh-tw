---
title: Azure Data Factory 中的驗證活動 |Microsoft Docs
description: 驗證活動不會繼續執行管線, 直到它以使用者指定的特定準則驗證附加的資料集為止。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 77fdab04e03429d135875cb2ef223e8c23d312a2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141613"
---
# <a name="validation-activity-in-azure-data-factory"></a>Azure Data Factory 中的驗證活動
您可以在管線中使用驗證, 確保管線只會在驗證附加的資料集參考存在、符合指定的準則, 或已達到 timeout 時, 才繼續執行。


## <a name="syntax"></a>語法

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 允許的值 | 必要項
-------- | ----------- | -------------- | --------
name | 「驗證」活動的名稱 | String | 是 |
Type | 必須設定為 [**驗證**]。 | String | 是 |
資料集 | 活動會封鎖執行, 直到其驗證此資料集參考存在, 而且符合指定的準則, 或已達到 timeout 為止。 提供的資料集應該支援 "MinimumSize" 或 "ChildItems" 屬性。 | 資料集參考 | 是 |
逾時 | 指定活動執行的逾時。 如果未指定任何值, 預設值為7天 ("7.00:00:00")。 格式為 d. hh: mm: ss | String | 否 |
狀態 | 驗證嘗試之間的延遲 (以秒為單位)。 如果未指定任何值, 預設值為10秒。 | Integer | 否 |
childItems | 檢查資料夾是否有子專案。 可以設定為-true:驗證資料夾是否存在, 以及它是否有專案。 封鎖, 直到至少有一個專案出現在資料夾中, 或到達 timeout 值為止。-false:驗證資料夾是否存在, 以及它是否為空白。 封鎖, 直到資料夾是空的或到達 timeout 值為止。 如果未指定任何值, 活動將會封鎖, 直到資料夾存在或達到 timeout 為止。 | Boolean | 否 |
minimumSize | 檔案大小下限 (以位元組為單位)。 如果未指定任何值, 預設值為0個位元組 | Integer | 否 |


## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動：

- [If Condition 活動](control-flow-if-condition-activity.md)
- [執行管道活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
