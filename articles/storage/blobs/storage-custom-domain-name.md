---
title: 針對 Azure 儲存體帳戶設定自訂網域名稱 | Microsoft Docs
description: 使用 Azure 入口網站，將您自己的正式名稱 (CNAME) 對應至 Azure 儲存體帳戶中的 Blob 儲存體或 Web 端點。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: f0fe4bef436576bec90d1d770d262c2c22d280a3
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694579"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>針對 Azure 儲存體帳戶設定自訂網域名稱

您可以設定自訂網域名稱，以供存取 Azure 儲存體帳戶中的 Blob 資料。 Azure Blob 儲存體的預設端點是 *\<儲存體帳戶名稱>.blob.core.windows.net*。 您也可以使用隨[靜態網站功能 (預覽)](storage-blob-static-website.md) 一起產生的 Web 端點。 如果您將自訂網域和子網域 (例如 *www.contoso.com*) 對應至儲存體帳戶的 Blob 或 Web 端點，您的使用者便可使用該網域來存取您儲存體帳戶中的 Blob 資料。

> [!IMPORTANT]
> Azure 儲存體尚未以原生方式支援使用自訂網域的 HTTPS。 您目前可以[使用 Azure CDN 透過 HTTPS 以自訂網域存取 Blob](storage-https-custom-domain-cdn.md)。
>

> [!NOTE]  
> 儲存體帳戶目前支援每個帳戶只能有一個自訂網域名稱。 您無法將自訂網域名稱同時對應至 Web 和 Blob 服務端點。

下表展示的幾個範例 URL，適用於位在名為 *mystorageaccount* 之儲存體帳戶中的 Blob 資料。 針對儲存體帳戶註冊的自訂網域為 *www.contoso.com*：

| 資源類型 | 預設 URL | 自訂網域 URL |
| --- | --- | --- | --- |
| 儲存體帳戶 | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| 根容器 | http://mystorageaccount.blob.core.windows.net/myblob 或 http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob 或 http://www.contoso.com/$root/myblob |
| Web |  http://mystorageaccount.[zone].web.core.windows.net/$web/[indexdoc] 或 http://mystorageaccount.[zone].web.core.windows.net/[indexdoc] 或 http://mystorageaccount.[zone].web.core.windows.net/$web 或 http://mystorageaccount.[zone].web.core.windows.net/ | http://www.contoso.com/$web 或 http://www.contoso.com/ 或 http://www.contoso.com/$web/[indexdoc] 或  http://www.contoso.com/[indexdoc] |

> [!NOTE]  
> 如下列各節中所示，Blob 服務端點的所有範例也適用於 Web 服務端點。

## <a name="direct-vs-intermediary-domain-mapping"></a>直接與中繼網域對應

您可以透過兩種方式其中之一，將自訂網域指向儲存體帳戶的 Blob 端點： 
* 使用直接 CNAME 對應。
* 使用 *asverify* 中繼子網域。

### <a name="direct-cname-mapping"></a>直接 CNAME 對應

第一種方法最為簡易，亦即建立正式名稱 (CNAME) 記錄將您的自訂網域與子網域直接對應至 Blob 端點。 CNAME 記錄是將來源網域對應至目的地網域的網域名稱系統 (DNS) 功能。 在我們的範例中，來源網域是您自己的自訂網域和子網域，(例如 *www.contoso.com*)。 目的地網域是您的 Blob 服務端點 (例如 *mystorageaccount.blob.core.windows.net*)。

＜註冊自訂網域＞一節涵蓋直接方法。

### <a name="intermediary-mapping-with-asverify"></a>使用 *asverify* 的中繼對應

第二種方法也使用 CNAME 記錄。 不過，為了避免發生停機，會先採用 Azure 認得的特殊子網域 *asverify*。

將自訂網域對應至 Blob 端點時，會在您於 [Azure 入口網站](https://portal.azure.com)中註冊網域的期間，造成短暫停機。 如果網域目前所支援之應用程式的服務等級協定 (SLA) 要求不得發生停機狀況，請使用 Azure *asverify* 子網域作為中繼註冊步驟。 此步驟可確保讓使用者能夠在 DNS 對應進行時存取您的網域。

[使用 *asverify* 子網域來註冊自訂網域](#register-a-custom-domain-using-the-asverify-subdomain)涵蓋此中繼方法。

## <a name="register-a-custom-domain"></a>註冊自訂網域
如果下列陳述與您的情況相符，請使用本節中的程序來註冊網域：
* 您不在乎網域在短暫的時間內無法供使用者使用。
* 您的自訂網域目前未裝載應用程式。 

您可以使用 Azure DNS 來設定 Azure Blob 存放區的自訂 DNS 名稱。 如需詳細資訊，請參閱[使用 Azure DNS 為 Azure 服務提供自訂網域設定](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage)。

如果您自訂網域目前支援的應用程式不允許發生任何停機狀況，請依照[使用 *asverify* 子網域來註冊自訂網域](#register-a-custom-domain-using-the-asverify-subdomain)中所述的程序進行操作。

若要設定自訂網域名稱，請在 DNS 中建立一個新的 CNAME 記錄。 CNAME 記錄會指定網域名稱的別名。 在我們的範例中，它會將自訂網域的位址對應至儲存體帳戶的 Blob 儲存體端點。

您通常可以在網域註冊機構的網站上管理您網域的 DNS 設定。 各註冊機構指定 CNAME 記錄的方法都很類似，只是稍微不同，但概念都一樣。 由於有些基本網域註冊套件並未提供 DNS 設定，因此您可能需要先升級網域註冊套件，然後再建立 CNAME 記錄。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。

1. 在功能表窗格中的 [Blob 服務] 底下，選取 [自訂網域]。  
   [自訂網域] 窗格隨即開啟。

1. 登入網域註冊機構的網站，然後前往 DNS 管理頁面。  
   您可能會在名為 [Domain Name] \(網域名稱\)、[DNS] 或 [Name Server Management] \(名稱伺服器管理\) 的區段中找到該頁面。

1. 尋找管理 CNAME 的區段。  
   您可能需要前往進階設定頁面，然後尋找 [CNAME]、[Alias] \(別名\) 或 [Subdomains] \(子網域\)。

1. 建立新的 CNAME 記錄、輸入子網域別名 (例如 **www** 或 **photos**)，然後提供主機名稱。  
   主機名稱是您的 Blob 服務端點。 其格式為 *\<mystorageaccount>.blob.core.windows.net*，其中 *mystorageaccount* 是您儲存體帳戶的名稱。 要使用的主機名稱會顯示在 [Azure 入口網站](https://portal.azure.com)之 [自訂網域] 窗格的項目 #1 中。

1. 在 [自訂網域] 窗格的文字方塊中，輸入您的自訂網域名稱 (包含子網域)。  
   例如，若您的網域為 *contoso.com*，且子網域別名為 *www*，請輸入 **www.contoso.com**。 若您的子網域為 *photos*，請輸入 **photos.contoso.com**。

1. 若要註冊您的自訂網域，請選取 [儲存]。  
   如果註冊成功，入口網站就會通知您已順利更新您的儲存體帳戶。

透過 DNS 傳播您的新 CNAME 記錄之後，使用者只要具備適當權限，即可使用您的自訂網域來檢視 Blob 資料。

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>使用 *asverify* 子網域來註冊自訂網域
如果您自訂網域目前所支援之應用程式的 SLA 要求不得發生任何停機狀況，請依照本節中的程序註冊自訂網域。 您可以建立從 *asverify.\<子網域>.\<自訂網域>* 指向 *asverify.\<儲存體帳戶>.blob.core.windows.net* 的 CNAME，向 Azure 預先註冊您的網域。 接著，您可以建立從 *\<子網域>.\<自訂網域>* 指向 *\<儲存體帳戶>.blob.core.windows.net* 的第二個 CNAME，將傳送到自訂網域的流量導向到您的 Blob 端點。

*asverify* 子網域是 Azure 認可的特殊子網域。 在您自己的子網域前面加上 *asverify*，即表示允許 Azure 無須修改網域的 DNS 記錄即可辨識您的自訂網域。 一旦修改網域的 DNS 記錄，其將會在無停機的情況下對應至 Blob 端點。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。

1. 在功能表窗格中的 [Blob 服務] 底下，選取 [自訂網域]。  
   [自訂網域] 窗格隨即開啟。

1. 登入 DNS 提供者的網站，然後前往 DNS 管理頁面。  
   您可能會在名為 [Domain Name] \(網域名稱\)、[DNS] 或 [Name Server Management] \(名稱伺服器管理\) 的區段中找到該頁面。

1. 尋找管理 CNAME 的區段。  
   您可能需要前往進階設定頁面，然後尋找 [CNAME]、[Alias] \(別名\) 或 [Subdomains] \(子網域\)。

1. 建立新的 CNAME 記錄、提供包含 *asverify* 子網域的子網域別名 (例如 **asverify.www** 或 **asverify.photos**)，然後提供主機名稱。  
   主機名稱是您的 Blob 服務端點。 其格式為 *asverify.\<mystorageaccount>.blob.core.windows.net*，其中 *mystorageaccount* 是您儲存體帳戶的名稱。 要使用的主機名稱會顯示在 [Azure 入口網站](https://portal.azure.com)之 [自訂網域] 窗格的項目 #2 中。

1. 在 [自訂網域] 窗格的文字方塊中，輸入您的自訂網域名稱 (包含子網域)。  
   不包含 *asverify*。 例如，若您的網域為 *contoso.com*，且子網域別名為 *www*，請輸入 **www.contoso.com**。 若您的子網域為 *photos*，請輸入 **photos.contoso.com**。

1. 選取 [使用間接 CNAME 驗證] 核取方塊。

1. 若要註冊您的自訂網域，請選取 [儲存]。  
   如果註冊成功，入口網站就會通知您已順利更新您的儲存體帳戶。 自訂網域已通過 Azure 的驗證，但傳送至網域的流量尚未路由傳送到儲存體帳戶。

1. 返回 DNS 提供者的網站，然後建立另一個將子網域對應至 Blob 服務端點的 CNAME 記錄。  
   例如，將子網域指定為 *www* 或 *photos* (無 *asverify*)，並將主機名稱指定為 *\<mystorageaccount>.blob.core.windows.net* (其中 *mystorageaccount* 是您儲存體帳戶的名稱)。 待這個步驟完成後，自訂網域的註冊作業也宣告完成。

1. 最後，您可以刪除包含 *asverify*子網域的新建立 CNAME 記錄，這是只有在中繼步驟中才需要的記錄。

透過 DNS 傳播您的新 CNAME 記錄之後，使用者只要具備適當權限，即可使用您的自訂網域來檢視 Blob 資料。

## <a name="test-your-custom-domain"></a>測試自訂網域

若要確認自訂網域是否對應至 Blob 服務端點，請在儲存體帳戶內的公用容器中建立 Blob。 接著，在網頁瀏覽器中，使用以下格式的 URI 來存取 Blob：`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

例如，若要存取 *photos.contoso.com* 自訂子網域之 *myforms*容器中的 Web 表單，您可以使用以下 URI：`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>取消註冊自訂網域

若要取消註冊您的 Blob 儲存體端點自訂網域，請使用下列其中一項程序。

### <a name="azure-portal"></a>Azure 入口網站

若要移除自訂網域設定，請執行下列操作：

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。

1. 在功能表窗格中的 [Blob 服務] 底下，選取 [自訂網域]。  
   [自訂網域] 窗格隨即開啟。

1. 清除包含自訂網域名稱的文字方塊內容。

1. 選取 [儲存] 按鈕。

順利移除自訂網域後，您會看到已成功更新儲存體帳戶的入口網站通知。

### <a name="azure-cli"></a>Azure CLI

若要移除自訂網域註冊，請使用 [az storage account update](https://docs.microsoft.com/cli/azure/storage/account) CLI 命令，然後指定空字串 (`""`) 作為 `--custom-domain` 引數值。

* 命令格式︰

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* 命令範例︰

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要移除自訂網域註冊，請使用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell Cmdlet，然後指定空字串 (`""`) 作為 `-CustomDomainName` 引數值。

* 命令格式︰

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* 命令範例︰

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>後續步驟
* [將自訂網域對應至 Azure 內容傳遞網路 (CDN) 端點](../../cdn/cdn-map-content-to-custom-domain.md)
* [使用 Azure CDN 透過 HTTPS 以自訂網域存取 Blob](storage-https-custom-domain-cdn.md)
* [Azure Blob 儲存體中的靜態網站代管 (預覽)](storage-blob-static-website.md)
