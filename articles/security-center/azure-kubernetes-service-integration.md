---
title: Azure 資訊安全中心和 Azure Kubernetes Service |Microsoft Docs
description: 瞭解 Azure 資訊安全中心與 Azure Kubernetes Services 的整合
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 4fc0654aab1c68888e86dc95de658bc69a01e02d
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123972"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Azure Kubernetes Services 與資訊安全中心整合（預覽）
Azure Kubernetes Service （AKS）是 Microsoft 的受控服務，可用於開發、部署和管理容器化應用程式。 

使用 AKS 搭配 Azure 資訊安全中心的標準層（請參閱[定價](security-center-pricing.md)），以深入瞭解您的 AKS 節點、雲端流量和安全性控制。

資訊安全中心使用 AKS 主要節點已收集的資料，為您的 AKS 叢集帶來安全性優勢。 

![Azure 資訊安全中心和 Azure Kubernetes Service （AKS）高階總覽](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

這兩種工具共同構成了最佳的雲端原生 Kubernetes 安全性供應專案。 

## <a name="benefits-of-integration"></a>整合的優點

同時使用這兩個服務可提供：

* **安全性建議**-資訊安全中心識別您的 AKS 資源，並將其分類：從叢集到個別的虛擬機器。 接著，您可以針對每個資源來查看安全性建議。 如需詳細資訊，請參閱[如何執行安全性建議](security-center-recommendations.md)。 

    > [!NOTE]
    > 如果資訊安全中心建議的名稱結尾為 "（Preview）" 標籤，則會參考建議的預覽性質;不是功能。

* **環境強化**-資訊安全中心會持續監視 Kubernetes 叢集的設定，並產生反映業界標準的安全性建議。

* **執行時間保護**-透過持續分析下列 AKS 來源，資訊安全中心在主機*和*AKS 叢集層級偵測到威脅和惡意活動的警示（如需詳細資訊，請參閱[Azure container service](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)):
    * 原始的安全性事件，例如網路資料和進程建立
    * Kubernetes audit 記錄檔

![更詳細的 Azure 資訊安全中心和 Azure Kubernetes Service （AKS）](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Azure 資訊安全中心從您的 Kubernetes 環境掃描的部分資料可能包含機密資訊。

## <a name="next-steps"></a>後續步驟

若要深入瞭解資訊安全中心的容器安全性功能，請參閱：

* [Azure 資訊安全中心和容器安全性](container-security.md)

* [與 Azure Container Registry 整合](azure-container-registry-integration.md)

* [虛擬機器保護](security-center-virtual-machine-protection.md)-描述資訊安全中心的建議

* [Microsoft 的資料管理](https://www.microsoft.com/trust-center/privacy/data-management)-說明 microsoft 服務的資料原則（包括 Azure、Intune 和 Office 365）、microsoft 資料管理的詳細資料，以及影響您資料的保留原則