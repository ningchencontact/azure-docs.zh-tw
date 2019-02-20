---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2019
ms.author: alkohli
ms.openlocfilehash: 783ae29e9ca0c9a609d1d1d283525a952c2f7f33
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118682"
---
| 連接埠號碼| 內或外 | 連接埠範圍| 必要|   注意 |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Out/In|WAN  |否|輸出連接埠用於網際網路存取以擷取更新。 <br>輸出 Web Proxy 可由使用者設定。 |
| TCP 443 (HTTPS)|Out/In|WAN|yes|輸出連接埠用來存取雲端中的資料。<br>輸出 Web Proxy 可由使用者設定。|
| UDP 123 (NTP)|外|WAN|在某些情況下<br>請參閱附註|只有當您使用網際網路型 NTP 伺服器時，才需要此連接埠。  |   
| UDP 53 (DNS)|外|LAN|在某些情況下<br>請參閱附註|只有當您使用網際網路型 DNS 伺服器時，才需要此連接埠。<br>我們建議使用本機 DNS 伺服器。 |
| TCP 5985 (WinRM)|Out/In|LAN|在某些情況下<br>請參閱附註|需要此連接埠才能透過 HTTP 上的遠端 PowerShell 連線到裝置。  |
| UDP 67 (DHCP)|外|LAN|在某些情況下<br>請參閱附註|只有當您使用本機 DHCP 伺服器時，才需要此連接埠。  |
| TCP 80 (HTTP)|Out/In|LAN|yes|此連接埠是裝置上用於本機管理本機 UI 的輸入連接埠。 <br>透過 HTTP 存取本機 UI 會自動重新導向至 HTTPS。  |
| TCP 443 (HTTPS)|Out/In|LAN|yes|此連接埠是裝置上用於本機管理本機 UI 的輸入連接埠。 |
| TCP 445 (SMB)|在|LAN|在某些情況下<br>請參閱附註|只有當您透過 SMB 連線時，才需要此連接埠。 |
| TCP 2049 (NFS)|在|LAN|在某些情況下<br>請參閱附註|只有當您透過 NFS 連線時，才需要此連接埠。 |

