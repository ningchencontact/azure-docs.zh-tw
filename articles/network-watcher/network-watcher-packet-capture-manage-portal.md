---
title: 使用 Azure 網路監看員管理封包擷取 - Azure 入口網站 | Microsoft Docs
description: 了解如何使用 Azure 入口網站管理網路監看員的封包擷取功能。
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: kumud
ms.openlocfilehash: 50092db9e2e3670168cbb3440b8cb99eb0c2ac20
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714723"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>使用入口網站以 Azure 網路監看員管理封包擷取

網路監看員封包擷取可讓您建立擷取工作階段來追蹤虛擬機器的流入和流出流量。 系統會為擷取工作階段提供篩選器，以確保您只會擷取到您想要的流量。 封包擷取有助於以被動和主動方式診斷網路異常。 其他用途包括收集網路統計資料、取得有關網路入侵的資訊，以及偵錯用戶端與伺服器間的通訊等等。 由於能夠從遠端觸發封包擷取，因此可以減輕在所需的虛擬機器上手動執行封包擷取的工作負擔，進而省下寶貴的時間。

在本文中，您將了解如何啟動、停止、下載及刪除封包擷取。 

## <a name="before-you-begin"></a>開始之前

封包擷取需要下列連線：
* 透過連接埠 443 的儲存體帳戶輸出連線。
* 對 169.254.169.254 的輸入和輸出連線
* 對 168.63.129.16 的輸入和輸出連線

如果網路安全性群組與網路介面或網路介面所在的子網路相關聯，請確定有允許前述連接埠的規則存在。 

## <a name="start-a-packet-capture"></a>啟動封包擷取

1. 在瀏覽器中瀏覽至 [Azure 入口網站](https://portal.azure.com)，並選取 [所有服務]，然後選取 [網路] 區段中的 [網路監看員]。
2. 選取 [網路診斷工具] 下方的 [封包擷取]。 任何現有的封包擷取都會列出，無論其狀態為何。
3. 選取 [新增] 以建立封包擷取。 您可以選取下列屬性的值：
   - 訂用帳戶：你要为其创建数据包捕获的虚拟机所在的订阅。
   - **资源组**：虚拟机的资源组。
   - **目標虛擬機器**：你要为其创建数据包捕获的虚拟机。
   - **数据包捕获名称**：数据包捕获的名称。
   - **存储帐户或文件**：选择“存储帐户”、“文件”或同时选择两者。 如果您選取 [檔案]，擷取將會寫入至虛擬機器內的路徑。
   - **本地文件路径**：虚拟机上将用来保存数据包捕获的本地路径（仅当选择了“文件”时有效）。 路徑必須是有效路徑。 如果您使用 Linux 虛擬機器，路徑必須以 */var/captures* 開頭。
   - **儲存體帳戶**：如果选择了“存储帐户”，请选择一个现有的存储帐户。 只有已選取 [儲存體] 時才可使用此選項。
   
     > [!NOTE]
     > 儲存封包擷取目前不支援進階儲存體帳戶。

   - **每个数据包的最大字节数**：捕获的每个数据包中的字节数。 如果保留為空白，則會擷取所有位元組。
   - **每个会话的最大字节数**：捕获的字节总数。 一旦達到此值，封包擷取就會停止。
   - **时间限制(秒)**：在停止数据包捕获之前的时间限制。 預設值為 18,000 秒。
   - 篩選 (選擇性)。 選取 [+ 新增篩選]
     - **通訊協定**：用來篩選封包擷取的通訊協定。 可用的值為 TCP、UDP 和 Any。
     - **本地 IP 地址**：在数据包捕获中筛选其中的本地 IP 地址与此值匹配的数据包。
     - **本地端口**：在数据包捕获中筛选其中的本地端口与此值匹配的数据包。
     - **远程 IP 地址**：将数据包捕获筛选为远程 IP 地址与此值匹配的数据包。
     - **远程端口**：在数据包捕获中筛选其中的远程端口与此值匹配的数据包。
    
     > [!NOTE]
     > 連接埠和 IP 位址的值可以是單一值、值的範圍，或特定範圍 (如 80-1024) 的連接埠。 您可以視需要定義不限數量的篩選。

4. 選取 [確定] 。

對封包擷取設定的時間限制到期之後，封包擷取即會停止，且可供檢閱。 您也可以手動停止封包擷取工作階段。

> [!NOTE]
> 入口網站會自動：
>  * 在您選取的虛擬機器所在的相同區域中建立網路監看員 (如果該區域還沒有網路監看員)。
>  * 將 *AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) 或 [Windows](../virtual-machines/windows/extensions-nwa.md) 虛擬機器擴充功能新增至虛擬機器 (如果尚未安裝)。

## <a name="delete-a-packet-capture"></a>刪除封包擷取

1. 在封包擷取檢視中，選取位於封包擷取右側的 [...]，或以滑鼠右鍵按一下現有的封包擷取，然後選取 [刪除]。
2. 系統會要求您確認您要刪除封包擷取。 選取 [是]。

> [!NOTE]
> 刪除封包擷取時，並不會刪除儲存體帳戶中或虛擬機器上的擷取檔案。

## <a name="stop-a-packet-capture"></a>停止封包擷取

在封包擷取檢視中，選取位於封包擷取右側的 [...]，或以滑鼠右鍵按一下現有的封包擷取，然後選取 [停止]。

## <a name="download-a-packet-capture"></a>下載封包擷取

封包擷取工作階段完成後，擷取檔案會上傳到 Blob 儲存體或虛擬機器上的本機檔案。 封包擷取的儲存位置會在建立封包擷取期間定義。 若要存取儲存至儲存體帳戶的擷取檔案，Microsoft Azure 儲存體總管是很便利的工具，您可以[下載](https://storageexplorer.com/)取得。

如果指定了儲存體帳戶，封包擷取檔案便會儲存到儲存體帳戶的下列位置︰

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

如果您在建立擷取時選取了 [檔案]，您可以從您在虛擬機器上設定的路徑檢視或下載檔案。

## <a name="next-steps"></a>後續步驟

- 若要了解如何透過虛擬機器警示將封包擷取自動化，請檢視[建立由警示觸發的封包擷取](network-watcher-alert-triggered-packet-capture.md)。
- 若要判斷是否允許進出於虛擬機器的特定流量，請參閱[診斷虛擬機器網路流量篩選問題](diagnose-vm-network-traffic-filtering-problem.md)。
