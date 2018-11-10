---
title: Avere vFXT for Azure 的連接埠白名單
description: Avere vFXT for Azure 所使用之連接埠的清單
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 394415ffc7593d058d8d0bf1c0040b88ec25073b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669925"
---
# <a name="required-ports"></a>所需連接埠

本節中所列的連接埠用於 vFXT 輸入和輸出通訊。

絕不會向公用網際網路公開 vFXT 叢集或叢集控制器執行個體。

## <a name="api"></a>API

| 輸入： | | |
| --- | ---- | --- |
| TCP | 22  | SSH  |
| TCP | 443 | HTTPS|



| 輸出： |     |       |
|----------|-----|-------|
| TCP      | 443 | HTTPS |

 
## <a name="nfs"></a>NFS

| 輸入和輸出  | | |
| --- | --- | ---|
| TCP/UDP | 111  | RPCBIND  |
| TCP/UDP | 2049 | NFS      |
| TCP/UDP | 4045 | NLOCKMGR |
| TCP/UDP | 4046 | MOUNTD   |
| TCP/UDP | 4047 | 狀態   |

