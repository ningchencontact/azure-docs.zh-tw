---
title: 在 Azure 時間序列深入解析預覽中選擇時間序列識別碼的最佳做法 | Microsoft Docs
description: 瞭解在 Azure 時間序列深入解析 (預覽) 中選擇時間序列識別碼的最佳做法。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 44c6c8a55bbcbf76cf48fd17a6e52d188a5e99fc
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556606"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>選擇時間序列識別碼的最佳做法

本文容涵蓋 Azure 時間序列深入解析 (預覽) 分割區索引鍵、時間序列識別碼，以及選擇識別碼的最佳做法。

## <a name="choose-a-time-series-id"></a>選擇時間序列識別碼

選擇時間序列識別碼就像選擇資料庫的分割區索引鍵一樣。 這是設計階段必須進行的一項重要決定。 您無法將現有的時間序列深入解析 (預覽) 環境更新為使用不同的時間序列識別碼。 也就是說，使用時間序列識別碼建立環境之後，原則就會變成無法變更的不可變屬性。

> [!IMPORTANT]
> 時間序列識別碼區分大小寫且不可變 (設定之後無法變更)。

因此請記住，選取適當的時間序列識別碼非常重要。 選取時間序列識別碼時，請考慮以下最佳做法：
* 請選擇具有各種不同的值、且有平均存取模式的屬性名稱。 最佳做法是讓分割區索引鍵具有許多相異值 (例如數百個或數千個)。 對許多客戶來說，這會像是您 JSON 中的 DeviceID 或 SensorID 一樣。
* 在您[時間序列模型](./time-series-insights-update-tsm.md).的分葉節點層級，時間序列識別碼應該是唯一的。
* 時間序列識別碼屬性名稱字元字串最多可以有 128 個字元，時間序列識別碼屬性值最多可以有 1024 個字元。
* 如果遺漏部分唯一時間序列識別碼屬性值，它們會被視為 Null 值，並將其納入唯一性限制式。

此外，您可以選取最多*三個*(3) 索引鍵屬性作為時間序列識別碼。

  > [!NOTE]
  > 這*三個*(3) 索引鍵屬性必須是字串。

下列案例描述選取超過一個索引鍵屬性作為時間序列識別碼：  

### <a name="scenario-1"></a>案例 1

* 您有舊的資產群，每項資產都具備一個唯一索引鍵。 
* 例如，某個資產群透過屬性 *deviceId* 識別，而另一個資產群的唯一屬性為 *objectId*。 兩個資產群都沒有包含對方的唯一屬性。 在此範例中，您會選取兩個索引鍵作為唯一索引鍵，分別是 deviceId 和 objectId。 
* 我們接受 null 值，而事件裝載中缺少屬性存在會計為 `null` 值。 這也是將資料傳送到兩個不同的事件來源時適當的處理方式，其中每個事件來源中的資料都會有唯一的時間序列識別碼。

### <a name="scenario-2"></a>案例 2

* 您需要讓同一資產群內的多個特性都必須是唯一的。 
* 例如，假設您是一位優秀的建築師，並且要在每個房間內部署感應器。 在每個房間內，*sensorId* 的值通常都是相同的，例如*sensor1*、*sensor2* 及 *sensor3*。
* 此外，您的建築物中所有位置都有屬性 *flrRm* 的重疊樓層與房間號碼，其值皆為像 *1a*、*2b*、*3a* 這樣的值，依此類推。
* 最後，您有一個屬性 *Location*，它包含像 *Redmond*、*Barcelona*, 及 *Tokyo* 這樣的值。 為了建立唯一性，您指定了下列三個屬性做為時間序列識別碼索引鍵：*sensorId*、*flrRm* 和 *location*。

## <a name="next-steps"></a>後續步驟

* 深入了解[資料模型建立](./time-series-insights-update-tsm.md)。
* 規劃您的 [Azure 時間序列深入解析 (預覽) 環境](./time-series-insights-update-plan.md)。
