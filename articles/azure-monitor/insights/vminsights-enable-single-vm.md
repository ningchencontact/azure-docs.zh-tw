---
title: 啟用 Azure 監視器評估的 Vm （預覽） |Microsoft Docs
description: 本文說明如何啟用 Azure 監視器適用於 Vm 的單一 Azure 虛擬機器或虛擬機器擴展集進行評估。
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
ms.openlocfilehash: 673f153b551e4b0c89a564c96d6bd9819ca26f5d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524081"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>啟用 Azure 監視器評估的 Vm （預覽）

若要評估 Azure 監視器的 Azure 虛擬機器或單一虛擬機器或虛擬機器擴展集的一小部分的 vm （預覽），若要啟用監視的最簡單且最直接的方法是從 Azure 入口網站。 在此程序結束時，您將已成功開始監視它們，並了解是否它們發生效能或可用性問題。 

取得啟動，請務必檢閱之前[必要條件](vminsights-enable-overview.md)並確認您的訂用帳戶和資源符合需求。  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>若要啟用單一 Azure vm 監視
若要在 Azure 入口網站中啟用 Azure VM 的監視，請執行下列動作：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [虛擬機器]。

1. 從清單中選取 VM。

1. 在 VM 頁面的 [監視] 區段中，選取 [Insights (預覽)]。

1. 在 [Insights (預覽)] 頁面上，選取 [立即試用]。

    ![為 VM 啟用適用於 VM 的 Azure 監視器](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. 在 [將 Azure 監視器 Insights 上線] 頁面上，如果您目前在同一個訂用帳戶中有 Log Analytics 工作區，請在下拉式清單中選取它。  

    此清單會預先選取已在訂用帳戶中部署虛擬機器的預設工作區和位置。 

    >[!NOTE]
    >如果您想要建立新的 Log Analytics 工作區來儲存 VM 的監視資料，請依照下列中的指示[建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)其中一個支援的區域中所列出[這裡](vminsights-enable-overview.md#log-analytics)。

啟用監視之後，可能需要約 10 分鐘的時間才能檢視虛擬機器的健康情況計量。

![啟用適用於 VM 的 Azure 監視器來監視部署處理](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>若要啟用監視單一虛擬機器擴展集

若要啟用監視您的 Azure 虛擬機器擴展集在 Azure 入口網站中，執行下列作業：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 **虛擬機器擴展集**。

3. 從清單中，選取 虛擬機器擴展集。

4. 在 [虛擬機器擴展集] 頁面上，在**監視**區段中，選取**Insights （預覽）**。

5. 在  **Insights （預覽）** 頁面上，如果您有現有的 Log Analytics 工作區，選擇您想要使用，請選取下拉式清單中。

    此清單會預先選取已在訂用帳戶中部署虛擬機器的預設工作區和位置。 

    ![啟用虛擬機器擴展集 Vm 的 Azure 監視器](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >如果您想要建立新的 Log Analytics 工作區來儲存 VM 的監視資料，請依照下列中的指示[建立 Log Analytics 工作區](../learn/quick-create-workspace.md)其中一個支援的區域中所列出[這裡](vminsights-enable-overview.md#log-analytics)。

您已啟用監視之後，可能需要約 10 分鐘才能檢視擴展集的監視資料。

>[!NOTE]
>如果您手動升級模型用於您的擴展集，您必須升級以完成安裝程序的執行個體。  這可以從 [執行個體] 頁面下**設定**一節。

![啟用適用於 VM 的 Azure 監視器來監視部署處理](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

## <a name="next-steps"></a>後續步驟

現在，為您的虛擬機器或虛擬機器擴展集已啟用監視，這項資訊是使用適用於 Vm 的 Azure 監視器可用於分析。 若要了解如何使用健康情況功能，請參閱[檢視適用於 VM 的 Azure 監視器健康情況](vminsights-health.md)。 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。 若要找出 VM 效能的瓶頸和整體使用率，請參閱[檢視 Azure VM 效能](vminsights-performance.md)，或者，若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。