---
title: 包含檔案
description: 包含檔案
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 30c6fc189ebcd497a214828f65213a55cefdf03f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632909"
---
儲存體受限於磁碟空間，或者索引、文件或其他高層級資源的「數目上限」，取決於何者較早出現。 下表記載儲存體限制。 如需索引、 文件和其他物件的最大限制，請參閱[資源限制](../articles/search/search-limits-quotas-capacity.md#index-limits)。

| 資源 | 免費 | 基本<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| 服務等級協定 (SLA)<sup>3</sup>  |否 |yes |是 |是 |是 |是 |是 |是 |
| 每個資料分割的儲存體 |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| 每個服務的分割區 |N/A |1 |12 |12 |12 |3 |12 |12 |
| 分割區大小 |N/A |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| 複本數 |N/A |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> [基本] 具有一個固定的磁碟分割。 在這一層，額外搜尋單位可增加的查詢工作負載配置更多複本。

<sup>2</sup> S3 HD 上有固定限制的三個資料分割，低於 S3 的分割區限制。 較低的分割區限制是因為 S3 HD 的索引計數較高。 假設計算資源 (儲存體和處理) 和內容 (索引和文件) 的服務限制都存在，會先達到內容限制。

<sup>3</sup>服務等級協定會提供專用資源上的可計費服務。 免費服務和預覽功能不提供 SLA。 對於可計費服務，SLA 會在您為您的服務佈建足夠的備援性時生效。 兩個或多個複本所需查詢 （讀取） Sla。 三個或多個複本所需查詢和檢索 （讀 / 寫） Sla。 資料分割數目並不是 SLA 考量。 