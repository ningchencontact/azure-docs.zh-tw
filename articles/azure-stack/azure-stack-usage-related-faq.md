---
title: 使用情況 API 相關常見問題集 | Microsoft Docs
description: Azure Stack 計量清單、與 Azure 使用情況 API 的比較、使用時間與回報時間、錯誤碼。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 11/08/2018
ms.openlocfilehash: 92774592f86a71a8482fd3d44eca404fcf2d4e6e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429548"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Azure Stack 使用量 API 的常見問題集

此文章提供 Azure Stack 使用情況 API 的一些常見問題集解答。

## <a name="what-meter-ids-can-i-see"></a>我可以看到哪個計量識別碼？
系統會針對下列資源提供者回報使用情況：

### <a name="network"></a>網路
  
**計量識別碼**：F271A8A388C44D93956A063E1D2FA80B  
**計量名稱**：靜態 IP 位址使用情況  
**單位**：IP 位址  
**注意**：已使用的 IP 位址計數。 如果您以每日規模呼叫使用情況 API，計量會傳回 IP 位址乘以時數。  
  
**計量識別碼**：9E2739BA86744796B465F64674B822BA  
**計量名稱**：動態 IP 位址使用情況  
**單位**：IP 位址  
**注意**：已使用的 IP 位址計數。 如果您以每日規模呼叫使用情況 API，計量會傳回 IP 位址乘以時數。  
  
### <a name="storage"></a>儲存體
  
**計量識別碼**：B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**計量名稱**：TableCapacity  
**單位**：GB\*小時  
**注意**：資料表所取用的總容量。  
  
**計量識別碼**：B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**計量名稱**：PageBlobCapacity  
**單位**：GB\*小時  
**注意**：分頁 Blob 所取用的總容量。  
  
**計量識別碼**：B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**計量名稱**：QueueCapacity  
**單位**：GB\*小時  
**注意**：佇列所取用的總容量。  
  
**計量識別碼**：09F8879E-87E9-4305-A572-4B7BE209F857  
**計量名稱**：BlockBlobCapacity  
**單位**：GB\*小時  
**注意**：區塊 Blob 所取用的總容量。  
  
**計量識別碼**：B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**計量名稱**：TableTransactions  
**單位**：要求計數 (以萬為單位)  
**注意**：資料表服務要求 (以萬為單位)。  
  
**計量識別碼**：50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**計量名稱**：TableDataTransIn  
**單位**：輸入資料 (GB)  
**注意**：資料表服務資料輸入 (GB)。  
  
**計量識別碼**：1B8C1DEC-EE42-414B-AA36-6229CF199370  
**計量名稱**：TableDataTransOut  
**單位**：輸出 (GB)  
**注意**：資料表服務資料輸出 (GB)  
  
**計量識別碼**：43DAF82B-4618-444A-B994-40C23F7CD438  
**計量名稱**：BlobTransactions  
**單位**：要求計數 (以萬為單位)  
**注意**：Blob 服務要求 (以萬為單位)。  
  
**計量識別碼**：9764F92C-E44A-498E-8DC1-AAD66587A810  
**計量名稱**：BlobDataTransIn  
**單位**：輸入資料 (GB)  
**注意**：Blob 服務資料輸入 (GB)。  
  
**計量識別碼**：3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**計量名稱**：BlobDataTransOut  
**單位**：輸出 (GB)  
**注意**：Blob 服務資料輸出 (GB)。  
  
**計量識別碼**：EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**計量名稱**：QueueTransactions  
**單位**：要求計數 (以萬為單位)  
**注意**：佇列服務要求 (以萬為單位)。  
  
**計量識別碼**：E518E809-E369-4A45-9274-2017B29FFF25  
**計量名稱**：QueueDataTransIn  
**單位**：輸入資料 (GB)  
**注意**：佇列服務資料輸入 (GB)。  
  
**計量識別碼**：DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**計量名稱**：QueueDataTransOut  
**單位**：輸出 (GB)  
**注意**：佇列服務資料輸出 (GB)  

### <a name="compute"></a>計算 
  
**計量識別碼**：FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**計量名稱**：基礎 VM 大小小時  
**單位**：虛擬核心小時數  
**注意**：虛擬核心數目乘以 VM 執行時數。  
  
**計量識別碼**：9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**計量名稱**：Windows VM 大小小時  
**單位**：虛擬核心小時數  
**注意**：虛擬核心數目乘以 VM 執行時數。  
  
**計量識別碼**：6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**計量名稱**：VM 大小小時  
**單位**：VM 小時  
**注意**：同時擷取基本與 VM。 不針對核心調整。  
  
### <a name="managed-disks"></a>受控磁碟

**計量識別碼**：380874f9-300c-48e0-95a0-d2d9a21ade8f   
**計量名稱**：S4   
**單位**：磁碟計數\*月   
**注意**：標準受控磁碟 – 32 GB 

**計量識別碼**：1b77d90f-427b-4435-b4f1-d78adec53222   
**計量名稱**：S6   
**單位**：磁碟計數\*月   
**注意**：標準受控磁碟 – 64 GB 

**計量識別碼**：d5f7731b-f639-404a-89d0-e46186e22c8d   
**計量名稱**：S10   
**單位**：磁碟計數\*月   
**注意**：標準受控磁碟 – 128 GB 

**計量識別碼**：ff85ef31-da5b-4eac-95dd-a69d6f97b18a   
**計量名稱**：S15   
**單位**：磁碟計數\*月   
**注意**：標準受控磁碟 – 256 GB 

**計量識別碼**：88ea9228-457a-4091-adc9-ad5194f30b6e   
**計量名稱**：S20   
**單位**：磁碟計數\*月      
**注意**：標準受控磁碟 – 512 GB 

**計量識別碼**：5b1db88a-8596-4002-8052-347947c26940   
**計量名稱**：S30   
**單位**：磁碟計數\*月   
**注意**：標準受控磁碟 – 1024 GB 

**計量識別碼**：7660b45b-b29d-49cb-b816-59f30fbab011   
**計量名稱**：P4   
**單位**：磁碟計數\*月   
**注意**：進階受控磁碟 – 32 GB 

**計量識別碼**：817007fd-a077-477f-bc01-b876f27205fd   
**計量名稱**：P6   
**單位**：磁碟計數\*月   
**注意**：進階受控磁碟 – 64 GB 

**計量識別碼**：e554b6bc-96cd-4938-a5b5-0da990278519   
**計量名稱**：P10   
**單位**：磁碟計數\*月   
**注意**：進階受控磁碟 – 128 GB  

**計量識別碼**：cdc0f53a-62a9-4472-a06c-e99a23b02907   
**計量名稱**：P15  
**單位**：磁碟計數\*月   
**注意**：進階受控磁碟 – 256 GB 

**計量識別碼**：b9cb2d1a-84c2-4275-aa8b-70d2145d59aa   
**計量名稱**：P20   
**單位**：磁碟計數\*月   
**注意**：進階受控磁碟 – 512 GB 

**計量識別碼**：06bde724-9f94-43c0-84c3-d0fc54538369   
**計量名稱**：P30   
**單位**：磁碟計數\*月   
**注意**：進階受控磁碟 – 1024 GB 

**計量識別碼**：7ba084ec-ef9c-4d64-a179-7732c6cb5e28   
**計量名稱**：ActualStandardDiskSize   
**單位**：GB\*月      
**注意**：標準受控磁碟的實際磁碟大小  

**計量識別碼**：daef389a-06e5-4684-a7f7-8813d9f792d5  
**計量名稱**：ActualPremiumDiskSize   
**單位**：GB\*月      
**注意**：進階受控磁碟的實際磁碟大小 

**計量識別碼**：75d4b707-1027-4403-9986-6ec7c05579c8  
**計量名稱**：ActualStandardSnapshotSize   
**單位**：GB\*月   
**注意**：受控標準快照集的實際磁碟大小。  

**計量識別碼**：5ca1cbb9-6f14-4e76-8be8-1ca91547965e   
**計量名稱**：ActualPremiumSnapshotSize   
**單位**：GB\*月   
**注意**：受控進階磁碟的實際大小。   

**計量識別碼**：5d76e09f-4567-452a-94cc-7d1f097761f0   
**計量名稱**：S4   
**單位**：磁碟計數\*小時   
**注意**：標準受控磁碟 – 32 GB (已淘汰) 

**計量識別碼**：dc9fc6a9-0782-432a-b8dc-978130457494   
**計量名稱**：S6   
**單位**：磁碟計數\*小時   
**注意**：標準受控磁碟 – 64 GB (已淘汰) 

**計量識別碼**：e5572fce-9f58-49d7-840c-b168c0f01fff   
**計量名稱**：S10   
**單位**：磁碟計數\*小時   
**注意**：標準受控磁碟 – 128 GB (已淘汰) 

**計量識別碼**：9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**計量名稱**：S15   
**單位**：磁碟計數\*小時   
**注意**：標準受控磁碟 – 256 GB (已淘汰) 

**計量識別碼**：5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**計量名稱**：S20   
**單位**：磁碟計數\*小時      
**注意**：標準受控磁碟 – 512 GB (已淘汰) 

**計量識別碼**：7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**計量名稱**：S30   
**單位**：磁碟計數\*小時   
**注意**：標準受控磁碟 – 1024 GB (已淘汰) 

**計量識別碼**：5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**計量名稱**：P4   
**單位**：磁碟計數\*小時   
**注意**：進階受控磁碟 – 32 GB (已淘汰) 

**計量識別碼**：518b412b-1927-4f25-985f-4aea24e55c4f   
**計量名稱**：P6   
**單位**：磁碟計數\*小時   
**注意**：進階受控磁碟 – 64 GB (已淘汰) 

**計量識別碼**：5cfb1fed-0902-49e3-8217-9add946fd624   
**計量名稱**：P10   
**單位**：磁碟計數\*小時   
**注意**：進階受控磁碟 – 128 GB (已淘汰)  

**計量識別碼**：8de91c94-f740-4d9a-b665-bd5974fa08d4   
**計量名稱**：P15  
**單位**：磁碟計數\*小時   
**注意**：進階受控磁碟 – 256 GB (已淘汰) 

**計量識別碼**：c7e7839c-293b-4761-ae4c-848eda91130b   
**計量名稱**：P20   
**單位**：磁碟計數\*小時   
**注意**：進階受控磁碟 – 512 GB (已淘汰) 

**計量識別碼**：9f502103-adf4-4488-b494-456c95d23a9f   
**計量名稱**：P30   
**單位**：磁碟計數\*小時   
**注意**：進階受控磁碟 – 1024 GB (已淘汰) 

**計量識別碼**：8a409390-1913-40ae-917b-08d0f16f3c38   
**計量名稱**：ActualStandardDiskSize   
**單位**：位元組\*小時      
**注意**：標準受控磁碟的實際磁碟大小 (已淘汰)  

**計量識別碼**：1273b16f-8458-4c34-8ce2-a515de551ef6  
**計量名稱**：ActualPremiumDiskSize   
**單位**：位元組\*小時      
**注意**：進階受控磁碟的實際磁碟大小 (已淘汰) 

**計量識別碼**：89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**計量名稱**：ActualStandardSnapshotSize   
**單位**：位元組\*小時   
**注意**：受控標準快照集的實際磁碟大小 (已淘汰) 

**計量識別碼**：95b0c03f-8a82-4524-8961-ccfbf575f536   
**計量名稱**：ActualPremiumSnapshotSize   
**單位**：位元組\*小時   
**注意**：受控進階磁碟的實際大小 (已淘汰) 

### <a name="sql-rp"></a>Sql RP
  
**計量識別碼**：CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**計量名稱**：DatabaseSizeHourSqlMeter  
**單位**：MB\*小時  
**注意**：建立時的資料庫容量總計。 如果您以每日規模呼叫使用情況 API，計量會傳回 MB 乘以時數。  
  
### <a name="mysql-rp"></a>MySql RP   
  
**計量識別碼**：E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**計量名稱**：DatabaseSizeHourMySqlMeter  
**單位**：MB\*小時  
**注意**：建立時的資料庫容量總計。 如果您以每日規模呼叫使用情況 API，計量會傳回 MB 乘以時數。    
### <a name="key-vault"></a>Key Vault   
  
**計量識別碼**：EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**計量名稱**：Key Vault 交易  
**單位**：要求計數 (以萬為單位)  
**注意**：Key Vault 資料層所接收的 REST API 要求數目。  
  
**計量識別碼**：2C354225-B2FE-42E5-AD89-14F0EA302C87  
**計量名稱**：進階金鑰交易  
**單位**：10000 個交易  
**注意**：RSA 3K/4K、ECC 金鑰交易。 (預覽)。  
  
### <a name="app-service"></a>App Service   
  
**計量識別碼**：190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**計量名稱**：App Service 方案  
**單位**：虛擬核心小時數  
**注意**：用來執行應用程式服務的虛擬核心數目。 注意：Microsoft 會使用此計量來收取 Azure Stack 上的 App Service 費用。 雲端服務提供者可以使用其他 App Service 計量 (下方) 來計算其租用戶的使用量。  
  
**計量識別碼**：67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**計量名稱**：函式要求  
**單位**：10 個要求  
**注意**：要求的執行總數 (每 10 個執行)。 每次執行函式以回應事件或是由繫結觸發函式執行時，就會計入一次執行。  
  
**計量識別碼**：D1D04836-075C-4F27-BF65-0A1130EC60ED  
**計量名稱**：函式 - 計算  
**單位**：GB-s  
**注意**：以十億位元組/秒 (GB/s) 為測量單位的資源取用量。 **觀察的資源取用量**計算方式是將平均記憶體大小 (GB) 乘以執行函式所花的時間 (毫秒)。 測量函式的記憶體使用量時，會進位到最接近 128 MB 但不超過記憶體大小上限 1,536 MB 的數值，執行時間的計算方式則是進位到最接近 1 毫秒的數值。 單一函式執行的最基本執行時間與記憶體使用量分別為 100 毫秒與 128 MB。  
  
**計量識別碼**：957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**計量名稱**：共用的 App Service 時數  
**單位**：1 小時  
**注意**：分區 App Service 方案的每小時使用量。 方案會依據每個應用程式來計量。  
  
**計量識別碼**：539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**計量名稱**：免費的 App Service 時數  
**單位**：1 小時  
**注意**：免費 App Service 方案的每小時使用量。 方案會依據每個應用程式來計量。  
  
**計量識別碼**：88039D51-A206-3A89-E9DE-C5117E2D10A6  
**計量名稱**：小型標準 App Service 時數  
**單位**：1 小時  
**注意**：根據執行個體的大小和數目來計算。  
  
**計量識別碼**：83A2A13E-4788-78DD-5D55-2831B68ED825  
**計量名稱**：中型標準 App Service 時數  
**單位**：1 小時  
**注意**：根據執行個體的大小和數目來計算。  
  
**計量識別碼**：1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**計量名稱**：大型標準 App Service 時數  
**單位**：1 小時  
**注意**：根據執行個體的大小和數目來計算。  
  
### <a name="custom-worker-tiers"></a>自訂的背景工作層   
  
**計量識別碼**：*自訂的背景工作層*  
**計量名稱**：自訂的背景工作層  
**單位**：小時  
**注意**：建立具決定性的計量識別碼時，會根據 SKU 和自訂的背景工作層名稱來建立。 此計量識別碼對每個自訂背景工作層而言，都是唯一的。  
  
**計量識別碼**：264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**計量名稱**：SNI SSL  
**單位**：每個 SNI SSL 繫結  
**注意**：App Service 支援兩種 SSL 連線類型：伺服器名稱指示 (SNI) SSL 連線以及 IP 位址 SSL 連線。 SNI 架構的 SSL 適用於新式瀏覽器，而 IP 架構的 SSL 則適用於所有瀏覽器。  
  
**計量識別碼**：60B42D72-DC1C-472C-9895-6C516277EDB4  
**計量名稱**：IP SSL  
**單位**：每個 IP 型 SSL 繫結  
**注意**：App Service 支援兩種 SSL 連線類型：伺服器名稱指示 (SNI) SSL 連線以及 IP 位址 SSL 連線。 SNI 架構的 SSL 適用於新式瀏覽器，而 IP 架構的 SSL 則適用於所有瀏覽器。  
  
**計量識別碼**：73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**計量名稱**：Web 處理序  
**單位**：  
**注意**：每小時針對作用中網站計算一次。  
  
**計量識別碼**：5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**計量名稱**：外部輸出頻寬  
**單位**：GB  
**注意**：連入要求回應位元組總數 + 連出要求位元組總數 + 連入 FTP 要求回應位元組總數 + 連入 Web 部署要求回應位元組總數。  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsdocsmicrosoftcomazurebillingbilling-usage-rate-card-overviewazure-resource-usage-api-preview-currently-in-public-preview"></a>Azure Stack 使用情況 API 與 [Azure 使用情況 API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (目前為公開預覽版) 相較有何異同？
* 租用戶使用量 API 與 Azure API 相同，但有一點除外：Azure Stack 目前不支援 *showDetails* 旗標。
* 提供者使用量 API 只適用於 Azure Stack。
* 目前，Azure Stack 不提供 Azure 中提供的 [RateCard API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview)。

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>「使用時間」與「回報時間」之間有何差異？
使用情況資料報告有兩個主要時間值：

* **回報時間**。 使用情況事件進入使用情況系統的時間
* **使用時間**。 取用 Azure Stack 資源的時間

針對特定使用情況事件，您可能會發現「使用時間」與「回報時間」的值有差異。 延遲在任何環境中可能長達數小時。

目前，您只能依「回報時間」查詢。

## <a name="what-do-these-usage-api-error-codes-mean"></a>這些使用情況 API 錯誤碼的意義為何？
| **HTTP 狀態碼** | **錯誤碼** | **說明** |
| --- | --- | --- |
| 400/不正確的要求 |*NoApiVersion* |未提供 *api-version* 查詢參數。 |
| 400/不正確的要求 |*InvalidProperty* |缺少屬性或屬性具有無效的值。 回應主體錯誤碼中的訊息指出缺少的屬性。 |
| 400/不正確的要求 |*RequestEndTimeIsInFuture* |*ReportedEndTime* 的值是未來時間。 此引數不允許未來時間值。 |
| 400/不正確的要求 |*SubscriberIdIsNotDirectTenant* |提供者 API 呼叫已使用的訂用帳戶識別碼並非呼叫者的有效租用戶。 |
| 400/不正確的要求 |*SubscriptionIdMissingInRequest* |缺少呼叫者的訂用帳戶識別碼。 |
| 400/不正確的要求 |*InvalidAggregationGranularity* |所要求的彙總細微性無效。 有效的值為每日與每小時。 |
| 503 |*ServiceUnavailable* |發生可重試的錯誤，因為服務忙碌中或呼叫已由系統進行節流處理。 |

## <a name="next-steps"></a>後續步驟
[Azure Stack 中的客戶帳務與退款](azure-stack-billing-and-chargeback.md)

[提供者資源使用情況 API](azure-stack-provider-resource-api.md)

[租用戶資源使用情況 API](azure-stack-tenant-resource-usage-api.md)
