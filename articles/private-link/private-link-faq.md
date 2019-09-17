---
title: Azure 私人連結常見問題（FAQ）
description: 瞭解 Azure 私用連結。
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 9239917599b40ac159e1cdbd4d3db6f6e70e12d2
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019040"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure 私人連結常見問題（FAQ）

## <a name="private-link"></a>私人連結

### <a name="what-is-azure-private-link-service-and-private-endpoint"></a>什麼是 Azure 私人連結服務和私人端點？

- **Azure 私用端點**：Azure 私用端點是一種網路介面，可讓您私下且安全地連線到 Azure 私用連結所支援的服務。 您可以使用私人端點來連線到支援私人連結或您自己私人連結服務的 Azure PaaS 服務。
- **Azure 私人連結服務**：Azure 私用連結服務是由服務提供者所建立的服務。 目前，私人連結服務可以附加至 Standard Load Balancer 的前端 IP 設定。 

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>我可以將服務連接到多個私人端點嗎？
是的。 一個私人連結服務可以接收來自多個私人端點的連接。 不過，一個私人端點只能連接到一個私人連結服務。  
 
### <a name="is-data-transferred-over-private-link-always-private"></a>是否透過私人連結傳送的資料一律是私用的？
是的。 所有透過 Azure 私人連結的資料都會保留在 Microsoft 骨幹上。 它不會跨越網際網路。  
 
### <a name="what-is-the-difference-between-a-vnet-service-endpoint-and-a-private-endpoint"></a>VNet 服務端點和私人端點之間有何差異？
- VNet 服務端點可透過直接連線，將您的虛擬網路私人位址空間和 VNet 的身分識別延伸至 Azure 服務。 服務端點可讓您將重要的 Azure 服務資源只放到您的虛擬網路保護，而流量會路由優化，並留在 Microsoft 骨幹網路中，它會 sdestined 到服務的公用 IP 位址。
- 私人端點是一種網路資源，可做為您虛擬網路中的進入點，並使用私人 IP 位址來連線到私用連結所支援的服務。 流量是路由優化的，並且會保留在 Microsoft 骨幹網路中。   

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>私人連結服務和私人端點之間的關係為何？
這是一對多關聯性。 一個私人連結服務可以接收來自多個私人端點的連接。 另一方面，一個私用端點只能連接到一個私人連結服務。    
 
### <a name="will-vnet-service-endpoints-be-deprecated-once-private-endpoints-are-available"></a>當私人端點可供使用時，VNet 服務端點會被取代嗎？ 
資料分割 VNet 服務端點和私人端點是獨立的技術/資源。 它們可以彼此互補，而且兩者都會並存。 某些功能和使用案例可能會重迭，您可以選擇符合您需求的模型。  
 
### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>我是使用 Azure 私用連結的服務提供者。 我是否需要確保所有的客戶都有唯一的 IP 空間，而不會與我的 IP 空間重迭？ 
資料分割 Azure 私用連結會為您提供這種功能。 因此，您不需要與客戶的位址空間有非重迭的位址空間。 
 
## <a name="private-link-service"></a>私人連結服務
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>建立私用連結服務的必要條件有哪些？ 
您的服務後端應該在虛擬網路中，並在 Standard Load Balancer 後方。
 
### <a name="how-can-i-scale-my-private-link-service"></a>如何調整我的私用連結服務？ 
您可以使用幾種不同的方式來調整私人連結服務： 
- 將後端 Vm 新增至 Standard Load Balancer 後方的集區 
- 將 IP 新增至私人連結服務。 每個私人連結服務最多允許8個 Ip。  
- 將新的私人連結服務新增至 Standard Load Balancer。 針對每個負載平衡器，我們最多允許八個私人連結服務。   


### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>如何控制我的私用連結服務公開？
您可以使用私人連結服務的可見度設定來控制曝光。 可見度支援三項設定：

- **無**-只有具有 RBAC 存取權的訂用帳戶可以找到服務。 
- 僅限**限制**的訂用帳戶會列入允許清單，並使用 RBAC 存取權來尋找服務。 
- **所有**-每個人都可以找到服務。 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>我可以建立具有基本 Load Balancer 的私人連結服務嗎？ 
資料分割 不支援透過基本 Load Balancer 的私用連結服務。
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>私人連結服務需要專用的子網嗎？ 
資料分割 私人連結服務不需要專用的子網。 您可以在您的服務部署所在的 VNet 中選擇任何子網。   

## <a name="private-endpoint"></a>私人端點 
 
### <a name="cani-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>CanI 在相同的 VNet 中建立多個私人端點嗎？ 他們可以連接到不同的服務嗎？ 
是的。 在相同的 VNet 或子網中，您可以有多個私人端點。 他們可以連接到不同的服務。  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>需要私人端點專用的子網嗎？ 
資料分割 私人端點不需要專用的子網。 您可以從您的服務部署所在的 VNet 中，選擇來自任何子網的私人端點 IP。  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>私人端點可以跨 Azure Active Directory 租使用者連線到私人連結服務嗎？ 
是的。 私人端點可以連接到私人連結服務，或跨 AD 租使用者的 Azure PaaS。  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>私人端點可以跨 Azure 區域連接到 Azure PaaS 資源嗎？
是的。 私人端點可以跨 Azure 區域連接到 Azure PaaS 資源。

##  <a name="next-steps"></a>後續步驟

- 瞭解[Azure 私人連結](private-link-overview.md)
