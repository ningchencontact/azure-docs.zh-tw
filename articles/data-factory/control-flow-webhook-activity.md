---
title: Azure Data Factory 中的 Webhook 活動 |Microsoft Docs
description: Webhook 活動不會繼續執行管線, 直到它以使用者指定的特定準則驗證附加的資料集為止。
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
ms.openlocfilehash: c11fb800dba06ab5566647489f020f727860a7ff
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142411"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure Data Factory 中的 Webhook 活動
您可以使用「攔截活動」, 透過您的自訂程式碼來控制管線的執行。 客戶可以使用 webhook 活動來呼叫端點, 並傳遞回呼 URL。 管線執行會等候回呼被叫用, 然後再繼續進行下一個活動。

## <a name="syntax"></a>語法

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>類型屬性



屬性 | 描述 | 允許的值 | 必要項
-------- | ----------- | -------------- | --------
name | Web 勾點活動的名稱 | String | 是 |
Type | 必須設定為**WebHook**。 | String | 是 |
方法 | 目標端點的 Rest API 方法。 | 字串。 支援的類型：POST | 是 |
url | 目標端點和路徑 | 字串 (或含有字串之 resultType 的運算式)。 | 是 |
headers | 傳送至要求的標頭。 例如, 若要設定要求的語言和類型: "標頭": {"Accept-Language": "en-us", "Content-type": "application/json"}。 | 字串 (或含有字串之 resultType 的運算式) | 是，Content-type 標頭是必要的。 "標頭": {"Content-type": "application/json"} |
內容 | 代表傳送至端點的承載。 | 傳回回呼 URI 的主體應該是有效的 JSON。 請在[要求乘載結構描述](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0)一節中查看要求乘載的結構描述。 | 是 |
驗證 | 呼叫端點所使用的驗證方法。 支援的類型為「基本」或「ClientCertificate」。 如需詳細資訊，請參閱[驗證](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0)一節。 如果不需要驗證，請排除這個屬性。 | 字串 (或含有字串之 resultType 的運算式) | 否 |
逾時 | 活動會等待&#39;callBackUri&#39;叫用的時間長度。 活動將等候 ' callBackUri ' 被叫用的時間長度。 預設值為 10mins ("00:10:00")。 格式為 Timespan, 例如 d. hh: mm: ss | String | 否 |

## <a name="additional-notes"></a>其他備註

Azure Data Factory 會將本文中的其他屬性 "callBackUri" 傳遞至 url 端點, 而且會預期在指定的超時值之前叫用此 uri。 如果未叫用 uri, 活動將會失敗, 並出現狀態 ' TimedOut '。

只有在自訂端點的呼叫失敗時, web 攔截活動本身才會失敗。 任何錯誤訊息都可以加入至回呼的主體, 並用於後續的活動中。

## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動：

- [If Condition 活動](control-flow-if-condition-activity.md)
- [執行管道活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
