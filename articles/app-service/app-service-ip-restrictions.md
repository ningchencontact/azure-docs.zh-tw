---
title: 限制用戶端 IP - Azure App Service | Microsoft Docs
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
ms.date: 07/30/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bb6ab29f02282a394e3f93e41682ceaec5208b75
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357627"
---
# <a name="azure-app-service-static-access-restrictions"></a>Azure 应用服务静态访问限制 #

使用访问限制可以定义允许访问应用的 IP 地址的允许/拒绝列表（按优先级排序）。 允許清單可以包含 IPv4 和 IPv6 位址。 有一個或多個項目時，清單結尾會有隱含的「拒絕全部」語句。

访问限制功能适用于所有应用服务托管工作负荷，包括 Web 应用、API 应用、Linux 应用、Linux 容器应用和 Functions。

向应用发出请求时，将针对访问限制列表评估 FROM IP 地址。 如果根據清單中的規則，不允許該位址進行存取，則服務會回覆 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 狀態碼。

访问限制功能是在应用服务前端角色（即代码运行所在的辅助角色主机中的上游）中实现的。 因此，访问限制是有效的网络 ACL。  

![访问限制流](media/app-service-ip-restrictions/ip-restrictions-flow.png)

门户中的访问限制功能曾经是 IIS 中的 ipSecurity 功能之上的一个层。 而现在的访问限制功能是不同的。 现在依然可以在应用程序 web.config 中配置 ipSecurity，但是会在任何流量到达 IIS 之前应用基于前端的访问限制规则。

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>在门户中添加并编辑访问限制规则 ##

若要向应用添加访问限制规则，请使用菜单打开“网络”>“访问限制”，然后单击“配置访问限制”

![App Service 網路功能選項](media/app-service-ip-restrictions/ip-restrictions.png)  

从访问限制 UI 可以查看为应用定义的访问限制规则列表。

![列出访问限制](media/app-service-ip-restrictions/ip-restrictions-browse.png)

如果您的規則設定與此圖片中的一樣，則應用程式只會接受來自 131.107.159.0/24 的流量，並且拒絕來自任何其他 IP 位址的流量。

可单击“[+] 添加”以添加新的访问限制规则。 當您新增規則之後，該規則會立即生效。 規則會依據優先順序強制執行，從最小數字開始往上增加。 當您新增規則時 (即使只新增一個規則)，隱含的「拒絕所有」語句就會發生作用。

![添加访问限制规则](media/app-service-ip-restrictions/ip-restrictions-add.png)

針對 IPv4 和 IPv6 位址，CIDR 標記法中必須指定 IP 位址標記法。 若要指定正確的位址，您可以使用類似 1.2.3.4/32 的位址，其中前四個八位元代表您的 IP 位址，而 /32 是遮罩。 適用於所有位址的 IPv4 CIDR 標記法是 0.0.0.0/0。 若要深入了解 CIDR 標記法，您可以參閱[無類別網域間路由](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。  

单击任一行，可编辑现有访问限制规则。 編輯內容與優先順序的變更都會立即生效。

![编辑访问限制规则](media/app-service-ip-restrictions/ip-restrictions-edit.png)

若要刪除規則，請按一下您規則上的 [...]，然後按一下 [移除]。

![删除访问限制规则](media/app-service-ip-restrictions/ip-restrictions-delete.png)

也可以在下一个选项卡中限制部署访问。若要添加/编辑/删除每个规则，请执行上述相同步骤。

![列出访问限制](media/app-service-ip-restrictions/ip-restrictions-scm-browse.png)

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

IP 限制可供這兩個函式使用應用程式與 App Service 方案相同的功能。 請注意，啟用 IP 限制會停用任何不允許的 Ip 入口網站的程式碼編輯器。

[在這裡深入了解](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)