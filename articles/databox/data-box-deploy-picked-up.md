---
title: 寄回 Azure 資料箱的教學課程 | Microsoft Docs
description: 了解如何將您的 Azure 資料箱寄給 Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: 5b43241be4e161cd6051dce02a3574fbdb580f28
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606245"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>教學課程：送回 Azure 資料箱，並確認資料上傳至 Azure

本教學課程將說明如何送回 Azure 資料箱，並確認資料已上傳至 Azure。

在本教學課程中，您將了解如下所列的主題：

> [!div class="checklist"]
> * 必要條件
> * 準備寄送
> * 將資料箱寄送給 Microsoft
> * 確認資料上傳至 Azure
> * 清除資料箱的資料

## <a name="prerequisites"></a>必要條件

在您開始前，請確定：

- 您已完成[教學課程：將資料複製到 Azure 資料箱並確認](data-box-deploy-copy-data.md)。 
- 複製作業已完成。 如果複製作業正在進行，則無法執行寄送準備。

## <a name="prepare-to-ship"></a>準備寄送

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>寄回資料箱

1. 請確認裝置的電源已關閉，然後移除纜線。 纏繞裝置隨附的電源線，並將其安全地放在裝置背後。
2. 請確定出貨標籤已顯示在電子筆墨顯示器上，並安排貨運業者取貨。 如果標籤損毀、遺失或未顯示在電子筆墨顯示器上，請與 Microsoft 支援服務連絡。 若支援人員提出建議，則您可以前往 Azure 入口網站中的 [概觀] > [下載出貨標籤]  。 下載出貨標籤並貼在裝置上。 
3. 如果要送回裝置，請安排由 UPS 取貨。 若要排定取貨時間：

    - 致電給當地的 UPS (國家/地區特定的免付費電話號碼)。
    - 在您的電話中提供反向出貨追蹤號碼，如 E-ink 顯示或您列印出的標籤中所示。
    - 若未提供追蹤號碼，UPS 將在取貨期間要求您支付額外的費用。

    除了排定取貨時間，您也可以在最接近的托運地點托運該資料箱。
4. 一旦貨運業者收取資料箱並進行掃描，入口網站的訂單狀態會更新為 [已取貨]  。 此外，也會顯示追蹤識別碼。

## <a name="verify-data-upload-to-azure"></a>確認資料上傳至 Azure

當 Microsoft 收到並掃描裝置之後，訂單狀態會更新為 [已收到]  。 然後，裝置會進行實體驗證，以檢查是否有損毀或遭到竄改的跡象。

完成驗證之後，資料箱會連線到 Azure 資料中心的網路。 資料複製會自動啟動。 視資料大小而定，複製作業可能需要數小時到數天才能完成。 您可以在入口網站中監視複製作業進度。

一旦複製完成，訂單狀態會更新為 [已完成]  。

請先確認您的資料已上傳至 Azure，然後再從來源予以刪除。 您的資料可以位於：

- 您的 Azure 儲存體帳戶。 當您將資料複製到資料箱時，資料會上傳到 Azure 儲存體帳戶中的下列其中一個路徑，視類型而定。

  - 區塊 Blob 與分頁 Blob：`https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure 檔案：`https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    或者，您可以在 Azure 入口網站中移至您 Azure 儲存體帳戶，並從該處瀏覽。

- 您的受控磁碟資源群組。 建立受控磁碟時，會上傳 VHD 以作為分頁 Blob，接著轉換為受控磁碟。 受控磁碟會連結至訂單建立時所指定的資源群組。 

    - 如果您已成功複製到 Azure 中的受控磁碟，則可前往 Azure 入口網站中的 [訂單詳細資料]  ，並記下針對受控磁碟所指定的資源群組。

        ![識別受控磁碟資源群組](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        前往所記下的資源群組，並找出您的受控磁碟。

        ![連結至資源群組的受控磁碟](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - 如果您複製了 VHDX 或動態/差異 VHD，則會將 VHDX/VHD 上傳至暫存的儲存體帳戶以作為分頁 Blob，但將 VHD 轉換為受控磁碟會失敗。 移至您暫存的 [儲存體帳戶] > [Blob]  ，然後選取適當的容器：標準 SSD、標準 HDD 或進階 SSD。 VHD 會以分頁 Blob 形式上傳到暫存的儲存體帳戶。

## <a name="erasure-of-data-from-data-box"></a>清除資料箱的資料
 
完成上傳到 Azure 之後，資料箱會根據 [NIST SP 800-88 修訂 1 指導方針](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)清除磁碟中的資料。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱的相關主題，像是：

> [!div class="checklist"]
> * 必要條件
> * 準備寄送
> * 將資料箱寄送給 Microsoft
> * 確認資料上傳至 Azure
> * 清除資料箱的資料

請前往下列文章，了解如何透過本機 Web UI 管理資料箱。

> [!div class="nextstepaction"]
> [使用本機 Web UI 來管理 Azure 資料箱](./data-box-local-web-ui-admin.md)


