---
title: "請確認儲存體帳戶在 Azure 入口網站的輸送量和延遲度量 |Microsoft 文件"
description: "了解如何驗證在入口網站的儲存體帳戶的輸送量和延遲度量資訊。"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: b3102bd4e40e10fe88c12295794da37e359c56f1
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>請確認儲存體帳戶的輸送量和延遲計量

本教學課程是系列課程的第四個部分，也是最後一個部分。 在上一個教學課程中，您學到如何上傳和下載 larges 少量的隨機的資料到 Azure 儲存體帳戶。 本教學課程會示範如何使用度量，以及在 Azure 入口網站中檢視輸送量和延遲。

在本系列的第一部分中，您了解如何：

> [!div class="checklist"]
> * 在 Azure 入口網站中設定圖表
> * 驗證輸送量和延遲計量

[Azure 儲存體度量](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)使用 Azure 監視來提供統一的檢視，造成效能和可用性的儲存體帳戶。

## <a name="configure-metrics"></a>設定計量

瀏覽至**度量 （預覽）**下**設定**儲存體帳戶中。

選擇 Blob 來自**子服務**下拉式清單。

在下**度量**，選取其中一個在下表中找到的度量：

下列度量資訊，可讓您了解的延遲和輸送量的應用程式。 在入口網站設定的度量資訊會在 1 分鐘的平均值。 如果中間一分鐘的時間完成的交易則 halfed 平均該分鐘的資料。 在應用程式上, 傳和下載作業已逾時，提供您的實際時間量的輸出所需上傳和下載檔案。 這項資訊可以用於搭配入口網站的度量，以充分了解輸送量。

|計量|定義|
|---|---|
|**成功 E2E 延遲**|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|
|**成功伺服器延遲**|Azure 儲存體用來處理成功要求的平均時間。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。 |
|**交易**|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 在此範例中，區塊大小設為 100 MB。 在此情況下，每個 100 MB 的區塊視為交易。|
|**輸入**|輸入資料量。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。 |
|**輸出**|輸出資料量。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。 |

選取**過去 24 小時 （自動）**旁**時間**。 選擇**過去一小時內**和**分鐘**如**時間資料粒度**，然後按一下 **套用**。

![儲存體帳戶計量](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

圖表可以具有多個計量指派給他們，但是指派多個度量會停用由維度的群組的能力。

## <a name="dimensions"></a>維度

[維度](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions)可用來查看更深入的圖表，並取得更詳細的資訊。 不同的度量資訊會有不同的維度。 所提供的一維度是**API 名稱**維度。 此維度會為每個個別的應用程式開發介面呼叫圖表時中斷。 第一個圖顯示範例圖表的儲存體帳戶的交易總數。 第二個影像會顯示相同的圖表，但 API 與選取的維度命名。 如您所見，會列出每一筆交易，到多少個呼叫所產生的 API 名稱中，提供更多詳細資料。

![儲存體帳戶計量不含維度的交易](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![儲存體帳戶計量交易](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請選取 VM 資源群組並按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在四個數列，您會學檢視範例方案，例如如何的度量：

> [!div class="checklist"]
> * 在 Azure 入口網站中設定圖表
> * 驗證輸送量和延遲計量

遵循以下連結以查看預先建立的儲存體範例。

> [!div class="nextstepaction"]
> [Azure 儲存體指令碼範例](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md