---
title: 包含檔案
description: 包含檔案
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 03/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 575483192954f4e05db50e701e223829e041cffc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
| 資源 | 基本 | 標準 | 進階 |
|---|---|---|---|---|
| 儲存體 | 10 GiB | 100 GiB| 500 GiB |
| 每分鐘的 ReadOps<sup>1、2</sup> | 1000 | 3000 | 10000 |
| 每分鐘的 WriteOps<sup>1、3</sup> | 100 | 500 | 2000 |
| 下載頻寬 Mbps<sup>1</sup> | 30 | 60 | 100 |
| 上傳頻寬 Mbps<sup>1</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| 異地複寫 | N/A | N/A | [支援 (預覽)](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*、*WriteOps* 和「頻寬」是最小預估值。 ACR 致力於改善需要使用時的效能。

<sup>2</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) 會根據映像中的圖層數目以及資訊清單擷取，來轉譯為多個讀取作業。

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) 會根據必須推送的圖層數目，來轉譯為多個寫入作業。 `docker push` 包含 *ReadOps*，以擷取現有映像的資訊清單。