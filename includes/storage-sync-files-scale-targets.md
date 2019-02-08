---
title: 包含檔案
description: 包含檔案
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/18/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 6d9370ce6a031377e37c4e830400337f3e57a62e
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2019
ms.locfileid: "55084809"
---
| 資源 | 目標 | 固定限制 |
|----------|--------------|------------|
| 每個區域的儲存體同步服務數目 | 15 個儲存體同步服務 | 是 |
| 每個儲存體同步服務的同步群組 | 100 個同步群組 | 是 |
| 每個儲存體同步服務的已註冊伺服器 | 99 部伺服器 | 是 |
| 每個同步群組的雲端端點 | 1 個雲端端點 | 是 |
| 每個同步群組的伺服器端點 | 50 個伺服器端點 | 否 |
| 每部伺服器的伺服器端點 | 30 個伺服器端點 | 是 |
| 端點大小 | 4 TiB | 否 |
| 每個同步群組的檔案系統物件 (目錄和檔案) | 2500 萬個物件 | 否 |
| 目錄中的檔案系統物件 (目錄和檔案) 數目上限 | 200,000 個物件 | 是 |
| 物件 (目錄和檔案) 名稱長度上限 | 255 個字元 | 是 |
| 物件 (目錄和檔案) 安全性描述元大小上限 | 4 KiB | 是 |
| 檔案大小 | 100 GiB | 否 |
| 要分層之檔案的檔案大小下限 | 64 KiB | 是 |
| 並行同步處理工作階段 | V4 代理程式：限制隨可用的系統資源而不同。 <BR> V3 代理程式：每個處理器 2 個作用中的同步處理工作階段，或每部伺服器最多 8 個作用中的同步處理工作階段 | 是
