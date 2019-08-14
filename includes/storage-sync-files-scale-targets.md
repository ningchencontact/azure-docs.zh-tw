---
title: 包含檔案
description: 包含檔案
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: e7aa2b4389fe60eed80b15aff04d6f7fcbc7b013
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968834"
---
| Resource | 目標 | 固定限制 |
|----------|--------------|------------|
| 每個區域的儲存體同步服務數目 | 20個儲存體同步服務 | 是 |
| 每個儲存體同步服務的同步群組 | 100 個同步群組 | 是 |
| 每個儲存體同步服務的已註冊伺服器 | 99 部伺服器 | 是 |
| 每個同步群組的雲端端點 | 1 個雲端端點 | 是 |
| 每個同步群組的伺服器端點 | 50 個伺服器端點 | 否 |
| 每部伺服器的伺服器端點 | 30 個伺服器端點 | 是 |
| 每個同步群組的檔案系統物件 (目錄和檔案) | 50000000物件 | 否 |
| 目錄中的檔案系統物件 (目錄和檔案) 數目上限 | 5000000物件 | 是 |
| 物件 (目錄和檔案) 安全性描述元大小上限 | 64 KiB | 是 |
| 檔案大小 | 100 GiB | 否 |
| 要分層之檔案的檔案大小下限 | 64 KiB | 是 |
| 並行同步處理工作階段 | V4 代理程式和更新版本：限制會根據可用的系統資源而有所不同。 <BR> V3 代理程式：每個處理器有兩個作用中的同步會話, 或每個伺服器最多八個作用中的同步會話。 | 是

> [!Note]  
> Azure 檔案同步端點可以相應增加至 Azure 檔案共用的大小。 如果達到 Azure 檔案共用大小限制, 同步處理將無法運作。
