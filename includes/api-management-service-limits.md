---
title: 包含檔案
description: 包含檔案
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: e01eebe41010135d0dc0a2cb4170e6b6687ff546
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292670"
---
| 資源 | 限制 |
| --- | --- |
| 縮放單位數上限 | 每個區域 10 個<sup>1</sup> |
| 快取大小 | 每個單位 5 GB<sup>2</sup> |
| 每個 HTTP 授權單位的並行後端連線數目<sup>3</sup> | 每個單位 2048 個<sup>4</sup> |
| 最大快取回應大小 | 2MB |
| 原則文件大小上限 | 256KB<sup>5</sup> | 
| 每個服務執行個體的自訂閘道網域數目上限<sup>6</sup> | 20 | 
| 每個訂用帳戶的服務執行個體數目上限<sup>7</sup> | 5 | 
| 每個服務執行個體的訂用帳戶數目上限<sup>7</sup> | 500 |
| 每個服務執行個體的用戶端憑證數目上限<sup>7</sup> | 50 | 
| 每個服務執行個體的 API 數目上限<sup>7</sup> | 50 | 
| 每個服務執行個體的 API 作業數目上限<sup>7</sup> | 1000 | 
| 總要求持續時間上限<sup>7</sup> | 30 秒 | 
| 緩衝承載大小上限<sup>7</sup> | 2MB | 


<sup>1</sup> 調整限制取決於定價層。 若要查看定價層和調整限制，請移至 [API 管理價格](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>2</sup> 每一單位快取大小取決於定價層。 若要查看定價層和調整限制，請移至 [API 管理價格](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>3</sup> 除非後端明確關閉連線，否則連線會經過集區化和重複使用。<br/>
<sup>4</sup> 基本、標準和高階層的每個單位。 開發人員層的限制為 1024 個。 不適用於使用層。<br/> 
<sup>5</sup> 在基本、標準和進階層中。 在使用層原則中，文件大小限制為 4 KB。<br/>
<sup>6</sup> 僅適用於進階層。<br/>
<sup>7</sup> 僅適用於使用層。<br/>



