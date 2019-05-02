---
title: 包含檔案
description: 包含檔案
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 04/29/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: d20e266d1331fc15e65b2d119468483ff53a4c06
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64951508"
---
| Resource | 基本 | 標準 | 進階 |
|---|---|---|---|
| 儲存體<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| 最大的映像層大小 | 200 GiB | 200 GiB | 200 GiB |
| 每分鐘的 ReadOps<sup>2, 3</sup> | 1,000 | 3,000 | 10,000 |
| 每分鐘的 WriteOps<sup>2, 4</sup> | 100 | 500 | 2,000 |
| 下載頻寬 Mbps<sup>2</sup> | 30 | 60 | 100 |
| 上傳頻寬 Mbps<sup>2</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| 異地複寫 | N/A | N/A | [支援][geo-replication] |
| 內容信任 (預覽) | N/A | N/A | [支援][content-trust] |

<sup>1</sup>是指當指定的儲存體限制*包含*每個層級的儲存體。 對於超過這些限制的影像儲存，您需要依每 GiB 的每日費率另外支付費用。 費率的詳細資訊，請參閱 < [Azure Container Registry 定價][pricing]。

<sup>2</sup>*ReadOps*， *WriteOps*，和*頻寬*是最小的估計值。 Azure Container Registry 致力於改善效能，需要使用。

<sup>3</sup>A [docker 提取](https://docs.docker.com/registry/spec/api/#pulling-an-image)會轉譯為多個映像，加上資訊清單擷取的圖層數目為基礎的讀取作業。

<sup>4</sup>A [docker 推播](https://docs.docker.com/registry/spec/api/#pushing-an-image)會轉譯為多個寫入作業，根據必須推送的圖層數目。 `docker push` 包含 *ReadOps*，以擷取現有映像的資訊清單。

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
