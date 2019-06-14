---
title: 包含檔案
description: 包含檔案
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 3f94481e6a8550479788d92c744327e1dc3b58c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66376915"
---
Azure Event Grid 系統主題和自訂主題，會適用下列限制*不*事件網域。

| 資源 | 限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的自訂主題 | 100 |
| 每個主題的事件訂用帳戶 | 500 |
| 自訂主題 (輸入) 的發佈速率 | 每個主題每秒 5,000 個事件 |
| 發佈要求 | 每秒 250 |
| 事件大小 | 支援的 64 KB 一般情況下運作 (GA)。 1 MB 的支援目前為預覽狀態。 |

下列限制適用於僅限事件的網域。

| 資源 | 限制 |
| --- | --- |
| 每個事件網域的主題 | 公開預覽期間 1,000 個 |
| 事件訂用帳戶每個網域中的主題 | 公開預覽期間 50 個 |
| 網域範圍事件訂用帳戶 | 公開預覽期間 50 個 |
| 發行事件網域 （輸入） 的速率 | 公開預覽期間每秒 5,000 個事件 |
| 發佈要求 | 每秒 250 |