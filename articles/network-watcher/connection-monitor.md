---
title: 利用 Azure 網路監看員來監視網路連線 - Azure 入口網站 | Microsoft Docs
description: 了解如何使用 Azure 入口網站來監視與 Azure 網路監看員的網路連線。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: b0eb10d373f47191933eb1c3c22b779e9e8d6685
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>使用 Azure 入口網站來監視與 Azure 網路監看員的網路連線

了解如何使用連線監視器來監視 Azure 虛擬機器 (VM) 和 IP 位址之間的網路連線。 連線監視器提供來源和目的地 IP 位址與連接埠之間的監視。 連線監視器適用的案例如下：監視虛擬網路中的 VM 與在相同或不同虛擬網路 (經由連接埠 1433) 中執行 SQL 伺服器之 VM 的連線能力。 連線監視器提供連線延遲當作 Azure 監視器計量 (每 60 秒記錄一次)。 它也提供逐一躍點的拓撲圖，並識別會影響您連線的設定問題。

## <a name="prerequisites"></a>先決條件

完成本文中的步驟之前，您必須符合下列必要條件：

* 您需要監視連線之區域中的網路監看員執行個體。 如果您還沒有網路監看員，可完成[建立 Azure 網路監看員執行個體](network-watcher-create.md)中的步驟來建立一個。
* 要從中監視的 VM。 若要了解如何建立 VM，請參閱「建立 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) VM」。
* 在您需要從中監視連線的 VM 中安裝 `AzureNetworkWatcherExtension`。 若要在 Windows VM 中安裝擴充功能，請參閱[適用於 Windows 的 Azure 網路監看員代理程式虛擬機器擴充功能](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)；若要在 Linux VM 中安裝擴充功能，請參閱[適用於 Linux 的 Azure 網路監看員代理程式虛擬機器擴充功能](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。 您要監視的目的地端點上不需要擴充功能。

## <a name="sign-in-to-azure"></a>登入 Azure 

登入 [Azure 入口網站](http://portal.azure.com)。

## <a name="create-a-connection-monitor"></a>建立連線監視

以下步驟會啟用經由連接埠 80 和 1433 對目的地 VM 的連線監視：

1. 在入口網站的左側，選取 [所有服務]。
2. 開始在 [篩選] 方塊中輸入「網路監看員」。 當搜尋結果中出現**網路監看員**時，請加以選取。
3. 在 [監視] 下，選取 [連線監視器]。
4. 選取 [+ 新增] 。
5. 輸入或選取您要監視之連線的資訊，然後選取 [新增]。 在下圖所顯示的範例中，受監視的是透過連接埠 80 從 *MultiTierApp0* 到 *Database0* VM 的連線：

    ![新增連線監視器](./media/connection-monitor/add-connection-monitor.png)

    開始監視。 連線監視每隔 60 秒會進行探查。
6. 指定相同的來源和目的地 VM 以及下列值，再次完成步驟 5：
    
    |設定  |值          |
    |---------|---------      |
    |Name     | AppToDB(1433) |
    |Port     | 1433          |

## <a name="view-connection-monitoring"></a>檢視連線監視

1. 完成[建立連線監視器](#create-a-connection-monitor)中的步驟 1-3，以檢視連線監視。
2. 下圖顯示 *AppToDB(80)* 連線的詳細資料。 [狀態] 為可連線。 [圖形檢視] 顯示 [平均來回時間] 和 [失敗探查百分比]。 圖形提供逐一躍點資訊，且顯示沒有問題會影響目的地連線能力。

    ![圖形檢視](./media/connection-monitor/view-graph.png)

3. 檢視 *AppToDB(1433)* 連線 (如下圖所示)，您就能看到對於相同的來源和目的地 VM，經由連接埠 1433 的狀態都是無法連線的。 此案例中的 [格線檢視] 提供逐一躍點資訊和影響連線能力的問題。 在此情況下，NSG 規則封鎖在第二躍點的連接埠 1433 上的所有流量。

    ![格線檢視](./media/connection-monitor/view-grid.png)

## <a name="next-steps"></a>後續步驟

- 透過[建立由警示觸發的封包擷取](network-watcher-alert-triggered-packet-capture.md)，來了解如何透過 VM 警示將封包擷取自動化。
- 使用 [IP 流量驗證](network-watcher-check-ip-flow-verify-portal.md)來判斷 VM 是否允許特定流量流入或流出。