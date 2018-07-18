---
title: Azure 媒體服務事件的 Azure 事件格線結構描述
description: 描述利用 Azure 事件格線提供給媒體服務事件的屬性
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: c443ea755c9e7e88b4685682d5e43f675d42efa4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782657"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Azure 媒體服務事件的 Azure 事件格線結構描述

本文提供媒體服務事件的結構描述和屬性。

## <a name="media-services-job-state-change-event"></a>媒體服務工作狀態變更事件

本節提供媒體服務工作狀態變更事件的屬性和結構描述。  

### <a name="available-event-types"></a>可用的事件類型

媒體服務**工作**會發出下列事件類型：

| 事件類型 | 說明 |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| 當工作的狀態變更時引發。 |

### <a name="example-event"></a>事件範例

以下範例說明已完成工作事件的結構描述： 

```json
[
  {
    "topic": "/subscriptions/{subscription id}/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
    "subject": "transforms/VideoAnalyzerTransform/jobs/{job id}",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "<id>",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

### <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| 主題 | 字串 | Event Grid 提供此值。 |
| 主旨 | 字串 | 媒體服務帳戶資源的資源路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 例如，"Microsoft.Media.JobStateChange"。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 | 媒體服務事件資料。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| previousState | 字串 | 工作在該事件之前的狀態。 |
| state | 字串 | 在此事件中，被通知的工作新狀態。 例如，「 已排入佇列： 工作正在等候資源」或「已排程： 工作是已準備好開始」。|

工作狀態可以是以下其中一個值：*已排入佇列*、*已排程*、*處理中*、*已完成*、*錯誤*、*已取消*、*取消中*

## <a name="next-steps"></a>後續步驟

[登記工作狀態變更事件](job-state-events-cli-how-to.md)
