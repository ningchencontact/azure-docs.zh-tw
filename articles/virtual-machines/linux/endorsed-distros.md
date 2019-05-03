---
title: Azure 背書的散發套件 | Microsoft Docs
description: 了解在 Azure 背書散發套件上的 Linux，包括 Ubuntu、CentOS、Oracle 和 SUSE 的準則。
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/01/2019
ms.author: szark
ms.openlocfilehash: db10c95c982132c41193ed1cccfdcdca6f60ba5e
ms.sourcegitcommit: abeefca6cd5ca01c3e0b281832212aceff08bf3e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "64993092"
---
# <a name="endorsed-linux-distributions-on-azure"></a>背書在 Azure 上的 Linux 散發套件
合作夥伴會在 Azure Marketplace 中提供 Linux 映像。 我們與各個 Linux 社群合作，以便為經背書的發佈清單新增更多版本選項。 在此同時，針對 Marketplace 未提供的發佈，您一律可以將自己的 Linux 帶入，方法是遵循[建立和上傳包含 Linux 作業系統的虛擬硬碟](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)中的指導方針。

## <a name="supported-distributions-and-versions"></a>支援的發佈和版本
下表列出 Azure 上支援的 Linux 散發套件和版本。 請參閱[支援的 Microsoft Azure 中的 Linux 映像](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)如需詳細資訊，關於適用於 Linux 和開放原始碼技術，在 Azure 中的支援。

適用於 Hyper-V 和 Azure 的 Linux Integration Services (LIS) 驅動程式是核心模組，Microsoft 會直接提供給上游 Linux Kernel。  根據預設，有些 LIS 驅動程式會建置到發佈的核心。 以 Red Hat Enterprise (RHEL)/CentOS 作為基礎的較舊分佈可用在[適用於 Hyper-V 和 Azure 的 Linux 整合服務 4.2 版](https://www.microsoft.com/download/details.aspx?id=55106)作為個別下載。 如需關於 LIS 驅動程式的詳細資訊，請參閱 [Linux 核心需求](create-upload-generic.md#linux-kernel-requirements)。

Azure Linux 代理程式已預先安裝於 Azure Marketplace 映像上，而且通常可透過發佈的套件存放庫來取得。 源代码可在 [GitHub](https://github.com/azure/walinuxagent)上找到。

  
| 配送映像 | Version | 驅動程式 | 代理程式 |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3+、7.0+ |CentOS 6.3：[LIS 下載](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+：在核心中 |套件：在 "WALinuxAgent" 下的[存放庫](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/)中 <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |在核心中 |原始程式碼：[GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+、8.2+ |在核心中 |套件：在 "waagent" 下的存放庫中 <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+、7.0+ |在核心中 |套件：在 "WALinuxAgent" 下的存放庫中 <br/>原始程式碼：[GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7+、7.1+ |在核心中 |套件：在 "WALinuxAgent" 下的存放庫中 <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES for SAP<br>11 SP4<br>12 SP1+<br>15|在核心中 |包：<p> 適用於 11：在 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 儲存機制中<br>適用於 12：包含在 "Public Cloud" 模組中的 "python-azure-agent" 底下<br/>原始程式碼：[GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |在核心中 |套件：在 "python-azure-agent" 下的 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 存放庫中 <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ **<sup>1</sup>** |在核心中 |套件：在 "walinuxagent" 下的存放庫中 <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>**  Ubuntu 12.04 和 14.04 的延伸支援的相關資訊可以在這裡找到：[擴充的安全性維護的 Ubuntu](https://www.ubuntu.com/esm)。


## <a name="image-update-cadence"></a>映像更新步調
Azure 會要求背書的 Linux 散發套件發行者定期使用的最新修補程式和安全性修正程式，每季或更快的頻率更新其於 Azure Marketplace 的映像。 更新 Azure Marketplace 映像可自動讓新版本的映像 SKU。 如何尋找 Linux 映像的詳細資訊：[Azure Marketplace 中尋找 Linux VM 映像](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage)。

### <a name="additional-links"></a>其他連結
 - [SUSE 公用雲端影像的生命週期](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Azure 調整核心

Azure 密切搭配各種背書的 Linux 散發套件，以最佳化其發佈至 Azure Marketplace 的映像。 這個共同作業的其中一個層面是 「 調整 」 是適用於 Azure 的平台，以完整支援的 Linux 散發套件的元件形式提供的 Linux 核心的開發。 Azure 調整的核心納入的新功能和效能改進，並更快的 （通常每季） 頻率相較於預設或一般都是從散發的核心。

在大部分情況下，您會發現在 Azure Marketplace 中的預設映像上已預先安裝這些核心，並讓 Azure 客戶會立即取得這些最佳化的核心的優點。 在下列連結，可以找到這些 Azure 調整核心的詳細資訊：

 - CentOS Azure 調整核心-可透過 CentOS 虛擬化 SIG-[的詳細資訊](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian 雲端核心-Debian 10 和 Azure 上的 Debian 9 「 反向 」 映像提供[更多資訊](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - SLES Azure 調整核心-[的詳細資訊](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu Azure 調整核心-[的詳細資訊](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>合作夥伴

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

從 CoreOS 網站：

*CoreOS 在设计时就已考虑到了安全性、一致性和可靠性。CoreOS 不會透過 yum 或 apt 安裝封裝，而是在較高層級的抽象概念中，使用 Linux 容器來管理您的服務。單一服務的程式碼和所有相依性都封裝於容器內，可在一或多部 CoreOS 機器上執行。*

### <a name="credativ"></a>Credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ 是一家獨立的顧問暨服務公司，專長為使用免費的軟體來開發和實作專業解決方案。 作為業界領先的開放原始碼專家，Credativ 的支援受到許多 IT 部門所採用，從而在國際上享有認可。 Credativ 目前與 Microsoft 合作，正準備適用於 Debian 8 (Jessie) 與 7 之前的 Debian (Wheezy) 的 Debian 映像。 這兩個映像都是專為在 Azure 上執行並可透過平台輕鬆管理所設計。 Credativ 也透過其開放原始碼支援中心，長期支援維護與更新適用於 Azure 的 Debian 映像。

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle 的策略是為公用和私人雲端提供廣泛的解決方案組合。 策略可讓客戶在 Oracle 雲端和其他雲端中部署 Oracle 軟體時能有所選擇且更有彈性。 Oracle 與 Microsoft 的合作，讓客戶能夠在安心知道已經過認證和會受到 Oracle 支援的情況下，於 Microsoft 公用與私人雲端中部署 Oracle 軟體。  Oracle 對 Oracle 公用與私人雲端解決方案的承諾與投資沒有改變。

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Red Hat 是開放原始碼解決方案的全球領導提供者，協助超過 90% 的 Fortune 500 大企業解決業務挑戰、調整其 IT 和商務策略，並做好技術的準備。 Red Hat 藉由透過開放的商務模型及可預測且價格實惠的訂閱模型，提供安全的解決方案來做到這點。

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

Azure 上的 SUSE Linux Enterprise Server 是一個經證實可為雲端運算提供優異可靠性與安全性的平台。 SUSE 彈性的 Linux 平台可與 Azure 雲端服務緊密整合，提供容易管理的雲端環境。 隨著 SUSE Linux Enterprise Server 有超過 1,800 家獨立軟體廠商提供 9,200 多個認證應用程式，SUSE 可確保資料中心內所執行受支援的工作負載可以安心部署於 Azure 上。

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Canonical 對工程與開放社群的治理，促使 Ubuntu 在用戶端、伺服器和雲端運算 (包括消費者的個人雲端服務) 方面獲致成功。 Canonical 的願景是在 Ubuntu 中從手機到雲端的統一、可用平台，能提供電話、平板電腦、電視和桌上型電腦的一致介面系列。 這個願景使得 Ubuntu 成為從公用雲端提供者到消費性電子產品製造商等各大機構的優先選擇，並且是個別技術專家的最愛。

Canonical 在全球各地皆有開發人員和工程中心，此獨特的地位使其能與硬體製造商、內容供應商和軟體開發商合作，將 Ubuntu 解決方案引進電腦、伺服器到掌上型裝置等市場。
