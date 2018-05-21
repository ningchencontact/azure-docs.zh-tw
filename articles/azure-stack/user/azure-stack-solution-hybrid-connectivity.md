---
title: 使用 Azure 和 Azure Stack 設定混合式雲端連線 | Microsoft Docs
description: 了解如何使用 Azure 和 Azure Stack 設定混合式雲端連線。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 048e2636aabe406728c8fe1b93ef861f13346256
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>教學課程：使用 Azure 和 Azure Stack 設定混合式雲端連線

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

您可以在全域 Azure 和 Azure Stack 中使用混合式連線模式安全地存取資源。 

在本教學課程中，您將建置範例環境，用以：

> [!div class="checklist"]
> - 使內部部署資料符合隱私權或法規需求，但具有全域 Azure 資源的存取權。
> - 在全域 Azure 中使用雲端調整應用程式部署和資源的同時，維護舊版系統。

## <a name="prerequisites"></a>先決條件

建置混合式連線部署時需要幾項元件，可能需要一些時間來準備。 

Azure OEM/硬體合作夥伴可部署生產 Azure Stack，而所有使用者均可部署 ASDK。 Azure Stack 操作員也必須部署 App Service、建立方案和供應項目、建立租用戶訂用帳戶，以及新增 Windows Server 2016 映像。 

如果您已擁有其中部分元件，請在開始之前確定它們符合需求。

本主題也假設您擁有 Azure 和 Azure Stack 的部分知識。 如果您想要深入了解之後再繼續，請務必從這些主題開始：
 - [Azure 簡介](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack 重要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure
 - 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。
 - 在 Azure 中建立 [Web 應用程式](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal)。 記下新的 Web 應用程式 URL，因為稍後將會用到。

### <a name="azure-stack"></a>Azure Stack
 - 使用生產 Azure Stack，或透過下方的連結部署 Azure Stack 開發套件：
    - https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 
    - 安裝程序通常需要幾個小時才能完成，因此請據以規劃時段。
 - 將 [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS 服務部署至 Azure Stack。 
 - 在 Azure Stack 環境中建立[方案/供應項目](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview)。 
 - 在 Azure Stack 環境中建立[租用戶訂用帳戶](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)。 


### <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您已符合下列條件：

 - 確認您的 VPN 裝置有對外開放的公用 IPv4 位址。 此 IP 位址不能位於 NAT 後方。
 - 確定所有資源部署在相同的區域/位置中。

#### <a name="example-values"></a>值的範例

本文的範例使用下列值。 您可以使用這些值來建立測試環境，或參考這些值，進一步了解本文中的範例。 如需有關 VPN 閘道的一般設定詳細資訊，請參閱 [關於 VPN 閘道設定](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings)。

連線規格：
 - **VPN 類型**：路由型
 - **連線類型**︰站對站 (IPsec)
 - **閘道類型**：VPN
 - **Azure 連線名稱**：Azure-Gateway-AzureStack-S2SGateway (入口網站會自動填入此值)
 - **Azure Stack 連線名稱**：AzureStack-Gateway-Azure-S2SGateway (入口網站會自動填入此值)
 - **共用金鑰**：任何與 VPN 硬體相容、在連線兩端皆具有相符值的金鑰
 - **訂用帳戶**：任何慣用的訂用帳戶
 - **資源群組**：Test-Infra

| Azure/Azure Stack 連線 | Name | 子網路 | IP 位址 |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure 虛擬網路閘道 | Azure-Gateway |  |  |
| Azure Stack 虛擬網路閘道 | AzureStack-Gateway |  |  |
| Azure 公用 IP | Azure-GatewayPublicIP |  | 在建立時決定 |
| Azure Stack 公用 IP | AzureStack-GatewayPublicIP |  | 在建立時決定 |
| Azure 區域網路閘道 | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Azure Stack 公用 IP 值 |
| Azure Stack 區域網路閘道 | Azure-S2SGateway<br>10.100.102.0/23 |  | Azure 公用 IP 值 |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>在全域 Azure 與 Azure Stack 中建立虛擬網路

> [!Note]  
> 您必須確定在 Azure 或 Azure Stack vNet 位址空間中沒有任何重疊的 IP。 

若要使用 Azure 入口網站在 Resource Manager 部署模型中建立 vNet。 如果您使用下列步驟作為教學課程，請使用[範例值](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values)。 如果您不執行這些步驟作為教學課程，請務必以自己的值取代這些值。 

1. 透過瀏覽器瀏覽至 [Azure 入口網站](http://portal.azure.com/) ，並使用您的 Azure 帳戶登入。
2. 按一下 [建立資源]。 在 [搜尋 Marketplace] 欄位中，輸入 `virtual network`'`。 在傳回的清單中找出 [虛擬網路]，並開啟 [虛擬網路] 頁面。
3. 從接近 [虛擬網路]頁面底部的 [選取部署模型] 清單中，選取 [Resource Manager]，然後選取 [建立]。 這會開啟 [建立虛擬網路] 頁面。
4. 在 [建立虛擬網路] 頁面上進行 VNet 設定。 當您填寫欄位時，若欄位中輸入的字元有效，紅色驚嘆號就會變成綠色核取記號。
5. 從 Azure Stack 的**租用戶入口網站**重複這些步驟。

## <a name="add-a-gateway-subnet"></a>新增閘道子網路

將虛擬網路連接到閘道之前，您必須先建立虛擬網路要連接的閘道子網路。 閘道服務會使用閘道子網路中指定的 IP 位址。

在[入口網站](http://portal.azure.com/)中，瀏覽至要建立虛擬網路閘道的 Resource Manager 虛擬網路。

1. 在 VNet 頁面的 [設定] 中，選取 [子網路] 以展開 [子網路] 頁面。
2. 在 [子網路] 頁面中，選取 [+閘道子網路] 以開啟 [新增子網路] 頁面。

    ![](media/azure-stack-solution-hybrid-connectivity/image4.png)

3. 子網路的 [名稱] 會自動填入 'GatewaySubnet' 這個值。 若要讓 Azure 將此子網路視為閘道子網路，則必須要有此值。 請調整自動填入的 [位址範圍] 值，以符合您的組態需求，然後選取頁面底部的 [確定] 以建立子網路。

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>在 Azure 與 Azure Stack 中建立虛擬網路閘道

1. 在入口網站頁面的左側，選取 + 並且在搜尋中輸入「虛擬網路閘道」。 在 [結果] 中，找出並選取 [虛擬網路閘道]。
2. 在 [虛擬網路閘道] 頁面的底部，選取 [建立]。 這會開啟 [建立虛擬網路閘道] 頁面。
3. 在 [建立虛擬網路閘道] 頁面上指定虛擬網路閘道的值，如範例值和下列其他值所詳細說明。
    - **SKU**：基本
    - **虛擬網路**：選取您先前建立的虛擬網路。 它會自動選取您先前建立的閘道子網路。 
    - **第一個 IP 組態**：這是您閘道的公用 IP。 
        - 按一下 [建立閘道 IP 設定]，您即會導向至 [選擇公用 IP 位址] 頁面。
        - 按一下 [+新建]，以開啟 [建立公用 IP 位址] 頁面。
        - 輸入公用 IP 位址的 [名稱]。 將 SKU 保留為 [基本]，然後選取此頁面底部的 [確定] 以儲存變更。

    > [!Note]  
    > VPN 閘道目前僅支援動態公用 IP 位址配置。 不過，這不表示 IP 位址變更之後已被指派至您的 VPN 閘道。 公用 IP 位址只會在刪除或重新建立閘道時變更。 它不會因為重新調整、重設或 VPN 閘道的其他內部維護/升級而變更。

4. 確認設定。 
5. 按一下 [建立]  即可開始建立 VPN 閘道。 設定會經過驗證，而您將會在儀表板上看到 [部署虛擬網路閘道] 圖格。 建立閘道可能需要長達 45 分鐘。 您可能需要重新整理入口網站頁面，才能看到完成的狀態。

    建立閘道之後，請查看入口網站中的虛擬網路，來檢視已指派給閘道的 IP 位址。 閘道會顯示為已連接的裝置。 您可以選取已連線的裝置 (虛擬網路閘道) 以檢視詳細資訊。
6. 對您的 Azure Stack 部署重複這些步驟。

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>在 Azure 與 Azure Stack 中建立區域網路閘道

區域網路閘道通常是指您的內部部署位置。 請為網站指定可供 Azure 或 Azure Stack 參考的名稱，然後指定您想要與其建立連線之內部部署 VPN 裝置的 IP 位址。 也請指定 IP 位址首碼，以供系統透過 VPN 閘道路由至 VPN 裝置。 您指定的位址首碼是位於內部部署網路上的首碼。 如果您的內部部署網路變更，或者您需要變更 VPN 裝置的公用 IP 位址，您稍後可以輕鬆地更新這些值。

1. 在入口網站中，選取 [+建立資源]。
2. 在搜尋方塊中輸入**區域網路閘道**，然後按 **Enter** 鍵進行搜尋。 這將會傳回結果清單。 按一下 [區域網路閘道]，然後選取 [建立] 按鈕以開啟 [建立區域網路閘道] 頁面。
3. 在 [建立區域網路閘道] 頁面上指定區域網路閘道的值，如範例值和下列其他值所詳細說明。
    - **IP 位址**：這是您要讓 Azure 或 Azure Stack 連線之 VPN 裝置的公用 IP 位址。 指定有效的公用 IP 位址。 此 IP 位址不能在 NAT 後方且必須可讓 Azure 觸達。 如果您目前沒有 IP 位址，您可以使用範例中顯示的值，但您必須返回，並以您的 VPN 裝置的公用 IP 位址取代預留位置 IP 位址。 否則，Azure 將無法連線。
    -  是指此區域網路所代表之網路的位址範圍。 您可以加入多個位址空間範圍。 確定您在此指定的範圍，不會與您要連接到的其他網路範圍重疊。 Azure 會將您指定的位址範圍路由傳送至內部部署 VPN 裝置 IP 位址。 如果您想要連線至內部部署網站，在此處請使用您自己的值，而不是範例中顯示的值。
    - **設定 BGP 設定**：僅在設定 BGP 時才使用。 否則，請勿選取此項目。
    - **訂用帳戶**：確認顯示的是正確的訂用帳戶。
    - **資源群組**：選取要使用的資源群組。 您可以建立新的資源群組或選取已建立的資源群組。
    - **位置**：選取將要建立此物件的位置。 建議您選取 VNet 所在的相同位置，但您可以不用這麼做。
4. 當您完成值的指定時，請選取頁面底部的 [建立] 按鈕，以建立區域網路閘道。
5. 對您的 Azure Stack 部署重複這些步驟。

## <a name="configure-your-connection"></a>設定您的連線

內部部署網路的站對站連線需要 VPN 裝置。 在此步驟中，您會設定 VPN 裝置，我們稱之為「連線」。 在設定「連線」時，您需要下列項目：
    - 共用金鑰。 這個共同金鑰與您建立站對站 VPN 連線時指定的共用金鑰相同。 在我們的範例中，我們會使用基本的共用金鑰。 我們建議您產生更複雜的金鑰以供使用。
    - 虛擬網路閘道的公用 IP 位址。 您可以使用 Azure 入口網站、PowerShell 或 CLI 來檢視公用 IP 位址。 若要使用 Azure 入口網站尋找 VPN 閘道的公用 IP 位址，請瀏覽至 [虛擬網路閘道]，然後選取閘道名稱。
您會在虛擬網路閘道與內部部署 VPN 裝置之間建立站對站 VPN 連線。
1. 在入口網站中，選取 [+建立資源]。
2. 在搜尋方塊中輸入**連線**，然後按 **Enter** 鍵進行搜尋。 這將會傳回結果清單。 按一下 [連線]，然後選取 [建立] 按鈕以開啟 [建立連線] 頁面。
3. 在 [建立連線] 頁面上，設定您的連線值。
     - 基本：
        1. **連線類型**：選取 [站對站 (IPSec)]。
        2. **資源群組**：(選取您的測試資源群組)
     - 設定：
        1. **虛擬網路閘道**：選取您先前建立的虛擬網路閘道。
        2. **區域網路閘道**：選取您先前建立的區域網路閘道。 
        3. **連線名稱**：這會自動填入來自兩個閘道的值。 
        4. **共用金鑰**：此處的值必須與您用於本機內部部署 VPN 裝置的值相符。 範例中使用的是 'abc123'，但是您可以(且應該) 使用更為複雜的值。 重要的是，您在此指定的值必須與您在設定 VPN 裝置時指定的值相同。
    - **訂用帳戶**、**資源群組**和**位置**的其餘值是固定的。
4. 按一下 [確定]  來建立連線。 您會看到畫面上閃爍「正在建立連線」。
5. 您可以在虛擬網路閘道的 [**連線] 頁面中檢視連線。 狀態將會從 [未知] 變成 [連線中]，然後變成 [成功]。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure 雲端模式，請參閱[雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns)。
