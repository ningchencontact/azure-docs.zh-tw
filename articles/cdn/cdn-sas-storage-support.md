---
title: "Azure CDN 使用 SAS |Microsoft 文件"
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
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="using-azure-cdn-with-sas"></a>使用 SAS 的 Azure CDN

當您提供從儲存體帳戶的儲存體容器的內容時，您可能要保護使用者如何存取您的檔案只要授與至儲存體容器的私用存取。 否則，可以存取儲存體容器的公用存取已授與的人知道它的 URL。 若要保護您所允許的內容傳遞網路 (CDN)，以存取儲存體帳戶，您可以使用共用存取簽章 (SAS) 功能，從 Azure 儲存體來授與有限的存取私用儲存體容器。

SAS 可授與有限存取您的 Azure 儲存體資源的權限，而不會讓您的帳戶金鑰的 URI。 您不信任與儲存體帳戶金鑰的用戶端，但您要委派給特定的儲存體帳戶資源的存取，您可以提供的 SAS。 發佈到這些用戶端的共用的存取簽章 URI，您授與這些資源的存取權的一段指定時間。
 
SAS 可讓您的 blob，例如開始和到期時間、 權限 （讀取/寫入） 和 IP 範圍中定義的存取各種不同的參數。 本文說明如何搭配 Azure CDN 使用 SAS。 如需 SAS，包括如何建立它和其參數的選項，請參閱[使用共用存取簽章 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>若要使用 SAS 儲存體設定 Azure CDN
搭配 Azure CDN 使用 SAS 的建議使用下列三個選項。 所有選項都假設您已經建立可運作的 SAS （請參閱必要條件）。 
 
### <a name="prerequisites"></a>必要條件
若要開始，建立儲存體帳戶，然後產生 SAS，使您的資產。 您可以產生兩種類型的預存的存取簽章： SAS 的服務或帳戶 SAS。 如需詳細資訊，請參閱[類型的共用的存取簽章](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures)。

您已經產生 SAS 之後，您可以存取您的 blob 儲存體檔案具有下列格式的 url:`https://<account>.blob.core.windows.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
例如︰
 ```
https://democdnstorage1.blob.core.windows.net/container1/sasblob.txt?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

如需設定參數的詳細資訊，請參閱[SAS 參數考量](#sas-parameter-considerations)和[共用存取簽章參數](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters)。

![CDN SAS 設定](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-the-cdn"></a>選項 1： 使用 SAS 傳遞至 blob 儲存體從 CDN

這個選項是最簡單，並使用只有單一 SAS 權杖，這從 CDN 傳遞至原始伺服器。 支援**Verizon 從 Azure CDN**，Standard 和 Premium 的設定檔，並**Akamai 從 Azure CDN**。 
 
1. 選取端點，請按一下**快取規則**，然後選取**快取每個唯一的 URL**從**查詢字串快取**清單。

    ![CDN 快取的規則](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. 設定 SAS 儲存體帳戶上之後，請使用 CDN 的 URL 使用 SAS 權杖來存取檔案。 
   
   產生的 URL 具有下列格式：`https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`

   例如︰   
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. 微調的快取持續時間，使用快取的規則或加入`Cache-Control`原始位置的標頭。 因為 CDN 會視為純文字的查詢字串中的 SAS 權杖，最佳作法是您應該設定 SAS 到期時間或之前到期的快取持續時間。 否則，如果檔案較長的時間比 SAS 為作用中快取，檔案可能是從 CDN 來源伺服器存取的 SAS 的到期時間經過之後。 如果發生這種情況，而且您想要讓您快取的檔案無法存取，您必須執行的清除作業，以清除從快取檔案。 如需設定 CDN 快取持續時間資訊，請參閱[控制 Azure 內容傳遞網路使用的快取規則快取行為](cdn-caching-rules.md)。

### <a name="option-2-hidden-cdn-security-token-using-rewrite-rule"></a>選項 2： 使用重寫規則的隱藏的 CDN 安全性權杖
 
使用此選項，您可以保護來源 blob 儲存體，而不需要 SAS 權杖 CDN 使用者。 若要使用此選項，如果您不需要特定的存取限制，對於檔案，但想要防止使用者存取儲存體原點直接改善 CDN 卸載的時間。 這個選項是僅供**Verizon 從 Azure CDN Premium**設定檔。 
 
1. 使用[規則引擎](cdn-rules-engine.md)建立 URL 重寫規則。 新的規則，大約需要 90 分鐘時間傳播。

   ![管理 CDN 按鈕](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN 規則引擎 按鈕](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   此範例 URL 重寫規則具有下列模式：
   
   來源：   
   `/test/demo.jpg`
   
   目的地：   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![CDN URL 重寫規則](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
 
2. 存取您的 CDN 不 SAS 權杖，以下列格式的檔案：`https://<endpoint>.azureedge.net/<folder>/<file>`
 
   例如︰   
   `https://demoendpoint.azureedge.net/test/demo.jpg`
       
   請注意，任何人，不論它們使用 SAS 權杖，可以存取 CDN 端點。 

3. 微調的快取持續時間，使用快取的規則或加入`Cache-Control`原始位置的標頭。 因為 CDN 會視為純文字的查詢字串中的 SAS 權杖，最佳作法是您應該設定 SAS 到期時間或之前到期的快取持續時間。 否則，如果檔案較長的時間比 SAS 為作用中快取，檔案可能是從 CDN 來源伺服器存取的 SAS 的到期時間經過之後。 如果發生這種情況，而且您想要讓您快取的檔案無法存取，您必須執行的清除作業，以清除從快取檔案。 如需設定 CDN 快取持續時間資訊，請參閱[控制 Azure 內容傳遞網路使用的快取規則快取行為](cdn-caching-rules.md)。

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>選項 3： 使用 CDN 安全性權杖驗證與重寫規則

這個選項是最安全且可自訂。 若要使用 CDN 安全性權杖驗證，您必須擁有**Verizon 從 Azure CDN Premium**設定檔。 用戶端存取根據設定 CDN 安全性權杖的安全性參數。 不過，如果 SAS 變成無效，CDN 將無法重新驗證來自原始伺服器的內容。

1. [建立 CDN 的安全性權杖](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication)並將它啟用使用規則引擎的 CDN 端點和路徑為您的使用者可以存取檔案的位置。

   SAS URL 具有下列格式：   
   `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
   例如︰   
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   CDN 安全性權杖驗證的參數選項都不同 SAS 權杖的參數選項。 如果您選擇使用當您建立 CDN 安全性權杖的到期時間，請將它設定為 SAS 權杖的到期時間相同的值。 這樣可以確保到期時間為可預測。 
 
2. 使用[規則引擎](cdn-rules-engine.md)建立 URL 重寫規則，若要啟用權杖存取容器中的所有 blob。 新的規則，大約需要 90 分鐘時間傳播。

   此範例 URL 重寫規則具有下列模式：
   
   來源：   
   `/test/demo.jpg`
   
   目的地：   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![CDN URL 重寫規則](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)

3. 當您更新 SAS 時，更新 Url 重寫規則，以使用新的 SAS 權杖。 

## <a name="sas-parameter-considerations"></a>SAS 參數考量

CDN 看不到 SAS 參數，因為 CDN 無法變更它們根據其傳送行為。 定義的參數限制只在 CDN 對原始伺服器，不會針對要求從用戶端與 cdn 之間的要求。 這樣的區別務必考量當您設定的 SAS 參數。 如果這些進階功能所需，而且您正使用[選項 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)，CDN 安全性權杖上設定適當的限制。

| SAS 參數名稱 | 說明 |
| --- | --- |
| Start | CDN 可以存取的 blob 檔案開始時間。 由於時鐘誤差 （時脈訊號到達在不同的時間為不同的元件） 時，選擇先前 15 分鐘的時間，如果您想要立即可用的資產。 |
| End | 其後 CDN 就無法再存取 blob 檔案的時間。 先前在 CDN 的快取的檔案是仍然可以存取。 若要控制檔案到期時間，請在 CDN 安全性權杖上設定適當的到期時間或清除資產。 |
| 允許的 IP 位址 | 選用。 如果您使用**Verizon 從 Azure CDN**，您可以設定此參數中所定義的範圍[Verizon 邊緣伺服器 IP 範圍從 Azure CDN](https://msdn.microsoft.com/library/mt757330.aspx)。 如果您使用**Akamai 從 Azure CDN**，您無法設定 IP 範圍參數，因為不是靜態的 IP 位址。|
| 允許的通訊協定 | 通訊協定，允許使用帳戶 SAS 提出的要求。 建議使用 HTTPS 設定。|

## <a name="see-also"></a>請參閱
- [使用共用存取簽章 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [共用存取簽章，第 2 部分：透過 Blob 儲存體來建立與使用 SAS](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [設定安全性權杖驗證 Azure 內容傳遞網路資產](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
