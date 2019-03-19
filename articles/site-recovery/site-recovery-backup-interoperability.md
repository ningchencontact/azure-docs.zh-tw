---
title: Azure Site Recovery-備份的互通性 |Microsoft Docs
description: 概述如何 Azure Site Recovery 和 Azure 備份可以搭配使用。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/26/2019
ms.author: sideeksh
ms.openlocfilehash: 6658ab8c967c70ac1deaeba3d1dfeac602515591
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731879"
---
# <a name="about-site-recovery-and-backup-interoperability"></a>關於站台復原和備份的互通性

本文章提供成功使用 Azure IaaS VM 備份和 Azure VM 的災害復原指引。

## <a name="azure-backup"></a>Azure 備份

Azure 備份可協助保護資料的內部部署伺服器、 虛擬機器、 虛擬化工作負載、 SQL server、 SharePoint 伺服器等等。 Azure Site Recovery 可協調及管理 Azure Vm、 內部部署 Vm 和實體伺服器災害復原。

## <a name="azure-site-recovery"></a>Azure Site Recovery

可以設定 VM 或一組 Vm 上的 Azure 備份和 Azure Site Recovery。 這兩項產品是互通的。 其中備份和 Azure Site Recovery 之間的互通性變得很重要的幾個案例如下所示：

### <a name="file-backuprestore"></a>檔案備份/還原

備份和複寫會同時啟用，並執行備份時，有任何問題，包含還原的來源端 VM 或 Vm 的群組上的任何檔案。 複寫健康狀態中的任何變更將會如往常般不繼續複寫。

### <a name="disk-backuprestore"></a>磁碟備份/還原

如果您從備份還原磁碟的虛擬機器的保護已重新啟用。

### <a name="vm-backuprestore"></a>VM 備份/還原

不支援備份和還原的 VM 或一組 Vm。 若要讓它運作，必須會重新啟用保護。

**案例** | **支援 Azure Site Recovery？** | **因應措施，如果有的話**  
--- | --- | ---
檔案/資料夾備份 | 是 | 不適用
磁碟備份 | 目前未 | 停用和啟用保護
VM 備份 | 否 | 停用和啟用保護
