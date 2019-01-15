---
title: 教學課程 - 將流量路由傳送至加權端點 - Azure 流量管理員
description: 本教學課程文章說明如何使用流量管理員將流量路由至加權端點。
services: traffic-manager
author: KumudD
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: kumud
ms.openlocfilehash: f70f3804bb1c6f385081b56fe6139b1b680a95cf
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54055008"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>教學課程：使用流量管理員控制加權端點的流量路由 

本教學課程說明如何使用 Azure 流量管理員，透過加權路由方法控制端點之間的使用者流量路由。 在此路由方法中，您需要在流量管理員設定檔設定中指派權數給每個端點。 隨後，使用者流量會根據指派給每個端點的權數進行路由。 權數是 1 到 1,000 之間的整數。 指派給端點的權數值越高，其優先順序越高。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 在 IIS 上建立兩個執行基本網站的 VM。
> * 建立兩個測試 VM 以檢視運作中的流量管理員。
> * 為執行 IIS 的 VM 設定 DNS 名稱。
> * 建立流量管理員設定檔。
> * 在流量管理員設定檔中新增 VM 端點。
> * 檢視流量管理員的運作。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件
若要檢視運作中的流量管理員，請為本教學課程部署下列項目：
- 在不同 Azure 區域中執行的兩個基本網站執行個體：美國東部和西歐。
- 用來測試流量管理員的兩個測試 VM：一個位於美國東部，另一個位於西歐。 測試 VM 可用來說明流量管理員如何將使用者流量路由傳送至端點獲派權數較高的網站。

### <a name="sign-in-to-azure"></a>登入 Azure 

登入 [Azure 入口網站](https://portal.azure.com)。

### <a name="create-websites"></a>建立網站

在本節中，您會建立兩個網站執行個體，這兩個執行個體可在兩個 Azure 區域中為流量管理員設定檔提供兩個服務端點。 若要建立兩個網站，請完成下列步驟：
1. 建立兩個執行基本網站的 VM：一個位於美國東部，另一個位於西歐。
2. 在每個 VM 上安裝 IIS 伺服器。 更新預設網頁；其中說明使用者在造訪網站時所連線的 VM 名稱。

#### <a name="create-vms-for-running-websites"></a>建立 VM 以供執行網站
在本節中，您會在美國東部和西歐的 Azure 區域中建立兩個 VM (*myIISVMEastUS* 和 *myIISVMWEurope*)。

1. 在 Azure 入口網站的左上角，選取 [建立資源] > [計算] > [Windows Server 2016 VM]。
2. 針對 [基本] 輸入或選取下列資訊。 接受其他設定的預設值，然後選取 [建立]。

    |設定|值|
    |---|---|
    |Name|輸入 **myIISVMEastUS**。|
    |使用者名稱| 輸入您選擇的使用者名稱。|
    |密碼| 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    |資源群組| 選取 [新增]，然後輸入 myResourceGroupTM1。|
    |位置| 選取 [美國東部]。|
    |||
4. 在 [選擇大小] 底下選取虛擬機器大小。
5. 針對 [設定] 選取下列值，然後選取 [確定]：
    
    |設定|值|
    |---|---|
    |虛擬網路| 選取 [虛擬網路]。 在 [建立虛擬網路] 中，輸入 **myVNet1** 作為 [名稱]。 針對 [子網路]，輸入 **mySubnet**。|
    |網路安全性群組|選取 [基本]。 在 [選取公用輸入連接埠] 下拉式清單中，選取 **HTTP** 和 **RDP**。 |
    |開機診斷|選取 [已停用]。|
    |||
6. 在 [摘要] 中的 [建立] 下方選取 [建立]，以開始部署 VM。

7. 以下列變更再次完成步驟 1-6：

    |設定|值|
    |---|---|
    |資源群組 | 選取 [新增]，然後輸入 **myResourceGroupTM2**。|
    |位置|輸入**西歐**。|
    |虛擬機器名稱 | 輸入 **myIISVMWEurope**。|
    |虛擬網路 | 選取 [虛擬網路]。 在 [建立虛擬網路] 中，輸入 **myVNet2** 作為 [名稱]。 針對 [子網路]，輸入 **mySubnet**。|
    |||
8. 可能需要數分鐘才會建立虛擬機器。 請等到這兩個 VM 都已建立後，再繼續進行其他步驟。

![建立 VM](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-webpage"></a>安裝 IIS 和自訂預設網頁

在本節中，您會在兩個 VM (&mdash;myIISVMEastUS 和 myIISVMWEurope&mdash;) 上安裝 IIS 伺服器，然後更新預設網頁。 自訂的網頁會顯示您從網頁瀏覽器瀏覽網站時所連線的 VM 名稱。

1. 在左側功能表上，選取 [所有資源]。 從資源清單中，選取 **myResourceGroupTM1** 資源群組中的 **myIISVMEastUS**。
2. 在 [概觀] 頁面上，選取 [連線]。 在 [連線至虛擬機器] 中，選取 [下載 RDP 檔案]。 
3. 開啟下載的 .rdp 檔案。 如果出現提示，請選取 [連線]。 輸入您在建立 VM 時所指定的使用者名稱和密碼。 您可能需要選取 [其他選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。 
4. 選取 [確定] 。
5. 您可能會在登入過程中收到憑證警告。 如果您收到警告，請選取 [是] 或 [繼續] 以繼續進行連線。
6. 在伺服器桌面上，瀏覽至 [Windows 系統管理工具] > [伺服器管理員]。
7. 在 VM1 上開啟 Windows PowerShell。 使用下列命令安裝 IIS 伺服器及更新預設 .htm 檔案。
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![安裝 IIS 和自訂網頁](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. 關閉與 **myIISVMEastUS** 的 RDP 連線。
9. 重複步驟 1 至 8。 與 **myResourceGroupTM2** 資源群組內的 **myIISVMWEurope** VM 建立 RDP 連線，以安裝 IIS 並自訂其預設網頁。

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>為執行 IIS 的 VM 設定 DNS 名稱

流量管理員會根據服務端點的 DNS 名稱來路由傳送使用者流量。 在本節中，您會設定 IIS 伺服器 myIISVMEastUS 和 myIISVMWEurope 的 DNS 名稱。

1. 在左側功能表上，選取 [所有資源]。 從資源清單中，選取 **myResourceGroupTM1** 資源群組中的 **myIISVMEastUS**。
2. 在 [概觀] 頁面的 [DNS 名稱] 底下，選取 [設定]。
3. 在 [組態] 頁面的 [DNS 名稱] 標籤下方，新增唯一的名稱。 然後選取 [儲存]。
4. 針對 **myResourceGroupTM1** 資源群組中名為 **myIISVMWEurope** 的 VM，重複步驟 1-3。

### <a name="create-a-test-vm"></a>建立測試 VM

在本節中，您會建立 VM mVMEastUS。 您會使用此 VM 來測試流量管理員如何將流量路由傳送至權數值較高的網站端點。

1. 在 Azure 入口網站的左上角，選取 [建立資源] > [計算] > [Windows Server 2016 VM]。
2. 針對 [基本] 輸入或選取下列資訊。 接受其他設定的預設值，然後選取 [建立]：

    |設定|值|
    |---|---|
    |Name|輸入 **myVMEastUS**。|
    |使用者名稱| 輸入您選擇的使用者名稱。|
    |密碼| 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    |資源群組| 選取 [使用現有的]，然後選取 [myResourceGroupTM1]。|
    |||

4. 在 [選擇大小] 底下選取虛擬機器大小。
5. 針對 [設定] 選取下列值，然後選取 [確定]：
    |設定|值|
    |---|---|
    |虛擬網路| 選取 [虛擬網路]。 在 [建立虛擬網路] 中，輸入 **myVNet3** 作為 [名稱]。 針對 [子網路]，輸入 **mySubnet**。|
    |網路安全性群組|選取 [基本]。 在 [選取公用輸入連接埠] 下拉式清單中，選取 **HTTP** 和 **RDP**。 |
    |開機診斷|選取 [已停用]。|
    |||

6. 在 [摘要] 中的 [建立] 下方選取 [建立]，以開始部署 VM。
8. 建立 VM 需要幾分鐘的時間。 請等到 VM 已建立後，再繼續進行其他步驟。

## <a name="create-a-traffic-manager-profile"></a>建立流量管理員設定檔
根據**加權**路由方法來建立流量管理員設定檔。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [流量管理員設定檔] > [建立]。
2. 在 [建立流量管理員設定檔] 中，輸入或選取下列資訊。 接受其他設定的預設值，然後選取 [建立]。

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | Name                   | 輸入 trafficmanager.net 區域內的唯一名稱。 它會產生 DNS 名稱 trafficmanager.net，用以存取流量管理員設定檔。                                   |
    | 路由方法          | 選取 [加權] 路由方法。                                       |
    | 訂用帳戶            | 選取您的訂用帳戶。                          |
    | 資源群組          | 選取 [使用現有的]，然後選取 [myResourceGroupTM1]。 |
    |        |   |

    ![建立流量管理員設定檔](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>新增流量管理員端點

新增兩個執行 IIS 伺服器的 VM (myIISVMEastUS 和 myIISVMWEurope)，以對其路由使用者流量。

1. 在入口網站的搜尋列中，搜尋您在上一節建立的流量管理員設定檔名稱。 在顯示的結果中選取設定檔。
2. 在 [流量管理員設定檔] 的 [設定] 區段中，選取 [端點] > [新增]。
3. 輸入或選取下列資訊。 接受其他設定的預設值，然後選取 [確定]。

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 類型                    | 輸入 Azure 端點。                                   |
    | Name           | 輸入 **myEastUSEndpoint**。                                        |
    | 目標資源類型           | 選取 [公用 IP 位址]。                          |
    | 目標資源          | 選擇公用 IP 位址，以顯示相同訂用帳戶下具有公用 IP 位址的資源清單。 在 [資源] 中，選取名為 **myIISVMEastUS-ip** 的公用 IP 位址。 這是美國東部 IIS 伺服器 VM 的公用 IP 位址。|
    |  Weight      | 輸入 **100**。        |
    |        |           |

4. 重複步驟 2 和 3，以針對公用 IP 位址 **myIISVMWEurope-ip** 新增另一個名為 **myWestEuropeEndpoint** 的端點。 此位址與名為 myIISVMWEurope 的 IIS 伺服器 VM 相關聯。 針對 [權數]，輸入 **25**。 
5.  這兩個端點新增完畢後，會顯示在 [流量管理員設定檔] 中，且其監視狀態為 [線上]。

## <a name="test-the-traffic-manager-profile"></a>測試流量管理員設定檔
若要檢視運作中的流量管理員，請完成下列步驟：
1. 決定流量管理員設定檔的 DNS 名稱。
2. 檢視流量管理員的運作。

### <a name="determine-dns-name-of-traffic-manager-profile"></a>決定流量管理員設定檔的 DNS 名稱
在本教學課程中，為了簡單起見，您可使用流量管理員設定檔的 DNS 名稱來瀏覽網站。 

您可以決定流量管理員設定檔的 DNS 名稱，如下所示：

1.  在入口網站的搜尋列中，搜尋您在上一節建立的流量管理員設定檔名稱。 在顯示的結果中，選取流量管理員設定檔。
1. 選取 [概觀]。
2. 流量管理員設定檔會顯示其 DNS 名稱。 在生產部署中，您會使用 DNS CNAME 記錄，將虛名網域名稱設定為指向流量管理員網域名稱。

   ![流量管理員 DNS 名稱](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>檢視流量管理員的運作
在本節中，您可以檢視運作中的流量管理員。 

1. 在左側功能表上，選取 [所有資源]。 從資源清單中，選取 **myResourceGroupTM1** 資源群組中的 **myVMEastUS**。
2. 在 [概觀] 頁面上，選取 [連線]。 在 [連線至虛擬機器] 中，選取 [下載 RDP 檔案]。 
3. 開啟下載的 .rdp 檔案。 如果出現提示，請選取 [連線]。 輸入您在建立 VM 時指定的使用者名稱和密碼。 您可能需要選取 [其他選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。 
4. 選取 [確定] 。
5. 您可能會在登入過程中收到憑證警告。 如果您收到警告，請選取 [是] 或 [繼續] 以繼續進行連線。 
6. 在 VM myVMEastUS 的網頁瀏覽器中，輸入流量管理員設定檔的 DNS 名稱，以檢視您的網站。 您會路由至裝載於 IIS 伺服器 myIISVMEastUS 上的網站，因為其指派的權數較高 (**100**)。 為 IIS 伺服器 myIISVMWEurope 指派的端點權數值較低 (**25**)。

   ![測試流量管理員設定檔](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)
   
## <a name="delete-the-traffic-manager-profile"></a>刪除流量管理員設定檔
當您不再需要您在本教學課程中建立的資源群組時，您可加以刪除。 若要這麼做，請選取資源群組 (**ResourceGroupTM1** 或 **ResourceGroupTM2**)，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [根據使用者的地理位置將流量路由至特定端點](traffic-manager-configure-geographic-routing-method.md)


