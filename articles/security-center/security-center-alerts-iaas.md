---
title: 威脅偵測的 Vm 和 Azure 資訊安全中心的伺服器 |Microsoft Docs
description: 本主題提供 VM 和伺服器可以使用 Azure 資訊安全中心警示。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 5487b4f49f5dbf7b968cd45d40555c69b54c329a
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571578"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Vm 與 Azure 資訊安全中心的伺服器威脅偵測

本主題會提供與下列作業系統的 Vm 和伺服器的不同類型的偵測方法及可用的警示。 如需支援版本的清單，請參閱 <<c0> [ 平台和 Azure 資訊安全中心所支援的功能](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)。

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

資訊安全中心整合與 Azure 服務，監視及保護您以 Windows 為基礎的機器。  資訊安全中心會簡單易用的格式呈現的警示和補救建議，從所有這些服務。

### Microsoft Server Defender ATP <a nanme="windows-atp"></a>

Azure 資訊安全中心會擴充其雲端工作負載保護平台，藉由整合與 Windows Defender 進階威脅防護 (ATP)。 這提供全方位的端點偵測和回應 (EDR) 功能。

> [!NOTE]
> Windows Server Defender ATP 感應器會自動啟用上架到 Azure 資訊安全中心的 Windows 伺服器上。

當 Windows Server Defender ATP 偵測到的威脅時，它就會觸發警示。 在資訊安全中心儀表板會顯示該警示。 從儀表板，也可以轉換至 Windows Defender ATP 主控台，才能執行詳細的調查，以發現攻擊的範圍。 如需有關 Windows Server Defender ATP 的詳細資訊，請參閱 <<c0> [ 登入伺服器，以在 Windows Defender ATP 服務](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

### 損毀傾印分析 <a nanme="windows-dump"></a>

當軟體損毀時，損毀傾印會在損毀時擷取部分的記憶體。

當機可能造成受到惡意程式碼或包含惡意程式碼。 若要避免被偵測到安全性產品，各種形式的惡意程式碼使用 fileless 攻擊中，以避免寫入至磁碟或加密的軟體元件，寫入至磁碟。 這種攻擊是很難偵測到使用傳統以磁碟為基礎的方法。

不過，這種攻擊可以藉由使用記憶體分析來偵測。 藉由分析損毀傾印中的記憶體，資訊安全中心可以偵測到攻擊用來惡意探索軟體弱點、 存取機密資料，以及暗中保存於遭入侵電腦的技術。 這是由資訊安全中心後端，對主機造成最小效能影響。

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**探索到的程式碼插入**|程式碼插入是將可執行的模組插入執行的程序或執行緒。 雖然要找到並移除已成功隱藏本身，以防止惡意程式碼，以存取資料，會使用這項技術。 <br/>此警示表示損毀傾印中有插入的模組。 若要區分惡意和非惡意插入的模組，資訊安全中心會檢查插入的模組是否符合可疑行為設定檔。|
|**探索到的可疑程式碼片段**|表示已使用非標準的方法，例如反射的插入和處理程序空白已配置的程式碼區段。 警示可讓您的程式碼片段提供功能的內容已處理的其他特性和報告的程式碼區段的行為。|
|**探索到 Shellcode**|Shellcode 是惡意程式碼惡意探索軟體弱點後執行的承載。<br/>此警示表示損毀傾印分析偵測可執行程式碼顯現經常由惡意承載執行的行為。 雖然非惡意軟體可能也會執行此行為，但是不典型的一般軟體開發實務中。|

### Fileless 攻擊偵測 <a nanme="windows-fileless"></a>

在 Azure 中，我們會定期看到 fileless 的攻擊目標客戶的端點。

若要避免偵測，fileless 攻擊會將惡意承載插入記憶體中。 攻擊者裝載保存於遭入侵的程序的記憶體，並執行各種不同的惡意活動。

使用 fileless 攻擊偵測 fileless 攻擊工具組、 技術和行為，找出自動化的記憶體鑑識技術。 此解決方案會定期掃描您的機器，在執行階段和安全性關鍵的程序的記憶體中直接擷取深入解析。

它會尋找惡意探索及程式碼資料隱碼攻擊，惡意承載執行的辨識項。 Fileless 攻擊偵測會產生詳細的安全性警示，以加速警示分級、 相互關聯，以及下游的回應時間。 這種方法可補充事件為基礎的 EDR 解決方案提供更高的偵測涵蓋範圍。

> [!NOTE]
> 您可以透過下載來模擬 Windows 警示[Azure 資訊安全中心腳本](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef):安全性警示，並遵循所提供的指導方針

> [!div class="mx-tableFixed"]

|警示|說明|
|---|---|
|**偵測到的 fileless 種攻擊技巧**|下面指定的處理序記憶體包含 fileless 攻擊工具組：Meterpreter。 Fileless 攻擊工具組通常不需要出現在檔案系統上，讓由傳統防毒偵測變得困難。|

### <a name="further-reading"></a>進階閱讀

如需範例和資訊安全中心偵測的詳細資訊：

* [Azure 資訊安全中心會偵測網路攻擊的自動化](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure 資訊安全中心會使用系統管理工具的弱點的偵測](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

資訊安全中心會收集稽核記錄，從使用的 Linux 機器**auditd**，下列其中一個最常見的 Linux 的稽核架構。 auditd 的優點是能夠讓已有很長的時間和活在主線的核心。 

### Linux auditd 警示和 Microsoft Monitoring Agent (MMA) 整合 <a name="linux-auditd"></a>

Auditd 系統是由負責監視系統呼叫、 篩選它們所指定的規則集，並為其將訊息寫入通訊端的核心層級子系統所組成。 資訊安全中心整合來自 auditd 封裝內 Microsoft Monitoring Agent (MMA) 的功能。 這項整合可讓 auditd 事件集合中所有支援的 Linux 散發套件，而不需要任何必要條件。  

auditd 記錄收集、 擴充，並彙總到使用 Linux MMA 代理程式的事件。 資訊安全中心不斷努力新增新的分析，Linux 信號以偵測惡意行為會在雲端和內部部署上的 Linux 機器，運用。 類似於 Windows 功能，這些分析跨越可疑的處理序、 登入嘗試、 核心模組載入方式和其他活動，這表示電腦可能遭受攻擊，或已經違反服務等級。  

以下是分析的幾個範例，示範我們如何跨越攻擊生命週期不同階段。

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**發現有不尋常的方式存取 SSH 授權的金鑰檔案的程序**|已在已知的惡意程式碼活動類似的方法存取的 SSH 授權的金鑰檔案。 此存取權可能表示攻擊者嘗試取得永續性的存取權的機器|
|**偵測到的持續性嘗試**|主機資料分析偵測到已安裝為單一使用者模式啟動指令碼。 <br/>因為很少發生的任何合法的處理程序需要在該模式下執行，這可能表示攻擊者已新增至每個執行層級保證持續性的惡意處理序。|
|**偵測到的排程工作的操作**|主機資料分析偵測到可能的操作，排程工作。 攻擊者通常會將排程的工作新增至它們已遭到入侵，以取得持續性的機器。|
|**可疑的檔案時間戳記修改**|主機資料分析偵測到可疑的時間戳記所做的修改。 攻擊者通常時間戳記從現有合法檔案複製到新的工具，以避免偵測的這些新的已卸除的檔案|
|**已在將新的使用者新增至 sudoers 群組**|主機資料分析偵測到，已將使用者新增至 sudoers 群組，可讓其成員，以使用較高權限執行命令。|
|**可能的惡意探索的 dhcp 用戶端中的弱點可能會 DynoRoot**|主機資料分析偵測到不尋常的命令與 dhclient 指令碼的父處理序的執行。|
|**偵測到可疑的核心模組**|主機資料分析偵測到共用的物件檔案做為核心模組載入。 這可能是合法的活動，或您的機器的其中一個指示已遭入侵。|
|**偵測到的數位貨幣採礦相關聯處理序**|主機資料分析偵測到處理序一般與數位貨幣採礦相關聯的執行|
|**可能的連接埠轉送至外部 IP 位址**|主機資料分析偵測到初始化的連接埠轉送至外部 IP 位址。|

> [!NOTE]
> 您可以透過下載來模擬 Windows 警示[Azure 資訊安全中心腳本：安全性警示](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)並遵循所提供的指導方針。


如需詳細資訊，請參閱這些文章：  

* [利用 Azure 資訊安全中心時遭到入侵的 Linux 機器攻擊偵測](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure 資訊安全中心可以偵測新的弱點，在 Linux 中](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 