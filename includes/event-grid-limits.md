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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "66376915"
---
下列限制適用于 Azure 事件方格系統主題和自訂主題，而*不*是事件網域。

| Resource | 限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的自訂主題 | 100 |
| 每個主題的事件訂用帳戶 | 500 |
| 自訂主題 (輸入) 的發佈速率 | 每個主題每秒 5,000 個事件 |
| 發行要求 | 每秒250 |
| 事件大小 | 公開上市（GA）中的 64 KB 支援。 1 MB 的支援目前為預覽狀態。 |

下列限制僅適用于事件網域。

| Resource | 限制 |
| --- | --- |
| 每個事件網域的主題 | 公開預覽期間 1,000 個 |
| 網域內每個主題的事件訂閱 | 公開預覽期間 50 個 |
| 網域範圍事件訂閱 | 公開預覽期間 50 個 |
| 事件網域的發佈速率（輸入） | 公開預覽期間每秒 5,000 個事件 |
| 發行要求 | 每秒250 |