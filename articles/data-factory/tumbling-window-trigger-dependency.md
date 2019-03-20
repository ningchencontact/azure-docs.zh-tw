---
title: 在 Azure Data Factory 中建立輪轉視窗觸發程序相依性 | Microsoft Docs
description: 了解如何在 Azure Data Factory 中的輪轉視窗觸發程序上建立相依性。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: f3a547f5b953262d7263d1be0897161cf4091a1d
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574384"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>建立輪轉視窗觸發程序相依性

本文提供在輪轉視窗觸發程序上建立相依性的步驟。 如需輪轉視窗觸發程序的一般資訊，請參閱[如何建立輪轉視窗觸發程序](how-to-create-tumbling-window-trigger.md)。

若要建置相依性鏈結，並確保系統只會在成功執行 Data Factory 中的另一個觸發程序之後才執行某個觸發程序，請使用此進階功能來建立輪轉視窗相依性。

## <a name="create-a-dependency-in-the-data-factory-ui"></a>在 Data Factory UI 中建立相依性

若要在觸發程序上建立相依性，請選取 [觸發程序] > [進階] > [新增]，然後選擇要進行相依的觸發程序，以及適當的偏移與大小。 選取 [完成] 並發佈 Data Factory 變更來使相依性生效。

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png)

## <a name="tumbling-window-dependency-properties"></a>輪轉視窗相依性屬性

輪轉視窗觸發程序相依性具有下列屬性：

```json
{
    "name": "DemoTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00.000Z",
            "delay": "00:00:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": "-02:00:00",
                    "offset": "-01:00:00",
                    "referenceTrigger": {
                        "referenceName": "DemoDependency1",
                        "type": "TriggerReference"
                    }
                }
            ]
        }
    }
}
```

下表提供定義輪轉視窗相依性所需之屬性的清單。

| **屬性名稱** | **說明**  | **類型** | **必要** |
|---|---|---|---|
| 觸發程序  | 所有現有的輪轉視窗觸發程序都會顯示在此下拉式清單中。 選擇要採取相依性的觸發程序。  | TumblingWindowTrigger | 是 |
| Offset | 相依性觸發程序的偏移。 提供時間範圍格式的值，同時允許負值和正值。 如果觸發程序相依於自己，此參數便是必要參數；針對所有其他情況，此參數則為選擇性參數。 自我相依性應一律為負值偏移。 | Timespan | 自我相依性：是 其他：否 |
| 視窗大小 | 相依性輪轉視窗的大小。 以時間範圍格式提供值。 這是選擇性參數。 | Timespan | 否  |
|||||

## <a name="tumbling-window-self-dependency-properties"></a>輪轉視窗自我相依性屬性

在觸發程序不應在先前的視窗已成功完成之前便移至下一個視窗的案例中，請建置自我相依性。 自我相依性觸發程序將會有下列屬性：

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```

以下是對輪轉視窗相依性屬性之案例和使用情況的描述。

## <a name="dependency-offset"></a>相依性偏移

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png)

## <a name="dependency-size"></a>相依性大小

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png)

## <a name="self-dependency"></a>自我相依性

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png)

## <a name="usage-scenarios"></a>使用案例

### <a name="dependency-on-another-tumbling-window-trigger"></a>另一個輪轉視窗觸發程序上的相依性

例如，仰賴另一個每日作業的每日遙測處理作業，其會對過去七天的輸出進行彙總，並產生七天的輪流視窗資料流：

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png)

### <a name="dependency-on-itself"></a>對其本身的相依性

作業輸出資料流中沒有間隔的每日作業：

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png)

## <a name="monitor-dependencies"></a>監視相依性

您可以從觸發程序執行監視頁面監視相依性鏈結與對應的視窗。 請瀏覽到 [監視] > [觸發程序執行]。

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png)

按一下眼鏡圖示以檢視所選取視窗的所有相依觸發程序執行。

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png)

## <a name="next-steps"></a>後續步驟

檢閱[如何建立輪轉視窗觸發程序](how-to-create-tumbling-window-trigger.md)。