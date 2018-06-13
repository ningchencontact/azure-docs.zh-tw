---
title: 針對 Azure 串流分析中格式不正確的輸入事件進行疑難排解 | Microsoft Docs
description: 如何知道我輸入資料中的哪個事件造成串流分析作業中的問題
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/19/2018
ms.author: sngun
ms.openlocfilehash: 2b4f82bae20c3cb398848a89715bfdc1316e1ba1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2018
ms.locfileid: "29766377"
---
# <a name="troubleshoot-for-malformed-input-events"></a>針對格式不正確的輸入事件進行疑難排解

當串流分析作業的輸入資料流包含格式不正確的訊息時，就會導致序列化問題。 格式不正確的訊息會包含不正確的序列化，例如遺漏 JSON 物件中的括號，或不正確的時間戳記格式。 當串流分析作業收到格式不正確的訊息時，它會捨棄訊息，並以警告通知使用者。 您串流分析作業的 [輸入] 圖格上會顯示警告符號：

![[輸入] 圖格](media/stream-analytics-malformed-events/inputs_tile.png)

## <a name="troubleshooting-steps"></a>疑難排解步驟

1. 瀏覽至 [輸入] 圖格，然後按一下以檢視警告。
2. 輸入詳細資料圖格會顯示一組警告，以及問題的相關詳細資料。 以下是警告訊息範例，警告訊息會顯示發生 JSON 資料格式不正確的磁碟分割、位移及序號。 

   ![位移的警告訊息](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. 若要取得具有不正確格式的 JSON 資料，請執行下列程式碼片段。 這個程式碼區塊會讀取磁碟分割識別碼、位移並列印資料。 您可以從 [GitHub 範例存放庫](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)取得完整的範例。 一旦您讀取資料後，可以分析並修正序列化格式。

```csharp
static void PrintMessages(string partitionId, long offset, int numberOfEvents)
        {
            EventHubReceiver receiver;
            
            try
            {
                foreach (var e in receiver.Receive(numberOfEvents, TimeSpan.FromMinutes(1)))
                {
                    Console.WriteLine(Encoding.UTF8.GetString(e.GetBytes()));
                    Console.WriteLine("----");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.ToString());
                return;
            }

            receiver.Close();
        }
```
## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
