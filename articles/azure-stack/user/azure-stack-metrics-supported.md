---
title: Azure Stack 上 Azure 監視器的受支援計量 | Microsoft Docs
description: 深入了解 Azure Stack 上 Azure 監視器的受支援計量。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: mabrigg
ms.openlocfilehash: 7ebb9d683803a6c5a5a0fa25308b348eeea586c4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097891"
---
# <a name="supported-metrics-with-azure-monitor-on-azure-stack"></a>Azure Stack 上 Azure 監視器的受支援計量

*適用範圍：Azure Stack 整合系統*

您可以從與全域 Azure 相同 Azure Stack 上的 Azure 監視器，擷取您的計量。 您可以在入口網站中建立量值、從 REST API 取得它們，或是使用 PowerShell 或 CLI 來查詢它們。

下表列出適用於 Azure Stack 上 Azure 監視器計量管線的計量。 若要查詢及存取這些計量，您需要使用 **2018-01-01** api-version 版本的 API 設定檔。 如需 API 設定檔和 Azure Stack 的詳細資訊，請參閱[管理 Azure Stack 中的 API 版本設定檔](azure-stack-version-profiles.md)。

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| 計量 | 計量顯示名稱 | 單位 | 彙總類型 | 說明 | 維度 |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Percentage CPU | Percentage CPU | 百分比 | 平均值 | 目前虛擬機器正在使用中的已配置計算單位百分比 | 無維度 |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| 計量 | 計量顯示名稱 | 單位 | 彙總類型 | 說明 | 維度 |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | 已使用容量 | 位元組 | 平均值 | 帳戶使用的容量 | 無維度 |
| 交易 | 交易 | Count | 總計 | 向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。 | ResponseType，GeoType，ApiName |
| 輸入 | 輸入 | 位元組 | 總計 | 輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。 | GeoType，ApiName |
| 輸出 | 輸出 | 位元組 | 總計 | 輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。 | GeoType，ApiName |
| SuccessServerLatency | 成功伺服器延遲 | 毫秒 | 平均值 | Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。 | GeoType，ApiName |
| SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | 平均值 | 向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。 | GeoType，ApiName |
| 可用性 | 可用性 | 百分比 | 平均值 | 儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。 | GeoType，ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| 計量 | 計量顯示名稱 | 單位 | 彙總類型 | 說明 | 維度 |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| BlobCapacity | Blob 容量 | 位元組 | 總計 | 儲存體帳戶之 Blob 服務所使用的儲存體量 (位元組)。 | BlobType |
| BlobCount | Blob 計數 | Count | 總計 | 儲存體帳戶之 Blob 服務中的 Blob 數目。 | BlobType |
| ContainerCount | Blob 容器計數 | Count | 平均值 | 儲存體帳戶之 Blob 服務中的容器數目。 | 無維度 |
| 交易 | 交易 | Count | 總計 | 向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。 | ResponseType，GeoType，ApiName |
| 輸入 | 輸入 | 位元組 | 總計 | 輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。 | GeoType，ApiName |
| 輸出 | 輸出 | 位元組 | 總計 | 輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。 | GeoType，ApiName |
| SuccessServerLatency | 成功伺服器延遲 | 毫秒 | 平均值 | Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。 | GeoType，ApiName |
| SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | 平均值 | 向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。 | GeoType，ApiName |
| 可用性 | 可用性 | 百分比 | 平均值 | 儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。 | GeoType，ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| 計量 | 計量顯示名稱 | 單位 | 彙總類型 | 說明 | 維度 |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | 資料表容量 | 位元組 | 平均值 | 儲存體帳戶之資料表服務所使用的儲存體量 (位元組)。 | 無維度 |
| TableCount | 資料表計數 | Count | 平均值 | 儲存體帳戶之資料表服務中的資料表數目。 | 無維度 |
| TableEntityCount | 資料表實體計數 | Count | 平均值 | 儲存體帳戶之資料表服務中的資料表實體數目。 | 無維度 |
| 交易 | 交易 | Count | 總計 | 向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。 | ResponseType，GeoType，ApiName |
| 輸入 | 輸入 | 位元組 | 總計 | 輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。 | GeoType，ApiName |
| 輸出 | 輸出 | 位元組 | 總計 | 輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。 | GeoType，ApiName |
| SuccessServerLatency | 成功伺服器延遲 | 毫秒 | 平均值 | Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。 | GeoType，ApiName |
| SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | 平均值 | 向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。 | GeoType，ApiName |
| 可用性 | 可用性 | 百分比 | 平均值 | 儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。 | GeoType，ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| 計量 | 計量顯示名稱 | 單位 | 彙總類型 | 說明 | 維度 |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | 佇列容量 | 位元組 | 平均值 | 儲存體帳戶之佇列服務所使用的儲存體量 (位元組)。 | 無維度 |
| QueueCount | 佇列計數 | Count | 平均值 | 儲存體帳戶之佇列服務中的佇列數目。 | 無維度 |
| QueueMessageCount | 佇列訊息計數 | Count | 平均值 | 儲存體帳戶之佇列服務中的佇列訊息大約數目。 | 無維度 |
| 交易 | 交易 | Count | 總計 | 向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。 | ResponseType，GeoType，ApiName |
| 輸入 | 輸入 | 位元組 | 總計 | 輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。 | GeoType，ApiName |
| 輸出 | 輸出 | 位元組 | 總計 | 輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。 | GeoType，ApiName |
| SuccessServerLatency | 成功伺服器延遲 | 毫秒 | 平均值 | Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。 | GeoType，ApiName |
| SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | 平均值 | 向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。 | GeoType，ApiName |
| 可用性 | 可用性 | 百分比 | 平均值 | 儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。 | GeoType，ApiName |

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Stack 上的 Azure 監視器](azure-stack-metrics-azure-data.md)。
