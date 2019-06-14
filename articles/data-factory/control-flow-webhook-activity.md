---
title: Azure Data Factory 中的 Webhook 活動 |Microsoft Docs
description: Webhook 的活動不會繼續執行管線，直到它會驗證連接的資料集，與特定使用者指定的準則。
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
ms.openlocfilehash: 6ec43b06ce266b9ceaddb5dd21cbf52f509d6596
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764300"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure Data Factory 中的 Webhook 活動
若要透過自訂程式碼控制的管線執行，您可以使用 web 勾點活動。 使用 webhook 的活動，客戶可呼叫端點，並傳遞回呼 URL。 管線執行等候後再繼續下一個活動會叫用的回呼。

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
name | Web 攔截程序活動的名稱。 | 字串 | 是 |
type | 必須設定為**WebHook**。 | 字串 | 是 |
method | 目標端點的 Rest API 方法。 | 字串。 支援的類型：' POST' | 是 |
url | 目標端點和路徑 | 字串 (或含有字串之 resultType 的運算式)。 | 是 |
headers | 傳送至要求的標頭。 例如，若要在要求上設定的語言和類型: [標頭]: {「 接受語言 」:"en-我們"、"Content-type":"application/json"}。 | 字串 (或含有字串之 resultType 的運算式) | 是，Content-type 標頭是必要的。 "headers":{ "Content-Type":"application/json"} |
body | 代表傳送至端點的承載。 | 主體傳回給呼叫回 URI 應為有效的 JSON。 請在[要求乘載結構描述](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0)一節中查看要求乘載的結構描述。 | 是 |
驗證 | 呼叫端點所使用的驗證方法。 支援的類型為 「 基本 」 或 「 ClientCertificate。 」 如需詳細資訊，請參閱[驗證](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0)一節。 如果不需要驗證，請排除這個屬性。 | 字串 (或含有字串之 resultType 的運算式) | 否 |
timeout | 活動會等待多久&#39;callBackUri&#39;會叫用。 活動會等待多久 'callBackUri' 會叫用。 預設值是 10mins ("00: 10:00")。 格式是 Timespan 也就是 d.hh:mm:ss | 字串 | 否 |

## <a name="additional-notes"></a>其他注意事項

Azure Data Factory 將會傳入額外的屬性"callBackUri 」 主體 url 端點，並會預期要在指定的逾時值之前叫用此 uri。 如果 uri 不會叫用，活動將會失敗 '逾時' 的狀態。

Web 勾點活動本身失敗只當自訂端點的呼叫失敗。 可以新增至回呼的主體和後續的活動中所使用的任何錯誤訊息。

## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動：

- [If Condition 活動](control-flow-if-condition-activity.md)
- [執行管道活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
