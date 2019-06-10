---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/04/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 46ade0ecb0e2e081585803a0b1bc7eab989e21e6
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735236"
---
**上次文件更新**:4 年 6 月 2019 下午 3:00，太平洋標準時間。

揭露的[新 CPU 漏洞類別](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) (也稱為推測性執行旁路攻擊)，讓許多客戶不知所措。  

Microsoft 已在我們所有的雲端服務上部署防護功能。 執行 Azure 的基礎結構，以及將客戶的工作負載互相隔離這方面已受到保護。 這表示，使用相同基礎結構的潛在攻擊者不能利用這些漏洞來攻擊您的應用程式。

Azure 會盡可能使用[記憶體保留維護](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot)，將客戶的影響降到最低，以及避免重新開機。 Azure 會在對主機進行全系統更新時，繼續使用這些方法，並且保護我們的客戶。

如何將安全性整合至 Azure 各個層面的詳細資訊位於 [Azure 安全性文件](https://docs.microsoft.com/azure/security/)網站。 

> [!NOTE] 
> 自從這份文件首次發行以來，已揭露此弱點類別的多個變體。 Microsoft 持續致力於保護客戶和提供指引。 我們會持續發行進一步的修正，而此頁面也會隨之更新。 
> 
> 於 2019，5 月 14 日[Intel 揭露](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html)一組新的推測性執行端通道又稱為微資料取樣的弱點可能會 (MDS，請參閱 「 Microsoft 安全性指引[ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013))，已被指派多個 Cve: 
> - CVE-2019年-11091-微資料取樣不可快取的記憶體 (MDSUM)
> - CVE-2018年-12126-微儲存緩衝區資料取樣 (MSBDS) 
> - CVE-2018年-12127-微負載連接埠資料取樣 (MLPDS)
> - CVE-2018年-12130-微填滿緩衝區資料取樣 (MFBDS)
>
> 此弱點會影響 Intel® Core® 處理器和 Intel® Xeon® 處理器。  Microsoft Azure 已發行的作業系統更新，並部署新的微碼，因為它可供由 Intel，在我們的團隊，以保護我們的客戶對這些新的弱點。   Azure 密切使用 Intel 註冊以進行測試和驗證新的微碼，平台上官方發行前。 
>
> **其 VM 內的客戶正在執行不受信任的程式碼**需要採取動作來保護這些弱點閱讀下方關於所有推測性執行旁路攻擊漏洞 (Microsoft 諮詢 ADV 的其他指引[180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)， [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)，並[190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013))。
>
> 其他客戶應該評估從防禦深度觀點來看這些弱點，並考慮他們所選擇的設定的安全性與效能影響。



## <a name="keeping-your-operating-systems-up-to-date"></a>將您的作業系統最新狀態

雖然無需更新作業系統，就能讓 Azure 上執行的應用程式與其他 Azure 客戶隔離，但最佳做法是讓軟體維持在最新狀態。 Windows 最新的安全性彙總套件包含數個推測性執行旁路弱點的防護功能。 同樣地，Linux 散發套件已發行多個更新來解決這些弱點。 以下是我們建議的動作，以更新您的作業系統：

| 供應項目 | 建議的動作  |
|----------|---------------------|
| Azure 雲端服務  | 啟用[自動更新](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal)，或確定您執行最新的客體作業系統。 |
| Azure Linux 虛擬機器 | 安裝來自作業系統提供者的更新。 如需詳細資訊，請參閱本文後面的 [Linux](#linux)。 |
| Azure Windows 虛擬機器  | 安裝最新的安全性彙總套件。
| 其他 Azure PaaS 服務 | 使用這些服務的客戶無需採取任何行動。 Azure 會自動將您的作業系統版本保持在最新狀態。 |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>如果您執行的是不受信任的程式碼的其他指引 

如果客戶允許不受信任的使用者執行任意程式碼，則可以建議他們在 Azure 虛擬機器或雲端服務內實作一些額外的安全性功能。 這些功能可抵禦多個推測性執行弱點描述的程序內洩漏向量。

建議使用額外安全性功能的案例：

- 您允許不受信任的程式碼在 VM 內執行。  
    - 例如，您允許其中一個客戶上傳二進位檔或指令碼，然後在您的應用程式內執行。  
- 您允許不受信任的使用者以低權限帳戶登入 VM。   
    - 例如，您允許低權限的使用者透過遠端桌面或 SSH 登入您其中一個 VM。   
- 允許不受信任的使用者存取透過巢狀虛擬化實作的虛擬機器。  
    - 例如，您控制 HYPER-V 主機，但將 VM 配置給不受信任的使用者。  

如果客戶實作的案例並未包含不受信任的程式碼，則無須啟用這些額外的安全性功能。 

## <a name="enabling-additional-security"></a>啟用額外的安全性 

如果您正在執行不受信任的程式碼，您可以讓您的 VM 或雲端服務內的其他安全性功能。 以平行方式，請確定您的作業系統是最新版本，啟用您的 VM 或雲端服務內的安全性功能

### <a name="windows"></a>Windows 

目標作業系統必須是最新版本，才能啟用這些額外的安全性功能。 雖然許多推測性執行旁路攻擊的防護功能會預設為啟用，但此處描述的其他功能必須手動啟用，並可能會對效能造成影響。 


**步驟 1：停用超執行緒的 VM 上**-超執行緒的 VM 上執行不受信任的程式碼的客戶將需要停用超執行緒，或移至非超執行緒的 VM 大小。 參考[此文件](https://docs.microsoft.com/azure/virtual-machines/windows/acu)取得一份超執行緒的 VM 大小 （其中的核心的 vCPU 的比例是 2:1）。 若要檢查您的 VM 是否啟用超執行緒，請參閱下列使用 Windows 命令列從 VM 內的指令碼。

型別`wmic`輸入互動式的介面。 然後輸入下列檢視的實體和邏輯在 VM 上的處理器。

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

如果邏輯處理器的數目大於實體處理器 （核心），然後超執行緒已啟用。  如果您正在執行的超執行緒的 VM，請[連絡 Azure 支援](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)取得超執行緒已停用。  停用超執行緒，則一旦**支援需要完整的 VM 重新開機**。 請參閱[核心計數](#core-count)若要了解為什麼您的 VM 核心計數減少。


**步驟 2**：以平行方式為步驟 1，請依照下列中的指示[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)若要確認使用啟用保護[SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell 模組。

> [!NOTE]
> 如果您先前已下載此模組，則必須安裝最新版本。
>


PowerShell 指令碼的輸出應該有下列值來驗證已啟用的保護來防止這些漏洞：

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

如果輸出所示`MDS mitigation is enabled: False`，請[連絡 Azure 支援](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)可用防護選項。



**步驟 3**：若要啟用核心虛擬位址的遮蔽功能 (KVAS) 與分支目標資料隱碼攻擊 (BTI) OS 支援，請依照下列中的指示[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)若要啟用使用防護`Session Manager`登錄機碼。 需要重新開機。


**步驟 4**：使用的部署[巢狀虛擬化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)（D3 和 E3 只）：這些指示適用於您使用做為 HYPER-V 主機的 vm。

1.  請依照下列中的指示[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)若要啟用使用防護`MinVmVersionForCpuBasedMitigations`登錄機碼。
2.  設定 hypervisor 的排程器型別為`Core`的指示[這裡](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)。


### <a name="linux"></a>Linux

<a name="linux"></a>若要在內部啟用一組額外安全性功能，目標作業系統必須完全是最新版本。 有些防護功能會預設為啟用。 下節會描述預設為關閉的功能，以及 (或) 相依於硬體支援 (微碼) 的功能。 啟用這些功能可能會對效能造成影響。 請參閱作業系統提供者的文件，以取得進一步的指示


**步驟 1：停用超執行緒的 VM 上**-超執行緒的 VM 上執行不受信任的程式碼的客戶將需要停用超執行緒，或移至非超執行緒的 VM。  參考[此文件](https://docs.microsoft.com/azure/virtual-machines/linux/acu)取得一份超執行緒的 VM 大小 （其中的核心的 vCPU 的比例是 2:1）。 若要檢查執行超執行緒的 VM，請執行`lscpu`命令，在 Linux VM。 

如果`Thread(s) per core = 2`，然後超執行緒已啟用。 

如果`Thread(s) per core = 1`，已停用然後超執行緒。 

 
具有啟用超執行緒的 VM 的範例輸出： 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

如果您正在執行的超執行緒的 VM，請[連絡 Azure 支援](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)取得超執行緒已停用。  停用超執行緒，則一旦**支援需要完整的 VM 重新開機**。 請參閱[核心計數](#core-count)若要了解為什麼您的 VM 核心計數減少。



**步驟 2**：若要避免任何下列推測性執行旁路攻擊漏洞，請參閱您作業系統提供者的文件：   
 
- [Redhat 和 CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>核心計數

建立超執行緒的 VM 時，Azure 會配置每個核心的 2 個執行緒-這些稱為 Vcpu。 停用超執行緒時，Azure 會移除執行緒及啟動單一執行緒核心數 （實體核心） 的介面。 CPU 的 vCPU 的比例 2:1，因此一次超執行緒已停用，在 VM 中的 CPU 計數會出現以減少一半。 比方說，D8_v3 VM 是超執行緒上執行的 VM 8 個 Vcpu （2 個執行緒，每個核心 x 4 核心）。  停用超執行緒時，Cpu 會卸除與每個核心的 1 個執行緒的 4 個實體核心。 

## <a name="next-steps"></a>後續步驟

本文提供指引，以下列會影響許多現代處理器的推測性執行旁路攻擊：

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715-分支目標導入 (BTI)  
- CVE-2017-5754-核心分頁表隔離 （KPTI）
- CVE-2018年-3639 – 推測性存放區略過 (KPTI) 
 
[L1 終端機的錯誤 (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018年-3615-Intel 軟體防護擴充功能 (Intel SGX)
- CVE-2018年-3620-作業系統 (OS) 和系統管理模式 (SMM)
- CVE-2018年-3646 – Virtual Machine Manager (VMM) 的影響

[微資料取樣](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019年-11091-微資料取樣不可快取的記憶體 (MDSUM)
- CVE-2018年-12126-微儲存緩衝區資料取樣 (MSBDS)
- CVE-2018年-12127-微負載連接埠資料取樣 (MLPDS)
- CVE-2018年-12130-微填滿緩衝區資料取樣 (MFBDS)








