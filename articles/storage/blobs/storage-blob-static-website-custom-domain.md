---
title: 教學課程：使用 Azure CDN 在靜態網站上啟用與 SSL 搭配運作的自訂網域 - Azure Storage
description: 了解如何為靜態網站代管設定自訂網域。
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: 4b074c9dee93ba44659b0321ae3eee7fbea1c61f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145046"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>教學課程：使用 Azure CDN 為靜態網站啟用與 SSL 搭配運作的自訂網域

本教學課程是一個系列的第二部分。 在此部分中，您會了解如何為靜態網站啟用與 SSL 搭配運作的自訂網域。 

本教學課程說明如何使用 [Azure CDN](../../cdn/cdn-overview.md) 為您的靜態網站設定自訂網域端點。 藉由 Azure CDN，您將可同時佈建自訂 SSL 憑證、使用自訂網域，以及設定自訂重寫規則。 設定 Azure CDN 會產生額外的費用，但可為從世界各地到您網站的連線提供一致的低延遲。 Azure CDN 也可利用您自己的憑證提供 SSL 加密。 如需有關 Azure CDN 的定價資訊，請參閱 [Azure CDN 定價](https://azure.microsoft.com/pricing/details/cdn/)。

在本系列的第二部分中，您將瞭解如何：

> [!div class="checklist"]
> * 在靜態網站端點上建立 CDN 端點
> * 啟用自訂網域和 SSL

## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前，請先完成第一部分[教學課程：將靜態網站裝載於 Blob 儲存體上](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)以開始進行操作。

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>在靜態網站端點上建立 CDN 端點

1. 在網頁瀏覽器中開啟 [Azure 入口網站](https://portal.azure.com/)。 
1. 找出您的儲存體帳戶，然後顯示帳戶概觀。
1. 選取 [Blob 服務] 功能表底下的 [Azure CDN] 以設定 Azure CDN。
1. 在 [新增端點] 區段中，填寫欄位以建立新的 CDN 端點。
1. 輸入端點名稱，例如 *mystaticwebsiteCDN*。
1. 輸入您的網站網域作為 CDN 端點的主機名稱。
1. 針對來源主機名稱，輸入您的靜態網站端點。 若要尋找您的靜態網站端點，請瀏覽至您儲存體帳戶的 [靜態網站] 區段，然後複製該端點。 
1. 在瀏覽器中瀏覽至 *mywebsitecdn.azureedge.net* 以測試您的 CDN 端點。

## <a name="enable-custom-domain-and-ssl"></a>啟用自訂網域和 SSL

1. 向您的網域名稱提供者建立一個 CNAME 記錄，以將自訂網域重新導向到 CDN 端點。 *www* 子網域的 CNAME 記錄應該與以下類似：

    ![為 www 子網域指定 CNAME 記錄](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. 在 Azure 入口網站中，按一下新建立的端點以設定自訂網域和 SSL 憑證。
1. 選取 [新增自訂網域] 並輸入您的網域名稱，然後按一下 [新增]。
1. 選取新建立的自訂網域對應以佈建 SSL 憑證。
1. 將 [自訂網域 HTTPS] 設定為 [開啟]。 選取 [由 CDN 管理]以讓 Azure CDN 管理您的 SSL 憑證。 按一下 [檔案] 。
1. 存取網站 URL 來測試您的網站。

## <a name="next-steps"></a>後續步驟

在本教學課程的第二部分中，您已了解如何在 Azure CDN 中為靜態網站設定與 SSL 搭配運作的自訂網域。

請依循此連結來深入了解「Azure 儲存體」上的靜態網站代管。

> [!div class="nextstepaction"]
> [深入了解靜態網站](storage-blob-static-website.md)
