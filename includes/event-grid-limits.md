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
ms.openlocfilehash: 443db1b4609e62fb7c57de417e42a2b4d0737ada
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553425"
---
Azure Event Grid 系統主題和自訂主題，會適用下列限制*不*事件網域。

| 資源 | 限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的自訂主題 | 100 |
| 每個主題的事件訂用帳戶 | 500 |
| 自訂主題 (輸入) 的發佈速率 | 每個主題每秒 5,000 個事件 |

下列限制適用於僅限事件的網域。

| 資源 | 限制 |
| --- | --- |
| 每個事件網域的主題 | 公開預覽期間 1,000 個 |
| 事件訂用帳戶每個網域中的主題 | 公開預覽期間 50 個 |
| 網域範圍事件訂用帳戶 | 公開預覽期間 50 個 |
| 發行事件網域 （輸入） 的速率 | 公開預覽期間每秒 5,000 個事件 |