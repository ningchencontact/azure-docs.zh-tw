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
ms.openlocfilehash: c7d98350dc8f66ebd4097f22b44dcbbe2653b25d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>使用 Azure 入口網站來監視與 Azure 網路監看員的網路連線

了解如何使用連線監視器來監視 Azure 虛擬機器和 IP 位址之間的網路連線。 連線監視器提供連線層級的監視。 連線定義為來源和目的地 IP 位址及連接埠的組合。 連線監視器適用的案例如：監視在虛擬網路中的虛擬機器對在相同或不同虛擬網路 (經由連接埠 1433) 中執行 SQL 伺服器的虛擬機器的連線能力。 連線監視器提供連線延遲當作 Azure 監視器計量 (每 60 秒記錄一次)。 它也提供逐一躍點的拓撲圖，並識別會影響您連線的設定問題。


## <a name="prerequisites"></a>先決條件

完成本文中的步驟之前，您必須符合下列必要條件：

* 您需要監視連線之區域中的網路監看員執行個體。 如果您還沒有網路監看員，可完成[建立 Azure 網路監看員執行個體](network-watcher-create.md)中的步驟來建立一個。
* 要在其中進行監視的虛擬機器。
* 在您需要監視連線的虛擬機器中安裝 `AzureNetworkWatcherExtension`。 若要在 Windows 虛擬機器中安裝擴充功能，請參閱[適用於 Windows 的 Azure 網路監看員代理程式虛擬機器擴充功能](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)，若要在 Linux 虛擬機器中安裝擴充功能，則請參閱[適用於 Linux 的 Azure 網路監看員代理程式虛擬機器擴充功能](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。

## <a name="sign-in-to-azure"></a>登入 Azure 

登入 [Azure 入口網站](http://portal.azure.com)。

## <a name="create-a-connection-monitor"></a>建立連線監視

以下步驟啟用經由連接埠 80 和 1433 對目的地虛擬機器的連線監視：

1. 在入口網站的左側，選取 [更多服務]。
2. 開始輸入網路監看員。 當搜尋結果中出現**網路監看員**時，請加以選取。
3. 在 [監視] 下，選取 [連線監視 (預覽)]。 預覽版本中的功能並沒有與一般版本中的功能相同層級的可靠性和區域可用性。
4. 選取 [+ 新增] 。
5. 輸入或選取您要監視之連線的資訊，然後選取 [新增]。 在以下圖片內顯示的範例中，受監視的是 *MultiTierApp0* 和 *Database0* 虛擬機器之間的連線：

    ![新增連線監視器](./media/connection-monitor/add-connection-monitor.png)

    開始監視。 連線監視每隔 60 秒會進行探查。

## <a name="view-connection-monitoring"></a>檢視連線監視

1. 完成[建立連線監視器](#create-a-connection-monitor)中的步驟 1-3，以檢視連線監視。
2. 以下圖片顯示 AppToDB(80) 連線的詳細資料。 [狀態] 為可連線。 [圖形檢視] 顯示 [平均來回時間] 和 [失敗探查百分比]。 圖形提供逐一躍點資訊，且顯示沒有問題會影響目的地連線能力。

    ![檢視連線監視器](./media/connection-monitor/view-connection-monitor.png)

3. 檢視 *AppToDB(1433)* 監視器，如以下圖片所示，您可以看到對於相同的來源和目的地虛擬機器，經由連接埠 1433 的狀態都是無法連線。 此案例中的 [格線檢視] 提供逐一躍點資訊和影響連線能力的問題。 在此情況下，NSG 規則封鎖在第二躍點的連接埠 1433 上的所有流量。

    ![檢視連線監視器](./media/connection-monitor/view-connection-monitor-2.png)

## <a name="next-steps"></a>後續步驟

- [建立由警示觸發的封包擷取](network-watcher-alert-triggered-packet-capture.md)來了解如何透過虛擬機器警示將封包擷取自動化。
- 使用 [IP 流量驗證](network-watcher-check-ip-flow-verify-portal.md)來判斷虛擬機器是否允許特定流量流入或流出。