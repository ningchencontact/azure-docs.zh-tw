---
title: Azure 網路來回延遲統計資料 |Microsoft Docs
description: 瞭解 Azure 區域之間的來回行程延遲統計資料。
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587585"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 網路來回延遲統計資料

Azure 會使用內部監視工具，以及[ThousandEyes](https://thousandeyes.com)（協力廠商綜合監視服務）所收集的度量，持續監控其網路核心區域的延遲（速度）。

## <a name="how-are-the-measurements-collected"></a>如何收集度量？

延遲測量會從全球裝載于 Azure 雲端區域的 ThousandEyes 代理程式收集，這會在1分鐘的間隔內持續傳送網路探查。 每月延遲統計資料是從每月收集的樣本平均值衍生而來。

## <a name="october-2019-latency-figures"></a>2019年10月的延遲圖形

在2019年10月31日結束的31天內，匯總區域中的每月最小和最大來回行程延遲時間為：

- 在**北美洲**區域內來回行程的**5 毫秒**到**72 毫秒**。
- 在**歐洲**地區內來回行程的**3 毫秒**到**28 毫秒**。
- 在**亞洲**地區的來回行程中， **4 毫秒**到**134 毫秒**。

下欄區域間的延遲測量是由[ThousandEyes](https://thousandeyes.com)提供技術支援。 下表中的度量單位為毫秒（毫秒）。

![Azure 區域間延遲統計資料](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>後續步驟
- 瞭解[Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。