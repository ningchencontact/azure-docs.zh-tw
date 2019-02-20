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
ms.openlocfilehash: c82717be3791a1bc397bbbdb369c55cd2ba04439
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212972"
---
| 資源 | 目標 | 固定限制 |
|----------|--------------|------------|
| 每個區域的儲存體同步服務數目 | 15 個儲存體同步服務 | yes |
| 每個儲存體同步服務的同步群組 | 100 個同步群組 | yes |
| 每個儲存體同步服務的已註冊伺服器 | 99 部伺服器 | yes |
| 每個同步群組的雲端端點 | 1 個雲端端點 | yes |
| 每個同步群組的伺服器端點 | 50 個伺服器端點 | 否 |
| 每部伺服器的伺服器端點 | 30 個伺服器端點 | yes |
| 端點大小 | 4 TiB | 否 |
| 每個同步群組的檔案系統物件 (目錄和檔案) | 2500 萬個物件 | 否 |
| 目錄中的檔案系統物件 (目錄和檔案) 數目上限 | 1,000,000 個物件 | yes |
| 物件 (目錄和檔案) 安全性描述元大小上限 | 4 KiB | yes |
| 檔案大小 | 100 GiB | 否 |
| 要分層之檔案的檔案大小下限 | 64 KiB | yes |
| 並行同步處理工作階段 | V4 代理程式和更新版本：限制隨可用的系統資源而不同。 <BR> V3 代理程式：每個處理器 2 個作用中的同步處理工作階段，或每部伺服器最多 8 個作用中的同步處理工作階段 | yes
