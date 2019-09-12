---
title: Azure Service Fabric 的安全性控制
description: 評估 Azure Service Fabric 的安全性控制清單
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: d62c7848588c494c8190f0d429ce2d6641928b52
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886547"
---
# <a name="security-controls-for-azure-service-fabric"></a>Azure Service Fabric 的安全性控制

本文記載內建于 Azure Service Fabric 的安全性控制項。 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| VNet 插入支援| 是 |  |
| 網路隔離和防火牆支援| 是 | 使用網路安全性群組 (NSG)。 |
| 強制通道支援| 是 | Azure 網路提供強制通道功能。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 使用 Azure 監視支援和協力廠商支援。 |
| 控制和管理平面記錄和審核| 是 | 所有控制平面作業都須通過稽核和核准的程序。 |
| 資料平面記錄和審核| N/A | 客戶擁有叢集。  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| Authorization| 是 | 透過 SFRP 對呼叫進行身分識別與存取管理 (IAM)。 直接呼叫叢集端點可支援兩種角色：使用者和管理員。客戶可將 API 對應至任一角色。 |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 待用的伺服器端加密：Microsoft 管理的金鑰 | 是 | 客戶擁有叢集, 以及用來建立叢集的虛擬機器擴展集。 可以在虛擬機器擴展集上啟用 Azure 磁片加密。 |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | 是 | 客戶擁有叢集, 以及用來建立叢集的虛擬機器擴展集。 可以在虛擬機器擴展集上啟用 Azure 磁片加密。 |
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| 是 |  |
| API 呼叫加密| 是 | Service Fabric API 呼叫會透過 Azure Resource Manager 進行。 需要有效的 JSON Web 權杖 (JWT)。 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。