---
title: 停用 Azure 監視器中監視 vm （預覽） |Microsoft Docs
description: 本文說明如何停止監視您的虛擬機器，在 Azure 監視器中的 Vm。
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
ms.openlocfilehash: eb667486a6e3279cb78fefe02723f14d9f7c9b4f
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155688"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>停用監視您的 Vm，Azure 監視器中的 Vm （預覽）

啟用您的虛擬機器 (Vm) 的監視之後，您可以稍後選擇停用 Vm 的 Azure 監視器中監視。 這篇文章會示範如何停用一或多個 Vm 的監視。  

目前，Azure 監視器的 Vm 不支援選擇性停用 VM 監視。 您的 Log Analytics 工作區可能會支援 Azure 監視器的 Vm 和其他解決方案。 它也可能會收集其他監視資料。 如果您的 Log Analytics 工作區會提供這些服務，您需要了解效果，以及停用監視開始之前的方法。

「適用於 VM 的 Azure 監視器」倚賴下列元件來提供其體驗：

* Log Analytics 工作區，儲存 Vm 和其他來源中的監視資料。
* 在工作區中設定的效能計數器的集合。 集合會更新在連線到工作區的所有 Vm 上的監視設定。
* `InfrastructureInsights` 和`ServiceMap`，這會監視工作區中設定的解決方案。 這些解決方案會更新在連線到工作區的所有 Vm 上的監視設定。
* `MicrosoftMonitoringAgent` 和`DependencyAgent`，這是 Azure VM 延伸模組。 這些擴充功能收集，並將資料傳送至工作區。

當您準備停用監視您的虛擬機器，請記住這些考量：

* 如果您使用單一 VM 評估，並使用預先選取的預設的 Log Analytics 工作區，您可以停用監視，可從 VM 解除安裝相依性代理程式，並中斷此工作區中的 Log Analytics 代理程式。 這個方法很適當，如果您想要用於其他用途的 VM，並決定稍後重新連線至不同的工作區。
* 如果您選取既有的 Log Analytics 工作區可支援其他監視解決方案和其他來源的資料收集時，您可以從工作區移除解決方案的元件，不會中斷或影響您的工作區項目。  

>[!NOTE]
> 從您的工作區中移除解決方案的元件之後，您可能會持續看到健全狀況狀態，從您的 Azure Vm;具體來說，您會看到效能，並對應資料，當您前往入口網站中的任一個檢視。 資料最終將會停止不會出現在**效能**並**地圖**檢視。 但是**健全狀況**檢視會繼續顯示為您的 Vm 的健康情況狀態。 **立即試用**選項就可以從選取的 Azure VM，因此您可以重新啟用未來監視。  

## <a name="remove-azure-monitor-for-vms-completely"></a>完全移除適用於 Vm 的 Azure 監視器

如果您仍然需要 Log Analytics 工作區，請遵循下列步驟來完全移除 適用於 Vm 的 Azure 監視器。 您將會移除`InfrastructureInsights`和`ServiceMap`解決方案從工作區。  

>[!NOTE]
>如果您使用服務對應監視解決方案，您的 Vm 中啟用 Azure 監視器和您仍需依賴它之前，請勿移除該方案，如下列程序的最後一個步驟中所述。  
>

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站中，選取 [所有服務]  。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會篩選根據您輸入的建議。 選取 [Log Analytics]  。
3. 在 Log Analytics 工作區清單中，選取 工作區選擇 當您啟用 Azure 監視器的 Vm。
4. 在左側，選取**解決方案**。  
5. 在解決方案的清單中，選取**InfrastructureInsights （工作區名稱）** 。 在 **概觀**方案，然後選取頁面**刪除**。 當提示確認，請選取**是**。  
6. 在解決方案的清單中，選取**ServiceMap （工作區名稱）** 。 在 **概觀**方案，然後選取頁面**刪除**。 當提示確認，請選取**是**。  

如果未指定，針對 Vm，啟用 Azure 監視器之前[收集效能計數器](vminsights-enable-overview.md#performance-counters-enabled)您的工作區的 Windows 或 Linux vm[停用這些規則](../platform/data-sources-performance-counters.md#configuring-performance-counters)Windows 和 Linux。

## <a name="disable-monitoring-and-keep-the-workspace"></a>停用監視，並保留工作區  

如果您的 Log Analytics 工作區仍然必須支援從其他來源，遵循下列步驟來監視在您用來評估 Azure 監視器適用於 Vm 的 VM 上停用監視。 針對 Azure Vm，您將相依性代理程式 VM 擴充功能和 Log Analytics 代理程式 VM 擴充功能適用於 Windows 或 Linux 直接從 VM 移除。 

>[!NOTE]
>如果不移除 Log Analytics 代理程式： 
>
> * Azure 自動化管理 VM，以協調處理或管理設定] 或 [更新。 
> * Azure 資訊安全中心管理安全性與威脅偵測的 VM。 
>
> 如果您移除 Log Analytics 代理程式，則將會導致這些服務和解決方案，從主動管理您的 VM。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 在 Azure 入口網站中，選取 [虛擬機器]  。 
3. 從清單中選取 VM。 
4. 在左側，選取**延伸模組**。 在 **延伸模組**頁面上，選取**DependencyAgent**。
5. 在 [擴充屬性] 頁面中，選取**解除安裝**。
6. 在 **延伸模組**頁面上，選取**MicrosoftMonitoringAgent**。 在 [擴充屬性] 頁面中，選取**解除安裝**。  
