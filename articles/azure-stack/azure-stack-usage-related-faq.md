---
title: 使用情況 API 相關常見問題集 | Microsoft Docs
description: Azure Stack 計量清單、與 Azure 使用情況 API 的比較、使用時間與回報時間、錯誤碼。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: f0df9b02e7672faec3a1d94997c9b27ffca275e5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159613"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Azure Stack 使用量 API 的常見問題集
此文章提供 Azure Stack 使用情況 API 的一些常見問題集解答。

## <a name="what-meter-ids-can-i-see"></a>我可以看到哪個計量識別碼？
系統會針對下列資源提供者回報使用情況：

| **資源提供者** | <bpt id="p1">**</bpt>Meter ID<ept id="p1">**</ept> | **計量名稱** | **單位** | **其他資訊** |
| --- | --- | --- | --- | --- |
| **網路** |F271A8A388C44D93956A063E1D2FA80B |靜態 IP 位址使用情況 |IP 位址| 已使用的 IP 位址計數。 如果您以每日規模呼叫使用情況 API，計量會傳回 IP 位址乘以時數。 |
| |9E2739BA86744796B465F64674B822BA |動態 IP 位址使用情況 |IP 位址| 已使用的 IP 位址計數。 如果您以每日規模呼叫使用情況 API，計量會傳回 IP 位址乘以時數。 |
| **儲存體** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*小時 |資料表所取用的總容量。 |
| |B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*小時 |分頁 Blob 所取用的總容量。 |
| |B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*小時 |佇列所取用的總容量。 |
| |09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*小時 |區塊 Blob 所取用的總容量。 |
| |B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |要求計數 (以萬為單位) |資料表服務要求 (以萬為單位)。 |
| |50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |輸入資料 (GB) |資料表服務資料輸入 (GB)。 |
| |1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |輸出 (GB) |資料表服務資料輸出 (GB) |
| |43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |要求計數 (以萬為單位) |Blob 服務要求 (以萬為單位)。 |
| |9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |輸入資料 (GB) |Blob 服務資料輸入 (GB)。 |
| |3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |輸出 (GB) |Blob 服務資料輸出 (GB)。 |
| |EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |要求計數 (以萬為單位) |佇列服務要求 (以萬為單位)。 |
| |E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |輸入資料 (GB) |佇列服務資料輸入 (GB)。 |
| |DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |輸出 (GB) |佇列服務資料輸出 (GB) |
| **Sql RP**            | CBCFEF9A-B91F-4597-A4D3-01FE334BED82 | DatabaseSizeHourSqlMeter   | MB\*小時   | 建立時的資料庫容量總計。 如果您以每日規模呼叫使用情況 API，計量會傳回 MB 乘以時數。 |
| **MySql RP**          | E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3 | DatabaseSizeHourMySqlMeter | MB\*小時    | 建立時的資料庫容量總計。 如果您以每日規模呼叫使用情況 API，計量會傳回 MB 乘以時數。 |
| **計算** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |基礎 VM 大小小時 |虛擬核心小時數 | 虛擬核心數目乘以 VM 執行時數。 |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Windows VM 大小小時 |虛擬核心小時數 | 虛擬核心數目乘以 VM 執行時數。 |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |VM 大小小時 |VM 小時 |同時擷取基本與 VM。 不針對核心調整。 |
| **金鑰保存庫** |EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Key Vault 交易 | 要求計數 (以萬為單位)| Key Vault 資料層所接收的 REST API 要求數目。 |
| |2C354225-B2FE-42E5-AD89-14F0EA302C87 |進階金鑰交易 | 10000 個交易|     RSA 3K/4K、ECC 金鑰交易。 (預覽)。 |
| **App Service** | 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA | App Service 方案 | 虛擬核心小時數 | 用來執行應用程式服務的虛擬核心數目。 注意：Microsoft 會使用此計量來收取 Azure Stack 上的 App Service 費用。 雲端服務提供者可以使用其他 App Service 計量 (下方) 來計算其租用戶的使用量。 |
|  | 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE | 函式要求 | 10 個要求 | 要求的執行總數 (每 10 個執行)。 每次執行函式以回應事件或是由繫結觸發函式執行時，就會計入一次執行。 |
|  | D1D04836-075C-4F27-BF65-0A1130EC60ED | 函式 - 計算 | GB-s | 以十億位元組/秒 (GB/s) 為測量單位的資源取用量。 **觀察的資源取用量**計算方式是將平均記憶體大小 (GB) 乘以執行函式所花的時間 (毫秒)。 測量函式的記憶體使用量時，會進位到最接近 128 MB 但不超過記憶體大小上限 1,536 MB 的數值，執行時間的計算方式則是進位到最接近 1 毫秒的數值。 單一函式執行的最基本執行時間與記憶體使用量分別為 100 毫秒與 128 MB。 |
|  | 957E9F36-2C14-45A1-B6A1-1723EF71A01D | 共用的 App Service 時數 | 1 小時 | 分區 App Service 方案的每小時使用量。 方案會依據每個應用程式來計量。 |
|  | 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9 | 免費的 App Service 時數 | 1 小時 | 免費 App Service 方案的每小時使用量。 方案會依據每個應用程式來計量。 |
|  | 88039D51-A206-3A89-E9DE-C5117E2D10A6 | 小型標準 App Service 時數 | 1 小時 | 根據執行個體的大小和數目來計算。 |
|  | 83A2A13E-4788-78DD-5D55-2831B68ED825 | 中型標準 App Service 時數 | 1 小時 | 根據執行個體的大小和數目來計算。 |
|  | 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6 | 大型標準 App Service 時數 | 1 小時 | 根據執行個體的大小和數目來計算。 |
|  | *自訂的背景工作層* | 自訂的背景工作層 | 小時 | 建立具決定性的計量識別碼時，會根據 SKU 和自訂的背景工作層名稱來建立。 此計量識別碼對每個自訂背景工作層而言，都是唯一的。 |
|  | 264ACB47-AD38-47F8-ADD3-47F01DC4F473 | SNI SSL | 每個 SNI SSL 繫結 | App Service 支援兩種 SSL 連線類型：伺服器名稱指示 (SNI) SSL 連線及 IP 位址 SSL 連線。 SNI 架構的 SSL 適用於新式瀏覽器，而 IP 架構的 SSL 則適用於所有瀏覽器。 |
|  | 60B42D72-DC1C-472C-9895-6C516277EDB4 | IP SSL | 每個 IP 型 SSL 繫結 | App Service 支援兩種 SSL 連線類型：伺服器名稱指示 (SNI) SSL 連線及 IP 位址 SSL 連線。 SNI 架構的 SSL 適用於新式瀏覽器，而 IP 架構的 SSL 則適用於所有瀏覽器。 |
|  | 73215A6C-FA54-4284-B9C1-7E8EC871CC5B | Web 處理序 |  | 每小時針對作用中網站計算一次。 |
|  | 5887D39B-0253-4E12-83C7-03E1A93DFFD9 | 外部輸出頻寬 | GB | 連入要求回應位元組總數 + 連出要求位元組總數 + 連入 FTP 要求回應位元組總數 + 連入 Web 部署要求回應位元組總數。 |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Azure Stack 使用情況 API 與 [Azure 使用情況 API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (目前為公開預覽版) 相較有何異同？
* 租用戶使用量 API 與 Azure API 相同，但有一點除外：Azure Stack 目前不支援 *showDetails* 旗標。
* 提供者使用量 API 只適用於 Azure Stack。
* 目前，Azure Stack 不提供 Azure 中提供的 [RateCard API](https://msdn.microsoft.com/library/azure/mt219004.aspx)。

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
