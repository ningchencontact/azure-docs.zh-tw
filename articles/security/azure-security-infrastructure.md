---
title: Azure 基礎結構安全性 | Microsoft Docs
description: 本文描述 Microsoft 如何確保 Azure 資料中心的安全性。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 397bd1f904b676a6ba020ec78fb1cad05c460be1
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903851"
---
# <a name="azure-infrastructure-security"></a>Azure 基礎結構安全性
Microsoft Azure 是在 Microsoft 管理並操作的資料中心執行。 這些分散各地的資料中心在安全性與可靠性方面皆符合主要的業界標準，例如 ISO/IEC 27001:2013 和 NIST SP 800-53。 這些資料中心是由 Microsoft 作業人員管理、監視及進行系統管理。 這些作業人員在全天候持續提供世界上最大型線上服務方面擁有多年的經驗。

本系列文章提供 Microsoft 為保護 Azure 基礎結構所做措施的相關資訊。 這些文章說明：

- [實體安全性](azure-physical-security.md)
- [可用性](azure-infrastructure-availability.md)
- [元件和界線](azure-infrastructure-components.md)
- [網路架構](azure-infrastructure-network.md)
- [生產網路](azure-production-network.md)
- [SQL Database](azure-infrastructure-sql.md)
- [作業](azure-infrastructure-operations.md)
- [監視](azure-infrastructure-monitoring.md)
- [完整性](azure-infrastructure-integrity.md)
- [資料保護](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>共同責任模式
了解您與 Microsoft 之間的責任劃分相當重要。 在內部部署環境中，您擁有整個堆疊，但是當您移到雲端時，部分責任就會轉移給 Microsoft。 以下責任矩陣圖顯示軟體即服務 (SaaS)、平台即服務 (PaaS) 及基礎結構即服務 (IaaS) 部署中，分別由您和 Microsoft 負責的堆疊領域。

![共同責任][1]

不論部署類型為何，一律由您承擔責任的對象包括：

- 資料
- 端點
- 帳戶
- 存取管理

請務必了解您與 Microsoft 之間就 SaaS、PaaS 及 IaaS 部署方面的職責劃分。 如需詳細資訊，請參閱[雲端運算的共同責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)。

## <a name="next-steps"></a>後續步驟
若要深入了解 Microsoft 為保護 Azure 基礎結構執行了哪些動作，請參閱：

- [Azure 設備、廠房以及實體安全性](azure-physical-security.md)
- [Azure 基礎結構可用性](azure-infrastructure-availability.md)
- [Azure 資訊系統元件和界限](azure-infrastructure-components.md)
- [Azure 網路架構](azure-infrastructure-network.md)
- [Azure 生產網路](azure-production-network.md)
- [Microsoft Azure SQL Database 安全性功能](azure-infrastructure-sql.md)
- [Azure 生產作業和管理](azure-infrastructure-operations.md)
- [監視 Azure 基礎結構](azure-infrastructure-monitoring.md)
- [Azure 基礎結構完整性](azure-infrastructure-integrity.md)
- [在 Azure 中保護客戶資料](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
