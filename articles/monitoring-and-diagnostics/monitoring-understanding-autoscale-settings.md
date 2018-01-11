---
title: "了解自動調整規模設定 |Microsoft 文件"
description: "自動調整設定，以及如何運作的細目。"
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
ms.openlocfilehash: cff2be1818417a19f36da08d8c2eaa227bb945ec
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="understand-autoscale-settings"></a>了解自動調整規模設定
自動調整規模設定可讓您以確定您有適合的執行來處理變動的負載，應用程式的資源容量。 您可以設定自動調整規模設定觸發根據度量，表示負載或效能，或觸發程序在排程的日期和時間。 這篇文章會詳細的探討自動調整規模設定的剖析。 發行項開始了解結構描述和屬性的設定，然後逐步解說的不同的設定檔類型，可以設定，而且最後將討論如何自動調整規模評估要在任何給定時間執行的設定檔。

## <a name="autoscale-setting-schema"></a>自動調整規模設定結構描述
為了說明自動調整規模設定結構描述，會使用下列的自動調整規模設定。 請務必注意，此自動調整設定具有：
- 一個設定檔 
- 這個設定檔; 中有兩個度量的規則其中一個向外延展，另一個小數位數中。
- 虛擬機器規模集的平均百分比 CPU 度量大於過去 10 分鐘的 85%時，就會觸發的向外延展規則。
- 標尺中就會觸發規則時虛擬機器規模集的平均值低於 60%的過去幾分鐘。

> [!NOTE]
> 設定可以有多個設定檔，請跳至[設定檔](#autoscale-profiles)區段，以進一步了解。
> 設定檔也可以有多個向外延展和小數位數的規則定義，請跳至[評估區段](#autoscale-evaluation)以查看評估的方式

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

| 區段 | 元素名稱 | 說明 |
| --- | --- | --- |
| 設定 | ID | 自動調整規模設定的資源識別碼。 自動調整規模設定是 Azure 資源管理員資源。 |
| 設定 | name | 自動調整規模設定名稱。 |
| 設定 | location | 自動調整規模設定的位置。 此位置可以是縮放資源的位置不同。 |
| properties | targetResourceUri | 正在調整資源的資源識別碼。 您只能有一個自動調整規模設定每個資源。 |
| properties | 設定檔 | 自動調整規模設定是由一或多個設定檔所組成。 每次執行時自動調整引擎，它會執行一個設定檔。 |
| 設定檔 | name | 設定檔的名稱，您可以選擇任何可協助您識別設定檔的名稱。 |
| 設定檔 | Capacity.maximum | 允許的最大容量。 它可確保該自動調整規模，當執行此設定檔時，不能擴充您的資源超過此數目。 |
| 設定檔 | Capacity.minimum | 允許的最小容量。 它可確保該自動調整規模，當執行此設定檔時，不能擴充您的資源低於此值。 |
| 設定檔 | Capacity.default | 如果沒有讀取 （在此情況下，「 vmss1"cpu） 的資源計量的問題，目前的容量低於預設的容量，然後以確保資源的可用性，自動調整標尺外為預設值。 如果目前的容量已大於預設容量，自動調整規模將會不小數位數單元。 |
| 設定檔 | 規則 | 使用規則的設定檔中的最大和最小容量之間自動縮放自動調整規模。 您可以設定檔中有多個規則。 基本的案例中是有兩個規則，一個判斷何時要向外延展，以判斷何時標尺中其他。 |
| 規則 | metricTrigger | 定義度量規則的條件。 |
| metricTrigger | metricName | 度量的名稱。 |
| metricTrigger |  metricResourceUri | 發出度量的資源的資源識別碼。 在大部分情況下，它是縮放資源相同。 在某些情況下，它可能會不同，比方說，您可以調整的儲存體佇列中的訊息數目為基礎的虛擬機器規模集。 |
| metricTrigger | timeGrain | 度量取樣持續時間。 例如，TimeGrain ="PT1M"應該是度量的方式使用彙總每隔 1 分鐘"統計資料。 」 中指定的彙總方法 |
| metricTrigger | statistic | TimeGrain 期間內的彙總方法。 例如，統計資料 = 「 平均數 」 和 timeGrain ="PT1M"，表示應該是度量所花費的平均彙總每隔 1 分鐘。 這個屬性會指定度量取樣的方式。 |
| metricTrigger | timeWindow | 回頭度量的時間量。 例如，timeWindow ="PT10M"表示，每次執行時自動調整規模，它會查詢度量過去 10 分鐘。 時間視窗可讓您的指標正規化，並避免對回應暫時性的高峰。 |
| metricTrigger | timeAggregation | 用來彙總的取樣的度量彙總方法。 例如，TimeAggregation = 「 平均數 」 應該彙總的取樣的計量方式是求得平均值。 在上述的情況下，採用的十個 1 分鐘樣本，和平均它們。 |
| 規則 | scaleAction | 要在觸發規則的 metricTrigger 時所採取的動作。 |
| scaleAction | direction | 「 增加 」 來向外延展，「 降低 」 到標尺中|
| scaleAction | value | 若要增加或減少資源的容量多少 |
| scaleAction | cooldown | 調整規模作業一次調整之前，先等待的時間量。 例如，如果 cooldown ="PT10M"，則調整規模作業發生之後，自動調整不會嘗試一次的擴充另一個 10 分鐘。 Cooldown 是允許穩定後之後新增或移除執行個體的, 度量。 |

## <a name="autoscale-profiles"></a>自動調整規模設定檔

有三種類型的自動調整規模設定檔：

1. **規則的設定檔：**最常見的設定檔。 如果您不需要調整您以不同的方式根據一週的星期幾，或在特定日期的資源，然後您只需要設定自動調整規模設定中的一般設定檔。 此設定檔然後設有聽寫至向外延展和時機小數位數中的計量規則。 您應該只有一個規則的設定檔定義。

    使用本文稍早的範例設定檔是一般的設定檔的範例。 執行不也很可能要調整的設定檔設為您的資源的靜態執行個體計數。

2. **固定日期設定檔：**規則的設定檔定義時，讓我們假設您有一個重要事件，隨後在 2017 年 12 月 26，」 (PST)，而且您想要能在那一天，但仍上的相同度量資訊縮放您資源的最小/最大容量. 在此情況下，您應該將固定的日期設定檔加入您的設定設定檔清單。 設定檔設定為只會在事件的一天執行。 任何其他一天，會執行規則的設定檔。

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
    
3. **循環設定檔：**這種類型的設定檔可讓您確保在特定日期的星期幾，一律使用此設定檔。 循環設定檔只能有一個開始時間，因此它們會執行直到下一個循環設定檔或固定的日期設定檔設定為啟動。 自動調整規模設定只能有一個循環設定檔，執行該設定檔，即使有未定義在相同的設定中的一般設定檔。 下列兩個範例說明此設定檔的使用方式：

    **範例 1-週間日 vs。週末**假設您想在週末您最大的容量是 4，但是工作日，預期更多的負載，因為您想要您的最大容量為 10。 在此情況下，您的設定會包含兩個循環設定檔，其中週末以及其他工作日上執行。
    此設定會看起來像這樣：

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

    藉由查看先前的設定，您會發現，每個循環設定檔有一個排程，此排程決定啟動執行設定檔。 設定檔就會停止執行時的時間來執行其他設定檔。

    比方說，在上述的設定中，「 weekdayProfile"設定為在星期一上午 12，這表示此設定檔可讓您開始在星期一執行在 12.am 開始。 它會繼續執行直到星期六 12a.m。 當"weekendProfile 「 排程開始執行時。

    **範例 2-上班時間**讓我們來看另一個範例中，或許您想要度量的閾值 = 'x' 在營業時間內，一直到上午 9 至下午 5 點，然後從下午 5 點 變成上午 9。 隔天，您會想要 'y' 度量的閾值。
    此設定會看起來像這樣：
    
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
    
    藉由查看先前的設定，「 businessHoursProfile 」 開始執行時在星期一上午 9 並保留直到下午 5 點執行 因為這是當 「 nonBusinessHoursProfile 」 開始執行。 「 NonBusinessHoursProfile 」 會執行到上午 9 星期二，然後"businessHoursProfile"接管。 這會重複直到星期五下午 5 點，此時"nonBusinessHoursProfile 」 會執行到星期一的上午 9 因為 「 businessHoursProfile 」 不會啟動執行到星期一的上午 9
    
> [!Note]
> 在 Azure 入口網站的自動調整規模 UX 會強制執行循環設定檔的結束時間，並開始執行的自動調整設定的預設設定檔之間循環設定檔。
    
## <a name="autoscale-evaluation"></a>評估自動調整規模
指定自動調整設定可以有多個自動調整規模設定檔，而且每個設定檔可以有多個度量的規則，請務必了解如何評估自動調整規模設定。 每次選擇設定檔選擇的設定檔的自動調整之後，適用，就會開始自動調整規模工作執行評估的最小值、 最大值和設定檔中的任何度量規則，並決定是否需要調整規模動作。

### <a name="which-profile-will-autoscale-pick"></a>自動調整規模將會挑選哪些設定檔？
- 自動調整規模會先尋找任何固定日期設定檔，如果沒有設定為現在執行時，自動調整規模加以執行。 如果應該執行的多個固定的日期設定檔，自動調整規模將會選取第一個。
- 如果沒有固定的日期設定檔，自動調整規模查看循環設定檔，如果找不到，然後就會執行它。
- 如果有固定或循環設定檔，然後自動調整執行規則的設定檔。

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>自動調整規模如何評估多個規則？

一旦自動調整規模將決定應該執行哪一個設定檔，它就會啟動藉由評估設定檔中的向外延展規則 (規則方向 = 「 增加 」)。
- 如果一或多個向外延展規則會觸發，自動調整規模會計算新的容量取決於每個這些規則的 scaleAction。 然後它標尺外的容量，以確保服務可用性的最大值。
- 例如： 如果沒有目前容量的 10 虛擬機器規模集，而且有兩個向外延展的規則。容量增加 10%的其中一個，另一個則容量增加 3。 第一項規則會導致第 11 的新容量和第二項規則會產生 13 的容量。 若要確保服務可用性自動調整規模選擇選擇結果中的最大容量，所以第二項規則的動作。

如果任何向外延展 」 規則會不觸發，自動調整規模評估所有的小數位數的規則 (規則方向 ="減少")。 如果所有的小數位數的規則觸發，自動調整規模才會在標尺的動作。
- 自動調整規模計算新的容量取決於每個這些規則的 scaleAction。 然後它會選擇導致這些容量，以確保服務可用性的最大調整規模動作。
- 例如： 如果沒有目前容量的 10 虛擬機器規模集，而且有兩個小數位數的規則。一個容量減少 50%，容量減少 3 的其中一個。 第一項規則會產生新的容量為 5，和第二項規則會產生 7 的容量。 若要確保服務可用性自動調整規模選擇選擇結果中的最大容量，所以第二項規則的動作。

## <a name="next-steps"></a>後續步驟
若要了解有關自動調整規模的詳細資訊，請參閱下列資源：

* [自動調整規模的概觀](monitoring-overview-autoscale.md)
* [Azure 監視器自動調整的常用度量](insights-autoscale-common-metrics.md)
* [Azure 監視器自動調整的最佳作法](insights-autoscale-best-practices.md)
* [使用自動調整動作傳送電子郵件和 Webhook 警示通知](insights-autoscale-to-webhook-email.md)
* [自動調整 REST API](https://msdn.microsoft.com/library/dn931953.aspx)
