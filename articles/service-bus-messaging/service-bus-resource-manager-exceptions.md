---
title: Azure 服務匯流排 Resource Manager 例外狀況 |Microsoft Docs
description: Azure Resource Manager 和建議的動作所呈現的服務匯流排例外狀況清單。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: darosa
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2019
ms.author: aschhab
ms.openlocfilehash: e666503b9e8888e7d61445639fe0f3adeeffe55f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329301"
---
# <a name="service-bus-resource-manager-exceptions"></a>服務匯流排 Resource Manager 例外狀況

本文列出使用 Azure Resource Manager 的範本或直接呼叫來與 Azure 服務匯流排互動時所產生的例外狀況。

> [!IMPORTANT]
> 這份檔經常更新。 請回來查看是否有更新。

以下是透過 Azure Resource Manager 呈現的各種例外狀況/錯誤。

## <a name="error-bad-request"></a>Error:錯誤的要求

「不正確的要求」表示 Resource Manager 收到的要求驗證失敗。

| 錯誤碼 | 錯誤子代碼 | 錯誤訊息 | 描述 | 建議 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 錯誤的要求 | 40000 | 子代碼 = 40000。 建立佇列時，無法設定屬性 *' property name '* ，因為命名空間 *' namespace name '* 使用「基本」層。 只有「標準」或「Premium」層才支援這項作業。 | 在 Azure 服務匯流排基本層上，無法設定或更新下列屬性- <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>requiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> 主題 </li> </ul> | 請考慮從基本升級為標準層或進階層，以利用此功能。 |
| 錯誤的要求 | 40000 | 子代碼 = 40000。 無法變更現有佇列的 ' requiresDuplicateDetection ' 屬性值。 | 在建立實體時，必須啟用/停用重複偵測。 建立之後，就無法變更重複偵測設定參數。 | 若要在先前建立的佇列/訂用帳戶上啟用重複偵測，您可以建立含有重複偵測的新佇列，然後從原始佇列轉送到新的佇列。 |
| 錯誤的要求 | 40000 | 子代碼 = 40000。 指定的值16384無效。 屬性 ' MaxSizeInMegabytes ' 必須是下列其中一個值：1024; 2048; 3072; 4096; 5120。 | MaxSizeInMegabytes 值無效。 | 請確定 MaxSizeInMegabytes 是下列其中一項-1024、2048、3072、4096、5120。 |
| 錯誤的要求 | 40000 | 子代碼 = 40000。 無法變更佇列的資料分割。 | 無法變更實體的資料分割。 | 建立新的實體並啟用資料分割。 | 
| 錯誤的要求 | 無 | 命名空間 *' namespace name '* 不存在。 | 命名空間不存在於您的 Azure 訂用帳戶中。 | 若要解決此錯誤，請嘗試下列 <ul> <li> 請確定 Azure 訂用帳戶正確。 </li> <li> 確定命名空間存在。 </li> <li> 請確認命名空間名稱是否正確（沒有任何拼寫錯誤或 null 字串）。 </li> </ul> | 
| 錯誤的要求 | 40400 | 子代碼 = 40400。 自動轉送目的地實體不存在。 | 自動轉寄目的地實體的目的地不存在。 | 目的地實體（佇列或主題）必須存在，才會建立來源。 建立目的地實體之後重試。 |


## <a name="error-code-429"></a>錯誤碼:429

| 錯誤碼 | 錯誤子代碼 | 錯誤訊息 | 描述 | 建議 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | 子代碼 = 50004。 要求已終止，因為您的*命名*空間正在進行節流。 | 當傳入要求的數目超過資源的限制時，就會叫用此錯誤狀況。 | 請稍候幾秒鐘，然後再試一次。 <br/> <br/> 深入瞭解[配額](service-bus-quotas.md)和[Azure Resource Manager 要求限制](../azure-resource-manager/resource-manager-request-limits.md)|
| 429 | 40901 | 子代碼 = 40901。 另一個衝突的操作正在進行中。 | 相同的資源/實體正在進行另一個衝突的操作 | 等候目前進行中的作業完成，然後再試一次。 |
| 429 | 40900 | 子代碼 = 40900。 合併. 您要求的作業在資源的目前狀態下不允許。 | 當有多個要求同時對相同的實體（佇列、主題、訂用帳戶或規則）執行作業時，可能會遇到此狀況。 | 請稍候幾秒鐘，然後再試一次 |
| 429 | 無 | 發生資源衝突。 另一個衝突的作業可能正在進行中。 如果這是失敗作業的重試，背景清除仍處於暫止狀態。 請稍後再試。 | 當對相同實體有暫止的作業時，可能會遇到此狀況。 | 等待前一個作業完成，然後再試一次。 |

