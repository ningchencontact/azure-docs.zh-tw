---
title: 如何在內部虛擬網路中使用 Azure API 管理 | Microsoft Docs
description: 了解如何在內部虛擬網路上安裝和設定 Azure API 管理
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 738dd451ca44801c799d793e6501c547655bd0e0
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524135"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>在內部虛擬網路中使用 Azure API 管理服務
在 Azure 虛擬網路中，Azure API 管理可以管理無法在網際網路上存取的 API。 有許多 VPN 技術可讓您建立連線。 API 管理在虛擬網路內有兩種主要的部署模式：
* 外部
* 內部

當 API 管理以內部虛擬網路模式部署時，只有在您控制存取的虛擬網路內才看得到所有服務端點 (閘道、開發人員入口網站、Azure 入口網站、直接管理和 Git)。 公用 DNS 伺服器上不會註冊任何服務端點。

在內部模式中使用 API 管理可讓您實現下列情節：

* 使用站對站或 Azure ExpressRoute VPN 連線，讓裝載於私人資料中心的 API 可供外部第三方安全地存取。
* 透過通用閘道器公開雲端式 API 和內部部署 API，以實現混合式雲端情節。
* 使用單一閘道端點，管理裝載於多個地理位置的 API。

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>必要條件

若要執行本文所述的步驟，您必須具有：

+ **作用中 Azure 訂用帳戶**。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Azure API 管理執行個體**。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
+ 在虛擬網路中，部署 API 管理服務時[的連接埠清單](./api-management-using-with-vnet.md#required-ports)習慣，以及必須開啟。 

## <a name="enable-vpn"> </a>在內部虛擬網路中建立 API 管理
内部虚拟网络中的 API 管理服务托管在[内部负载均衡器（经典）](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud)后面。 这是唯一可用的选项，不能更改。

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>使用 Azure 入口網站啟用虛擬網路連線

1. 在 [Azure 入口網站](https://portal.azure.com/)中瀏覽至您的 Azure API 管理執行個體。
2. 選取 [虛擬網路]。
3. 將 API 管理執行個體設定為在虛擬網路內部署。

    ![用於在內部虛擬網路中設定 Azure API 管理的功能表][api-management-using-internal-vnet-menu]

4. 選取 [ **儲存**]。

部署成功后，应该可以在概览边栏选项卡上看到 API 管理服务的**专用**虚拟 IP 地址和**公共**虚拟 IP 地址。 **专用**虚拟 IP 地址是 API 管理委托的子网中经负载均衡的 IP 地址，可以通过该子网访问 `gateway`、`portal`、`management` 和 `scm` 终结点。 **公共**虚拟 IP 地址**仅**用于经端口 3443 发往 `management` 终结点的控制平面流量，并且可以锁定到 [ApiManagement][ServiceTags] servicetag。

![已設定內部虛擬網路的 API 管理儀表板][api-management-internal-vnet-dashboard]

> [!NOTE]
> Azure 入口網站上可用的測試主控台不適用於**內部** VNET 部署服務，因為並未在公用 DNS 上註冊閘道器 URL。 您應該改為使用**開發人員入口網站**上提供的測試主控台。

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>使用 PowerShell Cmdlet 啟用虛擬網路連線

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您也可以使用 PowerShell Cmdlet 來啟用虛擬網路連線能力。

* 在虚拟网络中创建 API 管理服务：使用 cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) 在虚拟网络中创建 Azure API 管理服务，并将其配置为使用内部虚拟网络类型。

* 在虚拟网络中更新 API 管理服务的现有部署：使用 cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) 将现有 API 管理服务移到虚拟网络内，并将其配置为使用内部虚拟网络类型。

## <a name="apim-dns-configuration"></a> DNS 設定
當 API 管理處於外部虛擬網路模式時，DNS 是由 Azure 管理。 若是內部虛擬網路模式，您必須管理自己的路由。

> [!NOTE]
> API 管理服務不會接聽 IP 位址傳來的要求。 只有當要求指明其服務端點上所設定的主機名稱時，才會有所回應。 這些端點包括閘道、Azure 入口網站和開發人員入口網站、直接管理端點及 Git。

### <a name="access-on-default-host-names"></a>在預設主機名稱上存取
创建 API 管理服务（例如“contosointernalvnet”）时，将默认配置以下服务终结点：

   * 网关或代理：contosointernalvnet.azure-api.net

   * Azure 门户和开发人员门户：contosointernalvnet.portal.azure-api.net

   * 直接管理终结点：contosointernalvnet.management.azure-api.net

   * Git：contosointernalvnet.scm.azure-api.net

若要存取這些 API 管理服務端點，您可以在連線至虛擬網路 (已部署 API 管理) 的子網路中建立虛擬機器。 假设服务的内部虚拟 IP 地址为 10.1.0.5，则可映射 hosts 文件 (%SystemDrive%\drivers\etc\hosts)，如下所示：

   * 10.1.0.5     contosointernalvnet.azure-api.net

   * 10.1.0.5     contosointernalvnet.portal.azure-api.net

   * 10.1.0.5     contosointernalvnet.management.azure-api.net

   * 10.1.0.5     contosointernalvnet.scm.azure-api.net

然後，就可以從您建立的虛擬機器存取所有服務端點。
如果在虛擬網路中使用自訂 DNS 伺服器，您也可以建立 A DNS 記錄，並從虛擬網路中的任何地方存取這些端點。

### <a name="access-on-custom-domain-names"></a>在自訂網域名稱上存取

1. 如果不想要以預設主機名稱存取 API 管理服務，您可以為所有服務端點設定自訂網域名稱，如下圖所示：

   ![設定 API 管理的自訂網域][api-management-custom-domain-name]

2. 然後，您可以在 DNS 伺服器中建立記錄，以存取只能從虛擬網路存取的端點。

## <a name="routing"> </a> 路由
+ 子網路範圍中負載平衡的私人虛擬 IP 位址將會保留，而且會用來存取來自 VNET 內的 API 管理服務端點。
+ 負載平衡的公用 IP 位址 (VIP) 也會加以保留，用來存取僅通過連接埠 3443 的管理服務端點。
+ 子網路 IP 範圍的 IP (DIP) 位址會用來存取 VNET 中的資源，而公用 IP 位址 (VIP) 會用來存取 VNET 之外的資源。
+ 您可以在 Azure 入口網站的 [概觀/基本資訊] 刀鋒視窗上找到負載平衡的公用和私人 IP 位址。

## <a name="related-content"> </a>相關內容
若要深入了解，請參閱下列文章：
* [在虛擬網路中設定 Azure API 管理時常見的網路設定問題][Common network configuration problems]
* [虛擬網路常見問題集](../virtual-network/virtual-networks-faq.md)
* [在 DNS 中建立記錄](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

