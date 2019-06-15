---
title: 什麼是 Azure 私人 DNS？
description: Microsoft Azure 上的私人 DNS 主機服務概觀。
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: a8548b4972d5853f09630ae3e9ded05ed6fee32b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076399"
---
# <a name="what-is-azure-private-dns"></a>什麼是 Azure 私人 DNS？

> [!IMPORTANT]
> Azure 私人 DNS 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

「網域名稱系統」(DNS) 會負責將服務名稱轉譯 (或解析) 為其 IP 位址。  Azure DNS 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構來提供名稱解析。 除了支援網際網路對向 DNS 網域，Azure DNS 也支援私人 DNS 區域。

Azure 私人 DNS 提供可靠、 安全的 DNS 服務，以管理及解析而不需要新增自訂 DNS 解決方案的虛擬網路中的網域名稱。 藉由使用私人 DNS 區域，您就可以使用自己的自訂網域名稱，而不是現今可用的 Azure 提供名稱。 使用自訂網域名稱可協助您量身打造虛擬網路架構，來充分滿足您的組織需求。 它可以為虛擬網路內的虛擬機器 (VM) 及虛擬網路之間提供名稱解析。 此外，您還可以利用水平分割檢視來設定區域名稱，使私人與公用 DNS 區域能夠共用名稱。

若要解決的記錄，從您的虛擬網路的私人 DNS 區域，您必須連結虛擬網路與區域。 連結的虛擬網路具有完整存取權，並可以解決發行在私人區域中的所有 DNS 記錄。 此外，您也可以啟用自動註冊程序上的虛擬網路連結。 如果您啟用自動註冊程序上的虛擬網路連結時，該虛擬網路上的虛擬機器的 DNS 記錄都會註冊在私人區域中。 啟用自動註冊程序時，Azure DNS 也會更新區域記錄每次虛擬機器建立時，變更其 ' 的 IP 位址，或刪除。

![DNS 概觀](./media/private-dns-overview/scenario.png)

> [!NOTE]
> 最佳做法，請勿使用 *.local*私人 DNS 區域的網域。 並非所有作業系統都支援這。

## <a name="benefits"></a>優點

Azure 私人 DNS 提供下列優點：

* **不再需要自訂 DNS 解決方案**。 以前，許多客戶建立自訂 DNS 解決方案來管理其虛擬網路中的 DNS 區域。 您現在可以管理 DNS 區域，使用原生 Azure 基礎結構，這會移除建立和管理自訂 DNS 解決方案的負擔。

* **使用所有常見的 DNS 記錄類型**。 Azure DNS 支援 A、AAAA、CNAME、MX、PTR、SOA、SRV 及 TXT 記錄。

* **自動主機名稱記錄管理**。 除了裝載您的自訂 DNS 記錄之外，Azure 還會自動維護所指定虛擬網路中 VM 的主機名稱記錄。 在此案例中，您無須建立自訂 DNS 解決方案或修改應用程式，即可將您使用的網域名稱最佳化。

* **虛擬網路之間的主機名稱解析**。 不同於 Azure 提供的主機名稱，私人 DNS 區域可以在虛擬網路之間共用。 此功能簡化了跨網路及服務探索案例，例如虛擬網路對等互連。

* **熟悉的工具和使用者體驗**。 為了縮短學習曲線，此服務會使用妥善建立的 Azure DNS 工具 （Azure 入口網站、 Azure PowerShell、 Azure CLI、 Azure Resource Manager 範本和 REST API）。

* **水平分割 DNS 支援**。 Azure DNS 可讓您建立具有相同名稱，但從虛擬網路內與從公用網際網路會解析成不同答案的區域。 典型的水平分割 DNS 案例是提供一個專用的服務版本以供在您的虛擬網路內使用。

* **所有 Azure 區域均可使用**。 Azure DNS 私人區域已在 Azure 公用雲端中的所有 Azure 區域內正式運作。

## <a name="capabilities"></a>功能

Azure DNS 提供以下功能：

* **從啟用的自動註冊程序與連結至私人區域的虛擬網路的虛擬機器的自動註冊**。 虛擬機器就會註冊 （新增） 至私人區域作為指向其私人 IP 位址記錄。 刪除已啟用的自動註冊程序的虛擬網路連結中的虛擬機器時，Azure DNS 也會自動從連結的私人區域移除對應的 DNS 記錄。

* **正向 DNS 解析支援的虛擬網路連結至私人區域**。 針對跨虛擬網路的 DNS 解析，沒有任何明確的相依性，彼此對等互連虛擬網路。 不過，您可以針對其他案例 （例如，HTTP 流量） 的虛擬網路對等互連。

* **在虛擬網路範圍內支援反向 DNS 查閱**。 針對指派到私人區域的虛擬網路內私人 IP 位址的反向 DNS 查閱，將會傳回包含主機/記錄名稱以及用區域名稱作為尾碼的 FQDN。

## <a name="other-considerations"></a>其他考量

Azure DNS 有下列限制：

* 每個私人區域只能有 1 個註冊虛擬網路。
* 每個私人區域最多可以有 10 個解析虛擬網路。 這項功能正式上市時，將會移除此限制。
* 特定虛擬網路只可以連結到單一私人區域作為註冊虛擬網路。
* 特定虛擬網路可以連結到最多 10 個私人區域作為解析虛擬網路。 這項功能正式上市時，將會移除此限制。
* 如果您指定了註冊虛擬網路，在已註冊至私人區域的該虛擬網路中，將無法從 Azure PowerShell 和 AzureCLI API 檢視或擷取來自該虛擬網路 VM 的 DNS 記錄。 但虛擬機器記錄實際上已註冊，且將會成功解析。
* 反向 DNS 只適用於註冊虛擬網路中的私人 IP 空間。
* 未在私人區域中註冊的私人 IP (例如：在作為解析虛擬網路連結至私人區域的虛擬網路中，虛擬機器的私人 IP) 反向 DNS，將會傳回 internal.cloudapp.net  作為 DNS 尾碼。 但是此尾碼將無法解析。
* 您第一次將虛擬網路連結至作為註冊或解析虛擬網路的私人區域時，虛擬網路必須完全空白。 不過，對於未來要作為註冊或解析虛擬網路連結到其他私人區域的虛擬網路，則可為非空白。
* 同時，此階段不支援條件式轉送 (例如，啟用 Azure 和 OnPrem 網路之間的解析)。 如需客戶可如何透過其他機制實現此案例的詳細資訊，請參閱[虛擬機器與角色執行個體的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。
* 反向 DNS 只適用於連結的虛擬網路中的私人 IP 空間
* 連結的虛擬網路的私人 IP 的反向 DNS 會傳回"internal.cloudapp.net"作為虛擬機器的預設尾碼。 針對已啟用的自動註冊程序與連結至私人區域的虛擬網路，反向 DNS 的私人 ip 位址傳回 2 的 Fqdn，預設值的其中一個後置詞*internal.cloudapp.net* ，另一個私人區域的尾碼。
* 不支援條件式轉送。 例如，若要讓 Azure 和內部部署網路之間的解析。 了解如何啟用此案例中使用其他機制。 請參閱 [VM 與角色執行個體的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 


## <a name="pricing"></a>價格

如需定價資訊，請參閱[Azure DNS 定價](https://azure.microsoft.com/pricing/details/dns/)。

## <a name="next-steps"></a>後續步驟

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md)，在 Azure DNS 中建立私人區域。

* 請參閱一些常見[私人區域案例](./private-dns-scenarios.md)，這些案例可在 Azure DNS 中透過私人區域實現。

* 常見的問題和解答有關在 Azure DNS 私人區域，特定行為包括您可以針對特定類型的作業預期，請參閱[私人 DNS 常見問題集](./dns-faq-private.md)。

* 如需了解 DNS 區域和記錄，請瀏覽：[DNS 區域和記錄概觀](dns-zones-records.md)。

* 了解 Azure 的一些其他重要[網路功能](../networking/networking-overview.md)。
