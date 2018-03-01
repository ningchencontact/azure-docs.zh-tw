---
title: "搭配 SAS 使用 Azure CDN | Microsoft Docs"
description: 
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: rli v-deasim
ms.openlocfilehash: de30f4319be75362131f8c8ad71aad57b0528f05
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="using-azure-cdn-with-sas"></a>搭配 SAS 使用 Azure CDN

從您儲存體帳戶的儲存體容器提供內容時，您可以透過授與該儲存體容器的私用存取權，來為使用者存取檔案的方式提供安全防護。 否則，已授與公用存取權的儲存體容器將可供所有知道其 URL 的使用者存取。 若要保護您已允許內容傳遞網路 (CDN) 存取的儲存體帳戶，您可以使用來自 Azure 儲存體的「共用存取簽章」(SAS) 功能，來授與對私用儲存體容器的有限存取權。

SAS 是一個 URI，可在不公開您帳戶金鑰的情況下，授與對「Azure 儲存體」資源的有限存取權限。 針對您不信任其使用您儲存體帳戶金鑰但又想要將特定儲存體帳戶資源存取權委派給它們的用戶端，您可以將 SAS 提供給它們。 藉由將共用存取簽章 URI 提供給這些用戶端，您會授與它們一段指定時間的資源存取權。
 
SAS 可讓您定義各種 Blob 存取參數，例如開始和到期時間、權限 (讀取/寫入) 及 IP 範圍。 本文說明如何將 SAS 與 Azure CDN 搭配使用。 如需有關 SAS 的詳細資訊 (包括如何建立 SAS 及其參數選項)，請參閱[使用共用存取簽章 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>設定 Azure CDN 以與儲存體 SAS 搭配運作
以下是搭配 Azure CDN 使用 SAS 時的三個建議選項。 所有選項皆假設您已經建立一個可運作的 SAS (請參閱先決條件)。 
 
### <a name="prerequisites"></a>先決條件
若要開始，請建立一個儲存體帳戶，然後為您的資產生 SAS。 您可以產生兩種類型的預存存取簽章：服務 SAS 或帳戶 SAS。 如需詳細資訊，請參閱[共用存取簽章的類型](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures)。

產生 SAS 之後，您可以使用具有以下格式的 URL 來存取 Blob 儲存體檔案：`https://<account>.blob.core.windows.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
例如︰
 ```
https://democdnstorage1.blob.core.windows.net/container1/sasblob.txt?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

如需有關設定參數的詳細資訊，請參閱 [SAS 參數考量](#sas-parameter-considerations)和[共用存取簽章參數](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters)。

![CDN SAS 設定](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-the-cdn"></a>選項 1：使用具有從 CDN 至 Blob 儲存體之傳遞的 SAS

此選項是最簡單的選項，僅使用從 CDN 傳遞至原始伺服器的單一 SAS 權杖。 **來自 Verizon 的 Azure CDN** (「標準」和「進階」設定檔) 及**來自 Akamai 的 Azure CDN**都支援此選項。 
 
1. 選取一個端點，按一下 [快取規則]，然後從 [查詢字串快取] 清單中，選取 [快取所有不重複的 URL]。

    ![CDN 快取規則](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. 在您於儲存體帳戶上設定 SAS 之後，請搭配 CDN URL 使用 SAS 權杖來存取檔案。 
   
   產生的 URL 會具有以下格式：`https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`

   例如︰   
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. 使用快取規則或在原點新增 `Cache-Control` 標頭來微調快取持續時間。 由於 CDN 會將 SAS 權杖視為純查詢字串，因此最佳做法是，您應該設定一個在 SAS 到期時間或此時間之前到期的快取持續時間。 否則，如果檔案的快取持續時間比 SAS 的有效期長，便可能在過了 SAS 到期時間之後，從 CDN 原始伺服器存取該檔案。 如果發生這種情況，而您想要讓已快取的檔案變成無法存取，就必須對該檔案執行清除作業，以將它從快取中清除。 如需有關在 CDN 上設定快取持續時間的資訊請參閱[使用快取規則控制 Azure 內容傳遞網路快取行為](cdn-caching-rules.md)。

### <a name="option-2-hidden-cdn-security-token-using-rewrite-rule"></a>選項 2：使用重寫規則的隱藏式 CDN 安全性權杖
 
使用此選項時，不需要 CDN 使用者的 SAS 權杖，即可保護原始 Blob 儲存體的安全。 如果您不需要特定的檔案存取限制，但想要防止使用者直接存取儲存體原點來縮短 CDN 卸載時間，便可以使用此選項。 此選項僅適用於**來自 Verizon 的 Azure 進階 CDN**設定檔。 
 
1. 使用[規則引擎](cdn-rules-engine.md)來建立 URL 重寫規則。 新規則大約需要 90 分鐘的時間來進行傳播。

   ![CDN [管理] 按鈕](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN [規則引擎] 按鈕](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   此範例 URL 重寫規則具有下列模式：
   
   來源：   
   `/test/demo.jpg`
   
   目的地：   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![CDN URL 重寫規則](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
 
2. 在沒有 SAS 權杖的情況下，以下列格式存取 CDN 上的檔案：`https://<endpoint>.azureedge.net/<folder>/<file>`
 
   例如︰   
   `https://demoendpoint.azureedge.net/test/demo.jpg`
       
   請注意，所有使用者 (不論是否使用 SAS 權杖) 都能存取 CDN 端點。 

3. 使用快取規則或在原點新增 `Cache-Control` 標頭來微調快取持續時間。 由於 CDN 會將 SAS 權杖視為純查詢字串，因此最佳做法是，您應該設定一個在 SAS 到期時間或此時間之前到期的快取持續時間。 否則，如果檔案的快取持續時間比 SAS 的有效期長，便可能在過了 SAS 到期時間之後，從 CDN 原始伺服器存取該檔案。 如果發生這種情況，而您想要讓已快取的檔案變成無法存取，就必須對該檔案執行清除作業，以將它從快取中清除。 如需有關在 CDN 上設定快取持續時間的資訊請參閱[使用快取規則控制 Azure 內容傳遞網路快取行為](cdn-caching-rules.md)。

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>選項 3：搭配重寫規則使用 CDN 安性權杖驗證

此選項是最安全且可自訂的選項。 若要使用 CDN 安全性權杖驗證，您必須具有 **來自 Verizon 的 Azure 進階 CDN** 設定檔。 用戶端存取權會根據 CDN 安全性權杖上設定的安全性參數。 不過，如果 SAS 變成無效，CDN 將無法重新驗證來自原始伺服器的內容。

1. [建立 CDN 安全性權杖](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication)，並使用您使用者可存取檔案之 CDN 端點和路徑的規則引擎來啟用它。

   SAS URL 具有下列格式：   
   `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
   例如︰   
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   CDN 安全性權杖驗證的參數選項與 SAS 權杖的參數選項不同。 如果您在建立 CDN 安全性權杖時選擇使用到期時間，請將其設定為與 SAS 權杖到期時間相同的值。 這麼做可確保到期時間是可預測的。 
 
2. 使用[規則引擎](cdn-rules-engine.md)來建立 URL 重寫規則，以啟用對容器中所有 Blob 的權杖存取權。 新規則大約需要 90 分鐘的時間來進行傳播。

   此範例 URL 重寫規則具有下列模式：
   
   來源：   
   `/test/demo.jpg`
   
   目的地：   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![CDN URL 重寫規則](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)

3. 當您更新 SAS 時，請將 Url 重寫規則更新成使用新的 SAS 權杖。 

## <a name="sas-parameter-considerations"></a>SAS 參數考量

由於 CDN 看不到 SAS 參數，因此 CDN 無法根據這些參數變更其傳遞行為。 所定義的參數限制僅適用於 CDN 對原始伺服器提出的要求，而不適用於用戶端對 CDN 提出的要求。 當您設定 SAS 參數時，請務必考量這項區別。 如果需要這些進階功能且您使用[選項 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)，請在 CDN 安全性權杖上設定適當的限制。

| SAS 參數名稱 | 說明 |
| --- | --- |
| Start | CDN 可以開始存取 Blob 檔案的時間。 由於時鐘誤差 (當時鐘訊號針對不同元件在不同時間抵達時) 的緣故，因此如果您想要讓資產立即可供使用，請選擇提早 15 分鐘的時間。 |
| End | 之後 CDN 即無法再存取 Blob 檔案的時間。 CDN 上先前快取的檔案仍然可供存取。 若要控制檔案到期時間，請在 CDN 安全性權杖上設定適當的到期時間或清除資產。 |
| 允許的 IP 位址 | 選用。 如果您使用**來自 Verizon 的 Azure CDN**，您可以將此參數設定為[來自 Verizon 的 Azure CDN Edge Server IP 範圍](https://msdn.microsoft.com/library/mt757330.aspx) \(英文\) 中定義的範圍。 如果您使用**來自 Akamai 的 Azure CDN**，則無法設定 IP 範圍參數，因為 IP 位址並非靜態。|
| 允許的通訊協定 | 針對使用帳戶 SAS 來提出之要求允許的通訊協定。 建議使用 HTTPS 設定。|

## <a name="see-also"></a>另請參閱
- [使用共用存取簽章 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [共用存取簽章，第 2 部分：透過 Blob 儲存體來建立與使用 SAS](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [使用權杖驗證來保護 Azure 內容傳遞網路資產](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
