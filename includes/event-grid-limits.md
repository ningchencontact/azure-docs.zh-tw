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
ms.openlocfilehash: 890095718833c90a6764d0799d2081b393b1d0bd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887746"
---
下列限制適用于 Azure 事件方格系統主題和自訂主題，而*不*是事件網域。

| 資源 | 限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的自訂主題 | 100 |
| 每個主題的事件訂用帳戶 | 500 |
| 自訂主題 (輸入) 的發佈速率 | 每個主題每秒 5,000 個事件 |
| 發行要求 | 每秒250 |
| 事件大小 | 公開上市（GA）中的 64 KB 支援。 1 MB 的支援目前為預覽狀態。 |

下列限制僅適用于事件網域。

| 資源 | 限制 |
| --- | --- |
| 每個事件網域的主題 | 100,000 家 |
| 網域內每個主題的事件訂閱 | 500 |
| 網域範圍事件訂閱 | 50 |
| 事件網域的發佈速率（輸入） | 每秒5000個事件 |
| 發行要求 | 每秒250 |
| 每個 Azure 訂用帳戶的事件網域 | 100 |