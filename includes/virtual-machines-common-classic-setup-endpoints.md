---
title: 包含檔案
description: 包含檔案
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/23/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ee5faedd4f59aa791424a1f178f0462922f21d28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485205"
---
每個端點都有一個公用連接埠和一個私人連接埠：

* Azure 負載平衡器使用公用連接埠接聽從網際網路到虛擬機器的連入流量。
* 虛擬機器使用私人連接埠接聽目的地通常為虛擬機器上執行的應用程式或服務的連入流量。

當您使用 Azure 入口網站建立端點時，會提供 IP 通訊協定的預設值以及已知網路通訊協定的 TCP 或 UDP 通訊埠。 針對自訂端點，請指定正確的 IP 通訊協定 (TCP 或 UDP) 以及公用和私人連接埠。 若要將連入流量隨機分散到多部虛擬機器，請建立包含多個端點的負載平衡集合。

建立端點之後，您可以使用存取控制清單 (ACL) 定義規則，根據來源 IP 位址允許或拒絕端點公用連接埠的連入流量。 不過，如果虛擬機器位於 Azure 虛擬網路中，請改用網路安全性群組。 如需詳細資訊，請參閱 [關於網路安全性群組](../articles/virtual-network/security-overview.md)。

> [!NOTE]
> Azure 虛擬機器的防火牆組態會針對連接埠自動完成，該連接埠與 Azure 自動設定的遠端連線端點相關聯。 至於其他所有端點的指定連接埠，不會自動設定虛擬機器的防火牆。 您建立虛擬機器的端點時，請確定虛擬機器的防火牆也允許端點設定相對應通訊協定和私人連接埠的流量。 若要設定防火牆，請參閱文件或虛擬機器上執行之作業系統的線上說明。
>
>

## <a name="create-an-endpoint"></a>建立端點
1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [虛擬機器]，然後選取您想要設定的虛擬機器。

3. 選取 [設定] 群組中的 [端點]。 [端點] 頁面隨即出現，其中列出虛擬機器目前的所有端點。 (此範例適用於 Windows VM。 )

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)


4. 在端點項目上方的命令列中，選取 [新增]。 [新增端點] 頁面隨即出現。

5. 對於 [名稱]，輸入端點的名稱。

6. 對於 [通訊協定]，選擇 [TCP] 或 [UDP]。

7. 對於 [公用連接埠]，輸入網際網路連入流量的連接埠號碼。 

8. 對於 [私人連接埠]，輸入虛擬機器所接聽的連接埠號碼。 公用和私人連接埠號碼可以不同。 請確定已經設定虛擬機器的防火牆允許通訊協定和私人連接埠對應的流量。

9. 選取 [確定] 。

新的端點會列在 [端點] 頁面上。

![端點建立成功](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>在端點上管理 ACL
為了定義可以傳送流量的電腦集合，端點上的 ACL 能夠根據來源 IP 位址限制流量。 請依照這些步驟，在端點上新增、修改或移除 ACL。

> [!NOTE]
> 如果端點屬於負載平衡集合，則對端點上的 ACL 所做的任何變更，都將套用至此集合的所有端點。
>
>

如果虛擬機器位於 Azure 虛擬網路中，請改用網路安全性群組，而不使用 ACL。 如需詳細資訊，請參閱 [關於網路安全性群組](../articles/virtual-network/security-overview.md)。

1. 登入 Azure 入口網站。

2. 選取 [虛擬機器]，然後選取您想要設定之虛擬機器的名稱。

3. 選取 [端點]。 從 [端點] 清單中，選取適當的端點。 ACL 清單位於頁面底部。

   ![指定 ACL 详细信息](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. 使用清單中的資料列來新增、刪除或編輯 ACL 的規則並變更奇順序。 [遠端子網路] 值是網際網路連入流量的 IP 位址範圍，Azure 負載平衡器會根據流量的來源 IP 位址允許或拒絕流量。 請務必使用「無類別網域間路由選擇」(CIDR) 格式來指定 IP 位址範圍，該格式又稱為「位址首碼」格式。 例如： `10.1.0.0/8`。

   ![新增 ACL 項目](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


您可以使用規則僅允許網際網路上的特定電腦對應至您電腦的流量，或拒絕特定已知位址範圍的流量。

規則的評估順序是從第一個規則開始，一直到最後一個規則為止。 因此規則應會以最寬鬆到最嚴格的順序來排列。 如需詳細資訊，請參閱[什麼是網路存取控制清單](../articles/virtual-network/virtual-networks-acl.md)。
