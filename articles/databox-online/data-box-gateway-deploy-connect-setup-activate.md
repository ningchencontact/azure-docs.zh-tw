---
title: 在 Azure 入口網站中連線、設定和啟動 Azure 資料箱閘道 | Microsoft Docs
description: 部署資料箱閘道的第三個教學課程將說明如何連線、設定和啟動您的虛擬裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: de2ef4908260a62acf28a270dda6ad738a1760b9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402320"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>教學課程：連接、 設定、 啟動 Azure 中部署資料閘道

## <a name="introduction"></a>簡介

本教學課程說明如何連線至、 設定，並使用本機 web UI 來啟用您的資料方塊閘道裝置。 

設定和啟用程序可能需要約 10 分鐘才能完成。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 連接到虛擬裝置
> * 設定並啟動虛擬裝置

## <a name="prerequisites"></a>必要條件

在您設定及安裝資料箱閘道之前，請先確定：

* 您已佈建虛擬裝置，並取得它的連線的 URL 中所述[佈建為 HYPER-V 中的資料方塊閘道](data-box-gateway-deploy-provision-hyperv.md)或是[佈建在 VMware 中的資料方塊閘道](data-box-gateway-deploy-provision-vmware.md)。
* 您具有先前建立用以管理資料箱閘道裝置的資料箱閘道服務適用的啟用金鑰。 如需詳細資訊，請移至[準備部署 Azure 資料箱閘道](data-box-gateway-deploy-prep.md)。


## <a name="connect-to-the-local-web-ui-setup"></a>連線至本機 Web UI 設定 

1. 開啟瀏覽器視窗，然後存取本機 web UI 在裝置：
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   請使用您在上一個教學課程中記下的連線 URL。 您會看到錯誤或警告指出網站的安全性憑證有問題。

2. 選取 [繼續瀏覽此網頁]。 這些步驟可能會因您使用的瀏覽器而有所不同。
   
    ![網站安全性憑證錯誤訊息](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. 登入虛擬裝置的 Web UI。 預設密碼為 *Password1*。 
   
    ![登入本機 web UI](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. 出現提示時，變更裝置密碼。 新的密碼必須包含 8 到 16 個字元之間。 它必須包含下列其中 3 項： 大寫、 小寫、 數字和特殊字元。

    ![變更裝置密碼](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

您現在位於裝置的**儀表板**上。

## <a name="set-up-and-activate-the-virtual-device"></a>設定並啟動虛擬裝置
 
儀表板會顯示設定和註冊虛擬裝置與方塊部署資料閘道服務所需的各種設定。 [裝置名稱]、[網路設定]、[Web Proxy 設定] 及 [時間設定] 是可省略的。 只有 [雲端設定] 是必要的設定。
   
![本機 web UI 」 儀表板 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. 在左窗格中，選取**裝置名稱**，然後輸入您的裝置的易記名稱。 好記的名稱必須包含 1 到 15 個字元，並有字母、 數字和連字號。

    ![本機 web UI 的 [裝置名稱] 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. （選擇性）在左窗格中，選取**網路設定**然後進行設定。 在您的虛擬裝置，您會看到至少一個網路介面，取決於基礎虛擬機器中設定多少。 以下是已啟用一個網路介面的虛擬裝置所顯示的 [網路設定] 頁面。
    
    ![本機 web UI 的 [網路設定] 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    當您設定網路設定，請記住：

    - 如果您的環境中啟用了 DHCP，則會自動設定網路介面。 系統會自動指派 IP 位址、子網路、閘道 和 DNS。
    - 如果未啟用 DHCP，您可以視需要指派靜態 IP。
    - 您可以將網路介面設定為 IPv4。

     >[!NOTE] 
     > 我們建議您不要將網路介面的本機 IP 位址從靜態切換成 DHCP，除非您有另一個連接到裝置的 IP 位址。 如果使用一個網路介面並切換至 DHCP，就無法判斷 DHCP 位址。 如果您想要變更為 DHCP 位址，請等到裝置已向服務註冊之後再變更。 然後，您就可以在您服務的 Azure 入口網站中於 [裝置屬性] 中檢視所有介面卡的 IP。

3. (選用) 設定 Web Proxy 伺服器。 雖然 web proxy 設定是選擇性，如果您使用 web proxy，您可以只在此頁面上設定它。
   
   ![本機 web UI 的 [Web proxy 設定] 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   在  **Web proxy**頁面上，執行下列動作：
   
   1. 在 [Web proxy URL] 方塊中，以下列格式輸入 URL：`http://&lt;host-IP address or FQDN&gt;:Port number`。 此處不支援 HTTPS URL。
   2. 在 [驗證] 底下，選取 [無] 或 [NTLM]。
   3. 如果您使用的驗證，請輸入**使用者名稱**並**密碼**。
   4. 若要驗證及套用設定的 web proxy 設定，請選取**套用**。

4. (選擇性) 在左側窗格中，選取 [時間設定]，然後設定您裝置的時區，以及主要和次要 NTP 伺服器。 

    NTP 伺服器是必要的，因為您的裝置必須讓時間同步，才能與您的雲端服務提供者進行驗證。
    
    ![本機 web UI 的 [時間設定] 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    在 **時間設定**頁面上，執行下列動作：
    
    1. 在 **時區**下拉式清單中，選取對應至裝置部署所在的地理位置的時區。
        裝置的預設時區是太平洋標準時間。 裝置將針對所有排程的操作使用這個時區。

    2. 指定**主要 NTP 伺服器**為您的裝置，或接受預設值`time.windows.com`。   
        請確定您的網路允許 NTP 流量從您的資料中心通過網際網路。

    3. （選擇性） 在**次要 NTP 伺服器**方塊中，輸入您的裝置的次要伺服器。

    4. 若要驗證並套用所設定的時間設定，請選取 [套用]。

6. 在左窗格中，選取**雲端設定**，然後啟動您的裝置，與在 Azure 入口網站中部署資料閘道服務。
    
    1. 在 **啟用金鑰**方塊中，輸入**啟用金鑰**您[取得啟用金鑰](data-box-gateway-deploy-prep.md#get-the-activation-key)方塊部署資料閘道。

    2. 選取 [ **啟用**]。
       
         ![本機 web UI 的 [雲端設定] 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. 裝置啟動和重大更新，如果有的話，會自動套用。 您會看到該項影響的通知。 監視透過 Azure 入口網站的更新進度。

        ![本機 web UI 的 [雲端設定] 頁面](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **對話方塊也有修復金鑰，您應該複製並儲存在安全的位置。此金鑰用來復原資料，萬一裝置無法開機。**


    4. 您可能需要等候幾分鐘的時間才能順利完成更新。 更新之後已完成，請登入裝置。 **雲端設定**頁面更新，指出裝置已成功啟動。

        ![本機 web UI 的 [雲端設定] 頁面更新](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

裝置設定完成。 您現在可以在裝置上新增共用。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 連接到虛擬裝置
> * 設定並啟動虛擬裝置

若要了解如何使用您的 Data Gateway 傳輸資料，請參閱：

> [!div class="nextstepaction"]
> [使用資料箱閘道傳輸資料](./data-box-gateway-deploy-add-shares.md)。
