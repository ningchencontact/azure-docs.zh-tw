---
title: Azure 資訊安全中心中的 Azure 服務層威脅偵測 |Microsoft Docs
description: 本主題提供 Azure 資訊安全中心中可用的 Azure 服務層警示。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 9a600b6b45dbef49f600a5e9e913c9d1839eff93
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202457"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Azure 資訊安全中心中的 Azure 服務層威脅偵測

本主題提供監視下列 Azure 服務層級時可用的 Azure 資訊安全中心警示：

* [Azure 網路層](#network-layer)
* [Azure 管理層（Azure Resource Manager）（預覽）](#management-layer)

>[!NOTE]
>下列分析適用于所有資源類型。 它們會使用資訊安全中心提供的遙測，方法是透過切入 Azure 內部摘要。

## Azure 網路層<a name="network-layer"></a>

資訊安全中心的網路層分析是以範例[IPFIX 資料](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)為基礎, 這是 Azure 核心路由器所收集的封包標頭。 根據此資料摘要, 資訊安全中心機器學習模型會識別並標示惡意的流量活動。 為了擴充 IP 位址，資訊安全中心利用 Microsoft 威脅情報資料庫。

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**可疑的連出 RDP 網路活動**|取樣網路流量分析偵測到來自您部署中資源的異常傳出遠端桌面通訊協定（RDP）通訊。 此環境的此活動被視為異常。 這可能表示您的資源已遭入侵，且現在正用來對外部 RDP 端點進行暴力密碼破解攻擊。 請注意，這種類型的活動可能會導致外部實體將您的 IP 標記為惡意。|
|**對多個目的地的可疑連出 RDP 網路活動**|取樣的網路流量分析偵測到異常的連出 RDP 通訊，從部署中的資源到多個目的地。 此環境的此活動被視為異常。 這可能表示您的資源已遭入侵，且現在正用於暴力攻擊外部 RDP 端點。 請注意，這種類型的活動可能會導致外部實體將您的 IP 標記為惡意。|
|**可疑的連出 SSH 網路活動**|取樣的網路流量分析偵測到異常的連出安全殼層（SSH）通訊，源自您部署中的資源。 此環境的此活動被視為異常。 這可能表示您的資源已遭入侵，且現在正用來對外部 SSH 端點進行暴力密碼破解攻擊。 請注意，這種類型的活動可能會導致外部實體將您的 IP 標記為惡意。|
|**對多個目的地的可疑連出 SSH 網路活動**|取樣的網路流量分析偵測到異常的連出 SSH 通訊，從部署中的資源到多個目的地。 此環境的此活動被視為異常。 這可能表示您的資源已遭入侵，且現在正用於暴力攻擊外部 SSH 端點。 請注意，這種類型的活動可能會導致外部實體將您的 IP 標記為惡意。|
|**來自多個來源的可疑連入 SSH 網路活動**|取樣的網路流量分析偵測到來自多個來源的異常連入 SSH 通訊, 以及您部署中的資源。 在此環境中, 連線到您的資源的各種唯一 Ip 都會被視為異常。 此活動可能表示嘗試從多部主機（殭屍網路）對 SSH 介面進行暴力密碼破解攻擊。|
|**可疑的連入 SSH 網路活動**|取樣的網路流量分析偵測到您部署中資源的異常連入 SSH 通訊。 在此環境中，對您的資源的連入連線數量相對較高會被視為異常。 此活動可能表示嘗試對 SSH 介面進行暴力密碼破解攻擊。
|**來自多個來源的可疑連入 RDP 網路活動**|取樣的網路流量分析偵測到來自多個來源的異常連入 RDP 通訊, 以及您部署中的資源。 在此環境中, 連線到您的資源的各種唯一 Ip 都會被視為異常。 此活動可能表示嘗試從多部主機（殭屍網路）對 RDP 介面進行暴力密碼破解攻擊。|
|**可疑的連入 RDP 網路活動**|取樣的網路流量分析偵測到您部署中的資源有異常的連入 RDP 通訊。 在此環境中，對您的資源的連入連線數量相對較高會被視為異常。 此活動可能表示嘗試對 SSH 介面進行暴力密碼破解攻擊。|
|**偵測到具有惡意位址的網路通訊**|取樣的網路流量分析偵測到來自您部署中資源的通訊，以及可能的命令和控制（C & C）伺服器。 請注意，這類型的活動可能會導致外部實體將您的 IP 標記為惡意。|

若要瞭解資訊安全中心可以如何使用網路相關的信號來套用威脅防護, 請參閱[Azure 資訊安全中心中的啟發式 DNS](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)偵測。

>[!NOTE]
>在 Azure 資訊安全中心中的 Azure 網路層威脅偵測警示只會在已指派相同 IP 位址的虛擬機器上，于發生可疑通訊的整個一小時內產生。 這適用于虛擬機器，以及在客戶的訂用帳戶中建立為受控服務之一部分的虛擬機器（例如 AKS、Databricks）。

## Azure 管理層（Azure Resource Manager）（預覽）<a name ="management-layer"></a>

>[!NOTE]
>以 Azure Resource Manager 為基礎的資訊安全中心保護層目前為預覽狀態。

資訊安全中心藉由使用 Azure Resource Manager 事件來提供額外的保護層，這會被視為 Azure 的控制平面。 藉由分析 Azure Resource Manager 記錄, 資訊安全中心在 Azure 訂用帳戶環境中偵測到不尋常或可能有害的作業。

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**MicroBurst 工具組執行**|在您的環境中偵測到已知的雲端環境偵察工具組執行。 攻擊者（或滲透測試人員）可以使用工具[MicroBurst](https://github.com/NetSPI/MicroBurst)來對應您的訂用帳戶資源、識別不安全的設定，以及洩漏機密資訊。|
|**Azurite 工具組執行**|在您的環境中偵測到已知的雲端環境偵察工具組執行。 攻擊者（或滲透測試人員）可以使用工具[Azurite](https://github.com/mwrlabs/Azurite)來對應您的訂用帳戶資源，並識別不安全的設定。|
|**使用非作用中帳戶的可疑管理會話**|訂用帳戶活動記錄分析偵測到可疑的行為。 長時間未使用的主體，現在正在執行可保護攻擊者持續性的動作。|
|**使用 PowerShell 的可疑管理會話**|訂用帳戶活動記錄分析偵測到可疑的行為。 不定期使用 PowerShell 來管理訂用帳戶環境的主體現在會使用 PowerShell，並執行可保護攻擊者持續性的動作。|
|**使用先進的 Azure 持續性技術**|訂用帳戶活動記錄分析偵測到可疑的行為。 已為自訂角色提供 legitimized 身分識別實體。 這可能會導致攻擊者在 Azure 客戶環境中取得持續性。|
|**非經常性國家/地區的活動**|組織中任何使用者最近或未曾造訪過之位置的活動。<br/>此偵測會考慮過去的活動位置，以判斷新的和非經常性的位置。 異常偵測引擎會儲存組織中使用者先前所使用位置的相關資訊。 
|**來自匿名 IP 位址的活動**|已偵測到來自已識別為匿名 proxy IP 位址之 IP 位址的使用者活動。 <br/>這些 proxy 會由想要隱藏其裝置 IP 位址的人員使用，並可用於惡意用途。 這項偵測使用機器學習演算法來減少誤報，例如組織中使用者廣泛使用的誤標 IP 位址。|
|**偵測到不可能的旅遊**|發生兩個使用者活動（在單一或多個會話中），源自于相距遙遠的位置。 這段期間內的時間短于使用者從第一個位置移動到第二個位置所需的時間。 這表示有不同的使用者正在使用相同的認證。 <br/>這項偵測會使用機器學習演算法，忽略導致不可能的移動狀況的明顯誤報，例如，組織中的其他使用者經常使用的 Vpn 和位置。 偵測有七天的初始學習期間，在這段期間內，它會學習新使用者的活動模式。|

>[!NOTE]
> 其中幾個先前的分析是由 Microsoft Cloud App Security 提供技術支援。 若要受益于這些分析，您必須啟用 Cloud App Security 授權。 如果您有 Cloud App Security 授權，則預設會啟用這些警示。 若要停用它們:
>
> 1. 在 [**資訊安全中心**] 分頁中，選取 [**安全性原則**]。 針對您想要變更的訂用帳戶，選取 [**編輯設定**]。
> 2. 選取 [**威脅偵測**]。
> 3. 在 [**啟用**整合] 下，清除 [**允許 Microsoft Cloud App Security 存取我的資料**]，然後選取 [**儲存**]。

>[!NOTE]
>資訊安全中心會將安全性相關的客戶資料儲存在與其資源相同的地理位置。 如果 Microsoft 尚未在資源的地理位置部署資訊安全中心，則會將資料儲存在美國。 啟用 Cloud App Security 時，會根據 Cloud App Security 的地理位置規則來儲存這項資訊。 如需詳細資訊，請參閱[非區域服務的資料儲存體](https://azuredatacentermap.azurewebsites.net/)。
