---
title: 威脅偵測的 Azure 資訊安全中心的 Azure 服務層 |Microsoft Docs
description: 本主題提供 Azure 服務層提供 Azure 資訊安全中心警示。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 8c1733877834f82d9ee2524cf8bf54f532e7d9c4
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571721"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Azure 的服務層，Azure 資訊安全中心的威脅偵測

本主題提供可用的資訊安全中心警示，監視下列 Azure 服務層時。

* [Azure 網路層級](#network-layer)
* [Azure 管理層 (Azure Resource Manager) （預覽）](#management-layer)

>[!NOTE]
>使用資訊安全中心會利用從點選至 Azure 的內部摘要的遙測資料下, 面提供的分析是適用於所有的資源類型。

## Azure 網路層級<a name="network-layer"></a>

資訊安全中心的網路層級分析會根據樣本[IPFIX 資料](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)，這是 Azure 的核心路由器所收集的封包標頭。 根據此資料餵送，資訊安全中心的機器學習服務模型識別，並加上旗標的惡意流量的活動。 若要擴充的 IP 位址，資訊安全中心會利用 Microsoft 威脅情報資料庫。

> [!div class="mx-tableFixed"]

|警示|說明|
|---|---|
|**可疑的連出 RDP 網路活動**|取樣的網路流量分析偵測到源自於您的部署中的資源的異常連出遠端桌面通訊協定 (RDP) 通訊。 此活動會被視為異常情況，此環境，且可能表示資源已遭入侵，現在正以暴力密碼破解外部 RDP 端點。 請注意，這類型的活動可能會導致外部實體將您的 IP 標記為惡意。|
|**可疑的連出 RDP 網路活動至多個目的地**|取樣的網路流量分析偵測到來自您的部署中的資源，至多個目的地的異常連出遠端桌面通訊協定 (RDP) 通訊。 此活動會被視為異常情況，此環境，且可能表示資源已遭入侵，現在正以暴力密碼破解外部 RDP 端點。 請注意，這類型的活動可能會導致外部實體將您的 IP 標記為惡意。|
|**可疑的連出 SSH 網路活動**|取樣的網路流量分析偵測到源自於您的部署中的資源的異常連出安全殼層 (SSH) 通訊。 此活動會被視為異常情況，此環境，且可能表示資源已遭入侵，現在正以暴力密碼破解外部 SSH 端點。 請注意，這類型的活動可能會導致外部實體將您的 IP 標記為惡意。|
|**可疑的連出 SSH 網路活動的多個目的地**|取樣的網路流量分析偵測到來自您的部署中的資源，至多個目的地的異常連出安全殼層 (SSH) 通訊。 此活動會被視為異常情況，此環境，且可能表示資源已遭入侵，現在正以暴力密碼破解外部 SSH 端點。 請注意，這類型的活動可能會導致外部實體將您的 IP 標記為惡意。|
|**從多個來源可疑連入 SSH 網路活動**|在您的部署取樣的網路流量分析偵測到的異常連入 SSH 通訊多個來源資源。 各種唯一的 Ip 連線到您的資源會被視為異常情況此環境。 這項活動可能表示暴力密碼破解嘗試您的 SSH 介面，從多部主機 （殭屍網路）。|
|**可疑的連入 SSH 網路活動**|取樣的網路流量分析偵測到異常的連入 SSH 通訊，資源，在您的部署。 數量相對高的連入連線至您的資源會被視為異常情況此環境。 這項活動可能表示暴力密碼破解嘗試 SSH 介面。
|**從多個來源可疑連入 RDP 網路活動**|在您的部署取樣的網路流量分析偵測到的異常連入 RDP 通訊多個來源資源。 各種唯一的 Ip 連線到您的資源會被視為異常情況此環境。 這項活動可能表示暴力密碼破解嘗試您的 RDP 介面，從多部主機 （殭屍網路）。|
|**可疑的連入 RDP 網路活動**|取樣的網路流量分析偵測到異常的連入 RDP 通訊資源，在您的部署。 數量相對高的連入連線至您的資源會被視為異常情況此環境。 這項活動可能表示暴力密碼破解嘗試 SSH 介面。|

若要了解資訊安全中心如何使用網路相關的訊號，以套用威脅防護，請參閱[Azure 資訊安全中心的啟發式 DNS 偵測](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)。
## Azure 管理層 (Azure Resource Manager) （預覽）<a name ="management-layer"></a>

>[!NOTE]
>根據 Azure Resource Manager 的資訊安全中心保護層目前為預覽狀態。

資訊安全中心會提供一層額外的保護，利用 Azure Resource Manager 的事件，這會被視為是 Azure 的控制層。 藉由分析的 Azure Resource Manager 記錄，資訊安全中心會在 Azure 訂用帳戶環境中偵測不尋常或可能有害的作業。

> [!div class="mx-tableFixed"]

|警示|說明|
|---|---|
|**執行 microBurst 工具組**|在您的環境中偵測到已知的雲端環境的探查的工具組，執行。 工具"MicroBurst 」 (請參閱 https://github.com/NetSPI/MicroBurst) 可由攻擊者 （或滲透測試人員） 對應您的訂用帳戶資源，識別不安全的設定，會導致洩漏機密資訊。|
|**執行 azurite 工具組**|在您的環境中偵測到已知的雲端環境的探查的工具組，執行。 工具"Azurite 」 (請參閱 https://github.com/mwrlabs/Azurite) 可由攻擊者 （或滲透測試人員） 來對應您的訂用帳戶資源，並識別不安全的設定。|
|**可疑的管理工作階段使用的非作用中的帳戶**|訂用帳戶活動記錄檔分析偵測到可疑的行為。 不是很長一段時間的使用中的主體現在正在執行可保護的持續性攻擊者的動作。|
|**使用 PowerShell 的可疑的管理工作階段**|訂用帳戶活動記錄檔分析偵測到可疑的行為。 不會定期使用 PowerShell 來管理訂用帳戶環境中，主體現在是使用 PowerShell，並執行可保護的持續性攻擊者的動作。|
|**使用進階 Azure 的持續性技術**|訂用帳戶活動記錄檔分析偵測到可疑的行為。 自訂的角色會提供 legitimized 身分識別的實體。 這可能會導致攻擊者取得的 Azure 客戶環境中的持續性。|
|**非經常性國家/地區的活動**|發生的活動，從已不最近或從未前往過的組織中的任何使用者的位置。<br/>此偵測會考慮過去的活動位置，以判斷新的和非經常性的位置。 異常偵測引擎會儲存組織中使用者先前所使用位置的相關資訊。 
|**來自匿名 IP 位址的活動**|從已經識別為匿名 proxy IP 位址已偵測到的 IP 位址的使用者活動。 <br/>這些 Proxy 通常由想要隱藏其裝置 IP 位址的人員使用，而且可能用於惡意意圖。 此偵測利用可減少「誤判」的機器學習演算法，例如組織中使用者廣泛使用之誤標記的 IP 位址。|
|**偵測到不可能的旅遊**|兩個使用者活動 （可能是單一或多個工作階段），發生來自遙遠的地理位置的時間較短的時間週期內它就得花上從第一個位置移動到第二個使用者。 這表示有不同的使用者使用相同的認證。 <br/>這項偵測利用機器學習演算法會忽略明顯 「 誤判 」 提供給不可能移動條件，例如 Vpn 和組織中其他使用者定期使用的位置。 該偵測有七天的初始學習期間，在此期間它會學習新使用者的活動模式。|

>[!NOTE]
> 有幾個上述分析供電的 Microsoft 雲端應用程式安全性 (MCAS)。 若要受益於這些分析，就必須啟動的 MCAS 授權。 如果您有 MCAS 授權時，預設會啟用這些警示。 若要停用它們：
>
> 1. 在 [資訊安全中心] 刀鋒視窗中，選取 [安全性原則]  。 針對您要變更的訂用帳戶按一下 [編輯設定]  。
> 2. 按一下 [威脅偵測]  。
> 3. 在 [啟用整合]  底下，取消選取 [允許 Microsoft Cloud App Security 存取我的資料]  ，然後按一下 [儲存]  。

>[!NOTE]
>Azure 資訊安全中心會將安全性相關的客戶資料儲存在相同的地理位置，為其資源。 如果 Microsoft 已尚未部署 Azure 資訊安全中心，以在資源的地區，然後它將資料儲存在美國境內。 啟用 Microsoft 雲端應用程式安全性 (MCAS) 時，這項資訊會儲存根據地理位置的規則 MCAS。 請參閱[如需詳細資訊的非區域服務的資料儲存](http://azuredatacentermap.azurewebsites.net/)。
