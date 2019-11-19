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
ms.openlocfilehash: 84496fbc8a415171172d0a138f647ecb0310b6c7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173591"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區 

您可以視需要對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區。  Linux 虛擬機器的掛接指示可與 Azure NetApp Files 上取得。  

1. 按一下 [**磁片**區] 分頁，然後選取您要掛接的磁片區。 
2. 按一下所選磁片區中的 [**掛接指示**]，然後依照指示來裝載磁片區。 

    ![裝載指示 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![裝載指示 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    如果您使用 NFSv 4.1，請使用下列命令來掛接檔案系統： `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. 如果您想要在 Azure VM 啟動或重新開機時自動掛接 NFS 磁片區，請將專案新增至主機上的 `/etc/fstab` 檔案。 

    例如：`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` 是在 [磁片區屬性] 分頁中找到之 Azure NetApp Files 磁片區的 IP 位址。
    * `$FILEPATH` 是 Azure NetApp Files 磁片區的匯出路徑。
    * `$MOUNTPOINT` 是在用來裝載 NFS 匯出的 Linux 主機上建立的目錄。

## <a name="next-steps"></a>後續步驟

* [針對 Azure NetApp Files 設定 NFSv 4.1 預設網域](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS 常見問題](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
