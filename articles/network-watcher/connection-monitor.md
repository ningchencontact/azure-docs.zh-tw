---
title: "利用 Azure 網路監看員來監視網路連線 - Azure 入口網站 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站來監視與 Azure 網路監看員的網路連線。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: beaa458d727a05eccf496933deb3c998828868cd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>使用 Azure 入口網站來監視與 Azure 網路監看員的網路連線

了解如何使用連線監視器來監視 Azure 虛擬機器和 IP 位址之間的網路連線。 IP 位址可以指派給另一項 Azure 資源，或是網際網路或內部部署資源。

## <a name="prerequisites"></a>先決條件

完成本文中的步驟之前，您必須符合下列必要條件：

* 您需要監視連線之區域中的網路監看員執行個體。 如果您還沒有網路監看員，可完成[建立 Azure 網路監看員執行個體](network-watcher-create.md)中的步驟來建立一個。
* 要在其中進行監視的虛擬機器。
* 在您需要監視連線的虛擬機器中安裝 `AzureNetworkWatcherExtension`。 若要在 Windows 虛擬機器中安裝擴充功能，請參閱[適用於 Windows 的 Azure 網路監看員代理程式虛擬機器擴充功能](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)，若要在 Linux 虛擬機器中安裝擴充功能，則請參閱[適用於 Linux 的 Azure 網路監看員代理程式虛擬機器擴充功能](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。

## <a name="sign-in-to-azure"></a>登入 Azure 

登入 [Azure 入口網站](http://portal.azure.com)。

## <a name="create-a-connection-monitor"></a>建立連線監視

1. 在入口網站的左側，選取 [更多服務]。
2. 開始輸入網路監看員。 當搜尋結果中出現**網路監看員**時，請加以選取。
3. 在 [監視] 下，選取 [連線監視 (預覽)]。 預覽版本中的功能並沒有與一般版本中的功能相同層級的可靠性和區域可用性。
4. 選取 [+ 新增] 。
5. 輸入或選取您需要監視之連線的適當資訊，然後選取 [新增]。 在此範例中，myVmSource 和 myVmDestination 虛擬機器之間的連線會透過連接埠 80 進行監視。
    
    |  設定                                 |  值               |
    |  -------------------------------------   |  ------------------- |
    |  Name                                    |  myConnectionMonitor |
    |  來源虛擬機器                  |  myVmSource          |
    |  來源連接埠                             |                      |
    |  目的地、選取虛擬機器   |  myVmDestination     |
    |  目的地連接埠                        |  80                  |

6. 開始監視。 連線監視每隔 60 秒會進行探查。
7. 連線監視會顯示平均來回時間和失敗的探查百分比。 您可以在格線或圖表中檢視監視資料。

## <a name="next-steps"></a>後續步驟

- [建立由警示觸發的封包擷取](network-watcher-alert-triggered-packet-capture.md)來了解如何透過虛擬機器警示將封包擷取自動化。

- 使用 [IP 流量驗證](network-watcher-check-ip-flow-verify-portal.md)來判斷虛擬機器是否允許特定流量流入或流出。