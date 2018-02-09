---
title: "了解自動調整規模設定 | Microsoft Docs"
description: "自動調整規模設定及其運作方式的詳細解說。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ce2930aa-fc41-4b81-b0cb-e7ea922467e1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: ancav
ms.openlocfilehash: 79602cf053d834bf3d6dc6b4d5568637b179d5c7
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2018
---
# <a name="understand-autoscale-settings"></a>了解自動調整規模設定
自動調整規模設定可讓您確保執行中的資源數量正確，能夠處理變動的應用程式負載。 您可以設定根據指出負載或效能的計量，或是在排定的日期和時間，觸發自動調整規模設定。 本文將詳細探討自動調整規模設定的剖析。 本文會從了解一個設定的結構描述和屬性開始，接著逐步解說可設定的各種不同設定檔類型，最後再探討自動調整規模如何評估在任何指定時間需要執行哪個設定檔。

## <a name="autoscale-setting-schema"></a>自動調整規模設定結構描述
為了說明自動調整規模設定結構描述，將會使用以下自動調整規模設定。 請務必注意，此自動調整規模設定具有：
- 一個設定檔 
- 此設定檔中有兩個計量規則；一個用於相應放大，一個用於相應縮小。
- 當虛擬機器擴展集的平均「百分比 CPU」計量在過去 10 分鐘大於 85% 時，就會觸發相應放大規則。
- 當虛擬機器擴展集的平均值在過去 1 分鐘小於 60% 時，就會觸發相應縮小規則。

> [!NOTE]
> 一個設定可以有多個設定檔，請跳至[設定檔](#autoscale-profiles)一節以深入了解。
> 一個設定檔也可以有多個已定義的相應放大規則和相應縮小規則，請跳至[評估](#autoscale-evaluation)一節以查看規則評估方式。

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| 區段 | 元素名稱 | 描述 |
| --- | --- | --- |
| 設定 | 識別碼 | 自動調整規模設定的資源識別碼。 自動調整規模設定是 Azure Resource Manager 資源。 |
| 設定 | name | 自動調整規模設定名稱。 |
| 設定 | location | 自動調整規模設定的位置。 此位置可以與要調整規模的資源位置不同。 |
| properties | targetResourceUri | 要調整規模之資源的資源識別碼。 每個資源都只能有一個自動調整規模設定。 |
| properties | profiles | 自動調整規模設定是由一或多個設定檔所組成。 每次自動調整規模引擎執行時，都會執行一個設定檔。 |
| profile | name | 設定檔的名稱，您可以選擇任何有助於識別設定檔的名稱。 |
| profile | Capacity.maximum | 允許的最大容量。 執行此設定檔時，可確保自動調整規模不會將資源的規模調整到大於此數字。 |
| profile | Capacity.minimum | 允許的最小容量。 執行此設定檔時，可確保自動調整規模不會將資源的規模調整到小於此數字。 |
| profile | Capacity.default | 如果在讀取資源計量 (在此案例中為 “vmss1” 的 CPU) 時發生問題，而目前的容量低於預設容量，則為了確保資源的可用性，自動調整規模將會相應放大至預設值。 如果目前的容量已超過預設容量，自動調整規模將不會進行相應縮小。 |
| profile | rules | 自動調整規模會使用設定檔中的規則，在最大容量與最小容量之間進行調整。 一個設定檔中可以有多個規則。 基本案例是有兩個規則，一個用於決定何時要相應放大，另一個用於決定何時要相應縮小。 |
| rule | metricTrigger | 定義規則的計量條件。 |
| metricTrigger | metricName | 計量的名稱。 |
| metricTrigger |  metricResourceUri | 發出計量之資源的資源識別碼。 在大多數情況下，會與要調整規模的資源相同。 在某些情況下，則可能不同，例如，您可以根據儲存體佇列中的訊息數目來調整虛擬機器擴展集的規模。 |
| metricTrigger | timeGrain | 計量取樣持續時間。 例如 TimeGrain = “PT1M” 表示應該使用 “statistic” 中所指定的彙總方法，每分鐘彙總一次計量。 |
| metricTrigger | statistic | timeGrain 期間內的彙總方法。 例如 statistic = “Average” 和 timeGrain = “PT1M” 表示應該每分鐘計算一次計量平均值來彙總計量。 此屬性會指定進行計量取樣的方式。 |
| metricTrigger | timeWindow | 回顧計量的時間長度。 例如 timeWindow = “PT10M” 表示每次自動調整規模執行時，都會查詢過去 10 分鐘的計量。 時間範圍可讓您的計量正規化，而避免對暫時性尖峰做出反應。 |
| metricTrigger | timeAggregation | 用來彙總所取樣計量的彙總方法。 例如 TimeAggregation = “Average” 應該會透過計算平均值來彙總所取樣的計量。 在上述案例中會採用 10 個 1 分鐘樣本，然後計算其平均值。 |
| rule | scaleAction | 觸發 metricTrigger 時要採取的動作。 |
| scaleAction | direction | 設定為 "Increase" 時可相應放大，設定為 "Decrease" 可相應縮小|
| scaleAction | value | 要增加或減少多少資源容量 |
| scaleAction | cooldown | 在進行調整作業之後、再次調整之前，所要等待的時間長度。 例如，如果 cooldown = “PT10M”，則在進行調整作業之後，自動調整規模在接下來 10 分鐘內將不會再次嘗試進行調整。 cooldown 是用來在新增或移除執行個體之後，讓計量穩定。 |

## <a name="autoscale-profiles"></a>自動調整規模設定檔

自動調整規模設定檔有三種類型：

1. **一般設定檔：**最常見的設定檔。 如果您不需要根據星期幾或特定日子以不同方式調整資源規模，則您只需在自動調整規模設定中設定一般設定檔。 接著，可以為此設定檔設定計量規則，以指定何時要相應放大及何時要相應縮小。 您應該只定義一個一般設定檔。

    本文中稍早使用的範例設定檔是一般設定檔範例。 請注意，您也可以將設定檔設定成調整至資源的靜態執行個體計數。

2. **固定日期設定檔：**在已定義一般設定檔的情況下，假設您有一個即將在 2017 年 12 月 26 日 (PST) 來臨的重要事件，而您希望資源的最小/最大容量在該日有所不同，但仍依據相同的計量進行調整。 在此情況下，您應該在設定的設定檔清單中新增一個固定日期設定檔。 此設定檔會設定為只在事件當天執行。 在任何其他日子，則執行一般設定檔。

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
3. **週期設定檔：**此類型的設定檔可讓您確保在特定的星期幾一律使用此設定檔。 週期設定檔只有開始時間，因此它們會一直執行，直到所設定的下一個週期設定檔或固定日期設定檔開始為止。 自動調整規模設定如果只有一個週期設定檔，則即使在相同設定中已定義一般設定檔，也會執行該週期設定檔。 以下兩個範例將說明此設定檔的使用方式：

    **範例 1 - 工作日與週末之比較** 假設您希望在週末最大容量為 4，但在工作日因為預期會有較多負載，因此希望最大容量為 10。 在此情況下，您的設定會包含兩個週期設定檔，一個用來在週末執行，另一個在工作日執行。
    此設定看起來會像這樣：

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    在查看上述設定之後，您會發現每個週期設定檔都有排程，此排程會決定設定檔何時開始執行。 此設定檔會在該執行另一個設定檔時停止執行。

    例如，在上述設定中，“weekdayProfile” 是設定為在星期一上午 12 點開始，這表示此設定檔會在星期一上午 12 點開始執行。 它會持續執行到星球六上午 12 點為止，亦即已排定的 “weekendProfile” 開始執行時間。

    **範例 2 - 上班時間** 另舉一例來說，也許您希望將上班時間期間上午 9 點到下午 5 點的計量閾值設定為 ‘x’， 然後將下午 5 點 到隔天上午 9 點 的計量閾值設定為 ‘y’。
    此設定看起來會像這樣：
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    在查看上述設定之後，您會發現 “businessHoursProfile” 會在星期一上午 9 點開始執行， 並且會繼續執行到下午 5 點為止， 因為那是 “nonBusinessHoursProfile” 的開始執行時間。 “nonBusinessHoursProfile” 會執行到星期二上午 9 點為止， 然後 “businessHoursProfile” 又接著執行。 這會重複直到星期五下午 5 點為止，屆時 “nonBusinessHoursProfile” 會一直執行到星期一上午 9 點為止， 因為 “businessHoursProfile” 會直到星期一上午 9 點才開始執行。
    
> [!Note]
> Azure 入口網站中的自動調整規模 UX 會為週期設定檔強制執行結束時間，然後在週期設定檔之間開始執行自動調整規模設定的預設設定檔。
    
## <a name="autoscale-evaluation"></a>自動調整規模評估
由於自動調整規模設定可以有多個自動調整規模設定檔，且每個設定檔可以有多個計量規則，因此了解如何評估自動調整規模設定相當重要。 每次自動調整規模作業執行時，它都會從選擇適用的設定檔開始，選擇設定檔之後，自動調整規模就會評估設定檔中的最小值、最大值及任何計量值，然後判斷是否需要進行規模調整動作。

### <a name="which-profile-will-autoscale-pick"></a>自動調整規模會挑選哪個設定檔？
- 自動調整規模會先尋找任何設定為在現在執行的固定日期設定檔，如果有，自動調整規模就會執行它。 如果有多個應該執行的固定日期設定檔，自動調整規模將會選取第一個。
- 如果沒有任何固定日期設定檔，自動調整規模就會尋找週期設定檔，如果找到，就會執行它。
- 如果沒有任何固定或週期設定檔，則自動調整規模會執行一般設定檔。

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>自動調整規模如何評估多個規則？

在自動調整規模判斷應該執行哪個設定檔之後，它會從評估該設定檔中的所有相應放大規則 (direction = “Increase” 的規則) 開始。
- 如果觸發的相應放大規則有一或多個，自動調整規模就會計算出由這每一個規則的 scaleAction 所決定的新容量。 接著，它會相應放大至這些容量的最大值，以確保服務可用性。
- 例如：如果有一個目前容量為 10 的虛擬機器擴展集，而且有兩個相應放大規則；一個以 10% 為單位來增加容量，另一個以 3 為單位來增加容量。 第一個規則會產生新容量 11，而第二個規則則會產生容量 13。 為了確保服務可用性，自動調整規模會選擇產生最大容量的動作，因此會選擇第二個規則。

如果未觸發任何相應放大規則，自動調整規模就會評估所有相應縮小規則 (direction = “Decrease” 的規則)。 如果觸發所有相應縮小規則，則自動調整規模將只會執行相應縮小動作。
- 自動調整規模會計算出由這每一個規則的 scaleAction 所決定的新容量。 接著，它會選擇產生這些容量最大值的規模調整動作，以確保服務可用性。
- 例如：如果有一個目前容量為 10 的虛擬機器擴展集，而且有兩個相應縮小規則；一個以 50% 為單位來減少容量，另一個以 3 為單位來減少容量。 第一個規則會產生新容量 5，而第二個規則則會產生容量 7。 為了確保服務可用性，自動調整規模會選擇產生最大容量的動作，因此會選擇第二個規則。

## <a name="next-steps"></a>後續步驟
若要深入了解自動調整規模，請參考下列資源︰

* [自動調整規模的概觀](monitoring-overview-autoscale.md)
* [Azure 監視器自動調整規模的常用計量](insights-autoscale-common-metrics.md)
* [Azure 監視器自動調整規模的最佳作法](insights-autoscale-best-practices.md)
* [使用自動調整規模動作傳送電子郵件和 Webhook 警示通知](insights-autoscale-to-webhook-email.md)
* [自動調整規模 REST API](https://msdn.microsoft.com/library/dn931953.aspx)
