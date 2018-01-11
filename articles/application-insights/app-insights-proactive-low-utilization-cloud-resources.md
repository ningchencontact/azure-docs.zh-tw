---
title: "智慧偵測-Azure Application Insights 所偵測到的雲端資源的低使用率 |Microsoft 文件"
description: "監視 Azure Application Insights 的雲端資源的低使用率的應用程式。"
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
ms.openlocfilehash: 4c852d07d771a1eef0e6c2e4ef27cd36f31d8ddd
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="low-utilization-of-cloud-resources-preview"></a>低使用率的雲端資源 （預覽）

Application Insights 自動分析應用程式中的每個角色執行個體的 CPU 消耗，並偵測到低 CPU 使用率的執行個體。 這項偵測可讓您減少您的 Azure 資源，並降低成本，藉由降低會利用每個角色，角色執行個體數目，或藉由降低的角色數目。

這項功能需要任何特殊的安裝程式，除了[設定效能計數器](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters)應用程式。 當您的應用程式產生的 CPU 效能計數器遙測資料不足 （處理器時間百分比），所以使用。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>何時得到這種類型的智慧型偵測通知？
許多 Web/背景工作角色執行個體呈現低 CPU 使用率時發生一般通知。

## <a name="does-my-app-definitely-consume-too-many-resources"></a>我的應用程式明確地耗用太多的資源嗎？
否，通知並不表示您的應用程式一定會耗用太多資源。 雖然這類模式的低 CPU 使用率通常表示資源耗用量可能會降低，這種行為可能是一般到特定的角色，或可能會有自然業務理由，而且可以忽略。 例如，可能是多個執行個體所需的其他資源，例如記憶體或網路，並不 CPU。

## <a name="how-do-i-fix-it"></a>如何修正問題？
通知包括要在診斷程序中支援的診斷資訊：
1. **分級。** 通知會顯示角色中展現低 CPU 使用率的應用程式。 這可協助您將優先順序指派給此問題。
2. **範圍。** 多少角色展現低 CPU 使用率，並在每個角色執行個體數目利用低 CPU？ 可以從通知取得這項資訊。
3. **診斷。** 偵測包含 CPU 的使用率，顯示經過一段時間的每個執行個體的 CPU 使用率百分比。 您也可以使用的相關項目和連結到支援資訊，例如 CPU 使用率的百分位數的報表可協助您進一步診斷問題。
