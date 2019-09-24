---
title: Azure 資訊安全中心中 Vm 和伺服器的威脅偵測 |Microsoft Docs
description: 本主題提供 Azure 資訊安全中心中可用的 VM 和伺服器警示。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 0d6f05524be41693ba280b0f0162f65b517ec281
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219629"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Azure 資訊安全中心中 Vm 和伺服器的威脅偵測

本主題針對具有下列作業系統的 Vm 和伺服器，提供不同類型的偵測方法和警示。 如需支援版本的清單, 請參閱[Azure 資訊安全中心支援的平臺和功能](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)。

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## 時段<a name="windows-machines"></a>

Azure 資訊安全中心與 Azure 服務整合，以監視及保護您的 Windows 電腦。 資訊安全中心以便於使用的格式, 提供所有這些服務的警示和補救建議。

### Windows Server Defender ATP<a nanme="windows-atp"></a>

資訊安全中心藉由與 Windows Server Defender Advanced 威脅防護（ATP）整合，延伸其雲端工作負載保護平臺。 這會提供完整的端點偵測和回應（EDR）功能。

> [!NOTE]
> Windows Server Defender ATP 感應器會在使用資訊安全中心的 Windows 伺服器上自動啟用。

當 Windows Server Defender ATP 偵測到威脅時, 就會觸發警示。 警示會顯示在 [資訊安全中心] 儀表板上。 從儀表板，您可以將資料透視到 Microsoft Defender ATP 主控台，並執行詳細的調查，以找出攻擊的範圍。 如需 Windows Server Defender ATP 的詳細資訊，請參閱[將伺服器上架到 Microsoft DEFENDER atp 服務](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

### 損毀傾印分析<a nanme="windows-dump"></a>

當軟體損毀時，損毀傾印會在損毀時擷取部分的記憶體。

損毀可能是惡意軟體或包含惡意程式碼所造成。 為了避免安全性產品偵測到, 各種形式的惡意程式碼都會使用 fileless 攻擊, 以避免寫入磁片或加密寫入磁片的軟體元件。 使用傳統的磁片型方法, 很容易偵測到這種類型的攻擊。

不過，藉由使用記憶體分析，您可以偵測到這類攻擊。 藉由分析損毀傾印中的記憶體，資訊安全中心可以偵測到攻擊所使用的技術。 例如，攻擊可能會嘗試入侵軟體中的弱點、存取機密資料，以及暗中保存在遭入侵的電腦中。 資訊安全中心會執行這種操作，對主機的效能影響最小。

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**探索到程式碼插入**|程式碼插入是將可執行的模組插入執行的程序或執行緒。 惡意程式碼會使用這項技術來存取資料, 同時成功隱藏自己以防止找到和移除。 <br/>此警示表示損毀傾印中有插入的模組。 若要區分惡意和非惡意的插入模組, 資訊安全中心檢查插入的模組是否符合可疑行為的設定檔。|
|**發現可疑的程式碼片段**|表示已使用非標準的方法配置了程式碼片段，例如反射插入和進程程式空白。 警示會提供已處理的程式碼區段的其他特性, 以提供所報告的程式碼區段之功能和行為的內容。|
|**探索到的外殼**|Shellcode 是惡意程式碼惡意探索軟體弱點後執行的承載。<br/>此警示表示損毀傾印分析偵測到可執行檔程式碼, 其表現出惡意承載經常執行的行為。 雖然非惡意軟體也可以執行此行為，但這不是一般的軟體發展實務。|

### Fileless 攻擊偵測<a nanme="windows-fileless"></a>

以您的端點為目標的 Fileless 攻擊是常見的。 為了避免偵測, fileless 攻擊會將惡意承載插入記憶體中。 攻擊者承載會保存在遭入侵的進程記憶體中，並執行各式各樣的惡意活動。

透過 fileless 攻擊偵測, 自動化記憶體辨識技術可識別 fileless 攻擊工具組、技術和行為。 此解決方案會在執行時間定期掃描您的電腦，並直接從安全性關鍵進程的記憶體中抽取深入解析。

它會尋找入侵、程式碼插入和執行惡意承載的證據。 Fileless 攻擊偵測會產生詳細的安全性警示，以加速警示分級、相互關聯和下游回應時間。 這個方法會補充以事件為基礎的 EDR 解決方案，以提供更高的偵測涵蓋範圍。

> [!NOTE]
> 您可以藉由下載[Azure 資訊安全中心腳本來模擬 Windows 警示:安全性警示](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)。

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**偵測到 Fileless 攻擊技術**|所指定進程的記憶體包含 fileless 攻擊工具組：Meterpreter. Fileless 攻擊工具組通常不會出現在檔案系統上，讓傳統的防毒軟體偵測變得很棘手。|

### <a name="further-reading"></a>進一步閱讀

如需有關資訊安全中心偵測的範例和詳細資訊，請參閱：

* [Azure 資訊安全中心如何將網路攻擊的偵測自動化](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure 資訊安全中心如何使用系統管理工具偵測弱點](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## 廠商<a name="linux-machines"></a>

資訊安全中心會使用**auditd**（其中一個最常見的 linux 審核架構），從 Linux 機器收集審核記錄。 auditd 居住在主線核心中。 

### Linux auditd 警示和 Microsoft Monitoring Agent (MMA) 整合<a name="linux-auditd"></a>

Auditd 系統是由核心層級子系統所組成，負責監視系統呼叫。 它會依指定的規則集篩選它們，並將訊息寫入至通訊端。 資訊安全中心會從 Microsoft Monitoring Agent (MMA) 內的 auditd 套件整合功能。 這項整合可讓您在所有支援的 Linux 發行版本中收集 auditd 事件，而不需要任何必要條件。  

auditd 記錄會使用 Linux MMA 代理程式來收集、擴充及匯總至事件。 資訊安全中心持續新增使用 Linux 信號的新分析，以偵測雲端和內部部署 Linux 電腦上的惡意行為。 與 Windows 功能類似，這些分析會跨越可疑的程式、可疑登入嘗試、核心模組載入和其他活動。 這些活動可能表示電腦受到攻擊，或已被入侵。  

以下是跨越攻擊生命週期不同階段的幾個分析範例。

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**發現以不尋常的方式存取 SSH 授權金鑰檔的進程**|已在類似于已知惡意程式碼活動的方法中存取 SSH 授權金鑰檔案。 此存取權可能表示攻擊者正在嘗試取得電腦的持續存取權。|
|**偵測到持續性嘗試**|主機資料分析偵測到已安裝單一使用者模式的啟動腳本。 <br/>因為在該模式中必須執行任何合法的程式，這可能表示攻擊者已在每個執行層級中新增惡意進程，以確保持續性。|
|**偵測到已排程工作的操作**|主機資料分析偵測到可能的排程工作操作。 攻擊者通常會將已排程的工作新增至他們遭入侵的電腦, 以取得持續性。|
|**可疑的檔案時間戳記修改**|主機資料分析偵測到可疑的時間戳記修改。 攻擊者通常會將時間戳記從現有的合法檔案複製到新工具，以避免偵測這些新放置的檔案。|
|**已將新的使用者新增至 sudoers 群組**|主機資料分析偵測到已將使用者新增至 sudoers 群組, 這可讓其成員以高許可權執行命令。|
|**可能在 dhcp 用戶端中利用 DynoRoot 弱點**|主機資料分析偵測到執行不尋常的命令，包含 dhclient 腳本的父系進程。|
|**偵測到可疑的核心模組**|主機資料分析偵測到將共用物件檔案載入為核心模組。 這可能是合法的活動，或表示其中一部機器已遭入侵。|
|**偵測到與數位貨幣偵測相關聯的進程**|主機資料分析偵測到執行通常與數位貨幣挖掘相關聯的進程。|
|**可能轉送到外部 IP 位址的埠**|主機資料分析偵測到起始埠轉送至外部 IP 位址。|

> [!NOTE]
> 您可以藉由下載[Azure 資訊安全中心腳本來模擬 Windows 警示:Linux 偵測](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)。


如需詳細資訊，請參閱：  

* [利用 Azure 資訊安全中心來偵測遭到入侵的 Linux 機器攻擊](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure 資訊安全中心可以偵測 Linux 中新興的弱點](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 