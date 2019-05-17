---
title: Azure 的 NetApp 檔案相關的常見問題集 |Microsoft Docs
description: 關於 Azure NetApp 檔案常見問題的解答。
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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 6f1ca3398678b59a81e5c22b51b36a1f5505d4c2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806382"
---
# <a name="faqs-about-azure-netapp-files"></a>Azure 的 NetApp 檔案相關的常見問題集

本文會回答常見問題 (Faq) Azure NetApp 檔案。 

## <a name="networking-faqs"></a>網路功能的常見問題集

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>NFS 資料路徑會經由網際網路嗎？  

沒有。 NFS 資料路徑就不會經過網際網路。 Azure 的 NetApp 檔案是一種 Azure 的原生服務，部署到 Azure 虛擬網路 (VNet) 服務可使用的。 Azure 的 NetApp 檔案會使用委派的子網路，並佈建的網路介面，直接在 VNet 上。 

請參閱[指導方針適用於 Azure NetApp 檔案的網路規劃](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)如需詳細資訊。  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>可以連接的 VNet，已建立 Azure NetApp 檔案服務嗎？

是，您可以連接您所建立的 Vnet 至服務。 

請參閱[指導方針適用於 Azure NetApp 檔案的網路規劃](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)如需詳細資訊。  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>我可以掛接的 NFS 磁碟區，Azure NetApp 檔案使用的 DNS FQDN 名稱嗎？

是的您可以，如果您建立所需的 DNS 項目。 Azure 的 NetApp 檔案提供佈建的磁碟區的服務 IP。 

> [!NOTE] 
> Azure 的 NetApp 檔案可以部署額外服務的 Ip，視需要。  DNS 項目可能需要定期更新。

## <a name="security-faqs"></a>安全性常見問題集

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM 和儲存體之間的網路流量會加密？

未加密的資料流量 （Azure NetApp 檔案磁碟區有流量從 NFSv3 或 SMBv3 用戶端）。 不過，Azure NetApp 檔案到 Azure VM （執行 SMB 或 NFS 用戶端） 的流量會與任何其他的 Azure-VM-VM 流量一樣安全。 此流量是在 Azure 資料中心網路的本機。 

### <a name="can-the-storage-be-encrypted-at-rest"></a>可以靜止加密儲存體？

所有 Azure NetApp 檔案磁碟區會使用 FIPS 140-2 標準來都加密。 所有索引鍵是由 Azure NetApp 檔案服務管理。 

### <a name="how-are-encryption-keys-managed"></a>如何管理加密金鑰？ 

適用於 Azure NetApp 檔案的金鑰管理是由服務處理。  目前不支援使用者管理的金鑰 （攜帶您自己的金鑰）。

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>可以設定的 NFS 匯出原則規則，以控制存取 Azure NetApp 檔案服務掛接目標嗎？


是，您也可以在單一的 NFS 匯出原則中設定最多五個規則。

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp 檔案是否支援網路安全性群組？

否，目前您無法將網路安全性群組套用至委派的子網路的 Azure NetApp 檔案服務或服務所建立的網路介面。

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>可以使用 Azure IAM 與 Azure NetApp 檔案嗎？

是，Azure NetApp 檔案支援以 Azure IAM RBAC 功能。

## <a name="performance-faqs"></a>效能常見問題集

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>我該怎麼辦最佳化，或調整 Azure NetApp 檔案效能？

您可以採取下列動作，根據效能需求： 
- 請確定虛擬機器大小適當。
- 啟用 vm 的加速的網路。
- 選取所需的服務層級和容量集區大小。
- 建立磁碟區所需的配額大小的容量和效能。

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>如何將輸送量為基礎的服務層級，Azure NetApp 檔案轉換成 IOPS？

您可以使用下列公式，將 MB/秒轉換成 IOPS:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>如何變更服務層級的磁碟區？

目前不支援變更服務層級的磁碟區。

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>如何監視 Azure NetApp 檔案效能？

Azure 的 NetApp 檔案提供磁碟區效能計量。 您也可以使用 Azure 監視器來監視 Azure NetApp 檔案的使用計量。  請參閱[計量 Azure NetApp 檔案](azure-netapp-files-metrics.md)Azure NetApp 檔案的效能度量清單。

## <a name="nfs-faqs"></a>NFS 常見問題集

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>我想要有 Azure VM 已啟動或重新啟動時自動掛接磁碟區。  如何設定我的永續性的 NFS 磁碟區的主機？

NFS 磁碟區自動掛接在 VM 啟動或重新開機，項目新增至`/etc/fstab`主機上的檔案。 

例如：`$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    在 [磁碟區屬性] 刀鋒視窗中找到 Azure NetApp 檔案磁碟區的 IP 位址
- $FILEPATH  
    匯出路徑之 Azure NetApp 檔案磁碟區
- $MOUNTPOINT  
    建立用來掛接 NFS 匯出 Linux 主機上的目錄

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>為什麼 DF 命令，NFS 用戶端上的未顯示佈建的磁碟區大小？

DF 中報告的磁碟區大小是 Azure NetApp 檔案磁碟區可以成長的大小上限。 無法反射的配額或磁碟區大小的 DF 命令中的 Azure NetApp 檔案磁碟區大小。  您可以透過 Azure 入口網站或 API 來取得 Azure NetApp 檔案磁碟區大小或配額。

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp 檔案支援 NFS 版本為何？

Azure 的 NetApp 檔案服務目前支援 NFSv3。

### <a name="how-do-i-enable-root-squashing"></a>如何啟用根壓縮？

目前不支援壓縮的根。

## <a name="smb-faqs"></a>SMB 常見問題集

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp 檔案是否支援 Azure Active Directory？

否，它目前不支援。  Azure 的 NetApp 檔案支援 Active Directory 網域服務 （攜帶您擁有 AD），可以使用現有的 Active Directory 網域控制站與 Azure NetApp 檔案。 網域控制站可以位於 Azure 虛擬機器，或透過 ExpressRoute 的內部部署上。

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>是 Active Directory 連線所需的 SMB 存取權嗎？ 

是，您必須建立 Active Directory 連接之前部署的 SMB 磁碟區。 指定的網域控制站必須可由委派的子網路的連線成功的 Azure NetApp 檔案存取。  請參閱[建立的 SMB 磁碟區](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes#create-an-smb-volume)如需詳細資訊。 

### <a name="how-many-active-directory-connections-are-supported"></a>支援多少個 Active Directory 連線？

Azure 的 NetApp 檔案服務目前支援的訂用帳戶每一個 Active Directory 連線。 此外，Active Directory 連接僅以單一的 NetApp 帳戶;它不會跨帳戶共用。 

### <a name="what-versions-of-windows-ad-are-supported"></a>支援哪些版本的 Windows AD？

Azure 的 NetApp 檔案支援 Windows Server 2008r2SP1 2016年的 Active Directory 網域服務的版本。

## <a name="capacity-management-faqs"></a>容量管理常見問題集

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>如何監視容量集區和 Azure NetApp 檔案的磁碟區的使用量？ 

Azure 的 NetApp 檔案提供容量集區和磁碟區使用量計量。 您也可以使用 Azure 監視器來監視 Azure NetApp 檔案的使用情況。 請參閱[計量 Azure NetApp 檔案](azure-netapp-files-metrics.md)如需詳細資訊。 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>可以管理透過 Azure 儲存體總管的 Azure NetApp 檔案嗎？

沒有。 Azure 儲存體總管不支援 azure NetApp 檔案。

## <a name="data-migration-and-protection-faqs"></a>資料移轉和保護的常見問題集

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>如何將資料移轉至 Azure NetApp 檔案？
Azure 的 NetApp 檔案提供 NFS 和 SMB 磁碟區。  您可以使用任何檔案為基礎的複製工具，將資料移轉至該服務。 

NetApp 提供 SaaS 為基礎的解決方案， [NetApp 雲端同步](https://cloud.netapp.com/cloud-sync-service)。解決方案可讓您複寫 NFS 或 SMB 資料至 Azure NetApp 檔案 NFS 匯出或 SMB 共用。 

您也可以使用各式各樣的免費工具來複製資料。 For NFS，您可以使用工作負載的工具例如[rsync](https://rsync.samba.org/examples.html)複製和同步處理到 Azure NetApp 檔案磁碟區的來源資料。 針對 SMB，您可以使用工作負載[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)相同的方式。  這些工具也可以將複寫檔案或資料夾的權限。 

移轉的需求資料從內部部署至 Azure NetApp 檔案如下所示： 

- 請確定 Azure NetApp 檔案是在目標 Azure 區域中使用。
- 驗證來源與 Azure NetApp 檔案目標磁碟區的 IP 位址之間的網路連線能力。 在內部部署以及 Azure NetApp 檔案服務之間的資料傳輸是透過 ExpressRoute 所支援。
- 建立目標 Azure NetApp 檔案磁碟區。
- 使用您慣用的檔案複製工具，來源資料傳輸至目標磁碟區。

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>如何在另一個 Azure 區域中建立一份 Azure NetApp 檔案磁碟區？
    
Azure 的 NetApp 檔案提供 NFS 和 SMB 磁碟區。  任何檔案以複製工具可用來在 Azure 區域之間複寫資料。 

NetApp 提供基礎的 SaaS 解決方案， [NetApp 雲端同步](https://cloud.netapp.com/cloud-sync-service)。解決方案可讓您複寫 NFS 或 SMB 資料至 Azure NetApp 檔案 NFS 匯出或 SMB 共用。 

您也可以使用各式各樣的免費工具來複製資料。 For NFS，您可以使用工作負載的工具例如[rsync](https://rsync.samba.org/examples.html)複製和同步處理到 Azure NetApp 檔案磁碟區的來源資料。 針對 SMB，您可以使用工作負載[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)相同的方式。  這些工具也可以將複寫檔案或資料夾的權限。 

將 Azure NetApp 檔案磁碟區複寫到另一個 Azure 區域的需求如下所示： 
- 請確定 Azure NetApp 檔案是在目標 Azure 區域中使用。
- 驗證每個區域中 Vnet 之間的網路連線能力。 目前不支援全域之間的 Vnet 對等互連。  您可以建立連結的 ExpressRoute 線路，或使用 S2S VPN 連線的 Vnet 之間的連線。 
- 建立目標 Azure NetApp 檔案磁碟區。
- 使用您慣用的檔案複製工具，來源資料傳輸至目標磁碟區。

### <a name="is-migration-with-azure-data-box-supported"></a>使用 Azure 資料箱支援已移轉？

沒有。 Azure 資料箱不目前支援 Azure NetApp 檔案。 

### <a name="is-migration-with-azure-importexport-service-supported"></a>使用 Azure 匯入/匯出服務支援會移轉嗎？

沒有。 Azure 匯入/匯出服務不目前支援 Azure NetApp 檔案。

## <a name="next-steps"></a>後續步驟  

- [Microsoft Azure ExpressRoute 常見問題集](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Microsoft Azure 虛擬網路常見問題集](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure 資料箱](https://docs.microsoft.com/azure/databox-family/)