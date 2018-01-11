---
title: "智慧偵測-Azure Application Insights 所偵測到潛在的記憶體流失 |Microsoft 文件"
description: "監視應用程式使用 Azure Application Insights 潛在的記憶體流失之處。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 452d0a9d0231e54df2a7f1df76c3c2c0fcd94d87
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="memory-leak-detection-preview"></a>記憶體遺漏偵測 （預覽）

Application Insights 自動分析應用程式中的每個處理序的記憶體耗用量，並警告您有關潛在的記憶體遺漏或增加的記憶體耗用量。

這項功能需要任何特殊的安裝程式，除了[設定效能計數器](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters)應用程式。 當您的應用程式產生記憶體效能計數器遙測資料不足 （例如，私用位元組），所以使用。


## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>何時得到這種類型的智慧型偵測通知？
典型的通知將會遵循一致地增加記憶體耗用量很長一段時間 （幾個小時），在一或多個處理程序及/或一個或多個機器，您的應用程式的一部分。
機器學習演算法，可用來偵測符合指定之模式的記憶體流失，相較於增加的記憶體耗用量，因為自然增加應用程式使用情形的增加的記憶體耗用量。

## <a name="does-my-app-definitely-have-a-problem"></a>我的應用程式絕對有問題嗎？
否，通知並不表示您的應用程式一定有問題。 雖然記憶體流失模式通常表示應用程式問題，這些模式可能是一般至特定的程序，或可能會有自然業務理由，而且可以忽略。

## <a name="how-do-i-fix-it"></a>如何修正問題？
通知包括要在診斷分析程序中支援的診斷資訊：
1. **分級。** 通知會顯示您的記憶體數量增加 （以 gb 為單位），以及增加記憶體中的時間範圍。 這可協助您將優先順序指派給此問題。
2. **範圍。** 多少個機器展現記憶體流失模式？ 例外狀況數量觸發期間可能發生記憶體流失問題？ 可以從通知取得這項資訊。
3. **診斷。** 偵測包含記憶體流失模式中，顯示程序的一段時間的記憶體耗用量。 您也可以使用相關的項目和報表連結到支援資訊，可協助您進一步診斷問題。
