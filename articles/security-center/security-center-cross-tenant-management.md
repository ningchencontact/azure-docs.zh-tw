---
title: Azure 資訊安全中心中的跨租使用者管理 |Microsoft Docs
description: " 了解如何在 Azure 資訊安全中心啟用資料收集。 "
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: v-mohabe
ms.openlocfilehash: d6b5b528c3021bfb62bc30ad5910524db36e7e95
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950540"
---
# <a name="cross-tenant-management-in-security-center"></a>資訊安全中心中的跨租使用者管理

跨租使用者管理可讓您利用[Azure 委派的資源管理](../lighthouse/concepts/azure-delegated-resource-management.md), 來查看及管理資訊安全中心中多個租使用者的安全性狀態。 從單一視圖有效率地管理多個租使用者, 而不需要登入每個租使用者的目錄。

- 服務提供者可以從自己的租使用者中管理資源的安全性狀態 (適用于多個客戶)。

- 具有多個租使用者之組織的安全性小組可以從單一位置查看和管理其安全性狀態。

## <a name="set-up-cross-tenant-management"></a>設定跨租使用者管理

使用[Azure 委派的資源管理](../lighthouse/concepts/azure-delegated-resource-management.md), 將受管理租使用者資源的存取權委派給您自己的租使用者, 以設定跨租使用者管理。

> [!NOTE]
> Azure 委派的資源管理是 Azure Lighthouse 的重要元件之一。

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>跨租使用者管理在資訊安全中心中的運作方式

您可以透過在單一租使用者中管理多個訂用帳戶的相同方式, 在多個租使用者中檢查及管理訂用帳戶。

從頂端功能表列中, 按一下篩選圖示, 然後從每個租使用者的目錄中選取您想要查看的訂用帳戶。

  ![篩選租使用者](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Views 和動作基本上相同。 以下是一些範例：

- **管理安全性原則**:從一個觀點來看, 使用[原則](tutorial-security-policy.md)來管理許多資源的安全性狀態、對安全性建議採取動作, 以及收集和管理安全性相關資料。
- **改善安全分數和合規性**狀態:跨租使用者可見度可讓您查看所有租使用者的整體安全性狀態, 以及在何處和如何最佳地改善每個租使用者的[安全分數](security-center-secure-score.md)和[合規性](security-center-compliance-dashboard.md)狀態。
- **補救建議**:一次監視並補救來自不同租使用者的許多資源[建議](security-center-recommendations.md)。 接著, 您可以立即解決所有租使用者都有最高風險的弱點。
- **管理警示**:偵測不同租使用者中的[警示](security-center-alerts-overview.md)。 針對不符合可採取動作之[補救步驟](security-center-managing-and-responding-alerts.md)的資源採取行動。

- **管理先進的雲端防禦功能和更多**:管理各種威脅偵測和保護服務, 例如[即時 (JIT) VM 存取](security-center-just-in-time.md)、彈性[網路強化](security-center-adaptive-network-hardening.md)、彈性[應用](security-center-adaptive-application.md)程式控制等等。
 
## <a name="next-steps"></a>後續步驟
本文說明跨租使用者管理在資訊安全中心中的運作方式。 如要深入了解資訊安全中心，請參閱下列主題：

* [使用 Azure 資訊安全中心強化您的安全性](security-center-monitoring.md)狀態--瞭解如何監視 Azure 資源的健康情況。
* [Azure 資訊安全中心常見問題集](security-center-faq.md)-- 尋找有關使用服務的常見問題。
