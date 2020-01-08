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
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: b101fd30ddbdbf0f8fdf6e02394cb10b9af5f4b0
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666358"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Azure 資訊安全中心中 Vm 和伺服器的威脅偵測

本主題針對具有下列作業系統的 Vm 和伺服器，提供不同類型的偵測方法和警示。 如需支援版本的清單，請參閱[Azure 資訊安全中心支援的平臺和功能](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)。

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## 時段<a name="windows-machines"></a>

Azure 資訊安全中心與 Azure 服務整合，以監視及保護您的 Windows 電腦。 資訊安全中心以便於使用的格式，提供所有這些服務的警示和補救建議。

* **Microsoft defender ATP** <a name="windows-atp"></a> -藉由與 microsoft defender Advanced 威脅防護（ATP）整合，資訊安全中心擴充其雲端工作負載保護平臺。 這會提供完整的端點偵測和回應（EDR）功能。

    > [!NOTE]
    > Microsoft Defender ATP 感應器會在使用資訊安全中心的 Windows 伺服器上自動啟用。

    當 Microsoft Defender ATP 偵測到威脅時，它會觸發警示。 警示會顯示在 [資訊安全中心] 儀表板上。 從儀表板，您可以將資料透視到 Microsoft Defender ATP 主控台，並執行詳細的調查，以找出攻擊的範圍。 如需 Microsoft Defender ATP 的詳細資訊，請參閱[將伺服器上架到 Microsoft DEFENDER atp 服務](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

* 損毀傾印**分析** <a name="windows-dump"></a> -當軟體當機時，損毀傾印會在當機時，捕捉記憶體的一部分。

    損毀可能是惡意軟體或包含惡意程式碼所造成。 為了避免安全性產品偵測到，各種形式的惡意程式碼都會使用 fileless 攻擊，以避免寫入磁片或加密寫入磁片的軟體元件。 使用傳統的磁片型方法，很容易偵測到這種類型的攻擊。

    不過，藉由使用記憶體分析，您可以偵測到這類攻擊。 藉由分析損毀傾印中的記憶體，資訊安全中心可以偵測到攻擊所使用的技術。 例如，攻擊可能會嘗試入侵軟體中的弱點、存取機密資料，以及暗中保存在遭入侵的電腦中。 資訊安全中心會執行這種操作，對主機的效能影響最小。

    如需損毀傾印分析警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-crashdump)。

* **Fileless 攻擊偵測** <a name="windows-fileless"></a> -以您的端點為目標的 Fileless 攻擊是常見的。 為了避免偵測，fileless 攻擊會將惡意承載插入記憶體中。 攻擊者承載會保存在遭入侵的進程記憶體中，並執行各式各樣的惡意活動。

    透過 fileless 攻擊偵測，自動化記憶體辨識技術可識別 fileless 攻擊工具組、技術和行為。 此解決方案會在執行時間定期掃描您的電腦，並直接從安全性關鍵進程的記憶體中抽取深入解析。

    它會尋找入侵、程式碼插入和執行惡意承載的證據。 Fileless 攻擊偵測會產生詳細的安全性警示，以加速警示分級、相互關聯和下游回應時間。 這個方法會補充以事件為基礎的 EDR 解決方案，以提供更高的偵測涵蓋範圍。

    如需 fileless 攻擊偵測警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-filelessattackdetect)。

> [!NOTE]
> 您可以下載[Azure 資訊安全中心腳本：安全性警示](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)來模擬 Windows 警示。

## 廠商<a name="linux-machines"></a>

資訊安全中心會使用**auditd**（其中一個最常見的 linux 審核架構），從 Linux 機器收集審核記錄。 auditd 居住在主線核心中。 

* **Linux auditd 警示和 Microsoft Monitoring Agent （MMA）整合** <a name="linux-auditd"></a> -auditd 系統是由核心層級子系統所組成，負責監視系統呼叫。 它會依指定的規則集篩選它們，並將訊息寫入至通訊端。 資訊安全中心會從 Microsoft Monitoring Agent （MMA）內的 auditd 套件整合功能。 這項整合可讓您在所有支援的 Linux 發行版本中收集 auditd 事件，而不需要任何必要條件。  

    auditd 記錄會使用 Linux MMA 代理程式來收集、擴充及匯總至事件。 資訊安全中心持續新增使用 Linux 信號的新分析，以偵測雲端和內部部署 Linux 電腦上的惡意行為。 與 Windows 功能類似，這些分析會跨越可疑的程式、可疑登入嘗試、核心模組載入和其他活動。 這些活動可能表示電腦受到攻擊，或已被入侵。  

    以下是跨越攻擊生命週期不同階段的幾個分析範例。

    如需 Linux 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-linux)。

> [!NOTE]
> 您可以藉由下載 Azure 資訊安全中心腳本[： linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)偵測來模擬 Linux 警示。

 
 ## <a name="next-steps"></a>後續步驟

如需有關資訊安全中心偵測的範例和詳細資訊，請參閱：

* [Azure 資訊安全中心如何將網路攻擊的偵測自動化](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure 資訊安全中心如何使用系統管理工具偵測弱點](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)
* [利用 Azure 資訊安全中心來偵測遭到入侵的 Linux 機器攻擊](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure 資訊安全中心可以偵測 Linux 中新興的弱點](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)