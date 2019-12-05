---
title: 包含檔案
description: 包含檔案
services: backup
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: ca6c57b139b195b94dc7171336cfcc34d18b1872
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828980"
---
下列限制適用於 Azure 備份。

| **限制** | **預設值** |
| --- | --- |
| 可以在保存庫中註冊的伺服器或電腦。 | Windows Server/Windows Client/System Center Data Protection Manager：50。 <br/><br/> IaaS Vm：1000。  |
| 保存庫儲存體中的資料來源大小。 |最大 54400-GB。 此限制不適用於 IaaS VM 備份。 |
| Azure 訂用帳戶中的備份保存庫。 |500每個區域的保存庫。 |
| 排定每日備份。 |Windows Server/用戶端：一天三次。<br/> System Center DPM：一天兩次。 <br/> IaaS Vm：一天一次。  |
| 連接到 Azure VM 以進行備份的資料磁片。 | 16 |
| 連接到 Azure VM 以進行備份的個別資料磁片。| 32 TB|
