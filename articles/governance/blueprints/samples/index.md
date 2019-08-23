---
title: 藍圖範例的索引
description: 使用 Azure 藍圖部署的合規性和標準環境範例索引。
author: DCtheGeek
manager: carmonm
ms.service: blueprints
ms.topic: sample
ms.date: 08/20/2019
ms.author: dacoulte
ms.custom: fasttrack-edit
ms.openlocfilehash: 8a91ae0c576299e139cf036f1f20b90470f51349
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972293"
---
# <a name="azure-blueprints-samples"></a>Azure 藍圖範例

下表包含 Azure 藍圖的範例連結。 每個範例均具備生產品質且可立即部署，協助您達成各種合規性需求。

## <a name="standards-based-blueprint-samples"></a>標準型藍圖範例

|  |  |
|---------|---------|
| [IRS 1075](./irs-1075/index.md) | 提供成立條件以符合 IRS 1075。|
| [ISO 27001](./iso27001/index.md) | 提供成立條件以符合 ISO 27001。 |
| [ISO 27001 共用服務](./iso27001-shared/index.md) | 提供一組符合規範的基礎結構模式和原則防護措施，可協助您取得 ISO 27001 證明。 |
| [ISO 27001 App Service 環境/SQL Database 工作負載](./iso27001-ase-sql-workload/index.md) | 提供 [ISO 27001 共用服務](./iso27001-shared/index.md)藍圖範例的其他基礎結構。 |
| [NIST SP 800-53 R4](./nist-sp-800-53-rev4/index.md) | 提供成立條件以符合 NIST SP 800-53 R4。 |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | 提供一組原則以協助符合 PCI-DSS v3.2.1 規範。 |
| [UK OFFICIAL 與 UK NHS Governance](./ukofficial/index.md) | 提供一組符合規範的基礎結構模式和原則防護措施，可協助您取得 UK OFFICIAL 與 UK NHS 證明。 |
| [CAF 基礎](./caf-foundation/index.md) | 提供一組控制項來協助您管理雲端資產，使其與[適用於 Azure 的 Microsoft 雲端採用架構 (CAF)](/azure/architecture/cloud-adoption/governance/journeys/index)一致。 |
| [CAF 移轉登陸區域](./caf-migrate-landing-zone/index.md) | 提供一組控制項來協助您設定第一個工作負載的移轉及管理雲端資產，使其與[適用於 Azure 的 Microsoft Cloud 採用架構 (CAF)](/azure/architecture/cloud-adoption/migrate/index) 一致。 |

## <a name="samples-strategy"></a>範例策略

![藍圖範例的這略](../media/blueprint-samples-strategy.png)

CAF 基礎和 CAF 移轉登陸區域藍圖會假設客戶正在準備現有的全新單一訂用帳戶，以便將內部部署資產/工作負載遷移至 Azure。
(上圖中的區域 A 和 B)。  

您有機會逐一查看範例藍圖，並尋找客戶所套用的自訂模式。 也有機會主動處理產業專屬的藍圖，像是金融服務和電子商務 (區域 B 的頂端)。 同樣地，我們已預想到要為複雜的架構考量建立藍圖，例如多個訂用帳戶、高可用性、跨區域資源，以及對現有訂用帳戶和資源實作控制項的客戶 (區域 C 和 D)。

有一些範例藍圖可用於合規性需求及架構複雜性都很高的客戶案例 (上圖中的區域 E)。 上面的區域 F 就是由客戶和合作夥伴處理的區域，他們運用範例藍圖並根據其獨特需求進行自訂。

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。