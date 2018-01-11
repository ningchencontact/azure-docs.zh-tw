---
title: "智慧偵測-例外狀況的磁碟區，在 Azure Application Insights 中的異常升高 |Microsoft 文件"
description: "監視與 Azure Application Insights 例外狀況的磁碟區中的異常模式的應用程式例外狀況。"
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
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: 8030f3331a03170bb265c417a57725544bdc7d3f
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2017
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>例外狀況的磁碟區 （預覽） 的異常升高

Application Insights 自動分析應用程式中擲回的例外狀況，並警告您有關例外狀況遙測中的異常模式。

這項功能需要任何特殊的安裝程式，除了[設定例外狀況報告](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting)應用程式。 當您的應用程式會產生例外狀況遙測資料不足，所以使用。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>何時得到這種類型的智慧型偵測通知？
如果您的應用程式發生的特定類型的例外狀況數目異常地上升一天，相較於過去七天內計算基準，您可能會發生這種類型的通知。
機器學習演算法用於偵測例外狀況計數升高時在您的應用程式使用情形的自然成長納入考量。

## <a name="does-my-app-definitely-have-a-problem"></a>我的應用程式絕對有問題嗎？
否，通知並不表示您的應用程式一定有問題。 雖然過多的例外狀況通常表示應用程式問題，這些例外狀況可能會良性且正確地透過您的應用程式來處理。

## <a name="how-do-i-fix-it"></a>如何修正問題？
通知包括要在診斷程序中支援的診斷資訊：
1. **分級。** 通知會顯示您的使用者人數，或多少要求會受到影響。 這可協助您將優先順序指派給此問題。
2. **範圍。** 問題會影響所有流量或一些作業嗎？ 可以從通知取得這項資訊。
3. **診斷。** 偵測包含從中擲回例外狀況，以及例外狀況型別之方法的相關資訊。 您也可以使用相關的項目和報表連結到支援資訊，可協助您進一步診斷問題。