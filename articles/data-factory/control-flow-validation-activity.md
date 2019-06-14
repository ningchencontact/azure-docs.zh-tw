---
title: Azure Data Factory 中的驗證活動 |Microsoft Docs
description: 驗證活動不會繼續執行管線，直到它會驗證連接的資料集，與特定使用者指定的準則。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764317"
---
# <a name="validation-activity-in-azure-data-factory"></a>Azure Data Factory 中的驗證活動
您也可以在管線中使用的驗證，確保管線只會繼續執行，一旦驗證附加有資料集參考，其符合指定的準則，或已達到逾時。


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
name | '驗證' 活動的名稱。 | 字串 | 是 |
type | 必須設定為**驗證**。 | 字串 | 是 |
資料集 | 活動會阻止執行直到驗證這個資料集參考存在，而且符合指定的準則，或已達到逾時。 提供的資料集應支援 「 MinimumSize"或"ChildItems"屬性。 | 資料集參考 | 是 |
timeout | 指定活動執行的逾時。 如果未不指定任何值，預設值為 7 天 (「 7.00:00:00")。 格式是 d.hh:mm:ss | 字串 | 否 |
進入睡眠狀態 | 以秒為單位驗證嘗試之間的延遲。 如果未不指定任何值，預設值為 10 秒。 | Integer | 否 |
childItems | 檢查資料夾是否有子項目。 您可以設定以 true:驗證資料夾存在，且其項目。 封鎖，直到至少一個項目在資料夾或達到逾時值為止。-false:驗證資料夾存在，而且是空白。 值已到達封鎖，直到資料夾是空的或直到逾時。 如果未不指定任何值，活動將會封鎖直到資料夾存在，或達到逾時為止。 | Boolean | 否 |
minimumSize | 以位元組為單位的檔案大小下限。 如果未不指定任何值，預設值為 0 個位元組 | Integer | 否 |


## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動：

- [If Condition 活動](control-flow-if-condition-activity.md)
- [執行管道活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
