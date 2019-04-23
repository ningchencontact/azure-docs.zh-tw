---
title: Azure Service Fabric 中支援的叢集版本 |Microsoft Docs
description: 深入了解 Azure Service Fabric 中的叢集版本。
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
ms.openlocfilehash: 75e95737eecb9407a80103d1cad00d4987fe7091
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998816"
---
# <a name="supported-service-fabric-versions"></a>支援的 Service Fabric 版本

請確定您的叢集一律執行支援的 Azure Service Fabric 版本。 至少 60 天之後我們宣布發行新版本的 Service Fabric 中，針對先前版本的支援, 結束。 您可以上找到的新版本的公告[Service Fabric 小組部落格](https://blogs.msdn.microsoft.com/azureservicefabric/)。

請參閱下列文件，如需有關如何保留您的叢集執行支援的 Service Fabric 版本：

- [升級 Azure Service Fabric 叢集](service-fabric-cluster-upgrade.md)
- [升級獨立 Windows Server 叢集執行的 Service Fabric 版本](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>支援的版本

下表列出 Service Fabric 和其支援結束日期的版本。

| 在叢集中的 Service Fabric 執行階段 | 可以直接從叢集版本升級 |相容的 SDK 或 NuGet 套件版本 | 結束支援 |
| --- | --- |--- | --- |
| 5.3.121 之前的所有叢集版本 | 5.1.158.* |小於或等於 2.3 版本 |2017 年 1 月 20 日 |
| 5.3.* | 5.1.158.* |小於或等於 2.3 版本 |2017 年 2 月 24 日 |
| 5.4.* | 5.1.158.* |小於或等於 2.4 版本 |2017 年 5 月 10 日       |
| 5.5.* | 5.4.164.* |小於或等於 2.5 版本 |2017 年 8 月 10 日    |
| 5.6.* | 5.4.164.* |小於或等於 2.6 版本 |2017 年 10 月 13 日   |
| 5.7.* | 5.4.164.* |小於或等於 2.7 版 |2017 年 12 月 15日日  |
| 6.0.* | 5.6.205.* |小於或等於 2.8 版 |2018 年 3 月 30日日     |
| 6.1.* | 5.7.221.* |小於或等於 3.0 版 |2018 年 7 月 15 日      |
| 6.2.* | 6.0.232.* |小於或等於 3.1 版 |2018 年 10 月 26日日   |
| 6.3.* | 6.1.480.* |小於或等於 3.2 版 |2019 年 3 月 31日日  |
| 6.4.* | 6.2.301.* |小於或等於 3.3 版 |目前的版本，因此沒有結束日期 |

## <a name="supported-operating-systems"></a>受支援的作業系統

下表列出支援的 Service Fabric 版本支援的作業系統。

| 作業系統 | 最早的受支援的 Service Fabric 版本 |
| --- | --- |
| Windows Server 2012 R2 | 所有版本 |
| Windows Server 2016 | 所有版本 |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

