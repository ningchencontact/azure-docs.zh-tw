---
title: 將 Azure Functions 與 Azure 虛擬網路整合
description: 逐步示範如何將函式連線到 Azure 虛擬網路的教學課程
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: f8ddcbcb25dc45ee71304ffa1bc5c0c4d3751b61
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523727"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>整合 Azure 虛擬網路中的函式應用程式

本逐步教學課程將示範如何使用 Azure Functions 來連線到 Azure VNET 中的資源。

本教學課程中，我們會部署在私人非網際網路可存取的 VNET 中 VM 上的 WordPress 網站。 我們接著將部署可存取網際網路與 VNET 的函式。 我們將使用該函式，從部署在 VNET 內的 WordPress 網站存取資源。

如需系統運作方式、疑難排解及進階設定的詳細資訊，請參閱[將您的應用程式與 Azure 虛擬網路整合](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)一文。 「 進階 」 方案中的 azure 函式會有與 web 應用程式，相同的裝載功能，因此所有的功能和限制，在該文件中的適用於函式以及。

## <a name="topology"></a>拓撲

 ![VNet 整合 UI][1]

## <a name="creating-a-vm-inside-of-a-vnet"></a>在 VNET 內建立 VM

首先，我們將在 VNET 內建立執行 WordPress的預先設定 VM。 

已選擇 VM 上的 WordPress，因為它是可在 VNET 內部署的資源中成本最低的資源之一。 請注意，這種情況下也可以使用 VNET，例如 REST Api、 App Service 環境和其他 Azure 服務中的任何資源。

1. 移至 Azure 入口網站
2. 開啟 [建立資源] 刀鋒視窗以新增資源
3. 搜尋"[CentOS 上的 WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)"，然後開啟其建立刀鋒視窗 
4. 在建立刀鋒視窗中，使用下列資訊來設定 VM：
    1. 針對此 VM 建立新的資源群組，以便在本教學課程結束時更容易清除資源。 我將資源群組命名 “Function-VNET-Tutorial”
    1. 為虛擬機器提供唯一名稱。 我將名稱命名為 “VNET-Wordpress”
    1. 選取最靠近您的區域
    1. 選取 B1s 的大小 (1 個 vcpu，1 GB 記憶體)
    1. 針對系統管理員帳戶，選擇密碼驗證，然後輸入唯一的使用者名稱和密碼。 針對本教學課程，除非您需要進行疑難排解，否則將不需登入 VM。
    
        ![建立 VM 的基本概念 索引標籤](./media/functions-create-vnet/create-vm-1.png)

1. 移至 [網路] 索引標籤，然後輸入下列資訊：
    1.  建立新的虛擬網路
    1.  輸入您想要的私人位址範圍以及該位址範圍內的子網路。 子網路大小將決定您可以在 App Service 方案中使用的 VM 數量。 如果您不熟悉 IP 位址和子網路，此處提供一份[可說明基本概念的文件](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)。 IP 位址和子網路在此案例中很重要，因此，我建議閱讀數篇文章，並線上觀看一些影片，直到您了解它為止。 
        1. 針對此範例，我選擇使用 10.10.0.0/16 網路與 10.10.1.0/24 的子網路。 我選擇過度佈建並使用 /16 子網路，因為很容易計算 10.10.0.0/16 網路中有哪些子網路可供使用。
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. 返回 [網路] 索引標籤，將 [公用 IP] 設定為 [無]。 這將部署只能存取 VNET 的 VM。
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. 建立 VM。 這大約需要 5 分鐘。
8. 一旦建立 VM 之後，請瀏覽它的 [網路] 索引標籤，並記下私人 IP 位址以供稍後使用。 VM 不應有公用 IP。

    ![14]

您現在已將 WordPress 網站完全部署於您的虛擬網路內。 這個網站無法從公用網際網路存取。

## <a name="create-a-premium-plan-function-app"></a>建立進階方案函式應用程式

下一個步驟是建立進階方案中的函式應用程式。 「 進階 」 方案是全新的產品，提供無伺服器的規模的所有專用的 App Service 方案的優點。 耗用量計劃函式應用程式不支援 VNet 整合。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-vnet"></a>將函數應用程式連線到 VNET

與 WordPress 網站，用來裝載您的 VNET 中的檔案，您現在可以函式應用程式連線至 VNET。

1.  在入口網站中，針對上一個步驟中的函數應用程式選取 [平台功能]，然後選取 [網路]

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  在 [VNet 整合] 下方選取 [按一下以進行設定]

    ![設定網路功能狀態](./media/functions-create-vnet/Networking-1.png)

1. 在 [VNET 整合] 頁面上，選取 [新增 VNet (預覽)]

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  建立要針對您的函式和 App Service 方案使用的新子網路。 請注意，子網路大小將會限制您可新增到 App Service 方案的 VM 總數。 您的 VNET 會在您 VNET 中的子網路之間自動路由傳送流量，因此，不論您的函式是否位於不同的子網路，它都會執行此動作。 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-vnet"></a>建立函式來存取 VNET 中的資源

函數應用程式現在可以搭配我們的 WordPress 網站存取 VNET，因此，我們將使用此函式來存取該檔案，並將其再次提供給使用者。 針對此範例，我們將使用 WordPress 網站作為 API，並使用函式 Proxy 作為呼叫函式，因為它們都很容易設定並進行視覺化。 您就能輕鬆地使用 VNET 內部署的任何其他 API，以及含有可對您 VNET 內所部署的 API 進行 API 呼叫之程式碼的其他函式。 您 VNET 內所部署的 SQL server 即為最佳範例。

1. 在入口網站中，開啟上一個步驟中的函數應用程式
1. 選取 [Proxy] > [+] 來建立 Proxy

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. 設定 Proxy 名稱和路由。 我選擇了 /plant 作為我的路由。
1. 填寫稍早取得的 WordPress 網站 IP，然後將 [後端 URL] 設定為 `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

現在，如果您藉由將後端 URL 貼入新的瀏覽器索引標籤來試著瀏覽它，則該網頁應該會逾時。這是可預期的，因為您的 WordPress 網站只能連線到您的 VNET，而不能連線到網際網路。 如果您將 Proxy URL 貼入瀏覽器，應該會看到美麗的植物圖片，此圖片已從您 VNET 內的 WordPress 網站中提取。 

您的函數應用程式會同時連線到網際網路和您的 VNET。 Proxy 正透過公用網際網路接收要求，然後會做為簡單的 HTTP Proxy，將該要求向前轉送到虛擬網路。 Proxy 接著會透過公用網際網路，將回應轉送回來給您。 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>後續步驟

進階方案中執行的函式會共用相同的 「 基礎 」 App Service 基礎結構，當作 PV2 上的 Web 應用程式方案。 這表示所有 Web 應用程式的文件適用於您的進階方案函式。

1. [深入了解網路功能選項，在此處的函式](./functions-networking-options.md)
1. [讀取網路常見問題集，此函式](./functions-networking-faq.md)
1. [深入了解 Azure 中的 VNET](../virtual-network/virtual-networks-overview.md)
1. [啟用更多的網路功能和使用 App Service 環境的控制](../app-service/environment/intro.md)
1. [連線到個別的內部部署資源，而不需使用混合式連線進行防火牆變更](../app-service/app-service-hybrid-connections.md)
1. [深入了解函式 Proxy](./functions-proxies.md)

<!--Image references-->
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
