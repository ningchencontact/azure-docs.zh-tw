---
title: 為企業採用 Azure DevTest Labs
description: 此文章提供在企業中採用 Azure DevTest Labs 的規範性指導方針。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: e1d119f3c7c5d6dbdb570d362c53b80dad7886bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60198032"
---
# <a name="set-up-azure-devtest-labs-infrastructure-in-your-enterprise"></a>在企業中設定 Azure DevTest Labs 基礎結構
企業快速採用雲端，因為其[優點](/azure/architecture/cloud-adoption/business-strategy/cloud-migration-business-case)包含靈活度、 彈性與高經濟效益。 若要開始開發和測試工作負載是常見的第一個步驟，供客戶使用雲端。  DevTest Labs 提供[功能](devtest-lab-concepts.md)可獲益的企業和支援[主要 enterprise 開發/測試案例](devtest-lab-guidance-get-started.md)。

這些工作負載移轉至雲端時，還有一組常用的考量：

- [保護開發/測試資源](devtest-lab-guidance-governance-policy-compliance.md)
- [管理和了解成本](devtest-lab-guidance-governance-cost-ownership.md)
- 啟用適用於開發人員的自助功能，而不會危害企業安全性與合規性
- 自動化和擴充以涵蓋其他案例的 DevTest Labs
- [擴充至數千個資源 DevTest Labs 為基礎的解決方案](devtest-lab-guidance-scale.md)
- [DevTest Labs 的大規模部署](devtest-lab-guidance-orchestrate-implementation.md)
- [開始使用的概念證明](devtest-lab-guidance-orchestrate-implementation.md)

## <a name="intended-audience"></a>目標對象
企業導向的專屬文件適用於 IT 規劃人員、 架構設計人員和管理員會負責建立及檢閱整體部署及監督作業做法。 如此一來，本文件會強調整個程序，並建議升級安全且穩定開發/測試環境中，其最終負責驅使組織內的 Azure DevTest Labs 採用的設計原則。

## <a name="enterprise-customers"></a>企業客戶

許多現有的 DevTest Labs 企業客戶成功使用 DevTest Labs 進行開發和測試其組織中的工作負載。 [深入了解](https://azure.microsoft.com/en-us/case-studies/?term=DevTest+labs)。

## <a name="next-steps"></a>後續步驟
- [適用於企業的參考架構](devtest-lab-reference-architecture.md)
