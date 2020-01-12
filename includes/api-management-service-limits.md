---
ms.openlocfilehash: ee44cd4ee5ec6fedfec46cbbb68b0c30118a4b9f
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904449"
---
---

標題： include file description： include file services： api 管理作者： vladvino

assetid： 1b813833-39c8-46be-8666-fd0960cfbf04 ms-chap： api 管理。主題：包含 ms-chap： 01/10/2020 ms. author： vlvinogr ms. custom： include file
---| 資源 | 限制 |
| ---------------------------------------------------------------------- | -------------------------- |
| 縮放單位數上限 | 每個區域 10 個<sup>1</sup> |
| 快取大小 | 每個單位 5 GiB<sup>2</sup> |
| 每個 HTTP 授權單位的並行後端連線數<sup>3</sup> | 每單位 2048<sup>4</sup> |
| 最大快取回應大小 | 2 MiB |
| 原則文件大小上限 | 256 KiB<sup>5</sup> |
| 每個服務執行個體的自訂閘道網域數目上限<sup>6</sup> | 20 |
| 每個服務實例的 CA 憑證數目上限 | 10 |
| 每個訂用帳戶的服務執行個體數目上限<sup>7</sup> | 20 |
| 每個服務執行個體的訂用帳戶數目上限<sup>7</sup> | 500 |
| 每個服務執行個體的用戶端憑證數目上限<sup>7</sup> | 50 |
| 每個服務執行個體的 API 數目上限<sup>7</sup> | 50 |
| 每個服務執行個體的 API 作業數目上限<sup>7</sup> | 1,000 |
| 總要求持續時間上限<sup>7</sup> | 30 秒 |
| 緩衝承載大小上限<sup>7</sup> | 2 MiB |
| 要求 URL 大小上限<sup>8</sup> | 4096個位元組 |

<sup>1</sup>調整限制取決於定價層。 若要查看定價層和其調整限制，請參閱[API 管理價格](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>2</sup>每個單位的快取大小取決於定價層。 若要查看定價層和其調整限制，請參閱[API 管理價格](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>3</sup>除非後端明確關閉，否則連線會進行共用並重複使用。<br/>
<sup>4</sup>此限制是基本、標準和進階層的每個單位。 開發人員層的限制為 1024。 此限制不適用於消費層。<br/>
<sup>5</sup>此限制適用于基本、標準和 Premium 層。 在取用量層中，原則檔案大小限制為 4 KiB。<br/>
<sup>6</sup>此資源僅僅用於進階層。<br/>
<sup>7</sup>此資源僅適用於取用層。<br/>
<sup>8</sup>僅適用于耗用量層。 包含最多2048個位元組長的查詢字串。<br/>
