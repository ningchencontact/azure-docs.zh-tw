---
title: 包含檔案
description: 包含檔案
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 371cbcc50b574f95e8d9ba4efe79058b2b25a8ba
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127736"
---
**組態/處理序伺服器需求**

**元件** | **需求** 
--- | ---
**硬體設定** | 
CPU 核心 | 8 
RAM | 16 GB
磁碟數 | 3，包括作業系統磁碟、處理序伺服器快取磁碟和用於容錯回復的保留磁碟機 
可用磁碟空間 (處理序伺服器快取) | 600 GB
可用磁碟空間 (保留磁碟) | 600 GB
 | 
**軟體設定** | 
作業系統 | Windows Server 2012 R2 <br> Windows Server 2016
作業系統地區設定 | 英文 (en-us)
Windows Server 角色 | 請勿啟用這些角色： <br> - Active Directory Domain Services <br>- 網際網路資訊服務 <br> - Hyper-V 
群組原則 | 請勿啟用這些群組原則： <br> - 防止存取命令提示字元。 <br> - 防止存取登錄編輯工具。 <br> - 檔案附件的信任邏輯。 <br> - 開啟指令碼執行。 <br> [深入了解](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 沒有預先存在的預設網站 <br> - 沒有預先存在的網站/應用程式接聽連接埠 443 <br>- 啟用[匿名驗證](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 啟用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定 
| 
**網路設定** | 
IP 位址類型 | 靜態 
連接埠 | 443 (控制通道協調流程)<br>9443 (資料傳輸) 
NIC 類型 | VMXNET3 (如果組態伺服器是 VMware 虛擬機器)
 |
**網際網路存取** (伺服器需要能夠以直接的方式或透過 Proxy 存取下列 URL)：|
\*.backup.windowsazure.com | 用於所複寫資料的轉送和協調
\*.store.core.windows.net | 用於所複寫資料的轉送和協調
\*.blob.core.windows.net | 用於存取儲存體帳戶來儲存複寫的資料
\*.hypervrecoverymanager.windowsazure.com | 用於複寫管理作業和協調
https:\//management.azure.com | 用於複寫管理作業和協調 
*.services.visualstudio.com | 用於遙測目的 (此為選擇性項目)
time.nist.gov | 用於檢查系統時間與通用時間之間的時間同步處理。
time.windows.com | 用於檢查系統時間與通用時間之間的時間同步處理。
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | OVF 設定需要存取這些 URL。 其可供 Azure Active Directory 用於管理存取控制和身分識別
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | 完成 MySQL 下載
|
**要安裝的軟體** | 
VMware vSphere PowerCLI | 如果組態伺服器在 VMware 虛擬機器上執行，則應該安裝 [PowerCLI 6.0 版](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)。
MySQL | 應該安裝 MySQL。 您可以手動安裝，或者 Site Recovery 可以安裝。 (如需詳細資訊，請參閱[進行設定](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings))

**組態/處理序伺服器調整大小需求**

**CPU** | **記憶體** | **快取磁碟** | **資料變更率** | **複寫的機器**
--- | --- | --- | --- | ---
8 個 vCPU<br/><br/> 2 個插槽 * 4 個核心 \@ 2.5 GHz | 16 GB | 300 GB | 500 GB 或更少 | < 100 部機器
12 個 vCPU<br/><br/> 2 個插槽 * 6 個核心 \@ 2.5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 到 150 部機器
16 個 vCPU<br/><br/> 2 個插槽 * 8 個核心 \@ 2.5 GHz | 32 GB | 1 TB | 1-2 TB | 150 到 200 部機器

