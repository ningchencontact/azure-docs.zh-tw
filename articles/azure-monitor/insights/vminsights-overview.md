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
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: 7d86b3fe9aeddd603d0c40b1c760cabdee42e396
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522098"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>什麼是適用於 VM 的 Azure 監視器 (預覽)？

適用於 VM 的 Azure 監視器會大規模監視您的 Azure 虛擬機器 (VM) 和虛擬機器擴展集。 此服務會分析您 Windows 和 Linux VM 的效能與健康情況，並且在其他資源和外部處理序上監視其處理序及相依性。 

作為解決方案，適用於 VM 的 Azure 監視器包含針對在內部部署或其他雲端提供者中裝載的 VM，監視效能和應用程式相依性的支援。 有三個主要功能可提供深入解析：

* **執行 Windows 和 Linux 之 Azure VM 的邏輯元件**：已根據預先設定的健全準則進行衡量，而它們會在符合所評估的條件時警示您。  

* **預先定義的效能趨勢圖表**:顯示客體 VM 作業系統的核心效能計量。

* **相依性對應**：從各種不同的資源群組和訂用帳戶，顯示與 VM 互連的元件。  

這些功能可分成三個檢視方塊：

* 健康情況
* 效能
* 對應

>[!NOTE]
>目前，「 健全狀況 」 功能只適用於 Azure 虛擬機器。 效能和地圖功能支援的 Azure Vm、 Azure VM 擴展集和虛擬機器裝載在您的環境或其他雲端提供者。

與 Azure 監視器記錄整合，可提供強大的彙總與篩選，而且它可以隨著時間執行趨勢分析。 單獨使用 Azure 監視器或服務對應，無法達到這類全面性工作負載監視。  

您可以直接從虛擬機器的單一 VM 中檢視此資料，或者可以使用 Azure 監視器來提供 VM 的彙總檢視。 此檢視會以每個功能的檢視方塊為依據：

* **健康情況**：VM 與資源群組有關。
* **對應**與**效能**：VM 會設定為要回報給特定 Log Analytics 工作區。

![Azure 入口網站中的虛擬機器深入解析檢視方塊](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

適用於 VM 的 Azure 監視器可以提供重要應用程式的可預測效能與可用性。 它會識別危急的作業系統事件、效能瓶頸和網路問題。 適用於 VM 的 Azure 監視器也可協助您了解問題是否與其他相依性有關。  

## <a name="data-usage"></a>資料使用量 

當您部署適用於 VM 的 Azure 監視器時，您 VM 所收集的資料會內嵌並儲存於 Azure 監視器中。 健全狀況準則度量時，會儲存在 Azure 監視器上，在時間序列資料庫、 效能和相依性所收集的資料會儲存在 Log Analytics 工作區。 根據 [Azure 監視器價格頁面](https://azure.microsoft.com/pricing/details/monitor/)上所發佈的價格，適用於 VM 的 Azure 監視器會針對下列項目進行計費：

* 擷取並儲存的資料。
* 受監視的健全準則計量時間序列數目。
* 已建立的警示規則。
* 已傳送的通知。 

記錄檔大小而異的字串長度的效能計數器，且它會增加邏輯磁碟和配置給 VM 的網路介面卡數目。 如果您已經擁有工作區，並且正在收集這些計數器，則不會收取任何重複費用。 如果您已經使用服務對應，您將看到的唯一變更會是傳送到 Azure 監視器的額外連線資料。

## <a name="next-steps"></a>後續步驟
若要了解有助於您監視虛擬機器的需求和方法，請檢閱[部署適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)。
