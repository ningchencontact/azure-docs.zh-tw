---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: ba41f6cce5233491020a0b42f4fd40dac060be57
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65815575"
---
**上次文档更新时间**：太平洋标准时间 2019 年 5 月 14 日上午 10:00。

揭露的[新 CPU 漏洞類別](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) (也稱為推測性執行旁路攻擊)，讓許多客戶不知所措。  

Microsoft 已在我們所有的雲端服務上部署防護功能。 執行 Azure 的基礎結構，以及將客戶的工作負載互相隔離這方面已受到保護。 這表示，使用相同基礎結構的潛在攻擊者不能利用這些漏洞來攻擊您的應用程式。

Azure 會盡可能使用[記憶體保留維護](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot)，將客戶的影響降到最低，以及避免重新開機。 Azure 會在對主機進行全系統更新時，繼續使用這些方法，並且保護我們的客戶。

如何將安全性整合至 Azure 各個層面的詳細資訊位於 [Azure 安全性文件](https://docs.microsoft.com/azure/security/)網站。 

> [!NOTE] 
> 自從這份文件首次發行以來，已揭露此弱點類別的多個變體。 Microsoft 持續致力於保護客戶和提供指引。 我們會持續發行進一步的修正，而此頁面也會隨之更新。 
> 
> 在 2019 年 5 月 14 日，[Intel 披露了](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html)一组新的推理执行旁道漏洞，称为“微体系结构数据采样”（即 MDS，请参阅 Microsoft 安全指南 [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)），它已被分配多个 CVE： 
> - CVE-2019-11091 - 微体系结构数据采样不可缓存内存 (MDSUM)
> - CVE-2018-12126 - 微体系结构存储缓冲区数据采样 (MSBDS) 
> - CVE-2018-12127 - 微体系结构负载端口数据采样 (MLPDS)
> - CVE-2018-12130 - 微体系结构填充缓冲区数据采样 (MFBDS)
>
> 此弱點會影響 Intel® Core® 處理器和 Intel® Xeon® 處理器。  Microsoft Azure 已發行的作業系統更新，並部署新的微碼，因為它可供由 Intel，在我們的團隊，以保護我們的客戶對這些新的弱點。   Azure 正在与 Intel 密切合作，以便在平台上正式发布新的微代码之前对其进行测试和验证。 
>
> **正在 VM 中运行不受信任的代码的客户**需要通过阅读下面的信息来了解有关所有推理执行旁道漏洞的更多指南（Microsoft 安全公告 ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)、[180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018) 和 [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)），从而采取措施来免受这些漏洞影响。
>
> 其他客户应从“深度防御”角度评估这些漏洞，并考虑其所选配置的安全性和性能影响。



## <a name="keeping-your-operating-systems-up-to-date"></a>使操作系统保持最新

雖然無需更新作業系統，就能讓 Azure 上執行的應用程式與其他 Azure 客戶隔離，但最佳做法是讓軟體維持在最新狀態。 Windows 最新的安全性彙總套件包含數個推測性執行旁路弱點的防護功能。 同樣地，Linux 散發套件已發行多個更新來解決這些弱點。 建议执行下面的操作来更新操作系统：

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

如果运行的是不受信任的代码，可以在 VM 或云服务中启用额外的安全功能。 与此同时，请确保操作系统是最新版本，以便可以在 VM 或云服务内启用安全功能

### <a name="windows"></a> Windows 

目標作業系統必須是最新版本，才能啟用這些額外的安全性功能。 雖然許多推測性執行旁路攻擊的防護功能會預設為啟用，但此處描述的其他功能必須手動啟用，並可能會對效能造成影響。 


**步驟 1：在 VM 上禁用超线程功能** - 在超线程 VM 上运行不受信任的代码的客户将需要禁用超线程功能或转换到非超线程的 VM 大小。 若要检查 VM 是否启用了超线程功能，请从 VM 中使用 Windows 命令行来参考下面的脚本。

键入 `wmic` 以进入交互式界面。 然后键入以下命令来查看 VM 上的物理和逻辑处理器数量。

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

如果逻辑处理器数量大于物理处理器（核心）数量，则超线程功能已启用。  如果运行的是超线程 VM，请[联系 Azure 支持](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)以禁用超线程功能。  在超线程功能已禁用后，**支持人员将要求完全重启 VM**。 


**步驟 2**：在执行步骤 1 的同时，请按照 [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 中的说明，使用 [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell 模块验证是否已启用了保护。

> [!NOTE]
> 如果您先前已下載此模組，則必須安裝最新版本。
>


PowerShell 脚本的输出应具有以下值才能确认已针对这些漏洞启用了保护：

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

如果输出显示 `MDS mitigation is enabled: False`，请[联系 Azure 支持](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)以了解可用的缓解选项。



**步驟 3**：若要启用内核虚拟地址隐藏 (KVAS) 和分支目标注入 (BTI) OS 支持，请按照 [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 中的说明，使用 `Session Manager` 注册表项启用保护。 需要重新開機。


**步驟 4**：对于正在使用[嵌套虚拟化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)的部署（仅限 D3 和 E3）：这些说明适用于用作 HYPER-V 主机的 VM。

1.  按照 [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 中的说明，使用 `MinVmVersionForCpuBasedMitigations` 注册表项启用保护。
2.  可以按照[此处](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)的说明将虚拟机监控程序计划程序类型设置为 `Core`。


### <a name="linux"></a> Linux

<a name="linux"></a>若要在內部啟用一組額外安全性功能，目標作業系統必須完全是最新版本。 有些防護功能會預設為啟用。 下節會描述預設為關閉的功能，以及 (或) 相依於硬體支援 (微碼) 的功能。 啟用這些功能可能會對效能造成影響。 請參閱作業系統提供者的文件，以取得進一步的指示


**步驟 1：在 VM 上禁用超线程功能** - 在超线程 VM 上运行不受信任的代码的客户将需要禁用超线程功能或转换到非超线程 VM。  若要检查正在运行的 VM 是否是超线程 VM，请在 Linux VM 中运行 `lscpu` 命令。 

如果 `Thread(s) per core = 2`，则已启用超线程功能。 

如果 `Thread(s) per core = 1`，则已禁用超线程功能。 

 
启用了超线程功能的 VM 的输出示例： 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0,2,4,6
Off-line CPU(s) list:  1,3,5,7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

如果运行的是超线程 VM，请[联系 Azure 支持](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)以禁用超线程功能。  在超线程功能已禁用后，**支持人员将要求完全重启 VM**。


**步驟 2**：若要避免任何下列推測性執行旁路攻擊漏洞，請參閱您作業系統提供者的文件：   
 
- [Redhat 和 CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 

## <a name="next-steps"></a>後續步驟

对于会影响许多现代处理器的推理执行旁道攻击，本文提供了相应指南：

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002)：
- CVE-2017-5715 - 分支目标注入 (BTI)  
- CVE-2017-5754 - 内核页表隔离 (KPTI)
- CVE-2018年-3639 – 推測性存放區略過 (KPTI) 
 
[L1 终端故障 (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018)：
- CVE-2018-3615 - Intel 软件防护扩展 (Intel SGX)
- CVE-2018-3620 - 操作系统 (OS) 和系统管理模式 (SMM)
- CVE-2018年-3646 – Virtual Machine Manager (VMM) 的影響

[微体系结构数据采样](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013)： 
- CVE-2019-11091 - 微体系结构数据采样不可缓存内存 (MDSUM)
- CVE-2018-12126 - 微体系结构存储缓冲区数据采样 (MSBDS)
- CVE-2018-12127 - 微体系结构负载端口数据采样 (MLPDS)
- CVE-2018-12130 - 微体系结构填充缓冲区数据采样 (MFBDS)








