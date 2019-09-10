---
title: 包含檔案
description: 包含檔案
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/14/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ee8ff3529524a63ca2e54a64327570197f363538
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "67174343"
---
| Resource | 基本 | 標準 | 進階 |
|---|---|---|---|
| 儲存體<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| 影像圖層大小上限 | 200 GiB | 200 GiB | 200 GiB |
| 每分鐘的 ReadOps<sup>2, 3</sup> | 1,000 | 3,000 | 10,000 |
| 每分鐘的 WriteOps<sup>2, 4</sup> | 100 | 500 | 2,000 |
| 下載頻寬 Mbps<sup>2</sup> | 30 | 60 | 100 |
| 上傳頻寬 Mbps<sup>2</sup> | 10 | 20 | 50 |
| webhooks | 2 | 10 | 100 |
| 異地複寫 | N/A | N/A | [支援][geo-replication] |
| 內容信任 | N/A | N/A | [支援][content-trust] |

<sup>1</sup>指定的儲存體限制是每一層的*內含*儲存體數量。 對於超過這些限制的影像儲存，您需要依每 GiB 的每日費率另外支付費用。 如需費率資訊，請參閱[Azure Container Registry 定價][pricing]。

<sup>2</sup>*ReadOps*、 *WriteOps*和*頻寬*是最小的預估值。 Azure Container Registry 在使用需求時，致力於改善效能。

<sup>3</sup>[Docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image)會根據映射中的圖層數目以及資訊清單抓取來轉譯為多個讀取作業。

<sup>4</sup>[Docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image)會根據必須推送的圖層數目，來轉譯為多個寫入作業。 `docker push` 包含 *ReadOps*，以擷取現有映像的資訊清單。

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
