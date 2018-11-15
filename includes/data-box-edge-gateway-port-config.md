---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/06/2018
ms.author: alkohli
ms.openlocfilehash: 67de9042af11a2b17c4b65f8225ecd0580b95466
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263473"
---
| 連接埠號碼| 內或外 | 連接埠範圍| 必要|   注意 |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|外|WAN |否|輸出連接埠用於網際網路存取以擷取更新。 <br>輸出 Web Proxy 可由使用者設定。 |
| TCP 443 (HTTPS)|外|WAN|是|輸出連接埠用來存取雲端中的資料。<br>輸出 Web Proxy 可由使用者設定。|   
| UDP 53 (DNS)|外|WAN|在某些情況下<br>請參閱附註|只有當您使用網際網路型 DNS 伺服器時，才需要此連接埠。<br>我們建議使用本機 DNS 伺服器。 |
| UDP 123 (NTP)|外|WAN|在某些情況下<br>請參閱附註|只有當您使用網際網路型 NTP 伺服器時，才需要此連接埠。  |
| UDP 67 (DHCP)|外|WAN|在某些情況下<br>請參閱附註|只有當您使用 DHCP 伺服器時，才需要此連接埠。  |
| TCP 80 (HTTP)|在|LAN|是|此連接埠是裝置上用於本機管理本機 UI 的輸入連接埠。 <br>透過 HTTP 存取本機 UI 會自動重新導向至 HTTPS。  |
| TCP 443 (HTTPS)|在|LAN|是|此連接埠是裝置上用於本機管理本機 UI 的輸入連接埠。 |