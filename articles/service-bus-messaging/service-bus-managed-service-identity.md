---
title: Azure 資源的受控識別與 Azure 服務匯流排預覽 | Microsoft Docs
description: 搭配使用 Azure 資源的受控識別與 Azure 服務匯流排
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/01/2018
ms.author: aschhab
ms.openlocfilehash: 5edeebd9698384785082e5a441c24e136ed22481
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61317051"
---
# <a name="managed-identities-for-azure-resources-with-service-bus"></a>搭配服務匯流排之 Azure 資源的受控識別 

[Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)是一個跨 Azure 功能，可讓您建立與應用程式程式碼執行所在之部署相關聯的安全識別。 然後您可以將該識別與存取控制角色產生關連，該角色會授與用來存取應用程式所需之特定 Azure 資源的自訂權限。

使用受控識別，Azure 平台就能管理此執行階段識別。 您不需要為了識別本身或您需要存取的資源，在應用程式程式碼或設定中儲存及保護存取金鑰。 若服務匯流排用戶端應用程式在 Azure App Service 應用程式中執行，或在已啟用 Azure 資源的受控識別支援的虛擬機器中執行，則不需處理 SAS 規則和金鑰，或任何其他存取權杖。 用戶端應用程式只需要服務匯流排傳訊命名空間的端點位址。 當應用程式連線時，服務匯流排會將受控實體的內容繫結至作業 (在此文章稍後的範例顯示) 中的用戶端。 一旦它與受控識別相關聯，您的服務匯流排用戶端就能執行所有授權的作業。 授權是藉由將受控實體與服務匯流排角色相關聯來授與。 

## <a name="service-bus-roles-and-permissions"></a>服務匯流排角色和權限

您只能將受控識別新增到服務匯流排命名空間的 "Owner" 或 "Contributor" 角色。 它會授與身分識別對命名空間中所有實體的完整控制權。 不過，變更命名空間拓撲的管理作業一開始只能透過 Azure Resource Manager 來支援。 透過原生服務匯流排 REST 管理介面不支援。 此支援也表示您無法在受控識別內使用 .NET Framework 用戶端 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 物件。

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>搭配使用服務匯流排和 Azure 資源的受控識別

下列章節會說明要建立及部署應用程式範例 (依據受控識別來執行) 所需進行的步驟、如何對服務匯流排傳訊命名空間授與該身分識別存取權，以及應用程式如何使用該身分識別來與服務匯流排實體互動。

本簡介說明 [Azure App Service](https://azure.microsoft.com/services/app-service/) 中裝載的 Web 應用程式。 虛擬機器裝載之應用程式所需的步驟類似。

### <a name="create-an-app-service-web-application"></a>建立 App Service Web 應用程式

第一個步驟是建立 App Service ASP.NET 應用程式。 如果您不熟悉如何在 Azure 中這麼做，請遵循[此操作指南](../app-service/app-service-web-get-started-dotnet-framework.md)。 不過，並非如教學課程所示的建立 MVC 應用程式，而是建立 Web Form 應用程式。

### <a name="set-up-the-managed-identity"></a>設定受控識別

一旦您建立應用程式，在 Azure 入口網站瀏覽至新建立的 Web 應用程式 (也會在操作說明中顯示)，然後瀏覽至 [受控服務識別] 分頁，並啟用功能： 

![](./media/service-bus-managed-service-identity/msi1.png)

一旦您啟用此功能，新的服務識別會在您的 Azure Active Directory 中建立，並設定到 App Service 主機。

### <a name="create-a-new-service-bus-messaging-namespace"></a>建立新的服務匯流排傳訊命名空間

接下來，在具有 RBAC 之預覽支援的其中一個 Azure 區域中[建立服務匯流排傳訊命名空間](service-bus-create-namespace-portal.md)：**美國東部**、**美國東部 2** 或**西歐**。 

瀏覽至入口網站上的命名空間 [存取控制 (IAM)] 頁面，然後按一下 [新增角色指派] 以將受控識別新增至 [擁有者] 角色。 若要這樣做，請在 [新增權限] 面板 [選取] 欄位中搜尋 Web 應用程式的名稱，然後按一下項目。 然後按一下 [儲存] 。

Web 應用程式的受控識別現在具有服務匯流排命名空間的存取權，以及您先前建立之佇列的存取權。 

### <a name="run-the-app"></a>執行應用程式

現在修改您建立之 ASP.NET 應用程式的預設分頁。 您可以使用來自[這個 GitHub 存放庫](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)的 Web 應用程式程式碼。  

Default.aspx 頁面是您的登陸頁面。 您可以在 Default.aspx.cs 檔案中找到程式碼。 結果是最小的 Web 應用程式，具有數個項目欄位，以及具有連線到服務匯流排的 [傳送] 和 [接收] 按鈕，以傳送或接收訊息。

請注意 [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 物件如何初始化。 程式碼不會使用共用存取權杖 (SAS) 權杖提供者，而會改用 `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` 呼叫來建立受控識別的權杖提供者。 因此，不會保留及使用密碼。 受控識別內容到服務匯流排和授權交握的流程，都是由權杖提供者自動處理。 它是比使用 SAS 更簡單的模型。

當您進行這些變更之後，請發行並執行應用程式。 您只要下載發行設定檔然後在 Visual Studio 中匯入，就能取得正確的發行資料：

![](./media/service-bus-managed-service-identity/msi3.png)
 
若要傳送或接收訊息，請輸入命名空間名稱和您所建立之實體的名稱。 然後，按一下 [傳送] 或 [接收]。


> [!NOTE]
> - 受控識別只能在 Azure 環境中、在應用程式服務、Azure VM 和擴展集上運作。 對於 .NET 應用程式，Microsoft.Azure.Services.AppAuthentication 程式庫 (由服務匯流排 NuGet 套件使用) 提供讓應用程式透過此通訊協定進行提取的功能，也能支援本機部署經驗。 該程式庫還能讓您使用來自 Visual Studio、Azure CLI 2.0 或 Active Directory 整合式驗證的使用者帳戶，在部署機器上以本機方式測試程式碼。 如需使用此程式庫的本機開發選項詳細資訊，請參閱[使用 .NET 對 Azure Key Vault 進行服務對服務驗證](../key-vault/service-to-service-authentication.md)。  
> 
> - 受控識別目前不會使用 App Service 部署位置。

## <a name="next-steps"></a>後續步驟

若要深入了解服務匯流排傳訊，請參閱下列主題：

* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)