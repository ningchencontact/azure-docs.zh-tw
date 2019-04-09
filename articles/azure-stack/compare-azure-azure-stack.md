---
title: 比較 Azure Stack 與全域 Azure | Microsoft Docs
description: 了解 Microsoft 如何在單一 Azure 生態系統中提供 Azure 和 Azure Stack 系列服務
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 5e871d467fec476f1dac77fb879d180ea2322c80
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650075"
---
# <a name="differences-between-global-azure-azure-stack-and-azure-stack-hci"></a>全域 Azure、Azure Stack 與 Azure Stack HCI 之間的差異

Microsoft 在單一 Azure 生態系統中提供 Azure 和 Azure Stack 系列服務。 無論您的企業使用全域 Azure 還是內部部署資源，皆可使用相同的應用程式模型、自助式入口網站，以及 API 與 Azure Resource Manager 來提供雲端式功能。

本文說明全域 Azure、Azure Stack 和 Azure Stack HCI 的功能，並提供常見案例的建議，以協助您做出最好的選擇為您的組織傳遞 Microsoft 雲端式服務。

![Azure 生態系統概觀](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>全域 Azure

Microsoft Azure 是一款不斷擴大的雲端服務組合，可協助組織解決商業挑戰。 它可讓您自由地在大規模的全球網路上使用自己慣用的工具和架構來建置、管理及部署應用程式。

全域 Azure 提供 100 多項可在全球 54 個區域使用的服務。 如需全域 Azure 服務的最新清單，請參閱[*依區域提供的產品*](https://azure.microsoft.com/regions/services)。 Azure 中的可用服務會依類別、是否已正式推出或可供預覽而列出。

如需全域 Azure 服務的詳細資訊，請參閱[開始使用 Azure](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1)。

## <a name="azure-stack"></a>Azure Stack

Azure Stack 是 Azure 的擴充功能，可讓您將雲端運算的靈活度和創新性導入內部部署環境中。 Azure Stack 部署於內部，可用來提供已連線至網際網路 (和 Azure) 的 Azure 一致服務，或將其提供於未連接網際網路的無連線環境中。 Azure Stack 使用與全域 Stack 相同的基礎技術，包括基礎結構即服務 (IaaS)、軟體即服務 (SaaS) 和選擇性的平台即服務 (PaaS) 功能的核心元件，其中包括：

- 適用於 Windows 和 Linux 的 Azure VM
- Azure Web 應用程式和函式
- Azure 金鑰保存庫
- Azure Resource Manager
- Azure Marketplace
- 容器
- Azure IoT 中樞和事件中樞
- 管理工具 (方案、供應項目、RBAC 等等)

Azure Stack 的 PaaS 功能是選擇性的，因為 Microsoft 並未運作 Azure Stack，而是由客戶操作。 這表示如果您已準備好要抽離使用者的基礎結構和程序，即可提供任何 PaaS 服務給使用者。 不過，Azure Stack 包含數個選擇性的 PaaS 服務提供者，包括 App Service、SQL 資料庫和 MySQL 資料庫。 這些項目會以資源提供者的形式提供，因此已可供多租用戶使用、會使用標準 Azure Stack 更新持續更新、會顯示在 Azure Stack 入口網站中，並且已與 Azure Stack 妥善整合。

除了上述資源提供者以外，還有其他 PaaS 服務可供使用，並且已經過測試而成為可在 IaaS 中執行的 [Azure Resource Manager 範本型解決方案](https://github.com/Azure/AzureStack-QuickStart-Templates)，但您若是 Azure Stack 操作員，則可以將其作為 PaaS 服務提供給使用者，包括：

- Service Fabric
- Kubernetes 容器服務
- IoT 中樞和事件中樞
- Ethereum 區塊鏈
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack"></a>Azure Stack 的範例使用案例：

- 財務模型
- 臨床與宣告資料
- IoT 裝置分析
- 零售種類最佳化
- 供應鏈最佳化
- 企業 IoT
- 預測性維護
- 智慧城市
- 公民參與

透過[什麼是 Azure Stack](azure-stack-overview.md) 深入了解 Azure Stack。

## <a name="azure-stack-hci"></a>Azure Stack HCI 

Azure Stack HCI 解決方案可讓您透過超大範圍的基礎結構 (HCI) 解決方案在內部部署執行虛擬機器，並輕鬆連線到 Azure。 在內部部署使用一致的 Azure 服務來建置及執行雲端應用程式，以滿足法規或技術需求。 除了在內部執行虛擬化應用程式以外，Azure Stack HCI 可讓您取代及整合過時的伺服器基礎結構，並使用 Windows Admin Center 連線到 Azure 雲端服務。

Azure Stack HCI 提供經過驗證的 HCI 解決方案，採用 Hyper-V 與儲存空間直接存取與 Windows Server 2019 軟體定義資料中心 (SDDC) 等技術。 Windows Admin Center 用來對 Azure 服務進行管理和整合式存取，例如：

- Azure 備份
- Azure Site Recovery
- Azure 監視器和更新

如需可與 Azure Stack HCI 連線之 Azure 服務的更新清單，請參閱[將 Windows Server 連線至 Azure 混合式服務](https://docs.microsoft.com/windows-server/azure-hybrid-services/index)。

### <a name="example-use-cases-for-azure-stack-hci"></a>Azure Stack HCI 的範例使用案例
- 遠端辦公室或分公司系統
- 資料中心彙總
- 虛擬桌面基礎結構
- 商務關鍵基礎結構
- 低成本儲存體
- 雲端中的高可用性和災害復原
- 企業應用程式，例如 SQL Server

請瀏覽 [Azure Stack HCI 網站](https://azure.microsoft.com/overview/azure-stack/hci/)，檢視 Microsoft 合作夥伴目前提供的 70 餘個 Azure Stack HCI 解決方案。

## <a name="next-steps"></a>後續步驟

[Azure Stack 系統管理基本概念](azure-stack-manage-basics.md)

[快速入門：使用 Azure Stack 系統管理入口網站](azure-stack-manage-portals.md)
