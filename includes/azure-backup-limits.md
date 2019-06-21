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
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174379"
---
下列限制適用於 Azure 備份。

| **限制** | **預設值** |
| --- | --- |
| 伺服器或機器可在保存庫註冊。 | Windows Server/Windows 用戶端/System Center Data Protection Manager:50. <br/><br/> IaaS VM：1,000.  |
| 保存庫儲存體中的資料來源的大小。 |54,400 GB 最大值。 此限制不適用於 IaaS VM 備份。 |
| Azure 訂用帳戶中的備份保存庫。 |每個區域 500 個保存庫。 |
| 排程每日備份。 |Windows Server/用戶端：一天安排三。<br/> System Center DPM：兩個一天。 <br/> IaaS VM：一天一次。  |
| 連接到 Azure VM 備份的資料磁碟。 | 16 |
| 連接到 Azure VM 備份的個別資料磁碟。| 4,095 GB|
