---
title: 包含檔案
description: 包含檔案
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 9ad1208283527f35e04dede2706fd0e424096dc5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2019
ms.locfileid: "54300631"
---
下列限制適用於 Azure 備份。

| **限制** | **預設值** |
| --- | --- |
| 可在保存庫中註冊的伺服器/電腦 | Windows Server/Windows 用戶端/System Center DPM：50 <br/><br/> IaaS VM：1000  |
| 保存庫儲存體中的資料來源大小 |最大 54400 GB。 該上限不適用於 IaaS VM 備份 |
| 在 Azure 訂用帳戶中的備份保存庫 |每個區域 500 個保存庫 |
| 排定每日備份 |Windows Server/用戶端：一天 3 次<br/> System Center DPM：一天 2 次 <br/> IaaS VM：一天 1 次  |
| 連接到 Azure 虛擬機器進行備份的資料磁碟 | 16 |
| 連接到 Azure 虛擬機器進行備份的個別資料磁碟| 4095 GB|
