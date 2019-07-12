---
title: Azure 資訊安全中心的安全性警示 |Microsoft Docs
description: 本主題說明什麼是安全性警示和 Azure 資訊安全中心可用的不同類型。
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
ms.author: monhaber
ms.openlocfilehash: 06f41bbfd97d5deb59e7bfd08615b2f28e256070
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571669"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure 資訊安全中心的安全性警示

本文提供不同類型的 Azure 資訊安全中心 (ASC) 中可用的安全性警示。 有各種不同的許多不同的資源類型的警示。 ASC 會針對產生警示，在 Azure 上部署這兩個資源，並部署在內部部署和混合式雲端環境的資源。 

## <a name="what-are-security-alerts"></a>什麼是安全性警示：

警示是當它偵測到您的資源上的威脅時，資訊安全中心會產生通知。 其設定優先權和列出的警示，以及讓您快速調查問題所需的資訊。 資訊安全中心也會提供如何修復攻擊的建議。

## <a name="how-does-security-center-detect-threats"></a>資訊安全中心如何偵測威脅？

若要偵測真正的威脅並減少誤判，資訊安全中心會收集、 分析，並整合您的 Azure 資源、 網路和連線的合作夥伴解決方案，例如防火牆和端點保護解決方案的記錄檔資料。 資訊安全中心會分析這項資訊，通常相互關聯來自多個來源，以識別威脅的資訊。

ASC 會監視資源是否在 Azure 上部署或部署其他內部部署和混合式雲端環境。 如需偵測及回應威脅的詳細資訊，請參閱[如何資訊安全中心偵測及回應威脅](security-center-detection-capabilities.md#asc-detects)。

## <a name="security-alert-types"></a>安全性警示類型

下列主題會引導您完成根據資源類型的不同 ASC 警示：

* [IaaS Vm 和伺服器的警示](security-center-alerts-iaas.md)
* [原生計算，警示](security-center-alerts-compute.md)
* [資料服務警示](security-center-alerts-data-services.md)

下列主題說明資訊安全中心如何利用不同，它會從與 Azure 基礎結構整合，以便在 Azure 上套用額外的保護層級部署的資源收集的遙測：

* [圖層的服務警示](security-center-alerts-service-layer.md)
* [與 Azure 的安全性產品的整合](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>警示的事件有哪些？

安全性事件是相關的警示，而非個別列出每個警示的集合。 資訊安全中心會使用融合到安全性事件，相互關聯不同的警示和低精確度的訊號。

使用事件，資訊安全中心為您提供攻擊活動和所有相關警示的單一檢視。 這個檢視可讓您快速了解攻擊者所採取的動作，以及受到影響的資源。 如需詳細資訊，請參閱 <<c0> [ 雲端智慧警示相互關聯](security-center-alerts-cloud-smart.md)。

## <a name="get-started-with-alerts"></a>開始使用警示

請參閱下列主題中，若要深入了解監視 ASC、 以及由 ASC 警示的回應方式的指導方針負責的資源。

* 若要查看哪些平台和功能會受到 ASC，請參閱[平台和 Azure 資訊安全中心所支援的功能](security-center-os-coverage.md)。  
* 若要了解什麼是安全性事件與 ASC 回應給它們的方式，請參閱[如何處理 Azure 資訊安全中心的安全性事件](security-center-incident.md)。 
* 若要了解如何管理您所收到的警示，請參閱[管理及回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)。
* 如何資訊驗證 資訊安全中心已正確設定，並以促進測試警示，請參閱[Azure 資訊安全中心的警示驗證](security-center-alert-validation.md)。  


## <a name="upgrade-to-standard-for-advanced-detections"></a>升級為標準的進階偵測

若要設定進階偵測，請升級至 Azure 資訊安全中心標準版。 

1. 從 [資訊安全中心] 功能表中，選取**安全性原則**。
2. 您想要移至標準層的訂用帳戶，按一下**編輯設定**。 
3. 從 [設定] 頁面中，選取**定價層**。 
   一個月可免費試用版。 若要了解更多，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。 

## <a name="next-steps"></a>後續步驟

在本文中，您已了解什麼是安全性警示和不同類型的資訊安全中心可用的警示。 如需詳細資訊，請參閱下列主題：

* [Azure 資訊安全中心規劃和操作指南](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure 資訊安全中心常見問題](https://docs.microsoft.com/azure/security-center/security-center-faq)：尋找有關使用服務的常見問題。

