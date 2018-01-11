---
title: "智慧偵測-降低追蹤嚴重性比例，請在 Azure Application Insights |Microsoft 文件"
description: "監視應用程式追蹤，確定使用 Azure Application Insights 追蹤遙測中的異常模式。"
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
ms.date: 11/27/2017
ms.author: mbullwin
ms.openlocfilehash: 29ed195dadb7230df425d6d981a0a482e09ee79f
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2017
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>降低追蹤嚴重性比例 （預覽）

追蹤中很廣泛的應用程式，因為它們可以協助讓您在幕後發生什麼事。 當發生錯誤時，追蹤會提供導致不良的狀態事件的順序很重要的可視性。 雖然追蹤通常非結構化，沒有具體從它們 – 其嚴重性層級學到的一件事。 在應用程式的穩定狀態，我們會預期 「 良好 」 追蹤之間的比率 (*資訊*和*Verbose*) 和 「 不良 」 追蹤 (*警告*，*錯誤*和*重大*) 保持穩定。 此處的假設是 「 不良 」 的追蹤可能會發生在某個程度，因為許多原因而定期 （暫時性網路問題執行個體）。 但是，當實際的問題開始成長時，它通常都會顯示追蹤 「 不良 」 與 「 良好 」 追蹤的相對比例增加。 自動 Insights 智慧偵測應用程式會分析您的應用程式所記錄的追蹤，並警告您有關追蹤遙測的嚴重性的異常模式。

這項功能需要任何特殊的安裝程式，設定您的應用程式的追蹤記錄以外 (請參閱 < 如何設定追蹤記錄檔接聽程式[.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs)或[Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs))。 當您的應用程式會產生例外狀況遙測資料不足，所以使用。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>何時得到這種類型的智慧型偵測通知？
如果，可能會發生這種類型的通知 「 良好 」 追蹤之間的比率 (追蹤記錄的層級*資訊*或*Verbose*) 和 「 不良 」 追蹤 (追蹤記錄的層級*警告*，* 錯誤，或*嚴重錯誤*) 特定的日子，相較於過去七天內計算基準中導致效能變差。

## <a name="does-my-app-definitely-have-a-problem"></a>我的應用程式絕對有問題嗎？
否，通知並不表示您的應用程式一定有問題。 雖然 「 良好 」 與 「 不良 」 追蹤之間的比率降低可能表示應用程式問題，這項變更比例可能良性。 例如，增加可能是因為應用程式產生多個 「 不良 」 追蹤，比現有流程中新的流程）。

## <a name="how-do-i-fix-it"></a>如何修正問題？
通知包括要在診斷程序中支援的診斷資訊：
1. **分級。** 通知會顯示多少作業會受到影響。 這可協助您將優先順序指派給此問題。
2. **範圍。** 問題會影響所有流量或一些作業嗎？ 可以從通知取得這項資訊。
3. **診斷。** 您可以使用相關的項目和報表連結到支援資訊，可協助您進一步診斷問題。


