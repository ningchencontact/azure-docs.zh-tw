---
title: 包含檔案
description: 包含檔案
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: b345283f87c446ff3b583b0c5dd8a4be222303ae
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
下列限制適用於 Azure 備份。

| 限制識別碼 | 預設限制 |
| --- | --- |
| 可針對每個保存庫註冊的伺服器/電腦數目 |如為 Windows Server/用戶端/SCDPM 則有 50 個， <br/> 如為 IaaS VM 則有 200 個 |
| 在 Azure 保存庫儲存體中所儲存資料的資料來源大小 |最大 54400 GB<sup>1</sup> |
| 可以在每個 Azure 訂用帳戶中建立的備份保存庫的數目 |每個區域 25 個復原服務保存庫 |
| 每日可以排程的備份次數 |Windows 伺服器/用戶端每日 3 次 <br/> 對於 SCDPM 每日 2 次 <br/> IaaS VM 每日一次 |
| 連接到 Azure 虛擬機器進行備份的資料磁碟 |16 |
| 連接到 Azure 虛擬機器進行備份的個別資料磁碟大小| 4095 GB <sup>2</sup>|

* <sup>1</sup>54400 GB 上限並不適用於 IaaS VM 備份。
 

