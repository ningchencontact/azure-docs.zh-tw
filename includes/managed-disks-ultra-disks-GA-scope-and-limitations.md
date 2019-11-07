---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c847b110cb821ebe3ca7e706fcaec592dcc58fa8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73600982"
---
目前，ultra 磁片有額外的限制，如下所示：

- 在下欄區域中受到支援，而且每個區域的可用性區域數目各不相同：
    - 美國東部 2
    - 美國東部
    - 美國西部 2
    - 東南亞
    - 北歐
    - 西歐
    - 英國南部 
- 只能用於可用性區域 (區域以外的可用性設定組和單一 VM 部署將無法連結 Ultra磁碟)
- 僅支援下列 VM 系列：
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 並非每個 VM 大小都適用于具有 ultra 磁片的每個支援區域。
- 只能作為資料磁碟，且僅支援 4k 實體磁區大小  
- 只能建立為空磁碟  
- 尚不支援磁片快照集、VM 映射、可用性設定組和 Azure 磁片加密
- 尚不支援與 Azure 備份或 Azure Site Recovery 的整合
- GA Vm 上目前的 IOPS 上限為80000。