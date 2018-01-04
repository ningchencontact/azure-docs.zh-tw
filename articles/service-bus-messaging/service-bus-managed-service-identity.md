---
title: "管理服務身分識別與 Azure 服務匯流排 preview |Microsoft 文件"
description: "Azure 服務匯流排上使用受管理的服務身分識別"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 6965e80cf10b732d4d0a8fb78447f188c133979d
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="managed-service-identity-preview"></a>受管理的服務身分識別 （預覽）

管理服務身分識別 (MSI) 是一種跨 Azure 功能，可讓您建立安全的識別與應用程式程式碼執行所在的部署相關聯。 然後您可以將該身分識別關聯授與存取特定應用程式所需的 Azure 資源的自訂權限的存取控制角色。

使用 MSI，Azure 平台會管理此執行階段身分識別。 您不需要儲存及保護在您的應用程式程式碼或組態中，身分識別本身，或您需要存取資源的存取金鑰。 不需要啟用 MSI 支援執行於 Azure App Service 應用程式或虛擬機器中的服務匯流排用戶端應用程式不會處理 SAS 規則和索引鍵或任何其他存取權杖。 用戶端應用程式只需要 Service Bus 訊息的命名空間的端點位址。 當應用程式連接時，服務匯流排將 MSI 內容繫結中的作業會在本文稍後的範例所示的用戶端。 

一旦關聯的受管理的服務身分識別，服務匯流排用戶端可以執行所有授權的作業。 授權是授與將 MSI 與服務匯流排角色產生關聯。 

## <a name="service-bus-roles-and-permissions"></a>服務匯流排角色和權限

初始公開預覽版本中，您可以只加入受管理的服務身分識別服務匯流排命名空間，這會授與身分識別的完整控制命名空間中的所有實體的 「 擁有者 」 或 「 著作人 」 角色。 不過，管理操作來變更命名空間拓撲一開始都只能透過支援 Azure 資源管理員而不是透過原生的服務匯流排 REST 管理介面。 這項支援，也表示您無法使用.NET Framework 用戶端[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager)內受管理的服務身分識別的物件。

## <a name="use-service-bus-with-a-managed-service-identity"></a>使用受管理的服務身分識別的服務匯流排

下列章節將說明建立和部署範例應用程式的受管理的服務身分識別、 授與該身分識別存取至 Service Bus 訊息的命名空間，以及應用程式與服務匯流排的互動方式執行所需的步驟使用該身分識別的實體。

本簡介說明中裝載的 web 應用程式[Azure App Service](https://azure.microsoft.com/services/app-service/)。 VM 裝載的應用程式所需的步驟很類似。

### <a name="create-an-app-service-web-application"></a>建立 App Service web 應用程式

第一個步驟是建立應用程式服務的 ASP.NET 應用程式。 如果您不熟悉如何在 Azure 中執行這項操作，請遵循[本如何指南](../app-service/app-service-web-get-started-dotnet-framework.md)。 不過，而不是建立 MVC 應用程式的教學課程中所示，建立 Web Form 應用程式。

### <a name="set-up-the-managed-service-identity"></a>設定受管理的服務身分識別

一旦您建立應用程式時，瀏覽至新建立的 web 應用程式 （也顯示如何） 在 Azure 入口網站，然後瀏覽至**受管理的服務識別**頁面上，並啟用功能： 

![](./media/service-bus-managed-service-identity/msi1.png)

一旦您啟用此功能，新的服務身分識別是您 Azure Active Directory 中建立並設定在應用程式服務主機。

### <a name="create-a-new-service-bus-messaging-namespace"></a>建立新的 Service Bus 訊息命名空間

下一步[建立 Service Bus 訊息的命名空間](service-bus-create-namespace-portal.md)中其中一個 Azure 區域 RBAC 的預覽支援：**美國東部**，**美國東部 2**，或**西歐**. 

瀏覽至命名空間**存取控制 (IAM)**頁面在入口網站，然後再按一下**新增**新增到受管理的服務身分識別**擁有者**角色。 若要這樣做，請搜尋中的 web 應用程式名稱**新增權限**面板**選取**欄位，，然後按一下 項目。 然後按一下 [儲存] 。

![](./media/service-bus-managed-service-identity/msi2.png)
 
Web 應用程式的受管理的服務身分識別現在可以存取的服務匯流排命名空間，而佇列您先前建立的。 

### <a name="run-the-app"></a>執行應用程式

現在修改您所建立的 ASP.NET 應用程式的預設頁面。 您也可以使用 web 應用程式程式碼，從[這個 GitHub 儲存機制](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ManagedServiceIdentity)。 

Default.aspx 頁面會登陸頁面。 程式碼可以找到的 Default.aspx.cs 檔案中。 結果是最小的 web 應用程式具有數個項目欄位，以及具有**傳送**和**接收**傳送或接收訊息的服務匯流排連接的按鈕。

請注意如何[MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory)物件初始化。 而不是使用共用存取 Token (SAS) 權杖提供者，程式碼會建立與受管理的服務身分識別的權杖提供者`TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)`呼叫。 因此，會保留，並使用密碼。 是簡單的模型比使用 SAS 權杖提供者會自動處理受管理的服務身分識別內容，以服務匯流排和授權交握的流量。

一旦您擁有進行這些變更，發佈，並執行應用程式。 取得正確的發行資料的簡單方法是下載，然後再匯入 Visual Studio 中的發行設定檔：

![](./media/service-bus-managed-service-identity/msi3.png)
 
要傳送或接收訊息，請輸入命名空間名稱和您所建立之實體的名稱，然後按一下**傳送**或**接收**。 
 
請注意，受管理的服務身分識別只適用於 Azure 環境中，並只在應用程式服務部署中設定它。 也請注意，受管理的服務身分識別不使用應用程式服務的部署位置在此階段。

## <a name="next-steps"></a>後續步驟

若要深入了解服務匯流排訊息，請參閱下列主題。

* [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)
* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)