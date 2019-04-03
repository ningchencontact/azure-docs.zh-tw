---
title: 限制用戶端 IP - Azure App Service | Microsoft Docs
description: 如何使用 Azure App Service 中的存取限制
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
ms.openlocfilehash: 805de614246028bc75268e83991fa7831b990325
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882322"
---
# <a name="azure-app-service-static-access-restrictions"></a>Azure App Service 靜態存取限制 #

存取限制可讓您定義優先排序的允許存取您的應用程式的 IP 位址的允許/拒絕清單。 允許清單可以包含 IPv4 和 IPv6 位址。 有一個或多個項目時，清單結尾會有隱含的「拒絕全部」語句。

存取限制功能適用於所有 App Service 裝載的工作負載，包括：web apps、 API 應用程式、 Linux 應用程式、 Linux 容器應用程式，以及函式。

您的應用程式提出要求的來源 IP 位址會評估存取限制清單進行比對。 如果根據清單中的規則，不允許該位址進行存取，則服務會回覆 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 狀態碼。

App Service 前端角色，也就是上游的背景工作角色主機執行程式碼中實作的存取限制功能。 因此，存取限制實際上是網路 Acl。  

![存取限制流程](media/app-service-ip-restrictions/ip-restrictions-flow.png)

時間內，在入口網站的存取限制功能是在 IIS 中的 ipSecurity 功能之上的層級。 目前的存取限制功能會不同。 您仍然可以在您的應用程式 web.config 中設定 ipSecurity，但任何流量到達 IIS 之前，將會套用前端為基礎的存取限制的規則。

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>新增和編輯在入口網站的存取限制規則 ##

若要加入您的應用程式的存取限制的規則，請使用功能表來開啟**網路**>**存取限制**，然後按一下**設定存取限制**

![App Service 網路功能選項](media/app-service-ip-restrictions/ip-restrictions.png)  

從存取限制 UI 中，您可以檢閱針對您的應用程式所定義的存取限制規則的清單。

![清單的存取限制](media/app-service-ip-restrictions/ip-restrictions-browse.png)

如果您的規則設定與此圖片中的一樣，則應用程式只會接受來自 131.107.159.0/24 的流量，並且拒絕來自任何其他 IP 位址的流量。

您可以按一下 **[+] 新增**來新增新的存取限制規則。 當您新增規則之後，該規則會立即生效。 規則會依據優先順序強制執行，從最小數字開始往上增加。 當您新增規則時 (即使只新增一個規則)，隱含的「拒絕所有」語句就會發生作用。

![新增存取限制規則](media/app-service-ip-restrictions/ip-restrictions-add.png)

針對 IPv4 和 IPv6 位址，CIDR 標記法中必須指定 IP 位址標記法。 若要指定正確的位址，您可以使用類似 1.2.3.4/32 的位址，其中前四個八位元代表您的 IP 位址，而 /32 是遮罩。 適用於所有位址的 IPv4 CIDR 標記法是 0.0.0.0/0。 若要深入了解 CIDR 標記法，您可以參閱[無類別網域間路由](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。  

您可以按一下要編輯現有的存取限制規則的任何資料列。 編輯內容與優先順序的變更都會立即生效。

![編輯存取權限制規則](media/app-service-ip-restrictions/ip-restrictions-edit.png)

若要刪除規則，請按一下您規則上的 [...]，然後按一下 [移除]。

![刪除存取權限制規則](media/app-service-ip-restrictions/ip-restrictions-delete.png)

您也可以限制部署的下一步 索引標籤中的存取權。新增/編輯/刪除每個規則，請遵循與上述相同的步驟。

![清單的存取限制](media/app-service-ip-restrictions/ip-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>以程式設計方式管理存取限制規則 ##

目前沒有任何 PowerShell 或 CLI 適用於新的存取限制功能，但您可以手動設定值，與應用程式組態在 Resource Manager 上執行 PUT 作業。 例如，您可以使用 resources.azure.com，並編輯 ipSecurityRestrictions 區塊來新增必要的 JSON。

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
