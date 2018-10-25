---
title: 了解 Azure Service Fabric 叢集版本 | Microsoft Docs
description: 支援的 Azure Service Fabric 叢集版本
services: service-fabric
documentationcenter: .net
author: twhitney
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: TylerMSFT
ms.openlocfilehash: 1041f37486c556ae29eed47728336a1ccb518e78
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2018
ms.locfileid: "48040358"
---
# <a name="supported-service-fabric-versions"></a>支援的 Service Fabric 版本

請確定您的叢集一律執行支援的 Service Fabric 版本。 當我們宣布發行新版本的 Service Fabric 時，從當日起至少 60 天後，舊版就會標示為結束支援。 新的版本會於 [Service Fabric 小組部落格上](https://blogs.msdn.microsoft.com/azureservicefabric/)發佈。

請詳閱下列文件，以了解如何保持您的叢集執行支援的 Service Fabric 版本。

- [在 Azure 叢集上升級 Service Fabric 版本](service-fabric-cluster-upgrade.md)
- [在獨立 Windows 伺服器叢集上升級 Service Fabric 版本](service-fabric-cluster-upgrade-windows-server.md)

以下是支援的 Service Fabric 版本清單以及其支援結束日期。

| **叢集中的 Service Fabric 執行階段** | **可直接自叢集版本升級** |**相容的 SDK / NuGet 套件版本** | **結束支援日期** |
| --- | --- |--- | --- |
| 5.3.121 之前的所有叢集版本 | 5.1.158* |小於或等於 2.3 版本 |2017 年 1 月 20 日 |
| 5.3.* | 5.1.158.* |小於或等於 2.3 版本 |2017 年 2 月 24 日 |
| 5.4.* | 5.1.158.* |小於或等於 2.4 版本 |2017 年 5 月 10 日       |
| 5.5.* | 5.4.164.* |小於或等於 2.5 版本 |2017 年 8 月 10 日    |
| 5.6.* | 5.4.164.* |小於或等於 2.6 版本 |2017 年 10 月 13 日   |
| 5.7.* | 5.4.164.* |小於或等於 2.7 版 |2017 年 12 月 15 日  |
| 6.0.* | 5.6.205.* |小於或等於 2.8 版 |2018 年 3 月 30 日     |
| 6.1.* | 5.7.221.* |小於或等於 3.0 版 |2018 年 7 月 15 日      |
| 6.2.* | 6.0.232.* |小於或等於 3.1 版 |2018 年 10 月 26 日 |
| 6.3.* | 6.1.480.* |小於或等於 3.2 版 |目前版本，沒有結束日期 |