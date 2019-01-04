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
ms.openlocfilehash: 63922eb623576379058c9a8a367d6e52249115f2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109088"
---
下列限制適用於 Azure 備份。

| **限制** | **預設值** |
| --- | --- |
| 可在保存庫中註冊的伺服器/電腦 | Windows Server/Windows 用戶端/System Center DPM：50 <br/><br/> IaaS VM：1000  |
| 保存庫儲存體中的資料來源大小 |最大 54400 GB。 該上限不適用於 IaaS VM 備份 |
| 在 Azure 訂用帳戶中的備份保存庫 |每個區域 500 個保存庫 |
| 排定每日備份 |Windows Server/用戶端：一天 3 次<br/> System Center DPM：一天 2 次 <br/> IaaS VM：一天 1 次  |
| 連接到 Azure 虛擬機器進行備份的資料磁碟 | 32 |
| 連接到 Azure 虛擬機器進行備份的個別資料磁碟| 4095 GB|



