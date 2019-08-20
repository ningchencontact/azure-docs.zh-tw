---
title: 寄回 Azure 資料箱磁碟的教學課程 | Microsoft Docs
description: 使用本教學課程以了解如何將您的 Azure 資料箱磁碟寄回給 Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 9e3159a083d21e4a7a81df2fcf7a7d5db882b757
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976921"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>教學課程：退回 Azure 資料箱磁碟，並確認資料上傳至 Azure

這是一系列教學課程的最後一個：部署 Azure 資料箱磁碟。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 將資料箱磁碟寄送給 Microsoft
> * 確認資料上傳至 Azure
> * 清除資料箱磁碟的資料

## <a name="prerequisites"></a>必要條件

開始之前，請確定您已完成[教學課程：將資料複製到 Azure 資料箱磁碟並確認](data-box-disk-deploy-copy-data.md)。


## <a name="ship-data-box-disk-back"></a>寄回資料箱磁碟

1. 一旦完成資料驗證，請拔除磁碟。 移除連接纜線。
2. 使用發泡包裝材料將所有磁碟和連接纜線包裝在一起，然後放入包裹。 如果遺漏配件，則可能酌收費用。
    - 重複使用最初出貨的包裝。  
    - 我們建議您使用安全的氣泡片來包裝磁碟。
    - 確保穩固裝入磁碟，以免在箱子內移動。

後續步驟則取決於您退回裝置的地點。

### <a name="pick-up-in-us-canada"></a>美國、加拿大取貨

如果在美國或加拿大退回裝置，請執行下列步驟。

1. 使用黏貼在外盒上，透明塑膠套裡面的退貨標籤。 如果標籤受損或遺失：
    - 移至 [概觀 > 下載出貨標籤]  。

        ![下載出貨標籤](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        此動作會下載退貨標籤，如下所示。

        ![範例出貨標籤](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - 將標籤黏貼在裝置上。

2. 將包裹密封，並確定退貨標籤清楚可見。
3. 與 UPS 排定取貨時間。 若要排定取貨時間：

    - 致電給當地的 UPS (國家/地區特定的免付費電話號碼)。
    - 在您的電話中提供反向出貨追蹤號碼，如您列印出的標籤所示。
    - 若未提供追蹤號碼，UPS 將在取貨期間要求您支付額外的費用。
    - 除了排定取貨時間，您也可以在最接近的托運地點托運該資料箱磁碟。


### <a name="pick-up-in-europe"></a>在歐洲取貨

如果在歐洲退回裝置，請執行下列步驟。

1. 使用黏貼在外盒上，透明塑膠套裡面的退貨標籤。 如果標籤受損或遺失：
    - 移至 [概觀 > 下載出貨標籤]  。

        ![下載出貨標籤](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        此動作會下載退貨標籤，如下所示。

        ![範例出貨標籤](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - 將標籤黏貼在裝置上。

2. 將包裹密封，並確定退貨標籤清楚可見。
3. 如果是在歐洲經由 DHL 辦理裝置退貨，請造訪 DHL 的網站並且指定空運提單號碼，要求他們取貨。
4. 移至該國家/地區的 DHL Express 網站，然後選擇 [Book a Courier Collection > eReturn Shipment] \(預約快遞收貨 > eReturn 出貨\)  。

    ![DHL 退貨](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. 指定運貨單號碼，然後按一下 [Schedule Pickup] (排程取貨)  以安排取貨。

      ![排程取貨](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>在亞太地區取貨

此區域包含在日本、韓國、澳洲和新加坡取貨的指示。

#### <a name="pick-up-in-australia"></a>在澳洲取貨

澳洲的 Azure 資料中心有額外的安全性通知。 所有送達的貨物都必須有預先通知。 在澳洲收取可執行下列步驟。

1. 寄送電子郵件給 `adbops@microsoft.com`，要求具有唯一到貨識別碼或 TAU 代碼的出貨標籤。 至少在計劃出貨日期的 3 天前提出要求，以便及時取得標籤。
2. 電子郵件主旨應該是：*Request for reverse shipping label with TAU code*。 務必在電子郵件中包含下列詳細資料： 

    - 訂單名稱
    - 位址
    - 連絡人姓名

#### <a name="pick-up-in-japan"></a>日本取貨

1. 在理貨單上寫下貴公司名稱和地址資訊，作為您的寄件者資訊。
2. 使用下列電子郵件範本傳送電子郵件給 Quantium Solutions。

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```
    - **如果您要在大阪中挑選**，請將電子郵件範本中的主旨修改為：`Pickup request for Microsoft Azure OSA`。
    - 如果日本郵局運費到付託運單未隨附或遺失，請在這封電子郵件註明。 Quantium Solutions (Japan) 會要求日本郵局在取貨時提供理貨單。
    - 如果您有多個訂單，請透過電子郵件確保每件都會順利取貨。

3. 在預約取貨時間後，接收來自 Quantium Solutions 的電子郵件確認。 電子郵件確認也會包含運費到付理貨單的資訊。

如有需要，您可以透過下列資訊連絡 Quantium Solutions 支援人員 (日文)： 

- 電子郵件：Customerservice.JP@quantiumsolutions.com 
- 電話：03-5755-0150 

#### <a name="pick-up-in-korea"></a>韓國取貨

1. 請務必附上退貨理貨單。
2. 若要在附有理貨單時要求取貨：
    1. 在上班時間 (週一至週五上午 10 點到下午 5 點) 撥打 *Quantium Solutions International* 熱線 070-8231-1418。 報上 *Microsoft Azure Pickup* 和服務要求號碼以便安排收貨。  
    2. 如果熱線忙線中，請將主旨為 *Microsoft Azure Pickup* 的電子郵件傳送到 `microsoft@rocketparcel.com`，並提供服務要求號碼作為參考。
    3. 如果快遞人員未到府收貨，請撥打 *Quantium Solutions International* 熱線以便尋求替代安排。 
    4. 您會收到電子郵件向您確認排定的取貨時間。
3. 沒有理貨單時，才需要執行此步驟。 若要要求取貨：
    1. 在上班時間 (週一至週五上午 10 點到下午 5 點) 撥打 *Quantium Solutions International* 熱線 070-8231-1418。 報上 *Microsoft Azure Pickup* 和服務要求號碼以便安排收貨。 指定您需要新的理貨單來安排收貨。 請提供寄件者 (客戶)、接收者資訊 (Azure 資料中心)，以及參考號碼 (服務要求號碼)。 
    2. 如果熱線忙線中，請將主旨為 *Microsoft Azure Pickup* 的電子郵件傳送到 `microsoft@rocketparcel.com`，並提供服務要求號碼作為參考。
    3. 如果快遞人員未到府收貨，請撥打 *Quantium Solutions International* 熱線以便尋求替代安排。 
    4. 如果您透過電話提出要求，則會收到口頭確認。

### <a name="pick-up-in-singapore"></a>在新加坡取貨

1. 列印出貨標籤並黏貼在紙箱上。 如果標籤受損或遺失：
    - 移至 [概觀 > 下載出貨標籤]  。

        ![下載出貨標籤](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        此動作會下載退貨標籤，如下所示。

        ![範例出貨標籤](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - 將標籤黏貼在裝置上。 請確定標籤清楚可見。

2. 若要要求取貨：
    - 在上班時間 (週一至週五上午 9 點到下午 5 點) 撥打 **SingPost** 熱線 **6845 6485**。  
    - 報上 *Microsoft Azure Pickup* 和服務要求號碼 (退回出貨標籤上的追蹤號碼) 以便安排收貨。 
    - 您會獲得排定取貨時間的口頭確認。 
    - 如果快遞人員未到府收貨，請撥打 **SingPost** 熱線 **6845 6485** 以另作安排。 
3. 將貨交給快遞人員。 


## <a name="verify-data-upload-to-azure"></a>確認資料上傳至 Azure

一旦承運業者收取磁碟，入口網站的訂單狀態會更新為 [已取貨]  。 此外，也會顯示追蹤識別碼。

![磁碟已取貨](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

當 Microsoft 收到並掃描磁碟之後，作業狀態會更新為 [已收到]  。 

![磁碟已收到](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

一旦磁碟連線到 Azure 資料中心的伺服器，系統會自動複製資料。 視資料大小而定，複製作業可能需要數小時到數天才能完成。 您可以在入口網站中監視複製作業進度。

一旦複製完成，訂單狀態會更新為 [已完成]  。

![資料複製已完成](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

如果複製完成但發生錯誤，請參閱[對上傳錯誤進行疑難排解](data-box-disk-troubleshoot-upload.md)。

請先確認您的資料位於儲存體帳戶中，再從來源予以刪除。 您的資料可以位於：

- 您的 Azure 儲存體帳戶。 當您將資料複製到資料箱時，資料會上傳到 Azure 儲存體帳戶中的下列其中一個路徑，視類型而定。

  - 區塊 Blob 與分頁 Blob：`https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure 檔案：`https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    或者，您可以在 Azure 入口網站中移至您 Azure 儲存體帳戶，並從該處瀏覽。

- 您的受控磁碟資源群組。 建立受控磁碟時，會上傳 VHD 以作為分頁 Blob，接著轉換為受控磁碟。 受控磁碟會連結至訂單建立時所指定的資源群組。

  - 如果您已成功複製到 Azure 中的受控磁碟，則可前往 Azure 入口網站中的 [訂單詳細資料]  ，並記下針對受控磁碟所指定的資源群組。

      ![檢閱訂單詳細資料](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    前往所記下的資源群組，並找出您的受控磁碟。

      ![受控磁碟的資源群組](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - 如果您複製了 VHDX 或動態/差異 VHD，則會將 VHDX/VHD 上傳至暫存的儲存體帳戶以作為區塊 Blob。 移至您暫存的 [儲存體帳戶] > [Blob]  ，然後選取適當的容器：標準 SSD、標準 HDD 或進階 SSD。 VHDX/VHD 應該會在您暫存的儲存體帳戶中顯示為區塊 Blob。

若要確認資料已上傳至 Azure，請執行下列步驟：

1. 移至與磁碟訂單相關聯的儲存體帳戶。
2. 移至 [Blob 服務 > 瀏覽 Blob]  。 容器清單隨即顯示。 對應至您在 BlockBlob  和 PageBlob  資料夾底下建立的子資料夾，系統會在儲存體帳戶中建立具有相同名稱的容器。
    如果資料夾名稱不符合 Azure 命名慣例，則資料上傳至 Azure 會失敗。

4. 若要確認已載入整個資料集，請使用 Microsoft Azure 儲存體總管。 連接對應到磁碟出租訂單的儲存體帳戶，然後查看 Blob 容器的清單。 選取容器，按一下 [...更多]  ，然後按一下 [資料夾統計資料]  。 在 [活動]  窗格中，會顯示該資料夾的統計資料，包括 Blob 數目和 Blob 大小總計。 Blob 大小總計 (以位元組為單位) 應該符合資料集的大小。

    ![儲存體總管中的資料夾統計資料](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>清除資料箱磁碟的資料

一旦複製完成，而且已經確認資料位於 Azure 儲存體帳戶中，就會根據 NIST 標準安全地清除磁碟。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱磁碟的相關主題，像是：

> [!div class="checklist"]
> * 將資料箱磁碟寄送給 Microsoft
> * 確認資料上傳至 Azure
> * 清除資料箱磁碟的資料


請前進到下一個操作說明，以了解如何透過 Azure 入口網站管理資料箱磁碟。

> [!div class="nextstepaction"]
> [使用 Azure 入口網站來管理 Azure 資料箱磁碟](./data-box-portal-ui-admin.md)

::: zone-end


::: zone target="chromeless"

# <a name="return-the-disk-and-verify-upload-to-azure"></a>退回磁碟並確認已上傳至 Azure

## <a name="ship-to-azure"></a>寄送到 Azure

1. 完成資料驗證後，請拔除磁碟。 移除連接纜線。
2. 使用發泡包裝材料將所有磁碟和連接纜線包裝在一起，然後放入包裹。 如果遺漏配件，則可能酌收費用。
    - 重複使用最初出貨的包裝。  
    - 我們建議您使用安全的氣泡片來包裝磁碟。
    - 確保穩固裝入磁碟，以免在箱子內移動。
3. 後續步驟則取決於您退回裝置的地點。
    - [如果是在美國和加拿大退回裝置，請安排由 UPS 取貨](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada)。
    - 造訪 DHL 的網站並指定空運提單號碼，[安排由歐洲地區的 DHL 取貨](data-box-disk-deploy-picked-up.md#pick-up-in-europe)。
    - [安排澳洲/太平洋區域國家的取貨](data-box-disk-deploy-picked-up.md#pick-up-in-asia-pacific-region)，例如澳洲、日本、韓國和新加坡。
4. 一旦承運業者收取磁碟，入口網站的訂單狀態就會更新，並顯示追蹤識別碼。

## <a name="verify-upload-to-azure"></a>確認已上傳至 Azure

資料上傳至 Azure 後，請先確認您的資料位於儲存體帳戶中，再從來源予以刪除。 您的資料可以位於：

- 您的 Azure 儲存體帳戶。 當您將資料複製到資料箱時，資料會上傳到 Azure 儲存體帳戶中的下列其中一個路徑，視類型而定。

    - **區塊 Blob 和分頁 Blob**： https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt

    - **Azure 檔案儲存體**： https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt

    或者，您可以在 Azure 入口網站中移至您 Azure 儲存體帳戶，並從該處瀏覽。

- 您的受控磁碟資源群組。 建立受控磁碟時，會上傳 VHD 以作為分頁 Blob，接著轉換為受控磁碟。 受控磁碟會連結至訂單建立時所指定的資源群組。

::: zone-end


