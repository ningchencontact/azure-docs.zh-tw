---
title: Azure Service Fabric 應用程式與叢集最佳做法 | Microsoft Docs
description: 管理 Service Fabric 叢集和應用程式的最佳做法。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: a0174a5442de7a10e45e9dc2a2f43f5f401d1a69
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805065"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric 應用程式與叢集最佳做法

若要成功管理 Azure Service Fabric 應用程式和叢集，我們強烈建議您執行幾項作業，將生產環境最佳化以獲得可靠性；請執行本文件中定義的作業，然後選取我們 [Azure 範例 Service Fabric 叢集範本](https://github.com/Azure-Samples/service-fabric-cluster-templates)的其中之一，以開始設計您的生產解決方案，或修改現有的範本，以結合這些做法。

## <a name="security"></a>安全性 

* [安全性的最佳做法](service-fabric-best-practices-security.md)

## <a name="networking"></a>網路功能

* [建立網路的最佳做法](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>計算規劃和調整

* [調整計算規模的最佳做法](service-fabric-best-practices-capacity-scaling.md)
* [計算容量規劃](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>基礎結構即程式碼

* [實作基礎結構即程式碼性的最佳做法](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>監視和診斷

* [叢集監視和診斷的最佳做法](service-fabric-best-practices-monitoring.md)

## <a name="checklist"></a>檢查清單

完成上述各節之後，請確定您已在生產整備度檢查清單中整合所有最佳做法：
* [Azure Service Fabric 生產整備度檢查清單](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>後續步驟

* 在 VM 或執行 Windows Server 的電腦上建立叢集：[適用於 Windows Server 的 Service Fabric 叢集建立](service-fabric-cluster-creation-for-windows-server.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-cluster-creation-via-portal.md)
* 疑難排解：[Service Fabric 疑難排解指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)