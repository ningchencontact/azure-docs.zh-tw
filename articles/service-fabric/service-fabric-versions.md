---
title: 了解 Azure Service Fabric 叢集版本 | Microsoft Docs
description: 支援的 Azure Service Fabric 叢集版本
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: d99000e1682b662f4bceb28096395243c894282f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681602"
---
# <a name="supported-service-fabric-versions"></a>支援的 Service Fabric 版本

请确保群集始终运行受支持的 Service Fabric 版本。 當我們宣布發行新版本的 Service Fabric 時，從當日起至少 60 天後，舊版就會標示為結束支援。 新版本在 [Service Fabric 团队博客](https://blogs.msdn.microsoft.com/azureservicefabric/)中公布。

請詳閱下列文件，以了解如何保持您的叢集執行支援的 Service Fabric 版本。

- [在 Azure 叢集上升級 Service Fabric 版本](service-fabric-cluster-upgrade.md)
- [升級獨立 windows 伺服器叢集上的 Service Fabric 版本](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>支援的版本

下表列出支援的 Service Fabric 版本和其支援結束日期。

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
| 6.2.* | 6.0.232.* |小於或等於 3.1 版 |2018 年 10 月 26 日   |
| 6.3.* | 6.1.480.* |小於或等於 3.2 版 |31,2019 年 3 月  |
| 6.4.* | 6.2.301.* |小於或等於 3.3 版 |目前版本，沒有結束日期 |

## <a name="supported-operating-systems"></a>受支援的作業系統

 下表列出支援的 Service Fabric 版本支援的作業系統。

| **作業系統** | **最早的受支援的 Service Fabric 版本** |
| --- | --- |
| Windows Server 2012 R2 | 所有版本 |
| Windows Server 2016 | 所有版本 |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

