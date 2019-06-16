---
title: Azure 儲存體中的靜態網站代管
description: Azure 儲存體靜態網站代管，提供符合成本效益且可延展的解決方案來代管新式 Web 應用程式。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.reviewer: seguler
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 36cc8cebdb567cb9650ad1ad3baf72a0b5478247
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427974"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure 儲存體中的靜態網站代管

您可以提供靜態內容 （HTML、 CSS、 JavaScript 和影像檔），直接從儲存體容器，名為 *$web*。 裝載您 Azure 儲存體中的內容可讓您使用無伺服器架構，包括[Azure Functions](/azure/azure-functions/functions-overview)和其他平台即服務 (PaaS) 服務。

> [!NOTE]
> 如果您的網站取決於伺服器端程式碼，請使用[Azure App Service](/azure/app-service/overview)改。

## <a name="setting-up-a-static-website"></a>靜態網站設定

靜態網站代管是一項功能，您必須啟用儲存體帳戶。

若要啟用靜態網站代管，選取您的預設檔案名稱，然後選擇性地提供自訂的 404 網頁的路徑。 如果名為 blob 儲存體容器 **$web**還沒有在帳戶中，會為您建立一個。 將您的站台檔案新增到這個容器。

如需逐步指導，請參閱 <<c0> [ 裝載在 Azure 儲存體中的靜態網站](storage-blob-static-website-how-to.md)。

![Azure 儲存體靜態網站計量的計量](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

中的檔案 **$web**容器會區分大小寫，提供透過匿名存取要求，並且可用於只透過讀取作業。

## <a name="uploading-content"></a>上傳內容

您可以使用任何一種工具來上傳的內容 **$web**容器：

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell 模組](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code 擴充功能](https://code.visualstudio.com/tutorials/static-website/getting-started) \(英文\)

## <a name="viewing-content"></a>檢視內容

使用者可以使用公用網站 URL，以檢視從瀏覽器的網站內容。 您可以使用 Azure 入口網站、 Azure CLI 或 PowerShell，找到的 URL。 請使用下表作為指南。

|工具| 指引 |
|----|----|
|**Azure 入口網站** | [使用 Azure 入口網站來尋找網站 URL](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [使用 Azure CLI 來尋找網站 URL](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell 模組** | [使用 PowerShell 來尋找網站 URL](storage-blob-static-website-how-to.md#powershell-find-url) |

您網站的 URL 會包含區域的程式碼。 例如 URL`https://contosoblobaccount.z22.web.core.windows.net/`包含區域的程式碼`z22`。

該程式碼必須保留 URL，它是僅供內部使用，而您不需要任何其他方式使用該程式碼。

使用者開啟網站，且未指定特定的檔案時，會出現您指定當您啟用靜態網站代管，索引文件 (例如： `https://contosoblobaccount.z22.web.core.windows.net`)。  

如果伺服器傳回 404 錯誤，而且您沒有指定文件時發生錯誤，當您啟用網站，則會傳回使用者預設 404 頁面。

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>影響設定的 web 容器的公用存取層級

您可以修改公用存取層級 **$web**容器，但這沒有任何影響主要靜態網站端點因為這些檔案會提供透過匿名存取要求。 這表示公用的 （唯讀） 存取權的所有檔案。

下列螢幕擷取畫面會顯示在 Azure 入口網站中的公用存取層級設定：

![螢幕擷取畫面顯示如何在入口網站中設定公用存取層級](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

雖然不會影響主要靜態網站端點，公用存取層級變更會影響主要 blob 服務端點。

例如，如果您變更的公用存取層級 **$web**從容器**私用 （沒有匿名存取）** 來**Blob （匿名讀取權限僅限 blob）** ，則主要靜態網站端點的公用存取層級`https://contosoblobaccount.z22.web.core.windows.net/index.html`不會變更。

不過，公用存取為主要 blob 服務端點`https://contosoblobaccount.blob.core.windows.net/$web/index.html`並從私用變更為 public。 現在使用者可以使用這兩個端點的其中一種開啟該檔案。

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>內容傳遞網路 (CDN) 及安全通訊端層 (SSL) 支援

若要透過您的自訂網域和 HTTPS 進行靜態網站檔案，請參閱[使用 Azure CDN 透過 HTTPS 以自訂網域存取 blob](storage-https-custom-domain-cdn.md)。 此程序的一部分，您需要讓 CDN 指向主要*靜態網站*端點，而不是主要*blob 服務*端點。 您可能需要等候幾分鐘的時間之前，您的內容是可見的因為 CDN 組態不會立即執行。

當您更新您的靜態網站時，請務必清除 CDN 端點來清除快取在 CDN edge server 上的內容。 如需詳細資訊，請參閱[清除 Azure CDN 端點](../../cdn/cdn-purge-endpoint.md)。

> [!NOTE]
> HTTPS 原本就支援透過帳戶的 web 端點，因此 web 端點是可透過 HTTP 和 HTTPS 存取。 不過，如果儲存體帳戶設定為透過 HTTPS 要求使用安全傳輸，然後使用者必須使用 HTTPS 端點。 如需詳細資訊，請參閱 <<c0> [ 需要 Azure 儲存體中的安全傳輸](../common/storage-require-secure-transfer.md)。
>
> 使用透過 HTTPS 的自訂網域需要這一次的 Azure CDN 使用。

## <a name="custom-domain-names"></a>自訂網域名稱

您可以提供您的靜態網站透過自訂網域。 若要進一步了解，請參閱[設定您的 Azure 儲存體帳戶的自訂網域名稱](storage-custom-domain-name.md)。

深入了解將網域裝載於 Azure，請參閱 <<c0> [ 裝載在 Azure DNS 中的網域](../../dns/dns-delegate-domain-azure-dns.md)。

## <a name="pricing"></a>價格

您可以啟用靜態網站代管免費。 您只需要支付您的網站使用的 blob 儲存體和作業成本。 如需 Azure Blob 儲存體價格的詳細資訊，請參閱 [Azure Blob 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="metrics"></a>度量

您可以啟用靜態網站頁面上的度量。 一旦您啟用度量，流量統計資料中的檔案 **$web**報告計量儀表板中的容器。

若要啟用您的靜態網站頁面上的度量，請參閱[啟用靜態網站頁面上的度量](storage-blob-static-website-how-to.md#metrics)。

## <a name="next-steps"></a>後續步驟

* [裝載在 Azure 儲存體中的靜態網站](storage-blob-static-website-how-to.md)
* [使用 Azure CDN 透過 HTTPS 以自訂網域存取 blob](storage-https-custom-domain-cdn.md)
* [針對 Blob 或 Web 端點設定自訂網域名稱](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [建置您的第一個無伺服器 Web 應用程式](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [教學課程：在 Azure DNS 上託管您的網域](../../dns/dns-delegate-domain-azure-dns.md)
