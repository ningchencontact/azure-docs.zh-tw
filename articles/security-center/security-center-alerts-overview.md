---
title: Azure 資訊安全中心中的安全性警示 |Microsoft Docs
description: 本主題說明哪些安全性警示, 以及 Azure 資訊安全中心中可用的不同類型。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/20/2019
ms.author: v-mohabe
ms.openlocfilehash: d905096432c6e05e5386631480e5cc45b5c890c5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013285"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure 資訊安全中心中的安全性警示

在 Azure 資訊安全中心中, 有許多不同的資源類型都有各種警示。 資訊安全中心會針對部署在 Azure 上的資源, 以及部署在內部部署和混合式雲端環境中的資源, 產生警示。 

## <a name="what-are-security-alerts"></a>什麼是安全性警示：

警示是當偵測到您資源的威脅時, 資訊安全中心產生的通知。 它會排定優先順序並列出警示, 以及您快速調查問題所需的資訊。 資訊安全中心也會提供如何修復攻擊的建議。

## <a name="how-does-security-center-detect-threats"></a>資訊安全中心偵測到威脅的方式為何？

為了偵測真正的威脅並降低誤報, 資訊安全中心會收集、分析及整合來自 Azure 資源和網路的記錄資料。 它也適用于已連線的合作夥伴解決方案, 例如防火牆和端點保護解決方案。 資訊安全中心會分析這項資訊, 通常會將來自多個來源的資訊相互關聯, 以識別威脅。

資訊安全中心會監視任何可能的環境中的資源。 若要深入瞭解偵測和回應威脅, 請參閱[資訊安全中心如何偵測及回應威脅](security-center-detection-capabilities.md#asc-detects)。

## <a name="security-alert-types"></a>安全性警示類型

下列主題會根據資源類型, 引導您完成不同的警示:

* [IaaS Vm 和伺服器警示](security-center-alerts-iaas.md)
* [原生計算警示](security-center-alerts-compute.md)
* [資料服務警示](security-center-alerts-data-services.md)

下列主題說明資訊安全中心如何使用它從與 Azure 基礎結構整合所收集的不同遙測資料, 以針對部署在 Azure 上的資源套用額外的保護層級:

* [服務層警示](security-center-alerts-service-layer.md)
* [與 Azure 安全性產品整合](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>什麼是安全性事件？

安全性事件是相關警示的集合, 而不是個別列出每個警示。 資訊安全中心使用[雲端智慧警示相互關聯](security-center-alerts-cloud-smart.md), 將不同的警示和低精確度信號相互關聯至安全性事件。

資訊安全中心會使用事件, 為您提供攻擊活動和所有相關警示的單一觀點。 這個視圖可讓您快速瞭解攻擊者所採取的動作, 以及受影響的資源。 如需詳細資訊, 請參閱[雲端智慧警示相互關聯](security-center-alerts-cloud-smart.md)。

## <a name="get-started-with-alerts"></a>開始使用警示

以下文章可協助您深入瞭解資訊安全中心所監視的資源, 並提供如何回應所呈現警示的指導方針。

* [Azure 資訊安全中心所支援的功能和平台](security-center-os-coverage.md)  
* [如何處理 Azure 資訊安全中心中的安全性事件](security-center-incident.md) 
* [管理及回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)
* [Azure 資訊安全中心的警示驗證](security-center-alert-validation.md)  


## <a name="upgrade-to-standard-for-advanced-detections"></a>升級為適用于 advanced 偵測的標準

若要設定進階偵測，請升級至 Azure 資訊安全中心標準版。 

1. 從 [**資訊安全中心**] 功能表中, 選取 [**安全性原則**]。
2. 針對您想要移至標準層的訂用帳戶, 選取 [**編輯設定**]。 
3. 從 [設定] 頁面中, 選取 [**定價層**]。 
   一個月內有免費試用版。 若要深入了解，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。 

## <a name="next-steps"></a>後續步驟

在本文中, 您已瞭解資訊安全中心中可用的各種警示類型。 如需詳細資訊，請參閱：

* [Azure 資訊安全中心規劃和操作指南](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure 資訊安全中心常見問題](https://docs.microsoft.com/azure/security-center/security-center-faq)

