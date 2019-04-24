---
title: 使用 Azure CDN 透過 HTTPS 以自訂網域存取 Blob
description: 了解如何整合 Azure CDN 與 Blob 儲存體，以透過 HTTPS 使用自訂網域來存取 Blob
services: storage
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.subservice: blobs
ms.openlocfilehash: 90ddb58f3499180e17df559a98ac8a46b53fb824
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392515"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>使用 Azure CDN 透過 HTTPS 以自訂網域存取 Blob

「Azure 內容傳遞網路」(Azure CDN) 現在針對自訂網域名稱支援使用 HTTPS。 使用 Azure CDN 時，您可以透過 HTTPS 使用自訂網域名稱來存取 Blob。 若要這樣做，請在您的 Blob 或 Web 端點上啟用 Azure CDN，然後將 Azure CDN 對應至自訂網域名稱。 完成後，Azure 會透過單鍵存取及完整的憑證管理，簡化為您的自訂網域啟用 HTTPS 的程序。 在一般 Azure CDN 定價方面沒有任何增加。

Azure CDN 可在您的 Web 應用程式資料進行傳輸時，協助保護該資料的隱私性和資料完整性。 Azure CDN 會藉由使用 SSL 通訊協定透過 HTTPS 提供流量，讓您的資料在網際網路上傳送時保持加密。 搭配 Azure CDN 使用 HTTPS 有助於防止您的 Web 應用程式遭受攻擊。

> [!NOTE]  
> 除了為自訂網域名稱提供 SSL 支援之外，Azure CDN 還可協助您調整應用程式規模，以在全球各地提供高頻寬的內容。 若要深入了解，請參閱 [Azure CDN 概觀](../../cdn/cdn-overview.md)。

## <a name="quickstart"></a>快速入門

若要為您的自訂 Blob 儲存體端點啟用 HTTPS，請執行下列操作：

1.  [整合 Azure 儲存體帳戶與 Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md)。  
    如果您還沒有在 Azure 入口網站中建立儲存體帳戶，本文將逐步引導您建立該帳戶。

    > [!NOTE]  
    > 在「Azure 儲存體」中的靜態網站支援預覽期間，若要新增儲存體 Web 端點，請從 [原始來源類型] 下拉式清單中選取 [自訂原始來源]。 在 Azure 入口網站中，您必須從 Azure CDN 設定檔中執行此操作，而不是直接在儲存體帳戶中執行。

2.  [將 Azure CDN 內容對應至自訂網域](../../cdn/cdn-map-content-to-custom-domain.md)。

3.  [在 Azure CDN 自訂網域上啟用 HTTPS](../../cdn/cdn-custom-ssl.md)。

## <a name="shared-access-signatures"></a>共用存取簽章

Blob 儲存體端點預設不允許匿名讀取權限。 如果您的 Blob 儲存體端點已設定為不允許匿名讀取權限，請在對您自訂網域提出的每個要求中提供[共用存取簽章](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)權杖。 如需詳細資訊，請參閱 [管理對容器與 Blob 的匿名讀取權限](storage-manage-access-to-resources.md)。

Azure CDN 並不遵守加諸於共用存取簽章權杖的任何限制。 例如，所有共用存取簽章權杖都會過期。 您仍然可以使用過期的共用存取簽章來存取內容，直到該內容從 Azure CDN 邊緣節點上被清除為止。 您可以設定快取回應標頭，以控制可在 Azure CDN 上快取資料多久時間。 若要了解如何進行，請參閱[在 Azure CDN 中管理 Azure Blob 儲存體的到期](../../cdn/cdn-manage-expiration-of-blob-content.md)。

如果您為同一個 Blob 端點建立兩個以上的共用存取簽章 URL，建議您為 Azure CDN 開啟查詢字串快取功能。 此動作可確保 Azure 將每個 URL 都視為唯一實體。 如需詳細資訊，請參閱[使用查詢字串控制 Azure CDN 快取行為](../../cdn/cdn-query-string.md)。

## <a name="http-to-https-redirection"></a>HTTP 至 HTTPS 的重新導向

您可以將 HTTP 流量重新導向到 HTTPS。 若要這樣做，必須使用來自 Verizon 的 Azure CDN 進階供應項目。 請藉由套用下列規則來[使用 Azure CDN 規則引擎覆寫 HTTP 行為](../../cdn/cdn-rules-engine.md)：

![HTTP 到 HTTPS 重新導向規則](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

您從下拉式清單中選取的 *Cdn-endpoint-name* 係指您為 Azure CDN 端點設定的名稱。 *Origin-path* 係指您原始來源儲存體帳戶內儲存靜態內容的路徑。 如果您將所有靜態內容都裝載在單一容器中，則請以該容器的名稱取代 *origin-path*。

如需深入了解規則，請參閱 [Azure CDN 規則引擎功能](../../cdn/cdn-rules-engine-reference-features.md)。

## <a name="pricing-and-billing"></a>價格和計費

當您透過 Azure CDN 存取 Blob 時，針對邊緣節點與原始來源 (Blob 儲存體) 之間的流量，需支付 [Blob 儲存體價格](https://azure.microsoft.com/pricing/details/storage/blobs/)。 針對從邊緣節點存取的資料，則需支付 [Azure CDN 價格](https://azure.microsoft.com/pricing/details/cdn/)。

例如，假設您在美國西部有一個透過 Azure CDN 存取的儲存體帳戶。 當有人在英國嘗試透過 Azure CDN 存取該儲存體帳戶中的 Blob 時，Azure 會先檢查最接近英國的邊緣節點是否有該 Blob。 如果 Azure 找到該 Blob，就會存取一個複本並使用 Azure CDN 定價，因為 Azure CDN 正在存取它。 如果 Azure 找不到該 Blob，則會將該 Blob 複製到邊緣節點。 此動作會產生如 Blob 儲存體定價中所指定的輸出和交易費用。 接著，Azure 會存取邊緣節點上的檔案而產生 Azure CDN 計費。

在 [Azure CDN 價格頁面](https://azure.microsoft.com/pricing/details/cdn/)上，自訂網域名稱的 HTTPS 支援只適用於來自 Verizon「標準」和「進階」產品的 Azure CDN。

## <a name="next-steps"></a>後續步驟

* [針對 Blob 儲存體端點設定自訂網域名稱](storage-custom-domain-name.md)
* [Azure 儲存體中的靜態網站代管](storage-blob-static-website.md)
