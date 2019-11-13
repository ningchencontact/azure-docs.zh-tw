---
title: 對 Windows 或 Linux 虛擬機器掛接或取消掛接 Azure NetApp Files 磁碟區 | Microsoft Docs
description: 說明如何對 Windows 虛擬機器或 Linux 虛擬機器掛接或取消掛接磁碟區。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 4cd3bc7e4f95869d3efd2d92a7cdf1addc7ce5b2
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953105"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區 

您可以視需要對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區。  Linux 虛擬機器的掛接指示可與 Azure NetApp Files 上取得。  

1. 按一下 [**磁片**區] 分頁，然後選取您要掛接的磁片區。 
2. 按一下所選磁片區中的 [**掛接指示**]，然後依照指示來裝載磁片區。 

    ![裝載指示 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![裝載指示 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
如果您使用 NFSv 4.1，請使用下列命令來掛接檔案系統：  

`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

## <a name="next-steps"></a>後續步驟

* [針對 Azure NetApp Files 設定 NFSv 4.1 預設網域](azure-netapp-files-configure-nfsv41-domain.md)
* 請參閱[Nfs 常見問題](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)，瞭解如何在 Azure VM 啟動或重新開機時，自動裝載 nfs 磁片區。
