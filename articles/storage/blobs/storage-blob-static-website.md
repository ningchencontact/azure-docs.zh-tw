---
title: Azure 儲存體中的靜態網站託管 (預覽) | Microsoft Docs
description: Azure 儲存體現在推出靜態網站託管 (預覽)，提供符合成本效益且可延展的解決方案來託管新式 Web 應用程式。
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 06/26/18
ms.author: mihauss
ms.openlocfilehash: df1661b5fe7a2c0e37deef5259d6b5842ed6ee5e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131604"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Azure 儲存體中的靜態網站託管 (預覽)
Azure 儲存體現在推出靜態網站託管 (預覽)，讓您可以在 Azure 上部署符合成本效益且可延展的新式 Web 應用程式。 在靜態網站中，網頁會包含靜態內容和 JavaScript，或是其他用戶端程式碼。 相反地，動態網站取決於伺服器端程式碼，並可使用 [Azure Web Apps](/app-service/app-service-web-overview.md) 來託管。

當部署開始轉移到彈性化且符合成本效益的模型時，不需要管理伺服器即可傳遞 Web 內容的功能顯得相當重要。 在 Azure 儲存體中引進靜態網站託管即可實現此目標，並透過運用 [Azure Functions](/azure-functions/functions-overview.md) 和其他 PaaS 服務的無伺服器架構，來啟用豐富的後端功能。

## <a name="how-does-it-work"></a>運作方式
當您在儲存體帳戶上啟用靜態網站時，新的 Web 服務端點會以以下形式建立：`<account-name>.<zone-name>.web.core.windows.net`。

Web 服務端點一律允許匿名的讀取存取，並會傳回格式化的 HTML 網頁以回應服務錯誤，以及只允許物件讀取作業。 Web 服務端點會傳回所要求目錄中根目錄和所有子目錄的索引文件。 當儲存體服務傳回 404 錯誤時，Web 端點會傳回自訂錯誤文件 (如果您有設定的話)。

靜態網站的內容會託管於名為 "$web" 的特殊容器中。 作為啟用程序的一部分，如果尚未建立 "$web"，系統會為您建立一個。 "$web" 中的內容可以使用 Web 端點在帳戶根目錄中加以存取。 例如，`https://contoso.z4.web.core.windows.net/` 會傳回您為網站設定的索引文件，前提是 $web 的根目錄中有該名稱的文件。

將內容上傳至您的網站時，請使用 Blob 儲存體端點。 若要上傳可在帳戶根目錄存取且名為 'image.jpg' 的 Blob，請使用下列 URL `https://contoso.blob.core.windows.net/$web/image.jpg` 。 您可以在網頁瀏覽器中檢視上傳的影像，位於對應的 Web 端點 `https://contoso.z4.web.core.windows.net/image.jpg` 上。


## <a name="custom-domain-names"></a>自訂網域名稱
您可以使用自訂網域來託管您的 Web 內容。 若要這麼做，請遵循[針對 Azure 儲存體帳戶設定自訂網域名稱](storage-custom-domain-name.md)中的指引。 若要透過 HTTPS 存取在自訂網域名稱上託管的網站，請參閱[使用 Azure CDN 透過 HTTPS 以自訂網域存取 Blob](storage-https-custom-domain-cdn.md)。

## <a name="pricing-and-billing"></a>價格和計費
靜態網站託管是免費提供的。 如需 Azure Blob 儲存體價格的詳細資訊，請參閱 [Azure Blob 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="quickstart"></a>快速入門
### <a name="azure-portal"></a>Azure 入口網站
若要開始在 Azure 儲存體上託管 Web 應用程式，您可以使用 Azure 入口網站設定此功能，並在左側導覽列中的 [設定] 下，按一下 [靜態網站 (預覽)]。 按一下 [啟用]，然後輸入索引文件的名稱，以及選擇性地輸入自訂錯誤文件的路徑。

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

將 Web 資產上傳至 "$web" 容器，此容器是在靜態網站啟用程序中建立的。 您可以直接在 Azure 入口網站中執行此作業，或是利用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)來上傳整個目錄結構。 請務必使用您設定的名稱來包含索引文件。 在此範例中，文件的名稱是 "index.html"。

> [!NOTE]
> 文件名稱會區分大小寫，因此必須完全相符儲存體中的檔案名稱。

最後，瀏覽至您的 Web 端點，以測試您的網站。

## <a name="faq"></a>常見問題集
**靜態網站是否適用於所有的儲存體帳戶類型？**  
不是，靜態網站託管僅適用於 GPv2 標準儲存體帳戶。

**新的 Web 端點上支援儲存體 VNET 和防火牆規則嗎？**  
是，新的 Web 端點會遵守為儲存體帳戶設定的 VNET 和防火牆規則。

## <a name="next-steps"></a>後續步驟
* [使用 Azure CDN 透過 HTTP 以自訂網域存取 blob](storage-https-custom-domain-cdn.md)
* [針對 Blob 或 Web 端點設定自訂網域名稱](storage-custom-domain-name.md)
* [Azure Functions](/azure-functions/functions-overview.md)
* [Azure Web Apps](/app-service/app-service-web-overview.md)
* [建置您的第一個無伺服器 Web 應用程式](https://aka.ms/static-serverless-webapp)
