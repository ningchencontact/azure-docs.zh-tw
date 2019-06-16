---
title: 整合 Azure Functions 與 Azure 虛擬網路
description: 逐步教學課程說明如何連接到 Azure 虛擬網路的函式
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 55cce60ab3d1cda3cb870afd2f6214f917a04189
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063271"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>教學課程： 整合 Azure 虛擬網路中的函式

本教學課程會示範如何使用 Azure Functions 來連線到 Azure 的虛擬網路中的資源。 您將建立具有兩個網際網路與虛擬網路中執行 WordPress 的 VM 存取的函式。

> [!div class="checklist"]
> * 在 「 進階 」 方案中建立函數應用程式
> * 虛擬網路中將 WordPress 網站部署到 VM
> * 將函式應用程式連線至虛擬網路
> * 建立函數 proxy 以存取 WordPress 資源
> * 要求來自虛擬網路內的 WordPress 檔案

> [!NOTE]  
> 本教學課程中建立 「 進階 」 方案中的函式應用程式。 這個裝載的計劃目前為預覽狀態。 如需詳細資訊，請參閱 <<c0> [ 進階方案]。

## <a name="topology"></a>拓撲

下圖顯示您所建立之方案的架構：

 ![虛擬網路整合的 UI](./media/functions-create-vnet/topology.png)

在 「 進階 」 方案中執行的函式會將相同的裝載功能，為 web 應用程式在 Azure App Service，其中包括 VNet 整合功能。 若要深入了解 VNet 整合，包括疑難排解和進階組態，請參閱[整合您的應用程式與 Azure 虛擬網路](../app-service/web-sites-integrate-with-vnet.md)。

## <a name="prerequisites"></a>必要條件

本教學課程中，務必了解 IP 位址和子網路 >。 您可以開始[涵蓋基本位址和子網路的這篇文章](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)。 許多更多的文章與影片都可在線上。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-a-function-app-in-a-premium-plan"></a>在 進階方案中建立函數應用程式

首先，您可以建立函數應用程式中的[ 進階方案]。 此計劃提供無伺服器的規模，同時支援虛擬網路整合。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

您可以選取右上角的釘選圖示，釘選到儀表板函式應用程式。 釘選可讓您建立 VM 之後，返回此函式應用程式的工作變得更容易。

## <a name="create-a-vm-inside-a-virtual-network"></a>建立虛擬網路內的 VM

接下來，建立預先設定的 VM 虛擬網路內執行 WordPress ([WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)由 Jetware)。 WordPress 的 VM 會因為其低的成本和方便使用。 此相同案例適用於虛擬網路，例如 REST Api、 App Service 環境，以及其他 Azure 服務中的任何資源。 

1. 在入口網站中，選擇 **+ 建立資源**在左側的導覽窗格中，在搜尋欄位中輸入`WordPress LEMP7 Max Performance`，然後按 Enter。

1. 選擇**Wordpress LEMP Max Performance**搜尋結果中。 選取的軟體方案**Wordpress LEMP Max Performance centos**作為**軟體方案**，然後選取**建立**。

1. 在 **基本概念**索引標籤上，使用影像下方資料表中指定的 VM 設定：

    ![基本 索引標籤，來建立 VM](./media/functions-create-vnet/create-vm-1.png)

    | 設定      | 建議值  | 說明      |
    | ------------ | ---------------- | ---------------- |
    | **訂用帳戶** | 您的訂用帳戶 | 您的資源建立所在的訂用帳戶。 | 
    | **[資源群組](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | 選擇`myResourceGroup`，或您建立與您的函式應用程式的資源群組。 函式應用程式中，WordPress 的 VM，使用相同的資源群組和主控方案可讓您更輕鬆地清除資源，當您完成本教學課程。 |
    | **虛擬機器名稱** | VNET-Wordpress | VM 名稱必須是唯一的資源群組中 |
    | **[區域](https://azure.microsoft.com/regions/)** | （歐洲）西歐 | 選擇您附近或接近函式會存取 VM 的區域。 |
    | **大小** | B1s | 選擇**變更大小**，然後選取 B1s 標準映像，其具有 1 個 vCPU 和 1 GB 的記憶體。 |
    | **驗證類型** | 密碼 | 若要使用密碼驗證，您也必須指定**使用者名稱**，安全**密碼**，然後**確認密碼**。 本教學課程中，您不需要登入 VM 除非您需要進行疑難排解。 |

1. 選擇**Networking**索引標籤，然後在 [設定虛擬網路] 下選取**新建**。

1. 在 **建立虛擬網路**，使用影像下方資料表中的設定：

    ![網路索引標籤建立 VM](./media/functions-create-vnet/create-vm-2.png)

    | 設定      | 建議值  | 說明      |
    | ------------ | ---------------- | ---------------- |
    | **名稱** | myResourceGroup-vnet | 您可以使用您的虛擬網路產生的預設名稱。 |
    | **位址範圍** | 10.10.0.0/16 | 虛擬網路中使用單一的位址範圍。 |
    | **子網路名稱** | 教學課程-Net | 子網路的名稱。 |
    | **位址範圍**（子網路） | 10.10.1.0/24   | 子網路大小定義多少個介面可以新增至子網路。 這個子網路會使用 WordPress 網站。  A`/24`子網路提供 254 的主機位址。 |

1. 選取 **確定**建立虛擬網路。

1. 回到**Networking**索引標籤上，選擇**無**for**公用 IP**。

1. 選擇**管理** 索引標籤，然後在**診斷儲存體帳戶**，選擇您建立函式應用程式使用的儲存體帳戶。

1. 選取 [檢閱 + 建立]  。 驗證完成之後，請選取**建立**。 VM 建立程序需要幾分鐘的時間。 建立的 VM 只能存取虛擬網路。

1. 建立 VM 之後，請選擇**移至資源**若要檢視新的虛擬機器 頁面，然後選擇**網路**之下**設定**。

1. 請確認有無**公用 IP**。 請記下**私用 IP**，用來從您的函式應用程式連線到 VM。

    ![在 VM 中的網路設定](./media/functions-create-vnet/vm-networking.png)

您現在可以完全在您的虛擬網路內部署的 WordPress 網站。 此站台不是從公用網際網路存取。

## <a name="connect-your-function-app-to-the-virtual-network"></a>函式應用程式連接到虛擬網路

使用 WordPress 網站，在虛擬網路中的 VM 中執行，您現在可以函式應用程式連線至該虛擬網路。

1. 在新的函式應用程式，選取**平台功能** > **網路**。

    ![選擇 函式應用程式中的 網路功能](./media/functions-create-vnet/networking-0.png)

1. 底下**VNet 整合**，選取**按一下此處以設定**。

    ![設定網路功能的狀態](./media/functions-create-vnet/Networking-1.png)

1. 在 [虛擬網路整合] 頁面中，選取**新增 VNet （預覽）** 。

    ![新增 VNet 整合預覽](./media/functions-create-vnet/networking-2.png)

1. 在 **網路功能狀態**，使用影像下方資料表中的設定：

    ![定義函式應用程式的虛擬網路](./media/functions-create-vnet/networking-3.png)

    | 設定      | 建議值  | 描述      |
    | ------------ | ---------------- | ---------------- |
    | **虛擬網路** | MyResourceGroup-vnet | 此虛擬網路是您稍早建立的一個。 |
    | **子網路** | 建立新的子網路 | 在您的函式應用程式使用的虛擬網路中建立子網路。 VNet 整合必須設定為使用空白的子網路中。 它並不重要，您的函式會使用您的 VM 的不同子網路。 虛擬網路會自動將路由兩個子網路之間的流量。 |
    | **子網路名稱** | Function-Net | 新子網路的名稱。 |
    | **虛擬網路位址區塊** | 10.10.0.0/16 | 選擇 WordPress 網站所使用的相同位址區塊。 您應該只有一個定義的位址區塊。 |
    | **位址範圍** | 10.10.2.0/24   | 子網路大小會限制您 Premium 方案函式應用程式可以向外延展至執行個體的總數。 這個範例會使用`/24`254 可用的主機位址的子網路。 這個子網路是過度佈建，但容易計算。 |

1. 選取 **確定**新增子網路。 關閉 VNet 整合和網路功能狀態 頁面，以返回 函式應用程式頁面。

函式應用程式現在可以存取 WordPress 網站執行所在的虛擬網路。 接下來，使用[Azure Functions Proxy](functions-proxies.md)將檔案從 WordPress 網站。

## <a name="create-a-proxy-to-access-vm-resources"></a>建立 proxy，以存取虛擬機器資源

使用已啟用 VNet 整合，您可以將 proxy 在函式應用程式將要求轉送到虛擬網路中執行的 VM。

1. 在您函式應用程式中，選取**Proxy** >  **+** ，然後使用影像下方資料表中的 proxy 設定：

    ![定義的 proxy 設定](./media/functions-create-vnet/create-proxy.png)

    | 設定  | 建議值  | 說明      |
    | -------- | ---------------- | ---------------- |
    | **名稱** | 工廠 | 名稱可以是任何值。 它用來識別 proxy。 |
    | **路由範本** | /plant | 對應至 VM 資源的路由。 |
    | **後端 URL** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | 取代`<YOUR_VM_IP>`您稍早建立 WordPress VM 的 IP 位址。 此對應會傳回單一檔案，從站台。 |

1. 選取 **建立**將 proxy 加入至您的函式應用程式。

## <a name="try-it-out"></a>試做

1. 在瀏覽器中嘗試存取您所做的 URL**後端 URL**。 如預期般運作，要求會逾時。您的 WordPress 網站僅供您的虛擬網路，而非透過網際網路連接，就會發生逾時。

1. 複製**Proxy URL**值從新的 proxy，並將它貼到您的瀏覽器的網址列。 傳回的影像都是從您的虛擬網路內部執行的 WordPress 網站。

    ![傳回從 WordPress 網站的工廠映像檔案](./media/functions-create-vnet/plant.png)

函式應用程式連線到網際網路，您的虛擬網路。 Proxy 是透過公用網際網路，接收要求，並再做為簡單的 HTTP proxy，該要求轉送至連線的虛擬網路。 Proxy 接著會轉送回應給您公開透過網際網路。

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，WordPress 網站作為 API 呼叫的函式應用程式中使用的 proxy。 此案例可讓良好的教學課程中，因為很容易設定，並加以視覺化。 您可以使用任何其他虛擬網路內部署的 API。 您可能也已建立函式呼叫部署於虛擬網路內的 Api 的程式碼。 更真實的案例是使用資料的用戶端 Api 呼叫的虛擬網路中部署的 SQL Server 執行個體的函式。

進階方案中執行的函式會共用相同基礎 App Service 的基礎結構即 PremiumV2 計劃的 web 應用程式。 所有的文件[web 應用程式在 Azure App Service 中的](../app-service/overview.md)適用於 Premium 的計劃函式。

> [!div class="nextstepaction"]
> [深入了解函式中的網路功能選項](./functions-networking-options.md)

[ 進階方案]: functions-scale.md#premium-plan
