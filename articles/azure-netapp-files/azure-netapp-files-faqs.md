---
title: Azure NetApp Files 的相關常見問題 |Microsoft Docs
description: 回答有關 Azure NetApp Files 的常見問題。
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
ms.openlocfilehash: f97bb4842d9e24d879dd47757fda75b16bca48cf
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494811"
---
# <a name="faqs-about-azure-netapp-files"></a>關於 Azure NetApp Files 的常見問題

本文會回答有關 Azure NetApp Files 的常見問題 (Faq)。 

## <a name="networking-faqs"></a>網路功能常見問題

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>NFS 資料路徑是否經過網際網路？  

資料分割 NFS 資料路徑不會經過網際網路。 Azure NetApp Files 是一種 Azure 原生服務, 可部署到提供服務的 Azure 虛擬網路 (VNet) 中。 Azure NetApp Files 會使用委派的子網, 並直接在 VNet 上布建網路介面。 

如需詳細資訊, 請參閱[Azure NetApp Files 網路規劃的指導方針](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)。  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>我可以將已建立的 VNet 連線到 Azure NetApp Files 服務嗎？

是, 您可以將您建立的 Vnet 連接到服務。 

如需詳細資訊, 請參閱[Azure NetApp Files 網路規劃的指導方針](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)。  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>我可以使用 DNS FQDN 名稱掛接 Azure NetApp Files 的 NFS 磁片區嗎？

是的, 如果您建立必要的 DNS 專案, 您可以。 Azure NetApp Files 會為布建的磁片區提供服務 IP。 

> [!NOTE] 
> Azure NetApp Files 可以視需要為服務部署額外的 Ip。  DNS 專案可能需要定期更新。

## <a name="security-faqs"></a>安全性常見問題集

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM 與儲存體之間的網路流量是否可以加密？

資料流量 (從 NFSv3 或 SMBv3 用戶端到 Azure NetApp Files 磁片區的流量) 不會加密。 不過, 從 Azure VM (執行 NFS 或 SMB 用戶端) 到 Azure NetApp Files 的流量, 與任何其他 Azure VM 到 VM 的流量一樣安全。 這是 Azure 資料中心網路的本機流量。 

### <a name="can-the-storage-be-encrypted-at-rest"></a>儲存體可以在待用時加密嗎？

所有 Azure NetApp Files 磁片區都是使用 FIPS 140-2 標準進行加密。 所有金鑰都是由 Azure NetApp Files 服務所管理。 

### <a name="how-are-encryption-keys-managed"></a>加密金鑰的管理方式為何？ 

Azure NetApp Files 的金鑰管理是由服務所處理。  目前不支援使用者管理的金鑰 (攜帶您自己的金鑰)。

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>我可以設定 NFS 匯出原則規則來控制 Azure NetApp Files 服務裝載目標的存取權嗎？


是, 您可以在單一 NFS 匯出原則中設定最多五個規則。

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp Files 是否支援網路安全性群組？

否, 目前您無法將網路安全性群組套用至 Azure NetApp Files 的委派子網, 或服務所建立的網路介面。

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>我可以搭配使用 Azure IAM 與 Azure NetApp Files 嗎？

是, Azure NetApp Files 支援使用 Azure IAM 的 RBAC 功能。

## <a name="performance-faqs"></a>效能常見問題

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>我該怎麼做才能優化或調整 Azure NetApp Files 效能？

您可以根據效能需求來採取下列動作: 
- 確定虛擬機器已適當調整大小。
- 啟用 VM 的加速網路功能。
- 選取所需的服務層級和容量集區的大小。
- 針對容量和效能, 建立具有所需配額大小的磁片區。

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>如何? 將 Azure NetApp Files 的以輸送量為基礎的服務層級轉換為 IOPS 嗎？

您可以使用下列公式, 將 MB/s 轉換成 IOPS:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>如何? 變更磁片區的服務等級？

目前不支援變更磁片區的服務等級。

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>如何? 監視 Azure NetApp Files 效能？

Azure NetApp Files 提供磁片區效能計量。 您也可以使用 Azure 監視器來監視 Azure NetApp Files 的使用計量。  如需 Azure NetApp Files 的效能計量清單, 請參閱[Azure Netapp files 的計量](azure-netapp-files-metrics.md)。

## <a name="nfs-faqs"></a>NFS 常見問題

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>我想要在 Azure VM 啟動或重新開機時, 自動裝載磁片區。  如何? 為持續性 NFS 磁片區設定我的主機嗎？

若要讓 NFS 磁片區在 VM 啟動或重新開機時自動掛接, 請將`/etc/fstab`專案新增至主機上的檔案。 

例如：`$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    在 [磁片區內容] 分頁中找到之 Azure NetApp Files 磁片區的 IP 位址
- $FILEPATH  
    Azure NetApp Files 磁片區的匯出路徑
- $MOUNTPOINT  
    在 Linux 主機上建立的目錄, 用來裝載 NFS 匯出

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>為什麼 NFS 用戶端上的 DF 命令不會顯示已布建的磁片區大小？

DF 報告的磁片區大小是 Azure NetApp Files 磁片區可以成長的大小上限。 在 DF 命令中, Azure NetApp Files 磁片區的大小不會反映磁片區的配額或大小。  您可以透過 Azure 入口網站或 API 取得 Azure NetApp Files 磁片區大小或配額。

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp Files 支援哪些 NFS 版本？

Azure NetApp Files 目前支援 NFSv3。

### <a name="how-do-i-enable-root-squashing"></a>如何? 啟用根抓？

目前不支援根抓。

## <a name="smb-faqs"></a>SMB 常見問題

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB 存取需要 Active Directory 連接嗎？ 

是, 您必須先建立 Active Directory 連線, 才能部署 SMB 磁片區。 指定的網域控制站必須可由 Azure NetApp Files 的委派子網存取, 才能成功連接。  如需詳細資訊, 請參閱[建立 SMB 磁片](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb)區。 

### <a name="how-many-active-directory-connections-are-supported"></a>支援多少個 Active Directory 連接？

Azure NetApp Files 目前支援每個訂用帳戶有一個 Active Directory 連接。 此外, Active Directory 連線僅適用于單一 NetApp 帳戶;它不會在帳戶之間共用。 

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp Files 是否支援 Azure Active Directory？ 

同時支援[Azure Active Directory (AD) 網域服務](https://docs.microsoft.com/azure/active-directory-domain-services/overview)和[Active Directory Domain Services (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 。 您可以使用現有的 Active Directory 網域控制站搭配 Azure NetApp Files。 網域控制站可位於 Azure 中做為虛擬機器, 或透過 ExpressRoute 或 S2S VPN 在內部部署。 Azure NetApp Files 目前不支援[Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/)的 AD 聯結。

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>支援哪些版本的 Windows Server Active Directory？

Azure NetApp Files 支援 Active Directory Domain Services 的 Windows Server 2008r2SP1 2019 版。

## <a name="capacity-management-faqs"></a>容量管理常見問題

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>如何? 監視容量集區和 Azure NetApp Files 磁片區的使用量嗎？ 

Azure NetApp Files 提供容量集區和磁片區使用計量。 您也可以使用 Azure 監視器來監視 Azure NetApp Files 的使用量。 如需詳細資訊, 請參閱[Azure NetApp Files 的計量](azure-netapp-files-metrics.md)。 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>我可以透過 Azure 儲存體總管來管理 Azure NetApp Files 嗎？

資料分割 Azure 儲存體總管不支援 Azure NetApp Files。

## <a name="data-migration-and-protection-faqs"></a>資料移轉和保護常見問題

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>如何? 將資料移轉至 Azure NetApp Files？
Azure NetApp Files 提供 NFS 和 SMB 磁片區。  您可以使用任何以檔案為基礎的複製工具, 將資料移轉至服務。 

NetApp 提供以 SaaS 為基礎的解決方案, 即[Netapp Cloud Sync](https://cloud.netapp.com/cloud-sync-service)。解決方案可讓您將 NFS 或 SMB 資料複寫至 Azure NetApp Files NFS 匯出或 SMB 共用。 

您也可以使用各式各樣的免費工具來複製資料。 針對 NFS, 您可以使用工作負載工具 (例如[rsync](https://rsync.samba.org/examples.html) ), 將來源資料複製並同步處理至 Azure NetApp Files 磁片區。 針對 SMB, 您可以使用相同的方式來使用工作負載[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 。  這些工具也可以複寫檔案或資料夾許可權。 

從內部部署資料移轉至 Azure NetApp Files 的需求如下: 

- 請確定 Azure NetApp Files 在目標 Azure 區域中可供使用。
- 驗證來源與 Azure NetApp Files 目標磁片區 IP 位址之間的網路連線能力。 透過 ExpressRoute 支援內部部署與 Azure NetApp Files 服務之間的資料傳輸。
- 建立目標 Azure NetApp Files 磁片區。
- 使用您慣用的檔案複製工具, 將來源資料傳輸到目標磁片區。

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>如何? 在另一個 Azure 區域中建立 Azure NetApp Files 磁片區的複本？
    
Azure NetApp Files 提供 NFS 和 SMB 磁片區。  任何以檔案為基礎的複製工具都可用來在 Azure 區域之間複寫資料。 

NetApp 提供以 SaaS 為基礎的解決方案, 即[Netapp Cloud Sync](https://cloud.netapp.com/cloud-sync-service)。解決方案可讓您將 NFS 或 SMB 資料複寫至 Azure NetApp Files NFS 匯出或 SMB 共用。 

您也可以使用各式各樣的免費工具來複製資料。 針對 NFS, 您可以使用工作負載工具 (例如[rsync](https://rsync.samba.org/examples.html) ), 將來源資料複製並同步處理至 Azure NetApp Files 磁片區。 針對 SMB, 您可以使用相同的方式來使用工作負載[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 。  這些工具也可以複寫檔案或資料夾許可權。 

將 Azure NetApp Files 磁片區複寫至另一個 Azure 區域的需求如下: 
- 請確定 Azure NetApp Files 在目標 Azure 區域中可供使用。
- 驗證每個區域中 Vnet 之間的網路連線能力。 目前不支援 Vnet 之間的全域對等互連。  您可以藉由連結 ExpressRoute 線路或使用 S2S VPN 連接, 在 Vnet 之間建立連線能力。 
- 建立目標 Azure NetApp Files 磁片區。
- 使用您慣用的檔案複製工具, 將來源資料傳輸到目標磁片區。

### <a name="is-migration-with-azure-data-box-supported"></a>是否支援使用 Azure 資料箱進行遷移？

資料分割 Azure 資料箱目前不支援 Azure NetApp Files。 

### <a name="is-migration-with-azure-importexport-service-supported"></a>是否支援使用 Azure 匯入/匯出服務進行遷移？

資料分割 Azure 匯入/匯出服務目前不支援 Azure NetApp Files。

## <a name="next-steps"></a>後續步驟  

- [Microsoft Azure ExpressRoute 常見問題](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Microsoft Azure 虛擬網路常見問題](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure 資料箱](https://docs.microsoft.com/azure/databox-family/)
