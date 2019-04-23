---
title: 整合 Azure Functions 與 Azure 虛擬網路
description: 逐步教學課程說明如何連接到 Azure 虛擬網路的函式
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 96ab479d3373eb6e575a00898f7007a4df252e39
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002772"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>整合 Azure 虛擬網路中的函式應用程式

本教學課程會示範如何使用 Azure Functions 來連線到 Azure 的虛擬網路中的資源。

本教學課程中，我們會將部署在不是從網際網路存取虛擬網路中的 VM 上的 WordPress 網站。 然後，我們會將具有存取權的函式部署到網際網路，虛擬網路。 我們將使用該函式，從虛擬網路內部署的 WordPress 網站存取資源。

如需有關系統的運作方式的詳細資訊，疑難排解和進階的組態，請參閱[整合您的應用程式與 Azure 虛擬網路](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)。 「 進階 」 方案中的 azure 函式會有相同的裝載功能，為 web 應用程式，因此所有的功能和該文章中的限制套用至函式。

## <a name="topology"></a>拓撲

 ![虛擬網路整合的 UI][1]

## <a name="create-a-vm-inside-a-virtual-network"></a>建立虛擬網路內的 VM

若要開始，我們將建立預先設定的 VM 虛擬網路內執行 WordPress。 

我們選擇的 VM 上的 WordPress，因為它是其中一個最便宜的資源，可在虛擬網路內部署。 請注意，這種情況下也可以使用虛擬網路，例如 REST Api、 App Service 環境，以及其他 Azure 服務中的任何資源。

1. 移至 Azure 入口網站。
2. 加入新的資源，開啟**建立資源**刀鋒視窗。
3. 搜尋"[CentOS 上的 WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)"，然後開啟其建立刀鋒視窗。 
4. 在 **基本概念**索引標籤上，使用下列資訊來設定 VM:
    1. 針對此 VM 建立新的資源群組，以便在本教學課程結束時更容易清除資源。 在這裡，我們使用 「 函式 VNET-教學課程 」 做為範例。
    1. 為虛擬機器提供唯一名稱。 我們使用"VNET Wordpress"做為範例。
    1. 選取離您最近的區域。
    1. B1s 為選取的大小 （1 個 vCPU，1 GB 記憶體）。
    1. 針對系統管理員帳戶，選擇密碼驗證，然後輸入唯一的使用者名稱和密碼。 本教學課程中，您不需要登入 VM 除非您需要進行疑難排解。
    
        ![基本 索引標籤，來建立 VM](./media/functions-create-vnet/create-vm-1.png)

1. 移至**網路**索引標籤，然後輸入下列資訊：
    1.  建立新的虛擬網路。
    1.  輸入私人位址範圍和該位址範圍內的子網路。 子網路大小將決定您可以在 App Service 方案中使用的 VM 數量。 如果 IP 位址和子網路 > 是新手，有[涵蓋基本概念的文件](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)。 IP 位址和子網路是非常重要在此案例中，因此我們建議您閱讀幾篇文章，並觀看一些影片線上，直到合理。 
    
        針對此範例中，我們要選擇 10.10.0.0/16 網路使用 10.10.1.0/24 的子網路。 正在過度佈建，並使用 / 16 的子網路因為很容易計算 10.10.0.0/16 網路中可用的子網路。
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. 回到**Networking**索引標籤上，若要設定的公用 IP**無**。 此步驟會將具有存取權將 VM 部署至虛擬網路。
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. 建立 VM。 此程序需要大約 5 分鐘的時間。
8. 建立 VM 之後，請移至其**網路**索引標籤，並記下的私人 IP 位址，以便稍後使用。 VM 不應有公用 IP。

    ![14]

您現在可以完全在您的虛擬網路內部署的 WordPress 網站。 這個網站無法從公用網際網路存取。

## <a name="create-a-function-app-in-a-premium-plan"></a>在 進階方案中建立函數應用程式

下一個步驟是建立進階方案中的函式應用程式。 進階方案提供無伺服器的規模的專用的 App Service 方案的所有優點。 透過 「 取用 」 方案所建立的函式應用程式不支援虛擬網路整合。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-virtual-network"></a>函式應用程式連接到您的虛擬網路

使用 WordPress 網站裝載您的虛擬網路中的檔案中，您現在可以至虛擬網路，以連線函式應用程式。

1.  在入口網站函式應用程式，從上一個步驟中，選取**平台功能**。 然後選取**網路**。

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  選取 **按一下這裡以設定**下方**VNet 整合**。

    ![設定網路功能的狀態](./media/functions-create-vnet/Networking-1.png)

1. 在 [虛擬網路整合] 頁面中，選取**新增 VNet （預覽）**。

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  建立新的子網路，對您的函式使用的 App Service 方案。 請注意，子網路大小會限制您可以將它新增至您的 App Service 方案的 Vm 總數。 您的虛擬網路會自動將路由虛擬網路，因此並不重要，您的函式為不同的子網路，從您的 VM 中的子網路之間的流量。 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-virtual-network"></a>建立存取虛擬網路中的資源的函式

函式應用程式現在可以存取我們的 WordPress 網站的虛擬網路。 因此，我們要使用函數來存取該檔案並將其提供給使用者。 此範例中，我們將使用 WordPress 網站作為 API 和 proxy 呼叫的函式因為它們容易設定，並加以視覺化。 

您可以輕鬆地使用虛擬網路內部署的任何其他 API。 您也可以使用另一個函式，可讓您的虛擬網路內部署 API 的 API 呼叫的程式碼。 您的虛擬網路內部署的 SQL Server 執行個體是最好的例子。

1. 在入口網站中，開啟函式應用程式從上一個步驟。
1. 建立 proxy，方法是選取**Proxy** > **+**。

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. 設定 proxy 名稱和路由。 此範例會使用"/ 計劃 」 作為路由。
1. 填寫稍早的 WordPress 網站的 IP，然後設定**後端 URL**至 `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

現在，如果您嘗試將它貼到新的瀏覽器索引標籤，來直接前往您的後端 URL，頁面應該逾時。這是因為只有您的虛擬網路與而非透過網際網路連接您的 WordPress 網站。 如果您將您的 proxy URL 貼入瀏覽器時，您應該會看到您的虛擬網路內工廠圖片 （取自您的 WordPress 網站）。 

函式應用程式連線到網際網路，您的虛擬網路。 Proxy 正透過公用網際網路接收要求，然後會做為簡單的 HTTP Proxy，將該要求向前轉送到虛擬網路。 Proxy 接著會透過公用網際網路，將回應轉送回來給您。 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>後續步驟

進階方案中執行的函式會共用相同基礎 App Service 的基礎結構即 PremiumV2 計劃的 web 應用程式。 針對 web 應用程式的所有文件適用於您的進階方案函式。

* [深入了解函式中的網路功能選項](./functions-networking-options.md)
* [讀取網路常見問題集函式](./functions-networking-faq.md)
* [深入了解在 Azure 中的虛擬網路](../virtual-network/virtual-networks-overview.md)
* [啟用更多的網路功能和使用 App Service 環境的控制](../app-service/environment/intro.md)
* [使用混合式連線連線到內部個別資源，而不需要防火牆變更](../app-service/app-service-hybrid-connections.md)
* [深入了解 Functions Proxy](./functions-proxies.md)

<!--Image references -->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/create-function-app.png
[3]: ./media/functions-create-vnet/create-app-service-plan.png
[4]: ./media/functions-create-vnet/configure-vnet.png
[5]: ./media/functions-create-vnet/create-vm-1.png
[6]: ./media/functions-create-vnet/create-vm-2.png
[7]: ./media/functions-create-vnet/create-vm-2-1.png
[8]: ./media/functions-create-vnet/networking-1.png
[9]: ./media/functions-create-vnet/networking-2.png
[10]: ./media/functions-create-vnet/networking-3.png
[11]: ./media/functions-create-vnet/new-proxy.png
[12]: ./media/functions-create-vnet/create-proxy.png
[14]: ./media/functions-create-vnet/vm-networking.png
