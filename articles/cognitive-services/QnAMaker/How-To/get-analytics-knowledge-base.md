---
title: 知識庫的分析
titleSuffix: Azure Cognitive Services
description: 如果您在 QnA Maker 服務建立期間啟用 App Insights，QnA Maker 會儲存所有交談記錄和其他遙測。 執行範例查詢，從 App Insights 取得您的交談記錄。
services: cognitive-services
author: tulasim88
manager: cgronlun
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim88
ms.openlocfilehash: f3ca99a92d12acbf880a3a95098a90569e2a0f84
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309380"
---
# <a name="get-analytics-on-your-knowledge-base"></a>取得知識庫的分析

如果您在 [QnA Maker 服務建立](./set-up-qnamaker-service-azure.md)期間啟用 App Insights，QnA Maker 會儲存所有交談記錄和其他遙測。 執行範例查詢，從 App Insights 取得您的交談記錄。

1. 移至您的 App Insights 資源。

    ![選取您的 Application Insights 資源](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. 選取 [分析]。 您可以查詢 QnA Maker 遙測的新視窗隨即開啟。

    ![選取 [分析]](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. 貼上並執行下列查詢。

    ```query
        requests
        | where url endswith "generateAnswer"
        | project timestamp, id, name, resultCode, duration
        | parse name with *"/knowledgebases/"KbId"/generateAnswer"
        | join kind= inner (
        traces | extend id = operation_ParentId
        ) on id
        | extend question = tostring(customDimensions['Question'])
        | extend answer = tostring(customDimensions['Answer'])
        | project KbId, timestamp, resultCode, duration, question, answer
    ```

    選取 [執行] 執行查詢。

    ![執行查詢](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>對於 QnA Maker 知識庫執行其他分析的查詢

### <a name="total-90-day-traffic"></a>90 天總流量

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>指定期間內的問題流量總計

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>使用者流量

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse name with *"/knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>延遲的問題散發

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [管理金鑰](./key-management.md)
