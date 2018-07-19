---
title: 包含檔案
description: 包含檔案
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 05/29/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 92a5d162e7a0b2c752a2f8e9c5941edf43e539e3
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991064"
---
| 資源 | 基本 | 標準 | 進階 |
|---|---|---|---|---|
| 儲存體 | 10 GiB | 100 GiB| 500 GiB |
| 映像層大小上限 | 20 GiB | 20 GiB | 50 GiB |
| 每分鐘的 ReadOps<sup>1、2</sup> | 1,000 | 3,000 | 10,000 |
| 每分鐘的 WriteOps<sup>1、3</sup> | 100 | 500 | 2,000 |
| 下載頻寬 Mbps<sup>1</sup> | 30 | 60 | 100 |
| 上傳頻寬 Mbps<sup>1</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| 異地複寫 | N/A | N/A | [支援](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*、*WriteOps* 和「頻寬」是最小預估值。 ACR 致力於改善需要使用時的效能。

<sup>2</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) 會根據映像中的圖層數目以及資訊清單擷取，來轉譯為多個讀取作業。

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) 會根據必須推送的圖層數目，來轉譯為多個寫入作業。 `docker push` 包含 *ReadOps*，以擷取現有映像的資訊清單。
