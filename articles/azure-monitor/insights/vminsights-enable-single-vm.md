---
title: 啟用適用於 VM 的 Azure 監視器（預覽）進行評估 |Microsoft Docs
description: 瞭解如何在單一 Azure 虛擬機器或虛擬機器擴展集上評估適用於 VM 的 Azure 監視器。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 05/09/2019
ms.openlocfilehash: 1182f48d2d05c90cc90b1832f9305001dd2d1211
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553812"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>啟用適用於 VM 的 Azure 監視器（預覽）以進行評估

您可以在少量的 Azure 虛擬機器（Vm）或單一 VM 或虛擬機器擴展集上評估適用於 VM 的 Azure 監視器（預覽）。 啟用監視的最簡單且最直接的方式是從 Azure 入口網站。 您的目標是監視您的 Vm，並找出任何效能或可用性問題。 

開始之前，請先檢查[必要條件](vminsights-enable-overview.md)，並確定您的訂用帳戶和資源符合需求。  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>啟用單一 Azure VM 的監視
若要啟用 Azure VM 的監視：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [虛擬機器]。

1. 從清單中選取 VM。

1. 在 VM 頁面的 [監視] 區段中，選取 [Insights (預覽)]。

1. 在 [Insights (預覽)] 頁面上，選取 [立即試用]。

    ![為 VM 啟用適用於 VM 的 Azure 監視器](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. 在 [將 Azure 監視器 Insights 上線] 頁面上，如果您目前在同一個訂用帳戶中有 Log Analytics 工作區，請在下拉式清單中選取它。  

    清單會預先選取用來在訂用帳戶中部署 VM 的預設工作區和位置。 

    >[!NOTE]
    >若要建立新的 Log Analytics 工作區以儲存來自 VM 的監視資料，請參閱[建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。 您的 Log Analytics 工作區必須屬於其中一個[支援的區域](vminsights-enable-overview.md#log-analytics)。

啟用監視之後，您可能需要等候約10分鐘，才能查看 VM 的健康情況計量。

![啟用適用於 VM 的 Azure 監視器來監視部署處理](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>啟用單一虛擬機器擴展集的監視

若要啟用 Azure 虛擬機器擴展集的監視：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [**虛擬機器擴展集**]。

3. 從清單中選取虛擬機器擴展集。

4. 在 [虛擬機器擴展集] 頁面的 [**監視**] 區段中，選取 [ **Insights （預覽）** ]。

5. 在 [ **Insights （預覽）** ] 頁面上，如果您想要使用現有的 Log Analytics 工作區，請在下拉式清單中選取它。

    此清單會會預先選取訂用帳戶中部署 VM 的預設工作區和位置。 

    ![啟用虛擬機器擴展集的適用於 VM 的 Azure 監視器](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >若要建立新的 Log Analytics 工作區以儲存來自虛擬機器擴展集的監視資料，請參閱[建立 Log analytics 工作區](../learn/quick-create-workspace.md)。 您的 Log Analytics 工作區必須屬於其中一個[支援的區域](vminsights-enable-overview.md#log-analytics)。

啟用監視之後，您可能需要等候約10分鐘，才能看到擴展集的監視資料。

>[!NOTE]
>如果您對擴展集使用手動升級模型，請升級實例以完成安裝。 您可以在 [**設定**] 區段中，從 [**實例**] 頁面開始升級。

![啟用適用於 VM 的 Azure 監視器來監視部署處理](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

既然您已啟用 VM 或虛擬機器擴展集的監視功能，監視資訊就可以在適用於 VM 的 Azure 監視器中進行分析。 

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何使用健康情況功能，請參閱[瞭解 Azure 監視器 vm 的健全狀況](vminsights-health.md)。 
* 若要查看探索到的應用程式相依性，請參閱[使用適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。 
* 若要找出瓶頸、整體使用率和 VM 的效能，請參閱[查看 AZURE VM 效能](vminsights-performance.md)。