---
title: 限制存取-Azure App Service |Microsoft Docs
description: 如何将访问限制与 Azure 应用服务配合使用
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 558b67b5b0e1ce4f452ce2ca2e97dd7e785c80b6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728693"
---
# <a name="azure-app-service-access-restrictions"></a>Azure App Service 的存取限制 #

存取限制可讓您定義優先順序排序的允許/拒絕清單，以控制您的應用程式的網路存取。 此清單可以包含 IP 位址或 Azure 虛擬網路子網路。 當有一或多個項目時，就會再隱含 「 全部拒絕 」，位於清單結尾。

存取限制項功能適用於所有的 App Service 裝載的工作載入包括：web apps、 API 應用程式、 Linux 應用程式、 Linux 容器應用程式，以及函式。

當您的應用程式提出要求時 FROM 位址評估您的存取限制清單中的 IP 位址規則。 FROM 位址都是已使用 Microsoft.Web 的服務端點的子網路中，如果來源子網路會比較您的存取限制清單中的虛擬網路規則。 如果根據清單中的規則，不允許該位址進行存取，則服務會回覆 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 狀態碼。

存取限制功能被實作中的 App Service 」 前端角色，也就是您的程式碼執行所在的背景工作角色主機的上游。 因此，存取限制實際上是網路 Acl。

能夠限制對您的 web 應用程式的存取，從一個 Azure 虛擬網路 (VNet) 會呼叫[服務端點][serviceendpoints]。 服務端點可讓您限制從所選子網路存取的多租用戶的服務。 上的網路端以及它所啟用的服務，必須啟用它。 

![访问限制流](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>在门户中添加并编辑访问限制规则 ##

若要向应用添加访问限制规则，请使用菜单打开“网络”>“访问限制”，然后单击“配置访问限制”

![App Service 網路功能選項](media/app-service-ip-restrictions/access-restrictions.png)  

从访问限制 UI 可以查看为应用定义的访问限制规则列表。

![列出访问限制](media/app-service-ip-restrictions/access-restrictions-browse.png)

清單會顯示所有目前位於您的應用程式的限制。 如果您有 VNet 限制在您的應用程式時，資料表將會顯示為 Microsoft.Web 是否已啟用服務端點。 您的應用程式上沒有定義的限制時，您的應用程式將可從任何地方。  

可单击“[+] 添加”以添加新的访问限制规则。 當您新增規則之後，該規則會立即生效。 規則會依據優先順序強制執行，從最小數字開始往上增加。 當您新增規則時 (即使只新增一個規則)，隱含的「拒絕所有」語句就會發生作用。

![新增 IP 存取限制規則](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

建立規則時，您必須選取允許/拒絕以及規則的類型。 您也必須提供優先順序值，而且您必須限制存取權。  規則，您可以選擇性地加入名稱和描述。  

若要設定的 IP 位址型規則中，選取 IPv4 或 IPv6 的類型。 針對 IPv4 和 IPv6 位址，CIDR 標記法中必須指定 IP 位址標記法。 若要指定正確的位址，您可以使用類似 1.2.3.4/32 的位址，其中前四個八位元代表您的 IP 位址，而 /32 是遮罩。 適用於所有位址的 IPv4 CIDR 標記法是 0.0.0.0/0。 若要深入了解 CIDR 標記法，您可以參閱[無類別網域間路由](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 

![新增 VNet 存取限制規則](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

若要限制存取所選子網路，請選取一種虛擬網路。 下方，您可以挑選訂用帳戶、 VNet 和您想要允許或拒絕存取的子網路。 如果服務端點未已經啟用 Microsoft.Web 與您所選取的子網路，它會自動啟用，除非您核取方塊詢問您不要。 如果您有權限，以啟用子網路上的服務端點，或不，您想要在應用程式，但不是在子網路上啟用這種的情況多半會與相關。 如果您需要取得其他人，即可啟用子網路上的服務端點，您可以核取方塊，並讓您設定之服務端點的預期它在稍後啟用子網路上的應用程式。 

单击任一行，可编辑现有访问限制规则。 編輯內容與優先順序的變更都會立即生效。

![编辑访问限制规则](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

當您編輯規則時，您無法變更 IP 位址規則與虛擬網路規則之間的型別。 

![编辑访问限制规则](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

若要刪除規則，請按一下您規則上的 [...]，然後按一下 [移除]。

![删除访问限制规则](media/app-service-ip-restrictions/access-restrictions-delete.png)

### <a name="scm-site"></a>SCM 網站 

除了能夠控制應用程式存取權，您也可以限制存取您的應用程式所使用的 scm 網站。 Scm 網站是 web 部署端點以及 Kudu 主控台。 另外，您可以從應用程式，指派給 scm 網站的存取限制，或使用相同的應用程式和 scm 網站集合。 當您核取方塊，讓您的應用程式相同的限制時，所有項目會變成空白。如果您取消核取方塊，則會套用您稍早於 scm 網站有任何設定。 

![列出访问限制](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>访问限制规则的编程操作 ##

新的访问限制功能目前没有适用的 CLI 或 PowerShell，但是可以通过 PUT 操作在资源管理器中的应用配置上手动设置值。 例如，您可以使用 resources.azure.com，並編輯 ipSecurityRestrictions 區塊來新增必要的 JSON。

您可以在資源管理員中的以下位置找到此資訊：

management.azure.com/subscriptions/**subscription ID**/resourceGroups/**resource groups**/providers/Microsoft.Web/sites/**web app name**/config/web?api-version=2018-02-01

先前的 JSON 語法範例為：

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],

## <a name="function-app-ip-restrictions"></a>函式應用程式 IP 限制

IP 限制可供這兩個函式使用應用程式與 App Service 方案相同的功能。 啟用 IP 限制，將會停用任何不允許的 Ip 入口網站的程式碼編輯器。

[在這裡深入了解](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
