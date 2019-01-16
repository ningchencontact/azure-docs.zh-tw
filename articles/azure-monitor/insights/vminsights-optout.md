---
title: 如何使用適用於 VM 的 Azure 監視器 (預覽) 來停用監視 | Microsoft Docs
description: 本文說明如何使用「適用於 VM 的 Azure 監視器」來中斷對虛擬機器的監視。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: f0addcc0b6220bcf877c3cac20f2d1376eb19e61
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191066"
---
# <a name="how-to-disable-monitoring-of-your-virtual-machines-with-azure-monitor-for-vms-preview"></a>如何使用適用於 VM 的 Azure 監視器 (預覽) 來停用對虛擬機器的監視

如果您在對虛擬機器啟用監視之後，決定不要再使用「適用於 VM 的 Azure 監視器」來監視它們，您可以停用監視。 本文說明如何針對單一或多個 VM 完成此操作。  

目前，「適用於 VM 的 Azure 監視器」不支援選擇性地停用 VM。 如果您的 Log Analytics 工作區已設定為支援此解決方案和其他解決方案，以及收集其他監視資料，請務必先了解下述影響和方法，再繼續進行。

「適用於 VM 的 Azure 監視器」倚賴下列元件來提供其體驗：

* Log Analytics 工作區，此工作區會儲存收集自 VM 及其他來源的監視資料。
* 工作區中設定的效能計數器集合，此集合會更新連線至工作區之所有 VM 上的監視設定。
* 工作區中設定的兩個監視解決方案 - **InfrastructureInsights** 和 **ServiceMap**，這兩個解決方案會更新連線至工作區之所有 VM 上的監視設定。
* 兩個 Azure 虛擬機器延伸模組 - **MicrosoftMonitoringAgent** 和 **DepenendencyAgent**，這兩個延伸模組會收集資料並將資料傳送給工作區。

準備使用「適用於 VM 的 Azure 監視器」來停用對虛擬機器的監視時，請考量下列做法：

* 如果您要評估的是單一 VM 且已接受預先選取的預設 Log Analytics 工作區，則您可以從 VM 解除安裝相依性代理程式，然後將 Log Analytics 代理程式與此工作區中段連線，來停用監視。 如果您打算將 VM 用於其他用途，並決定稍後將它重新連線到不同的工作區，便適合採用此方法。
* 如果您要使用 Log Analytics 工作區來支援其他監視解決方案和從其他來源收集資料，則可以從工作區中移除「適用於 VM 的 Azure 監視器」解決方案元件，而不會對工作區造成中斷或影響。  

>[!NOTE]
> 從工作區中移除解決方案元件之後，您可能會繼續看到來自 Azure VM 的健全狀態；特別是效能和對應資料，當您在入口網站中瀏覽至上述任一檢視時，就會看到該資料。 資料最終會在一段時間後停止出現在 [效能] 和 [對應] 檢視中；不過，[健康情況] 檢視會繼續顯示 VM 的健康情況狀態。 所選 Azure VM 會有 [立即試用] 選項可供使用，以允許您在未來重新啟用監視。  

## <a name="complete-removal-of-azure-monitor-for-vms"></a>完成適用於 VM 的 Azure 監視器的移除

下列步驟說明如果您仍然需要 Log Analytics 工作區，要如何完整移除「適用於 VM 的 Azure 監視器」。 您將從工作區中移除 **InfastructureInsights** 和 **ServiceMap** 解決方案。  

>[!NOTE]
>如果您在啟用「適用於 VM 的 Azure 監視器」之前使用「服務對應」監視解決方案，且仍然倚賴該解決方案，請勿依照下方步驟 6 所述移除該解決方案。  
>

1. 在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。
2. 在 Azure 入口網站中，按一下 [所有服務]。 在資源清單中，輸入 Log Analytics。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics]。
3. 在 Log Analytics 工作區清單中，選取將「適用於 VM 的 Azure 監視器」上線時所選擇的工作區。
4. 從左側窗格中，選取 [解決方案]。  
5. 在解決方案清單中，選取 [InfrastructureInsights (工作區名稱)]，然後在解決方案的 [概觀] 頁面上，按一下 [刪除]。  在系統提示您確認時，按一下 [Yes] (是)。  
6. 從解決方案清單中，選取 [ServiceMap (工作區名稱)]，然後在解決方案的 [概觀] 頁面上，按一下 [刪除]。  在系統提示您確認時，按一下 [Yes] (是)。  

如果在將「適用於 VM 的 Azure 監視器」上線之前，您沒有針對工作區中的 Windows 或 Linux 型 VM [收集已啟用的效能計數器](vminsights-onboard.md?toc=/azure/azure-monitor/toc.json#performance-counters-enabled)，則必須依照[這裡](../../azure-monitor/platform/data-sources-performance-counters.md?toc=/azure/azure-monitor/toc.json#configuring-performance-counters)所述的步驟，為 Windows 和 Linux 停用這些規則。

## <a name="disable-monitoring-for-an-azure-vm-and-retain-workspace"></a>停用 Azure VM 的監視並保留工作區  

下列步驟說明如何針對已啟用來評估「適用於 VM 的 Azure 監視器」的虛擬機器停用監視，但仍然需要 Log Analytics 工作區，才能支援從其他來源進行的監視。 如果這是 Azure VM，您將直接從 VM 移除 Windows/Linux 的相依性代理程式 VM 延伸模組和 Log Analytics 代理程式 VM 延伸模組。 

>[!NOTE]
>如果是由「Azure 自動化」管理虛擬機器以協調程序、管理設定或管理更新，或是由「Azure 資訊安全中心」管理來進行安全性管理和威脅偵測，則不應該移除 Log Analytics 代理程式。 否則，將會導致這些服務和解決方案無法主動管理您的 VM。 

1. 在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。 
2. 在 Azure 入口網站中，選取 [虛擬機器]。 
3. 從清單中選取 VM。 
4. 從左窗格中，選取 [延伸模組]，然後在 [延伸模組] 頁面上，選取 [DependencyAgent]。
5. 在延伸模組屬性頁面上，按一下 [解除安裝]。
6. 在 [延伸模組] 頁面上，選取 [MicrosoftMonitoringAgent]，然後在延伸模組屬性頁面上，按一下 [解除安裝]。  
