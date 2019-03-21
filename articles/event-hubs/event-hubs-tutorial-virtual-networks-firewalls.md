---
title: 教學課程 - 在事件中樞上啟用虛擬網路整合和防火牆 | Microsoft Docs
description: 在本教學課程中，您會了解如何整合事件中樞與虛擬網路和防火牆，以便啟用安全存取。
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 11/28/2018
ms.topic: tutorial
ms.service: event-hubs-messaging
ms.custom: mvc
ms.openlocfilehash: 9eea40a8ad2f08099b2662a0e7539c326d4d143e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779040"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>教學課程：在事件中樞命名空間中啟用虛擬網路整合和防火牆

[虛擬網路 (VNet) 服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)可透過直接連線，將您的虛擬網路私人位址空間和 VNet 的身分識別延伸至 Azure 服務。 端點可讓您將重要的 Azure 服務資源只放到您的虛擬網路保護。 從您的 VNet 到 Azure 服務的流量一定會保留在 Microsoft Azure 骨幹網路上。

防火牆可讓您限制特定 IP 位址 (或 IP 位址範圍) 對事件中樞命名空間的存取

本教學課程會說明如何使用入口網站，透過現有的 Azure 事件中樞命名空間來整合虛擬網路服務端點並設定防火牆 (IP 篩選)。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 如何整合虛擬網路服務端點與事件中樞命名空間。
> * 如何使用事件中樞命名空間來設定防火牆 (IP 篩選)。

>[!WARNING]
> 實作「虛擬網路」整合可防止其他 Azure 服務與「事件中樞」進行互動。
>
> 在已啟用虛擬網路的情況下，不支援第一方整合。
> 無法與虛擬網路搭配運作的常見 Azure 案例有：
> * Azure 診斷和記錄
> * Azure 串流分析
> * 事件方格整合
> * 虛擬網路上必須有 Web 應用程式和 Azure Functions。
> * IoT 中樞路由
> * IoT Device Explorer


> [!IMPORTANT]
> 事件中樞的**標準**和**專用**層級支援虛擬網路。 基本層中不支援虛擬網路。

如果您沒有 Azure 訂用帳戶，請在開始前建立免費帳戶。

## <a name="prerequisites"></a>必要條件

我們會利用現有的事件中樞命名空間，因此請確定您有可用的事件中樞命名空間。 如果您手上沒有，請參閱[此教學課程](./event-hubs-create.md)

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

首先，移至 [Azure 入口網站][Azure portal]，並使用您的 Azure 訂用帳戶登入。

## <a name="select-event-hubs-namespace"></a>選取事件中樞命名空間

為了進行本教學課程，我們建立了事件中樞命名空間，且將會瀏覽至該命名空間。

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>瀏覽至防火牆和虛擬網路的體驗

使用入口網站左窗格上的導覽功能表，挑選 [防火牆和虛擬網路] 選項。

  ![瀏覽至功能表](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  第一次造訪此頁面時，必須選取 [所有網路] 選項按鈕。 也就是說，事件中樞命名空間允許所有連入連線。

## <a name="add-virtual-network-service-endpoint"></a>新增虛擬網路服務端點

若要限制存取，您必須為此事件中樞命名空間整合虛擬網路服務端點。

1. 按一下頁面頂端的 [選取的網路] 選項按鈕，以啟用有功能表選項的頁面其餘部分。
  ![選取的網路](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. 在頁面的 [虛擬網路] 區段中，選取可 [+新增現有虛擬網路] 的選項。 此時會有窗格滑入，讓您可以選取已建立的虛擬網路。
  ![新增現有虛擬網路](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. 從清單中選取虛擬網路，並挑選子網路。
   ![選取子網路](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. 您必須先啟用服務端點，才能將虛擬網路新增至清單中。 如果未啟用服務端點，入口網站會提示您啟用。
  ![選取子網路並啟用端點](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > 如果您無法啟用服務端點，則可以忽略使用 ARM 範本的遺漏虛擬網路服務端點。 在入口網站上無法使用這項功能。

5. 在所選子網路上啟用服務端點之後，您可以繼續操作，將它新增至允許的虛擬網路清單中。
  ![在啟用端點之後新增子網路](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. 請繼續操作，在頂端的功能區上按下 [儲存] 按鈕，以在服務上儲存虛擬網路組態。 請等候幾分鐘的時間，讓入口網站通知上顯示確認訊息。

## <a name="add-firewall-for-specified-ip"></a>為指定 IP 新增防火牆

我們可以使用防火牆規則，以限制只有一定範圍內的 IP 位址或特定的 IP 位址才能存取事件中樞命名空間。

1. 按一下頁面頂端的 [選取的網路] 選項按鈕，以啟用有功能表選項的頁面其餘部分。
  ![選取的網路](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. 在 [防火牆] 區段的 [位址範圍] 方格下方，您可以新增一或多個特定的 IP 位址或 IP 位址範圍。
  ![新增 IP 位址](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. 新增了多個 IP 位址 (或 IP 位址範圍) 後，請在頂端的功能區上按下 [儲存]，以確實將組態儲存在服務端上。 請等候幾分鐘的時間，讓入口網站通知上顯示確認訊息。
  ![新增 IP 位址並按下儲存](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>將目前的 IP 位址新增至防火牆規則

1. 您也可以核取 [位址範圍] 方格正上方的 [新增用戶端 IP 位址 (您目前的 IP 位址)] 核取方塊，來快速新增目前的 IP 位址。
  ![新增目前的 IP 位址](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. 將目前的 IP 位址新增至防火牆規則後，請在頂端的功能區上按下 [儲存]，以確實將組態儲存在服務端上。 請等候幾分鐘的時間，讓入口網站通知上顯示確認訊息。
  ![新增目前的 IP 位址並按下儲存](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>結論

在本教學課程中，您已整合虛擬網路端點和防火牆規則與現有的事件中樞命名空間。 您已了解如何：
> [!div class="checklist"]
> * 如何整合虛擬網路服務端點與事件中樞命名空間。
> * 如何使用事件中樞命名空間來設定防火牆 (IP 篩選)。


[Azure portal]: https://portal.azure.com/
