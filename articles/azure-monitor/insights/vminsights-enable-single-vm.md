---
title: 啟用 Azure 監視器評估的 Vm （預覽） |Microsoft Docs
description: 了解如何評估 vm 的 Azure 監視器，在單一的 Azure 虛擬機器或虛擬機器擴展集上。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ec909bcd16f923bbd7036f6a69df2bbb07e561b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122461"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>啟用 Azure 監視器評估的 Vm （預覽）

您可以評估 vm （預覽） 的 Azure 監視器的一小部分的 Azure 虛擬機器 (Vm)，或在單一 VM 或虛擬機器擴展集。 若要啟用監視的最簡單且最直接的方式是從 Azure 入口網站。 您的目標是要監視您的 Vm，並找出任何效能或可用性問題。 

您開始之前，請先檢閱[必要條件](vminsights-enable-overview.md)請確定您的訂用帳戶和資源需求。  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>若要啟用單一 Azure vm 監視
若要啟用 Azure VM 的監視：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [虛擬機器]  。

1. 從清單中選取 VM。

1. 在 VM 頁面的 [監視]  區段中，選取 [Insights (預覽)]  。

1. 在 [Insights (預覽)]  頁面上，選取 [立即試用]  。

    ![為 VM 啟用適用於 VM 的 Azure 監視器](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. 在 [將 Azure 監視器 Insights 上線]  頁面上，如果您目前在同一個訂用帳戶中有 Log Analytics 工作區，請在下拉式清單中選取它。  

    清單 preselects 的預設工作區和訂用帳戶中部署 VM 的所在位置。 

    >[!NOTE]
    >若要建立新的 Log Analytics 工作區來儲存 VM 的監視資料，請參閱[建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。 您的 Log Analytics 工作區必須屬於其中一個[支援的區域](vminsights-enable-overview.md#log-analytics)。

啟用監視之後，您可能需要等候大約 10 分鐘才可以檢視 vm 的健康情況計量。

![啟用適用於 VM 的 Azure 監視器來監視部署處理](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>若要啟用監視單一虛擬機器擴展集

若要啟用監視您的 Azure 虛擬機器擴展集：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 **虛擬機器擴展集**。

3. 從清單中，選取 虛擬機器擴展集。

4. 在 [虛擬機器擴展集] 頁面上，在**監視**區段中，選取**Insights （預覽）** 。

5. 在  **Insights （預覽）** 頁面上，如果您想要使用現有的 Log Analytics 工作區，在下拉式清單中選取它。

    清單 preselects 的預設工作區和 VM 部署至訂用帳戶中的位置。 

    ![啟用虛擬機器擴展集 Vm 的 Azure 監視器](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >若要建立新的 Log Analytics 工作區來儲存虛擬機器擴展集的監視資料，請參閱[建立 Log Analytics 工作區](../learn/quick-create-workspace.md)。 您的 Log Analytics 工作區必須屬於其中一個[支援的區域](vminsights-enable-overview.md#log-analytics)。

啟用監視之後，您可能需要等候大約 10 分鐘才能檢視擴展集的監視資料。

>[!NOTE]
>如果您手動升級模型用於您的擴展集時，升級的執行個體，以完成設定。 您可以開始從升級**執行個體**頁面上，於**設定**一節。

![啟用適用於 VM 的 Azure 監視器來監視部署處理](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

既然您已啟用監視 VM 或虛擬機器擴展集，就有一個監視的資訊可供適用於 Vm 的 Azure 監視器中的分析。 

## <a name="next-steps"></a>後續步驟

* 若要了解如何使用健全狀況的功能，請參閱[了解您的 Azure 監視 Vm 的健全狀況](vminsights-health.md)。 
* 若要檢視探索到的應用程式相依性，請參閱[使用 Azure 監視器 Vm 對應](vminsights-maps.md)。 
* 若要找出瓶頸、 整體使用率，以及您的 VM 效能，請參閱[檢視 Azure 虛擬機器效能](vminsights-performance.md)。