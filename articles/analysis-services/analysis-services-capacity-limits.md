---
title: Azure Analysis Services 資源和物件限制 | Microsoft Docs
description: 描述 Azure Analysis Services 資源和物件限制。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: beb0c1f9d6b54c029af48684fd81b38699dd2720
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630643"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services 資源和物件限制。

本文描述資源和模型物件限制。

## <a name="tier-limits"></a>階層限制

### <a name="developer-tier"></a>開發人員層

建議將此層用於評估、開發及測試案例。 單一方案包含與標準層相同的功能，但其處理能力、QPU 及記憶體大小有限。 此層無法使用查詢複本相應放大功能。 此層不提供 SLA。

|規劃  |QPU  |記憶體 (GB)  |
|---------|---------|---------|
|D1    |    20     |    3     |


### <a name="basic-tier"></a>基本層

此層建議用於具備小型表格式模型、使用者並行能力有限，且只需簡單資料重新整理功能的生產解決方案。 此層「無法使用」查詢複本相應放大功能。 此層不支援檢視方塊、多個分割區和 DirectQuery 表格式模型功能。  

|規劃  |QPU  |記憶體 (GB)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    20     |

### <a name="standard-tier"></a>標準層

對於需要靈活使用者並行且具備快速成長資料模型的任務關鍵性生產應用程式，適合使用此層。 此層支援進階的資料重新整理以提供近乎即時的資料模型更新，並支援所有表格式模型化功能。

|規劃  |QPU  |記憶體 (GB)  |
|---------|---------|---------|
|S1    |    40     |    10     |
|S2    |    100     |    25     |
|S3    |    200     |    50     |
|S4    |    400     |    100     |
|S8*    |    320     |    200     |
|S9*    |    640    |    400     |

\* 並非所有區域都有提供。  

## <a name="object-limits"></a>物件限制

這些是理論上的限制。 效能會因數量減少而降低。

|Object|大小/數目的最大值|  
|------------|----------------------------|  
|執行個體中的資料庫|16,000|  
|資料庫中資料表和資料行的合併數|16,000|  
|資料表中的資料列|無限<br /><br /> **警告：** 有一項限制，就是資料表中的任何資料行都不能擁有超過 1,999,999,997 個相異值。|  
|資料表中的階層|15,999|  
|階層中的層級|15,999|  
|關聯性|8,000|  
|所有資料表中的索引鍵資料行|15,999|  
|資料表中的量值|2^31-1 = 2,147,483,647|  
|由查詢所傳回的資料格|2^31-1 = 2,147,483,647|  
|來源查詢的記錄大小|64 K|  
|物件名稱的長度|512 個字元|  


