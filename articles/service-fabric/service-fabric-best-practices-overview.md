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
ms.date: 04/24/2019
ms.author: pepogors
ms.openlocfilehash: 051d6b1129724ce4e8a67bde4e56ebe61cd832f3
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231377"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric 應用程式與叢集最佳做法

若要成功地管理 Azure Service Fabric 應用程式和叢集，有作業，我們強烈建議您執行的實際執行環境; 可靠性最佳化請執行此文件中定義的作業，然後選取其中一個我們[Azure 範例 Service Fabric 叢集範本](https://github.com/Azure-Samples/service-fabric-cluster-templates)開始設計您的生產環境方案，或修改您現有的範本，以納入這些做法。

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

## <a name="application-design"></a>應用程式設計

* [應用程式的設計的最佳作法](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>檢查清單

完成上述各節之後，請確定您已在生產整備度檢查清單中整合所有最佳做法：
* [Azure Service Fabric 生產整備度檢查清單](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>後續步驟

* 在 VM 或執行 Windows Server 的電腦上建立叢集：[適用於 Windows Server 的 Service Fabric 叢集建立](service-fabric-cluster-creation-for-windows-server.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-cluster-creation-via-portal.md)
* 疑難排解：[Service Fabric 疑難排解指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)