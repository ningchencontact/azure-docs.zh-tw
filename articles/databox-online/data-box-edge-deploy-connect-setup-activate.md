---
title: 在 Azure 入口網站中連線、設定和啟動 Azure Data Box Edge | Microsoft Docs
description: 部署 Data Box Edge 的第三個教學課程將說明如何連線、設定和啟動實體裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3d6742bec54e612b5dca6d9ef6c4f67a33929448
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166571"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-edge-preview"></a>教學課程：連線、設定、啟用 Azure Data Box Edge (預覽) 

本教學課程說明如何使用本機 Web UI 連線、設定和啟動 Data Box Edge 裝置。 

設定和啟用程序可能需要約 20 分鐘才能完成。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 連線至實體裝置
> * 設定並啟動實體裝置

> [!IMPORTANT]
> Data Box Edge 目前處於預覽狀態。 部署訂購並部署此解決方案之前，請檢閱 [Azure 預覽版使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 


## <a name="prerequisites"></a>必要條件

在您設定及安裝 Data Box Edge 之前，請先確定：

* 您已依照[安裝 Data Box Edge](data-box-edge-deploy-install.md) 中的詳細說明安裝實體裝置。
* 您具有先前建立用以管理 Data Box Edge 裝置的 Data Box Edge 服務適用的啟用金鑰。 如需詳細資訊，請移至[準備部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)。

## <a name="connect-to-the-local-web-ui-setup"></a>連線至本機 Web UI 設定 

1. 使用靜態 IP 位址 192.168.100.5 和子網路 255.255.255.0，在您要用來連線到 Edge 服務的電腦上設定乙太網路介面卡。
2. 將電腦連線到裝置上的連接埠 1。 
3. 開啟瀏覽器視窗，然後在 https://192.168.100.10 存取裝置的本機 Web UI。 在開啟裝置後，此動作可能需要進行幾分鐘的時間。 
4. 您會看到錯誤或警告指出網站的安全性憑證有問題。 請按一下 [繼續瀏覽此網頁] 。 (這些步驟可能會隨著使用的瀏覽器而不同。)
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

2. 登入裝置的 Web UI。 預設密碼為 *Password1*。 
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

3. 系統會提示您變更裝置系統管理員密碼。 請輸入包含 8 到 16 個字元的新密碼。 此密碼必須包含下列其中 3 種字元：大寫、小寫、數字和特殊字元。

您現在位於裝置的**儀表板**上。

## <a name="set-up-and-activate-the-physical-device"></a>設定並啟動實體裝置
 
1. 在儀表板中，您可以移至對 Data Box Edge 服務設定和註冊實體裝置所需的各種設定。 [裝置名稱]、[網路設定]、[Web Proxy 設定] 及 [時間設定] 是可省略的。 只有 [雲端設定] 是必要的設定。
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

2. 在 [裝置名稱] 頁面中，為您的裝置設定易記名稱。 易記名稱的長度可以是 1 至 15 個字元，並且可包含字母、數字和連字號。

    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

3. (選擇性) 設定您的 [網路設定]。 在實體裝置上，您會看到六個網路介面。 連接埠 1 和連接埠 2 是 1 Gbps 的網路介面。 連接埠 3、連接埠 4、連接埠 5 和連接埠 6 則都是 25 Gbps 的網路介面。 連接埠 1 會自動設定為管理專用連接埠，連接埠 2 到連接埠 6 則都是資料連接埠。 [網路設定] 頁面如下所示。
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    設定網路設定時，請留意下列事項：

    - 如果您的環境中啟用了 DHCP，則會自動設定網路介面。 系統會自動指派 IP 位址、子網路、閘道 和 DNS。
    - 如果未啟用 DHCP，您可以視需要指派靜態 IP。
    - 您可以將網路介面設定為 IPv4。
   
4. (可省略) 設定 Web Proxy 伺服器。 雖然 Web Proxy 設定是選用的，但如果您使用 Web Proxy，就只能在此處設定它。
   
   ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   在 [Web Proxy]  頁面中：
   
   1. 以下列格式提供 **Web Proxy URL**：`http://host-IP address or FDQN:Port number`。 此處不支援 HTTPS URL。
   2. 將 [驗證] 指定為 [基本] 或 [無]。
   3. 如果要使用驗證功能，您也必須提供 [使用者名稱] 和 [密碼]。
   4. 按一下 [套用] 以便驗證並套用所設定的 Web Proxy 設定。

5. (可省略) 設定裝置的時間設定，例如時區，以及主要和次要 NTP 伺服器。 NTP 伺服器是必要的，因為您的裝置必須讓時間同步，才能與您的雲端服務提供者進行驗證。
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    在 [時間設定]  頁面中：
    
    1. 根據裝置部署的地理位置，從下拉式清單中選取 [時區]  。 裝置的預設時區是太平洋標準時間。 裝置將針對所有排程的操作使用這個時區。
    2. 指定裝置的 [主要 NTP 伺服器]  ，或是接受預設值 time.windows.com。 請確定您的網路允許 NTP 流量從您的資料中心通過網際網路。
    3. (選擇性) 指定裝置的 [次要 NTP 伺服器]  。
    4. 按一下 [套用] 以便驗證並套用所設定的時間設定。

6. 在 [雲端設定] 頁面中，對 Azure 入口網站中的 Data Box Edge 服務啟動裝置。
    
    1. 請輸入您在[取得啟用金鑰](data-box-edge-deploy-prep.md#get-the-activation-key)中為 Data Box Edge 取得的 [啟用金鑰]。

    2. 按一下 [套用]。 
       
         ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    3. 裝置成功啟用之後，您會看到連線模式選項。 如果您需要使用處於部分中斷連線或已中斷連線模式的裝置，便會設定這些設定。 

        ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

裝置設定完成。 您現在可以在裝置上新增共用。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Data Box Edge 的相關主題，像是：

> [!div class="checklist"]
> * 連線至實體裝置
> * 設定並啟動實體裝置


請繼續進行下一個教學課程，以了解如何使用 Data Box Edge 傳輸資料。

> [!div class="nextstepaction"]
> [使用 Data Box Edge 傳輸資料](./data-box-edge-deploy-add-shares.md)。