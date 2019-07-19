---
title: Azure 資訊安全中心中的安全性警示 |Microsoft Docs
description: 本主題說明 Azure 資訊安全中心中可用的安全性警示和不同的類型。
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
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 91dd397095718b3b43e41767af422801fd50b7f1
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295690"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure 資訊安全中心中的安全性警示

本文提供 Azure 資訊安全中心 (ASC) 中可用的各種安全性警示類型。 許多不同的資源類型都有各種警示。 ASC 會針對部署在 Azure 上的資源, 以及部署在內部部署和混合式雲端環境上的資源, 產生警示。 

## <a name="what-are-security-alerts"></a>什麼是安全性警示：

警示是當偵測到您資源的威脅時, 資訊安全中心產生的通知。 它會排定優先順序並列出警示, 以及您快速調查問題所需的資訊。 資訊安全中心也會提供如何修復攻擊的建議。

## <a name="how-does-security-center-detect-threats"></a>資訊安全中心偵測到威脅的方式為何？

為了偵測真正的威脅並降低誤報, 資訊安全中心會收集、分析及整合來自 Azure 資源、網路和已連線合作夥伴解決方案 (例如防火牆和端點保護解決方案) 的記錄資料。 資訊安全中心會分析這項資訊, 通常會將來自多個來源的資訊相互關聯, 以識別威脅。

ASC 會監視資源是否已部署在 Azure 上, 或部署于其他內部部署和混合式雲端環境。 若要深入瞭解偵測和回應威脅, 請參閱[資訊安全中心如何偵測及回應威脅](security-center-detection-capabilities.md#asc-detects)。

## <a name="security-alert-types"></a>安全性警示類型

下列主題會根據資源類型引導您完成不同的 ASC 警示:

* [IaaS Vm & 伺服器警示](security-center-alerts-iaas.md)
* [原生計算警示](security-center-alerts-compute.md)
* [資料服務警示](security-center-alerts-data-services.md)

下列主題說明資訊安全中心如何利用其所收集的不同遙測來與 Azure 基礎結構整合, 以針對部署在 Azure 上的資源套用額外的保護層級:

* [服務層警示](security-center-alerts-service-layer.md)
* [與 Azure 安全性產品整合](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>什麼是警示事件？

安全性事件是相關警示的集合, 而不是個別列出每個警示。 資訊安全中心使用融合, 將不同的警示和低精確度信號相互關聯至安全性事件。

資訊安全中心會使用事件, 為您提供攻擊活動和所有相關警示的單一觀點。 這個視圖可讓您快速瞭解攻擊者所採取的動作, 以及受影響的資源。 如需詳細資訊, 請參閱[雲端智慧警示相互關聯](security-center-alerts-cloud-smart.md)。

## <a name="get-started-with-alerts"></a>開始使用警示

請參閱下列主題, 以深入瞭解 ASC 所監視的資源, 以及有關如何回應 ASC 所提供之警示的指導方針。

* 若要查看哪些平臺和功能受到 ASC 保護, 請參閱[Azure 資訊安全中心支援的平臺和功能](security-center-os-coverage.md)。  
* 若要瞭解什麼是安全性事件以及 ASC 如何回應, 請參閱[如何處理 Azure 資訊安全中心中的安全性事件](security-center-incident.md)。 
* 若要瞭解如何管理您收到的警示, 請參閱[管理和回應 Azure 資訊安全中心中的安全性警示](security-center-managing-and-responding-alerts.md)。
* 如需如何驗證是否已正確設定資訊安全中心並使用測試警示的相關資訊, 請參閱[Azure 資訊安全中心中的警示驗證](security-center-alert-validation.md)。  


## <a name="upgrade-to-standard-for-advanced-detections"></a>升級為適用于 advanced 偵測的標準

若要設定進階偵測，請升級至 Azure 資訊安全中心標準版。 

1. 從 [資訊安全中心] 功能表中, 選取 [**安全性原則**]。
2. 針對您想要移至 [標準] 層的訂用帳戶, 按一下 [**編輯設定**]。 
3. 從 [設定] 頁面中, 選取 [**定價層**]。 
   一個月內有免費試用版。 若要深入瞭解, 請參閱[定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。 

## <a name="next-steps"></a>後續步驟

在本文中, 您已瞭解哪些是安全性警示, 以及資訊安全中心中可用的不同警示類型。 如需詳細資訊，請參閱下列主題：

* [Azure 資訊安全中心規劃和操作指南](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure 資訊安全中心常見問題](https://docs.microsoft.com/azure/security-center/security-center-faq)：尋找有關使用服務的常見問題。

