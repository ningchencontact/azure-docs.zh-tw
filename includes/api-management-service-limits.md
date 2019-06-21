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
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174382"
---
| 資源 | 限制 |
| --- | --- |
| 縮放單位數上限 | 每個區域 10 個<sup>1</sup> |
| 快取大小 | 每個單位 5 GB<sup>2</sup> |
| 並行的後端連線<sup>3</sup>每個 HTTP 授權單位 | 每個單位 2048<sup>4</sup> |
| 最大快取回應大小 | 2 MB |
| 原則文件大小上限 | 256 KB<sup>5</sup> | 
| 每個服務執行個體的自訂閘道網域數目上限<sup>6</sup> | 20 |
| 每個服務執行個體的 CA 憑證的最大數目 | 10 | 
| 每個訂用帳戶的服務執行個體數目上限<sup>7</sup> | 20 | 
| 每個服務執行個體的訂用帳戶數目上限<sup>7</sup> | 500 |
| 每個服務執行個體的用戶端憑證數目上限<sup>7</sup> | 50 | 
| 每個服務執行個體的 API 數目上限<sup>7</sup> | 50 | 
| 每個服務執行個體的 API 作業數目上限<sup>7</sup> | 1,000 | 
| 最大值的總要求持續時間<sup>7</sup> | 30 秒 | 
| 緩衝承載大小上限<sup>7</sup> | 2 MB | 


<sup>1</sup>調整限制取決於定價層。 若要查看定價層和調整限制，請參閱[API 管理價格](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>2</sup>每單位快取大小取決於定價層。 若要查看定價層和調整限制，請參閱[API 管理價格](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>3</sup>連線集區，而且除非明確關閉由後端重複使用。<br/>
<sup>4</sup>這項限制是每個 Basic、 Standard 和 Premium 層單位。 開發人員層次是限制為 1,024。 這項限制不適用於使用情況層。<br/> 
<sup>5</sup>這個限制適用於 Basic、 Standard 和 Premium 層。 在耗用量層中，原則文件大小會限制為 4 KB。<br/>
<sup>6</sup>此資源是只在進階層中可用。<br/>
<sup>7</sup>耗用量層僅適用於此資源。<br/>



