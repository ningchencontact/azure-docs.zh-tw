---
title: 傳回您的 Azure 資料方塊的邊緣裝置 |Microsoft Docs
description: 描述如何返回 Azure 資料方塊的邊緣裝置，並刪除裝置的順序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.openlocfilehash: 9aeae0ab68d809b36a3316054f12a5a9657721f1
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468600"
---
# <a name="return-your-azure-data-box-edge-device"></a>傳回您的 Azure 資料方塊的邊緣裝置

本文說明如何抹除資料，然後再回頭執行您的 Azure 資料方塊的邊緣裝置。 您已傳回裝置之後，您也可以刪除與裝置相關聯的資源。

在本文中，您將了解：

> [!div class="checklist"]
> * 在裝置上抹除資料的資料磁碟
> * 開啟支援票證，以傳回您的裝置
> * 封裝註冊裝置，並排定取貨時間
> * 刪除 Azure 入口網站中的資源

## <a name="erase-data-from-the-device"></a>清除裝置資料

若要清除的資料，關閉您的裝置的資料磁碟，您需要重設您的裝置。 您可以重設您的裝置使用本機 web UI 或 PowerShell 介面。

您重設之前，請視需要建立一份本機資料在裝置上。 您可以在 Azure 儲存體容器，從裝置複製資料。

若要使用本機 web UI 將裝置重設，請執行下列步驟。

1. 在本機 web UI 中，移至**維護 > 重設裝置**。
2. 選取 **重設裝置**。

    ![重設裝置](media/data-box-edge-return-device/device-reset-1.png)

3. 當提示確認，請檢閱警告，然後選取**是**以繼續。

    ![確認重設](media/data-box-edge-return-device/device-reset-2.png)  

重設清除裝置資料磁碟關閉的資料。 根據您的裝置上的資料量，此程序大約需要 30 到 40 分鐘。

或者，連線至裝置並使用的 PowerShell 介面`Reset-HcsAppliance`cmdlet 來清除資料的資料磁碟。 如需詳細資訊，請參閱 <<c0> [ 將裝置重設](data-box-edge-connect-powershell-interface.md#reset-your-device)。

> [!NOTE]
> - 如果您正在交換，或升級至新的裝置，我們建議只在收到新的裝置之後，重設您的裝置。
> - 裝置重設只會刪除所有本機的資料，關閉裝置。 在雲端中的資料不會刪除，並收集[費用](https://azure.microsoft.com/pricing/details/storage/)。 此資料必須先刪除使用之類的雲端儲存體管理工具，分別[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。

## <a name="open-a-support-ticket"></a>開啟支援票證

若要開始傳回的處理程序，請執行下列步驟。

1. 開啟支援票證，向 Microsoft 支援服務，指出您想要將裝置還原。 選取問題類型**資料方塊邊緣硬體**。

    ![開啟支援票證](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Microsoft 支援工程師會連絡您。 提供出貨詳細資料。
3. 如果您需要傳回出貨 方塊中，您可以要求它。 答案 **[是]** 問題**需要空白的方塊，以傳回**。


## <a name="schedule-a-pickup"></a>排定取貨時間

1. 關閉裝置。 在本機 Web UI 中，移至 [維護] > [電源設定]。
2. 選取 **關閉**。 當提示確認，請按一下**是**以繼續。 如需詳細資訊，請參閱 <<c0> [ 管理能力](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)。
3. 拔下電源纜線，並從裝置移除所有的網路纜線。
4. 使用您自己或您從 Azure 收到空白的方塊，以準備出貨的套件。 放置在裝置和電源線所隨附的方塊中的裝置。
5. 詞綴封裝從 Azure 收到出貨標籤。
6. 使用您區域的貨運公司排定取貨時間。 傳回位於美國的裝置，您的電訊廠商 UPS。 若要排定取貨時間：

    1. 致電給當地的 UPS (國家/地區特定的免付費電話號碼)。
    2. 在您呼叫中，加上引號反向運送追蹤號碼列印標籤所示。
    3. 如果追蹤號碼不加上引號，UPS 會要求您支付額外的費用，期間收取。

    而不是排定取貨時間，您也可以卸除關閉資料方塊邊緣最接近下車的位置。

## <a name="delete-the-resource"></a>刪除資源

在 Azure 資料中心收到裝置之後，裝置會檢查有損毀或竄改任何徵兆。

- 如果裝置到達不變，而且在良好狀態，就會停止該資源計費的計量。 Microsoft 支援服務將會連絡您以確認裝置已傳回。 此外，您就可以刪除 Azure 入口網站中的裝置相關聯的資源。
- 如果裝置到達大幅損毀，可能會套用罰金。 如需詳細資訊，請參閱 <<c0> [ 遺失或損毀的裝置上的常見問題集](https://azure.microsoft.com/pricing/details/databox/edge/)並[產品服務條款](https://www.microsoft.com/licensing/product-licensing/products)。  


您可以刪除 Azure 入口網站中的裝置：
-   您有下過訂單之後，才能在裝置準備好 microsoft。
-   您已回到 Microsoft 中的裝置之後，將實體檢查傳遞 Azure 資料中心內，並確認裝置已傳回呼叫的 Microsoft 支援服務。

如果已啟用對另一個訂用帳戶或位置的裝置，Microsoft 將一個工作天內就到新的訂用帳戶或位置移動您的訂單。 順序移動之後，您可以刪除此資源。


執行下列步驟來刪除裝置和 Azure 入口網站中的資源。

1. 在 Azure 入口網站中，移至您的資源然後**概觀**。 從命令列中，選取**刪除**。

    ![選取 [刪除]](media/data-box-edge-return-device/delete-resource-1.png)

2. 在 **刪除裝置**刀鋒視窗中，輸入您想要刪除選取的裝置名稱**刪除**。

    ![確認刪除](media/data-box-edge-return-device/delete-resource-2.png)

您會收到通知的裝置之後，已成功刪除相關聯的資源。

## <a name="next-steps"></a>後續步驟

- 了解如何[管理頻寬](data-box-edge-manage-bandwidth-schedules.md)。
