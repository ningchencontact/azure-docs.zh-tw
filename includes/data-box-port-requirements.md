---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839750"
---
| 連接埠號碼| 內或外 | 連接埠範圍| 必要項| 注意 |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|在|LAN|是|此連接埠用來透過 HTTP 連接到資料方塊部落格儲存體 REST Api。 如果未連接至 REST Api，這會自動重新導向至本機 web UI 超過 8443。 |
| TCP 443 (HTTPS)|在|LAN|是|此連接埠用來透過 HTTPS 連線到資料方塊部落格儲存體 REST Api。 如果未連接至 REST Api，這會自動重新導向至本機 web UI 超過 8443。 |
| TCP 8443 (HTTPS-Alt)|在|LAN|是|這是適用於 HTTPS 的替代連接埠，並連接至本機 web UI 進行裝置管理時。 |
| TCP 445 (SMB)|Out/In|LAN|在某些情況下<br>請參閱附註|只有當您透過 SMB 連線時，才需要此連接埠。 |
| TCP 2049 (NFS)|Out/In|LAN|在某些情況下<br>請參閱附註|只有當您透過 NFS 連線時，才需要此連接埠。 |
| TCP 111 (NFS)|Out/In|LAN|在某些情況下<br>請參閱附註|此連接埠是用於 rpcbind/連接埠對應，而且如果您要連線透過 NFS 才需要。  |
