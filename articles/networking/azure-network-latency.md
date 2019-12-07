---
title: Azure 網路來回延遲統計資料 |Microsoft Docs
description: 瞭解 Azure 區域之間的來回行程延遲統計資料。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/04/2019
ms.author: mnayak
ms.openlocfilehash: 3947df81b67d5aefc1b628b6ddaf8275152a4cd3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893075"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 網路來回延遲統計資料

Azure 會使用內部監視工具，以及[ThousandEyes](https://thousandeyes.com)（協力廠商綜合監視服務）所收集的度量，持續監控其網路核心區域的延遲（速度）。

## <a name="how-are-the-measurements-collected"></a>如何收集度量？

延遲測量是從全球 Azure 雲端區域中裝載的 ThousandEyes 代理程式收集而來，在1分鐘的間隔內持續傳送網路探查。 每月延遲統計資料是從每月收集的樣本平均值衍生而來。

## <a name="november-2019-latency-figures"></a>2019年11月的延遲圖形

**11 月更新：** 已新增3個區域。

* 挪威東部
* 挪威西部
* 澳洲

以下顯示過去30天內 Azure 區域之間的每月平均來回行程時間（從2019年11月30日結束）。 下列測量值是由[ThousandEyes](https://thousandeyes.com)提供技術支援。

![Azure 區域間延遲統計資料](media/azure-network-latency/latency-nov-2019.png)

## <a name="next-steps"></a>後續步驟

瞭解[Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。
