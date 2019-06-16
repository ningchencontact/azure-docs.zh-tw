---
title: 裝載在 Azure 儲存體中的靜態網站
description: 了解如何在 Azure 儲存體 GPv2 帳戶中提供直接從容器的靜態內容 （HTML、 CSS、 JavaScript 和影像檔）。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 61477767c59dd521e3f46db4445238a5a1ea759e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071445"
---
# <a name="host-a-static-website-in-azure-storage"></a>裝載在 Azure 儲存體中的靜態網站

您可以提供靜態內容 （HTML、 CSS、 JavaScript 和影像檔），直接從容器中的 Azure 儲存體 GPv2 帳戶。 若要進一步了解，請參閱[Azure 儲存體中的靜態網站代管](storage-blob-static-website.md)。

本文說明如何啟用靜態網站代管，使用 Azure 入口網站、 Azure CLI 或 PowerShell。

<a id="portal" />

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

如需逐步教學課程，請參閱[教學課程：將靜態網站裝載於 Blob 儲存體上](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

啟用靜態網站代管之後，您可以使用公用網站 URL 來檢視您的網站，從瀏覽器的頁面。

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>使用 Azure 入口網站來尋找網站 URL

在您的儲存體帳戶的帳戶概觀頁面的旁邊會顯示窗格中，選取**靜態網站**。 您網站的 URL 會出現在**主要端點**欄位。

![Azure 儲存體靜態網站計量的計量](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>使用 Azure CLI

您可以使用的靜態網站代管[Azure 命令列介面 (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

1. 首先，開啟[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)，或如果您已經[安裝](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)在本機 Azure CLI 開啟命令主控台應用程式，例如 Windows PowerShell。

2. 從您開啟 [命令] 視窗中，安裝儲存體的預覽延伸模組。

   ```azurecli-interactive
   az extension add --name storage-preview
   ```

3. 如果您的身分識別與多個訂用帳戶相關聯，然後設定您的有效訂用帳戶至訂用帳戶會裝載您的靜態網站的儲存體帳戶。

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   取代`<subscription-id>`訂用帳戶識別碼的預留位置值。

4. 啟用靜態網站代管。

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

   * 取代`<error-document-name>`預留位置取代為瀏覽器要求不存在的網站上的頁面時要對使用者顯示錯誤文件的名稱。

   * 取代`<index-document-name>`預留位置索引文件的名稱。 這份文件通常是 「 index.html"。

5. 將物件從來源目錄上傳至 *$web* 容器。

   > [!NOTE]
   > 如果您使用 Azure Cloud Shell，請務必新增`\`參考時，逸出字元`$web`容器 (例如： `\$web`)。 如果您使用 Azure CLI 的本機安裝，然後您就不必使用逸出字元。

   這個範例假設您從 Azure Cloud Shell 工作階段執行命令。

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

   * 取代`<source-path>`預留位置取代為您想要上傳的檔案位置的路徑。

   > [!NOTE]
   > 如果您使用 Azure CLI 的位置安裝，則可以在本機電腦上的任何位置使用路徑 (例如： `C:\myFolder`。
   >
   > 如果您使用 Azure Cloud Shell，您必須參考會顯示與 Cloud Shell 的檔案共用。 這個位置可以是雲端的您檔案共用共用本身或現有的檔案共用，您從 Cloud Shell 中掛接。 若要了解如何執行這項操作，請參閱[在 Azure Cloud Shell 中保存檔案](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)。

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>使用 Azure CLI 來尋找網站 URL

您可以使用公用網站 URL，以檢視從瀏覽器的內容。

使用下列命令來尋找 URL:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

* 取代`<resource-group-name>`預留位置的值，您的資源群組的名稱。

<a id="powershell" />

## <a name="use-powershell"></a>使用 PowerShell

您可以啟用靜態網站代管，使用 Azure PowerShell 模組。

1. 開啟 Windows PowerShell 命令視窗。

2. 請確認您有 Azure PowerShell 模組 Az 版 0.7 或更新版本。

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。

3. 使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

   ```powershell
   Connect-AzAccount
   ```

4. 如果您的身分識別與多個訂用帳戶相關聯，然後設定您的有效訂用帳戶至訂用帳戶會裝載您的靜態網站的儲存體帳戶。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   取代`<subscription-id>`訂用帳戶識別碼的預留位置值。

5. 取得儲存體帳戶內容，定義您想要使用的儲存體帳戶。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * 取代`<resource-group-name>`預留位置的值，您的資源群組的名稱。

   * 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

6. 啟用靜態網站代管。

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * 取代`<error-document-name>`預留位置取代為瀏覽器要求不存在的網站上的頁面時要對使用者顯示錯誤文件的名稱。

   * 取代`<index-document-name>`預留位置索引文件的名稱。 這份文件通常是 「 index.html"。

7. 將物件從來源目錄上傳至 *$web* 容器。

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * 取代`<path-to-file>`預留位置的值，您要上傳檔案的完整路徑 (例如： `C:\temp\index.html`)。

   * 取代`<blob-name>`預留位置的值，以您想要提供所產生的 blob 名稱 (例如： `index.html`)。

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>使用 PowerShell 來尋找網站 URL

您可以使用公用網站 URL，以檢視從瀏覽器的內容。

使用下列命令來尋找 URL:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* 取代`<resource-group-name>`預留位置的值，您的資源群組的名稱。

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>啟用靜態網站頁面上的度量

一旦您啟用度量，流量統計資料中的檔案 **$web**報告計量儀表板中的容器。

1. 按一下 **設定** > **監視** > **計量**。

   藉由連結到不同的計量 API 來產生計量資料。 入口網站只會顯示指定時間範圍內所使用的 API 成員，以便只專注於傳回資料的成員。 為了確保您能夠選取所需的 API 成員，第一個步驟是依序展開 時間範圍。

2. 按一下 [時間範圍] 按鈕，然後選取**過去 24 小時**，然後按一下**套用**。

   ![Azure 儲存體靜態網站計量的時間範圍](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. 選取  **Blob**從*命名空間*下拉式清單。

   ![Azure 儲存體靜態網站計量的命名空間](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. 然後選取 [輸出]  計量。

   ![Azure 儲存體靜態網站計量的計量](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. 從 [彙總]  選取器中選取 [總和]  。

   ![Azure 儲存體靜態網站計量的彙總](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. 按一下 **新增篩選器**按鈕，然後選擇**API 名稱**從*屬性*選取器。

   ![Azure 儲存體靜態網站計量的 API 名稱](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. 核取方塊旁**GetWebContent**中*值*選取器以填入計量報表。

   ![Azure 儲存體靜態網站計量的 GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>後續步驟

* [Azure 儲存體中的靜態網站代管](storage-blob-static-website.md)
* [使用 Azure CDN 透過 HTTPS 以自訂網域存取 blob](storage-https-custom-domain-cdn.md)
* [針對 Blob 或 Web 端點設定自訂網域名稱](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [建置您的第一個無伺服器 Web 應用程式](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [教學課程：在 Azure DNS 上託管您的網域](../../dns/dns-delegate-domain-azure-dns.md)
