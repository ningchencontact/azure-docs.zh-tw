---
title: 什麼是適用於 VM 的 Azure 監視器 (預覽)？ | Microsoft Docs
description: 適用於 VM 的 Azure 監視器是結合了 Azure VM 作業系統健康情況與效能監控的 Azure 監視器功能，其會自動探索應用程式元件和與其他資源的相依性，並且會對應它們之間的通訊。 本文提供概觀。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: 69aa2cbcaa6861b1d5c5c71769be2fb8046d9ea5
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53188488"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>什麼是適用於 VM 的 Azure 監視器 (預覽)？

適用於 VM 的 Azure 監視器會大規模監視您的 Azure 虛擬機器 (VM) 和虛擬機器擴展集。 此服務會分析您 Windows 和 Linux VM 的效能與健康情況，在其他資源和外部處理序上監視其處理序及其相依性。 

作為解決方案，適用於 VM 的 Azure 監視器包含針對在內部部署或其他雲端提供者中裝載的 VM，監視效能和應用程式相依性的支援。 有三個主要功能可提供深入解析：

* **執行 Windows 和 Linux 之 Azure VM 的邏輯元件**：已根據預先設定的健全準則進行衡量，而它們會在符合所評估的條件時警示您。  

* **預先定義的趨勢效能圖**：顯示客體 VM 作業系統的核心效能計量。

* **相依性對應**：從各種不同的資源群組和訂用帳戶，顯示與 VM 互連的元件。  

這些功能可分成三個檢視方塊：

* 健康情況
* 效能
* 對應

>[!NOTE]
>目前，僅針對 Azure 虛擬機器和虛擬機器擴展集提供健康情況功能。 效能與對應功能可支援您的環境或其他雲端提供者中裝載的 Azure VM 和虛擬機器。

與 Log Analytics 整合，可提供強大的彙總與篩選，而且它可以隨著時間執行趨勢分析。 單獨使用 Azure 監視器、服務對應或 Log Analytics，無法達到這類全面性工作負載監視。  

您可以直接從虛擬機器的單一 VM 中檢視此資料，或者可以使用 Azure 監視器來提供 VM 的彙總檢視。 此檢視會以每個功能的檢視方塊為依據：

* **健康情況**：VM 與資源群組有關。
* **對應**與**效能**：VM 會設定為要回報給特定 Log Analytics 工作區。

![Azure 入口網站中的虛擬機器深入解析檢視方塊](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Azure DevOps 可以提供重要應用程式的可預測效能與可用性。 它會識別危急的作業系統事件、效能瓶頸和網路問題。 Azure DevOps 也可協助您了解問題是否與其他相依性有關。  

## <a name="data-usage"></a>資料使用量 

當您部署適用於 VM 的 Azure 監視器時，您 VM 所收集的資料會內嵌並儲存於 Azure 監視器中。 根據 [Azure 監視器價格頁面](https://azure.microsoft.com/pricing/details/monitor/)上所發佈的價格，適用於 VM 的 Azure 監視器會針對下列項目進行計費：
* 擷取並儲存的資料。
* 受監視的健全準則計量時間序列數目。
* 已建立的警示規則。
* 已傳送的通知。 

記錄大小會隨著計數器的字串長度而異，而它可以增加邏輯磁碟和網路介面卡數目。 如果您已經擁有工作區，並且正在收集這些計數器，則不會收取任何重複費用。 如果您已經使用服務對應，您將看到的唯一變更會是傳送到 Azure 監視器的額外連線資料。

## <a name="next-steps"></a>後續步驟
若要了解有助於您監視虛擬機器的需求和方法，請檢閱[部署適用於 VM 的 Azure 監視器](vminsights-onboard.md)。
