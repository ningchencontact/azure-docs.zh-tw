---
title: 包含檔案
description: 包含檔案
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 04/30/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 2425d9455606f5eabba4b89cfa238b7a928be30e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285760"
---
####

下列限制適用於事件方格系統主題和自訂主題，「不」適用於事件網域。

| 資源 | 限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的自訂主題 | 100 |
| 每個主題的事件訂用帳戶 | 500 |
| 自訂主題 (輸入) 的發佈速率 | 每個主題每秒 5,000 個事件 |

####

下列限制僅適用於事件網域。

| 資源 | 限制 |
| --- | --- |
| 每個事件網域的主題 | 公開預覽期間 1,000 個 |
| 網域內每個主題的事件訂用帳戶 | 公開預覽期間 50 個 |
| 網域範圍事件訂用帳戶 | 公開預覽期間 50 個 |
| 事件網域的發佈速率 (輸入) | 公開預覽期間每秒 5,000 個事件 |