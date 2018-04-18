---
title: Azure 排程器的計劃和計費
description: Azure 排程器的計劃和計費
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: b25e97b0f0d0b6f63134a774856eb7ec8f77b679
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
# <a name="plans-and-billing-in-azure-scheduler"></a>Azure 排程器的計劃和計費
## <a name="job-collection-plans"></a>工作集合計劃
工作集合是 Azure 排程器中可計費的實體。 作業集合包含許多作業，並分成三個計劃 (標準、P10 進階和 P20 進階)，如下所述。

| **工作集合計劃** | **每個工作集合的工作數上限** | **最大週期** | **每個訂用帳戶的工作集合數上限** | **限制** |
|:--- |:--- |:--- |:--- |:--- |
| **標準** |每個工作集合 50 個工作 |每分鐘一次。 執行工作不得超過一分鐘 |一個訂用帳戶允許最多 100 個標準工作集合 |存取排程器的完整功能集 |
| **P10 Premium** |每個工作集合 50 個工作 |每分鐘一次。 執行工作不得超過一分鐘 |一個訂用帳戶允許最多 10,000 個 P10 進階工作集合。 如需詳細資訊，請<a href="mailto:wapteams@microsoft.com">與我們連絡</a>。 |存取排程器的完整功能集 |
| **P20 Premium** |每個作業集合 1000 個工作 |每分鐘一次。 執行工作不得超過一分鐘 |一個訂用帳戶允許最多 10,000 個 P20 進階工作集合。 如需詳細資訊，請<a href="mailto:wapteams@microsoft.com">與我們連絡</a>。 |存取排程器的完整功能集 |

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>升級或降級工作集合計劃
您隨時可在標準、P10 進階和 P20 進階計劃之間升級或降級作業集合計劃。

## <a name="billing-and-azure-plans"></a>計費與 Azure 計劃
如果您有 100 個以上的標準工作集合 (10 個標準計費單位)，則具有高階計劃中的所有工作集合，這是更好的方式。

如果有一個標準作業集合和一個高階作業集合，則您會支付一個標準計費單位「和」一個高階計費單位。 排程器服務會根據設定為標準或高階的作用中工作集合數目來計費；下兩節將有進一步的說明。

## <a name="standard-billable-units"></a>標準可計費單位
標準可計費單位可以包含最多 10 個標準工作集合。 由於標準工作集合可以讓每個工作集合最多具有 50 個工作，因此一個標準計費單位可讓一個訂用帳戶最多具有 500 個工作 – 每個月幾乎高達 2 千 2 百萬個工作執行。

如果有 1 到 10 個標準工作集合，則您將支付 1 個標準計費單位。 如果有 11 到 20 個標準工作集合，則您將支付 2 個標準計費單位。 如果有 21 到 30 個標準工作集合，則您將支付 3 個標準計費單位。

## <a name="p10-premium-billable-units"></a>P10 進階可計費單位
P10 進階可計費單位可以包含最多 10,000 個 P10 進階工作集合。 由於 P10 進階工作集合可以讓每個工作集合最多具有 50 個工作，因此一個進階計費單位可讓一個訂用帳戶最多具有 500,000 個工作 – 每個月幾乎高達 220 億個工作執行。

如果有 1 到 10,000 個進階工作集合，則您將支付 1 個 P10 進階計費單位。 如果有 10,001 到 20,000 個進階工作集合，則您將支付 2 個 P10 進階計費單位，依此類推。

因此，P10 進階工作集合具有與標準工作集合相同的功能，但萬一您的應用程式需要大量工作集合時提供折價。

## <a name="p20-premium-billable-units"></a>P20 進階可計費單位
P20 進階可計費單位可以包含最多 5,000 個 P20 進階工作集合。 由於 P20 進階工作集合可以讓每個工作集合最多具有 1,000 個工作，因此一個進階計費單位可讓一個訂用帳戶最多具有 5,000,000 個工作 – 每個月幾乎高達 2200 億個工作執行。

P20 進階工作集合提供與 P10 進階工作集合相同的功能，但整體比起 P10 進階，每個工作集合支援更大量的工作數和更大量的工作總數，可讓您有更多的延展性。

## <a name="billing-and-active-status"></a>計費和作用中狀態
工作集合會永遠作用中，除非您整個訂用帳戶由於計費問題而進入部分暫時停用狀態。 確保作業集合不計費的唯一方法，就是將它設定成「免費」計劃或刪除作業集合。

雖然您可能在單一作業中停用作業集合內的所有作業，但是它不會變更作業集合的計費狀態 – 作業集合「仍」會計費。 同樣地，空白的工作集合會被視為作用中，並將計費。

## <a name="pricing"></a>價格
如需定價詳細資料，請參閱 [排程器定價](https://azure.microsoft.com/pricing/details/scheduler/)。

## <a name="see-also"></a>另請參閱
 [排程器是什麼？](scheduler-intro.md)

 [Azure 排程器概念、術語及實體階層](scheduler-concepts-terms.md)

 [在 Azure 入口網站中開始使用排程器](scheduler-get-started-portal.md)

 [Azure 排程器 REST API 參考](https://msdn.microsoft.com/library/mt629143)

 [Azure 排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)

 [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 排程器限制、預設值和錯誤碼](scheduler-limits-defaults-errors.md)

 [Azure 排程器輸出驗證](scheduler-outbound-authentication.md)

