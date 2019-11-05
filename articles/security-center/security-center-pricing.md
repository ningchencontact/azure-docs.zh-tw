---
title: 升級為 Azure 資訊安全中心標準層以增強安全性 | Microsoft Docs
description: 這篇文章提供 Azure 資訊安全中心的價格資訊。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 43ae34b946fada4d27c6843dbb8c3f9511aefd81
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520790"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>升級為 Azure 資訊安全中心標準層以增強安全性
Azure 資訊安全中心提供統一的安全性管理和進階的威脅防護，保護 Azure、內部部署和其他雲端中執行的工作負載。 其提供了針對混合式雲端工作負載的可見性和控制能力、可降低威脅曝露度的主動防禦措施、還有智慧型偵測可幫助您跟上網路攻擊快速發展的腳步。

## <a name="pricing-tiers"></a>定價層
資訊安全中心提供兩個層級：

- 當您第一次造訪 Azure 入口網站中的 [Azure 資訊安全中心] 儀表板，或透過 API 以程式設計方式啟用時，您的所有 Azure 訂用帳戶都會啟用**免費**層。 免費層提供安全性原則、持續的安全性評估和可採取動作的安全性建議，以協助您保護 Azure 資源。
- **標準**層將免費層的功能擴展到在私人雲端和其他公用雲端中執行的工作負載，為您的混合式雲端工作負載提供統一的安全性管理和威脅防護。 標準層也新增了先進的威脅偵測功能，其使用內建行為分析和機器學習來識別攻擊和零時差惡意探索、存取和應用程式控制，以降低暴露于網路攻擊和惡意程式碼的風險，以及個. 您可以免費試用標準層。 資訊安全中心 Standard 支援 Azure 資源，包括 Vm、虛擬機器擴展集、App Service、SQL server 和儲存體帳戶。 如果您有 Azure 資訊安全中心 Standard，可以根據資源類型退出宣告支援。 

大部分的 Vm 免費層安全性評定，以及許多標準層安全性警示，都需要安裝 Microsoft Monitoring Agent （MMA）功能。 您可以在資訊安全中心上啟用自動布建，自動為您的 Azure Vm 部署代理程式。

## <a name="try-standard-free-for-30-days"></a>免費試用「標準」30 天
標準層的前 30 天免費。 在 30 天結束時，如果您選擇繼續使用服務，我們將自動開始根據使用量計費。

您可以將整個 Azure 訂用帳戶升級至標準層，該層會繼承訂用帳戶中的所有資源。

若要取得標準層：

1. 選取**資訊安全中心**主功能表上的 [**定價 & 設定**]。
2. 選取您要升級至「標準」的訂用帳戶。
3. 選取 [定價層]。
4. 選取 [標準版] 以進行升級。
5. 按一下 [儲存]。

（僅提供影像中的價格供說明之用）[![資訊安全中心定價](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> 若要啟用資訊安全中心的所有功能，您必須將標準版定價層套用至包含適用虛擬機器的訂用帳戶。 設定工作區的定價並不會啟用 Azure 資源的即時 VM 存取、彈性應用程式控制和網路偵測。
>

## <a name="why-upgrade-to-standard"></a>為何要升級至標準？
資訊安全中心為您的混合式雲端工作負載提供增強的安全性與威脅防護，包括：

- **混合式安全性** – 取得您所有內部部署和雲端工作負載中安全性的統一檢視。 套用安全性原則，並持續評估您混合式雲端工作負載的安全性，以確保符合安全性標準。 收集、搜尋及分析來自多個來源（包括防火牆和其他合作夥伴解決方案）的安全性資料。
- **進階威脅偵測** - 利用進階分析和 Microsoft Intelligent Security Graph 勝過不斷進化的網路攻擊。  有效率的調控內建行為分析和機器學習服務，以發現攻擊和零時差惡意探索。 監視網路、機器和雲端服務中是否有傳入攻擊和侵入後活動。 使用互動式工具和內容相關威脅情報來簡化調查。
- **存取與應用**程式控制-藉由套用機器學習服務的允許清單建議，並配合您的特定工作負載，來封鎖惡意程式碼和其他不必要的應用程式。 使用 Azure Vm 上管理埠的即時控制存取來減少網路攻擊面。 這會大幅減少暴力密碼破解和其他網路攻擊的風險。
- **容器安全性功能**-受益于您容器化環境的弱點管理和即時威脅偵測。 啟用 container registry 資源時，可能需要12hrs，直到啟用所有功能為止。


## <a name="next-steps"></a>後續步驟
在本文中，已向您介紹資訊安全中心的價格。 若要深入了解標準層的增強式安全性和進階威脅防護，請參閱：

- [進階威脅偵測](security-center-threat-report.md)
- [即時 VM 存取控制](security-center-just-in-time.md)
- [容器安全性總覽](container-security.md)
- [以您選擇的貨幣和您的區域為依據的定價詳細資料](https://azure.microsoft.com/pricing/details/security-center/)