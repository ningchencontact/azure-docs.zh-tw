---
title: Azure 與 AppSource Marketplace 供應項目 | Microsoft Docs
description: 建立和管理 Azure 與 AppSource Marketplace 的供應項目
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: 0d6879c6b9be06af4bb289761cc8f26b7e56d18e
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355126"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Azure 與 AppSource Marketplace 供應項目

本節的第一部分將介紹用來建立及管理 Azure 和 AppSource Marketplace 供應項目的一般作業。  此部分會提供您管理特定供應項目類型所需了解的背景，以及所有供應項目類型的常見技術資訊。  本節大部分內容包含有關如何建立和管理特定供應項目類型的詳細指示。  

下列影片將介紹 Azure Marketplace 或 AppSource 中可用的各種功能和不同供應項目類型。  其中也涵蓋在這些市集中發佈應用程式或服務的重要技術面與商務面。

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**建置適用於 Azure Marketplace 和 AppSource 的應用程式與服務 - 組建 2018**

如需這些市集的詳細資訊，請參閱 [Azure Marketplace 和 AppSource 發佈指南](../marketplace-publishers-guide.md)。


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure Marketplace 和 AppSource 供應項目類型

下表列出 [Cloud Partner 入口網站](https://cloudpartner.azure.com)目前支援的供應項目類型。  此資料表針對每個供應項目類型列出可陳列供應項目的市集，以及供應項目解決方案技術的一般描述。

|                供應項目類型                |  Marketplace  |   說明                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure 應用程式](./azure-applications/cpp-azure-app-offer.md) | Azure | 解決方案由透過 Azure Resource Manager 範本部署的一個或多個虛擬機器 (VM) 及選擇性自訂 Azure 程式碼組成。  部署可以由客戶透過解決方案範本進行，或者由發行者進行管理。 此類型主要用來提供更大的彈性 (與所提供的虛擬機器供應項目類型相比)。  |
| [諮詢服務](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | 兩者皆可 | Microsoft 的合格顧問可以在 Azure Marketplace 或 AppSource 列出他們領域專屬的服務。  他們的專業可協助客戶評估問題，並建立和部署符合客戶營運目標的適當解決方案。  |
| [容器](./containers/cpp-containers-offer.md)  | Azure | 解決方案是佈建成 Kubernetes 型服務或 Azure 容器執行個體的 Docker 容器映像。 |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | 擴充此企業資源規劃 (ERP) 及企業管理系統的套件。 |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | 透過銷售、服務、專案服務及現場服務模組擴充此資源管理 (CRM) 系統的套件。  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | 擴充此企業資源規劃 (ERP) 服務的套件，ERP 可支援進階財務、營運、製造和供應鏈管理。 |
| [IoT Edge 模組](./iot-edge-module/cpp-offer-process-parts.md) | Azure | 在 IoT Edge 裝置上執行且與 Docker 相容的容器。  其包含使用自訂程式碼、其他 Azure 服務和第 3 方服務組合的小型計算模組。 |
| [SaaS 應用程式](./saas-app/cpp-saas-offer.md) | Azure | 解決方案是由發行者管理的軟體即服務訂用帳戶，使用者會透過運用 Azure Active Directory 技術的自訂介面來登入此訂用帳戶。 |
| [虛擬機器](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | 解決方案包含在部署至客戶訂用帳戶的單一虛擬機器內。  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

如需詳細資訊，請參閱[各個供應項目類型的發佈指南](../publisher-guide-by-offer-type.md)。


## <a name="next-steps"></a>後續步驟

在[管理供應項目](./manage-offers/cpp-manage-offers.md)主題中，您將了解可以對市集供應項目執行的一般作業，以及其技術屬性和資產。
