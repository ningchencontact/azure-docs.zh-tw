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
ms.date: 05/24/2019
ms.author: aljo
ms.openlocfilehash: 606b14fba093b6ec8039c646a49bc3bf7d24eb51
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66296792"
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

## <a name="supported-version-names"></a>支援的版本名稱

下表列出 Service Fabric 和其對應的版本號碼的版本名稱。

| 版本名稱 | Windows 版本號碼 | Linux 版本號碼 |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | NA |
| 5.3 CU1 | 5.3.204.9494 | NA |
| 5.3 CU2 | 5.3.301.9590 | NA |
| 5.3 CU3 | 5.3.311.9590 | NA |
| 5.4 CU2 | 5.4.164.9494 | NA |
| 5.5 CU1 | 5.5.216.0    | NA |
| 5.5 CU2 | 5.5.219.0    | NA |
| 5.5 CU3 | 5.5.227.0    | NA |
| 5.5 CU4 | 5.5.232.0    | NA |
| 5.6 RTO | 5.6.204.9494 | NA |
| 5.6 CU2 | 5.6.210.9494 | NA |
| 5.6 CU3 | 5.6.220.9494 | NA |
| 5.7 RTO | 5.7.198.9494 | NA |
| 5.7 CU4 | 5.7.221.9494 | NA |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | NA |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | NA |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | NA |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |