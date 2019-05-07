---
title: Azure 儲存體中的靜態網站代管
description: Azure 儲存體靜態網站代管，提供符合成本效益且可延展的解決方案來代管新式 Web 應用程式。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.reviewer: seguler
ms.date: 04/29/2019
ms.subservice: blobs
ms.openlocfilehash: cd1fa71cb2a10c7e61f76bdd224ba6d0f039346f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148462"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure 儲存體中的靜態網站代管
Azure 儲存體 GPv2 帳戶可讓您直接從名為 *$web* 的儲存體容器提供靜態內容 (HTML、CSS、JavaScript 和影像檔)。 利用 Azure 儲存體中的代管，讓您能夠使用無伺服器架構，包括 [Azure Functions](/azure/azure-functions/functions-overview) 和其他 PaaS 服務。

相較於靜態網站代管，取決於伺服器端程式碼的動態網站最適合使用 [Azure App Service](/azure/app-service/overview) 來代管。

## <a name="how-does-it-work"></a>運作方式
當您在儲存體帳戶中啟用靜態網站代管時，請選取您的預設檔案名稱，並選擇性地提供自訂 404 頁面的路徑。 啟用此功能時，即會建立名為 *$web* 的容器 (如果尚未存在)。

*$web* 容器中的檔案：

- 透過匿名存取要求來提供
- 僅能透過物件讀取作業來取得
- 區分大小寫
- 適用於遵循下列模式的公用網路：
    - `https://<ACCOUNT_NAME>.<ZONE_NAME>.web.core.windows.net/<FILE_NAME>`
- 可透過遵循下列模式的 Blob 儲存體端點來取得：
    - `https://<ACCOUNT_NAME>.blob.core.windows.net/$web/<FILE_NAME>`

您可以使用 Blob 儲存體端點來上傳檔案。 例如，已上傳到下列位置的檔案：

```bash
https://contoso.blob.core.windows.net/$web/image.png
```

可在瀏覽器中取得，位置如下：

```bash
https://contoso.z4.web.core.windows.net/image.png
```

未提供檔案名稱時，即會在根目錄和所有子目錄中使用選取的預設檔案名稱。 如果伺服器傳回 404，而您未提供錯誤文件路徑，則會將預設的 404 頁面傳回給使用者。

> [!NOTE]
> 檔案的預設公用存取層級是私用。 因為檔案會提供透過匿名存取要求，則會忽略此設定。 沒有公用存取權的所有檔案，並會忽略 RBAC 權限。

## <a name="cdn-and-ssl-support"></a>CDN 和 SSL 支援

若要透過您的自訂網域和 HTTPS 進行靜態網站檔案，請參閱[使用 Azure CDN 透過 HTTPS 以自訂網域存取 blob](storage-https-custom-domain-cdn.md)。 在此程序的過程中，您需要*將 CDN 指向 Web 端點*，而不是 Blob 端點。 您可能需要等候數分鐘，然後才會顯示您的內容，因為 CDN 設定不會立即執行。

當您更新您的靜態網站時，請務必清除 CDN 端點來清除快取在 CDN edge server 上的內容。 如需詳細資訊，請參閱[清除 Azure CDN 端點](../../cdn/cdn-purge-endpoint.md)。

> [!NOTE]
> 透過帳戶的 web 端點原本就支援 HTTPS。 使用透過 HTTPS 的自訂網域需要這一次的 Azure CDN 使用。 
>
> 透過 HTTPS 公開帳戶 web 端點： `https://<ACCOUNT_NAME>.<ZONE_NAME>.web.core.windows.net/<FILE_NAME>`

## <a name="custom-domain-names"></a>自訂網域名稱

您可以[設定 Azure 儲存體帳戶的自訂網域名稱](storage-custom-domain-name.md)，透過自訂網域來使您的靜態網站可供使用。 若要深入了解如何在 Azure 中裝載您的網域，請參閱[在 Azure DNS 中裝載您的網域](../../dns/dns-delegate-domain-azure-dns.md)。

## <a name="pricing"></a>價格
啟用靜態網站代管是免費。 客戶需支付使用的 blob 儲存體和作業成本。 如需 Azure Blob 儲存體價格的詳細資訊，請參閱 [Azure Blob 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="quickstart"></a>快速入門

### <a name="azure-portal"></a>Azure 入口網站
一開始，在 https://portal.azure.com 中開啟 Azure 入口網站，然後在 GPv2 儲存體帳戶上執行下列步驟：

1. 按一下 [設定]
2. 按一下 [靜態網站]
3. 輸入「索引文件名稱」 (常用的值是 *index.html)*。
4. (選擇性) 輸入連至自訂 404 頁面的「錯誤文件路徑」 (常用的值是 *404.html)*。

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

接下來，透過 Azure 入口網站，將您的資產上傳到 *$web* 容器，或使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)來上傳整個目錄。 請務必包含符合您在啟用此功能時所選取之「索引文件名稱」的檔案。

最後，瀏覽至您的 Web 端點，以測試您的網站。

### <a name="azure-cli"></a>Azure CLI
安裝儲存體預覽延伸模組：

```azurecli-interactive
az extension add --name storage-preview
```
如果有多個訂用帳戶，請將您的 CLI 設定為您想要啟用之 GPv2 儲存體帳戶的訂用帳戶：

```azurecli-interactive
az account set --subscription <SUBSCRIPTION_ID>
```
啟用此功能。 請務必將所有的預留位置值 (包含括號) 取代為您自己的值：

```azurecli-interactive
az storage blob service-properties update --account-name <ACCOUNT_NAME> --static-website --404-document <ERROR_DOCUMENT_NAME> --index-document <INDEX_DOCUMENT_NAME>
```
Web 端點 URL 的查詢：

```azurecli-interactive
az storage account show -n <ACCOUNT_NAME> -g <RESOURCE_GROUP> --query "primaryEndpoints.web" --output tsv
```

將物件從來源目錄上傳至 *$web* 容器。 請務必使用命令，將參考適當地逸出至 *$web* 容器。 例如，如果您在 Azure 入口網站中使用來自 CloudShell 的 Azure CLI，會逸出 *$web* 容器，如下所示：

```azurecli-interactive
az storage blob upload-batch -s <SOURCE_PATH> -d \$web --account-name <ACCOUNT_NAME>
```

## <a name="deployment"></a>部署

以下是可用來將內容部署至儲存體容器的方法：

- [AzCopy](../common/storage-use-azcopy.md)
- [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)
- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
- [Visual Studio Code 擴充功能](https://code.visualstudio.com/tutorials/static-website/getting-started) \(英文\)

在所有情況下，都要確定您會將檔案複製到 *$web* 容器。

## <a name="metrics"></a>指标

若要在您的靜態網站頁面上啟用計量，請按一下 [設定] > [監視] > [計量]。

藉由連結到不同的計量 API 來產生計量資料。 入口網站只會顯示指定時間範圍內所使用的 API 成員，以便只專注於傳回資料的成員。 為了確保您能夠選取所需的 API 成員，第一個步驟是展開時間範圍。

按一下時間範圍按鈕、選取 [最近 24 小時]，然後按一下 [套用]

![Azure 儲存體靜態網站計量的時間範圍](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

接下來，從 [命名空間] 下拉式清單中選取 [Blob]。

![Azure 儲存體靜態網站計量的命名空間](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

然後選取 [輸出] 計量。

![Azure 儲存體靜態網站計量的計量](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

從 [彙總] 選取器中選取 [總和]。

![Azure 儲存體靜態網站計量的彙總](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

接下來，按一下 [新增篩選] 按鈕，然後從 [屬性] 選取器中選擇 [API 名稱]。

![Azure 儲存體靜態網站計量的 API 名稱](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

最後，在 [值] 選取器中勾選 [GetWebContent] 旁的方塊，以填入計量報表。

![Azure 儲存體靜態網站計量的 GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

啟用之後，計量儀表板中會報告與 *$web* 容器中檔案有關的流量統計資料。

## <a name="faq"></a>常見問題集

**此靜態網站功能是否適用於所有的儲存體帳戶類型？**  
不是，靜態網站託管僅適用於 GPv2 標準儲存體帳戶。

**新的 Web 端點上支援儲存體 VNET 和防火牆規則嗎？**  
是，新的 Web 端點會遵守為儲存體帳戶設定的 VNET 和防火牆規則。

**Web 端點會區分大小寫嗎？**  
是，Web 端點與 Blob 端點一樣，都會區分大小寫。

**是 web 端點可透過 HTTP 和 HTTPS 存取？**
Web 端點是可透過 HTTP 和 HTTPS 存取。 不過，如果儲存體帳戶設定為透過 HTTPS 要求使用安全傳輸，然後使用者必須使用 HTTPS 端點。 如需詳細資訊，請參閱 <<c0> [ 需要 Azure 儲存體中的安全傳輸](../common/storage-require-secure-transfer.md)。

## <a name="next-steps"></a>後續步驟
* [使用 Azure CDN 透過 HTTP 以自訂網域存取 blob](storage-https-custom-domain-cdn.md)
* [針對 Blob 或 Web 端點設定自訂網域名稱](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [建置您的第一個無伺服器 Web 應用程式](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [教學課程：在 Azure DNS 上託管您的網域](../../dns/dns-delegate-domain-azure-dns.md)
