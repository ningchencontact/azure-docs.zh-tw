---
title: 教學課程：使用 Azure CDN 在靜態網站上啟用與 SSL 搭配運作的自訂網域 - Azure Storage
description: 了解如何為靜態網站代管設定自訂網域。
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: 2b0bb94be2ba8ea983cda8fd015d05fcd532f2bc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226139"
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

1. 在 Azure 入口網站中找出您的儲存體帳戶，然後顯示帳戶概觀。
1. 選取 [Blob 服務]  功能表底下的 [Azure CDN]  以設定 Azure CDN。
1. 在 [CDN 設定檔]  區段中，指定新的或現有的 CDN 設定檔。 如需詳細資訊，請參閱[快速入門：建立 Azure CDN 設定檔和端點](../../cdn/cdn-create-new-endpoint.md)。
1. 指定 CDN 端點的定價層。 本教學課程會使用**標準 Akamai** 定價層，此層的傳播速度快，通常在幾分鐘內就能完成。 其他定價層可能需要較久的傳播時間，但也可能提供其他優點。 如需詳細資訊，請參閱[比較 Azure CDN 產品功能](../../cdn/cdn-features.md)。
1. 在 [CDN 端點名稱]  欄位中，指定 CDN 端點的名稱。 CDN 端點在整個 Azure 中必須是唯一的。
1. 在 [原始主機名稱]  欄位中指定靜態網站端點。 若要尋找靜態網站端點，請瀏覽至儲存體帳戶的 [靜態網站]  設定。 複製主要端點，然後將其貼至 CDN 設定中，並移除通訊協定識別碼 (例如  ，HTTPS)。

    下圖顯示端點設定範例：

    ![螢幕擷取畫面：顯示 CDN 端點設定範例](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. 建立 CDN 端點，並等待其進行傳播。
1. 為了確認 CDN 端點是否已正確設定，請按一下端點以瀏覽至其設定。 從儲存體帳戶的 CDN 概觀，找出端點主機名稱，並瀏覽到端點，如下圖所示。 CDN 端點的格式會類似 `https://staticwebsitesamples.azureedge.net`。

    ![螢幕擷取畫面：顯示 CDN 端點概觀](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    CDN 端點傳播完成後，瀏覽至 CDN 端點便會顯示您先前上傳至靜態網站的 index.html 檔案內容。

1. 若要檢閱 CDN 端點的原始設定，請瀏覽至 CDN 端點 [設定]  區段底下的 [原始來源]  。 您會看到 [原始來源類型]  欄位設定為 [自訂原始來源]  且 [原始主機名稱]  欄位會顯示靜態網站端點。

    ![螢幕擷取畫面：顯示 CDN 端點的原始來源設定](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>啟用自訂網域和 SSL

1. 向您的網域名稱提供者建立一個 CNAME 記錄，以將自訂網域重新導向到 CDN 端點。 *www* 子網域的 CNAME 記錄應該與以下類似：

    ![為 www 子網域指定 CNAME 記錄](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. 在 Azure 入口網站中，顯示 CDN 端點的設定。 瀏覽至 [設定]  底下的 [自訂網域]  來設定自訂網域和 SSL 憑證。
1. 選取 [新增自訂網域]  並輸入您的網域名稱，然後按一下 [新增]  。
1. 選取新的自訂網域對應以佈建 SSL 憑證。
1. 將 [自訂網域 HTTPS]  設定為 [開啟]  ，然後按一下 [儲存]  。 可能需要數小時才能設定好自訂網域。 入口網站會顯示進度，如下圖所示。

    ![螢幕擷取畫面：顯示自訂網域設定的進度](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. 存取自訂網域的 URL，以測試靜態網站對自訂網域的對應。

如需如何為自訂網域啟用 HTTPS 的詳細資訊，請參閱[教學課程：在 Azure CDN 自訂網域上設定 HTTPS](../../cdn/cdn-custom-ssl.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程的第二部分中，您已了解如何在 Azure CDN 中為靜態網站設定與 SSL 搭配運作的自訂網域。

如需如何設定和使用 Azure CDN 的詳細資訊，請參閱[什麼是 Azure CDN？](../../cdn/cdn-overview.md)。