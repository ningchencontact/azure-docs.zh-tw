---
title: "Azure Event Hubs Role-Based 存取控制 (RBAC) 預覽 |Microsoft 文件"
description: "Azure 事件中心以角色為基礎的存取控制"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 0d3a779eb2cccf242bcd42d82c1a90048b3512ab
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="active-directory-role-based-access-control-preview"></a>Active Directory Role-Based 存取控制 （預覽）

Microsoft Azure 提供資源和 Azure Active Directory (Azure AD) 為基礎的應用程式的整合式的存取控制的管理。 使用 Azure AD，您可以管理使用者帳戶和應用程式，專為您的 Azure 架構的應用程式，或您可以建立同盟您現有的 Active Directory 基礎結構與 Azure AD 進行全公司的單一登入也跨越 Azure 資源與 Azure 託管應用程式。 然後，您可以指派給全域和服務特定角色的 Azure AD 使用者和應用程式識別才能授與存取權的 Azure 資源。

Azure 事件中心的命名空間和所有相關的資源，透過 Azure 入口網站的管理和 Azure 資源管理應用程式開發介面已受到保護使用*角色型存取控制*(RBAC) 模型。 RBAC 執行階段作業一項功能目前處於公開預覽狀態。 

不需要使用 Azure AD RBAC 的應用程式不會處理 SAS 規則和索引鍵或特定事件中心的任何其他存取權杖。 用戶端應用程式互動與 Azure AD 建立驗證內容，並取得存取權杖建立事件中樞。 使用網域使用者帳戶需要互動式登入，應用程式永遠不會處理任何認證直接。

## <a name="event-hubs-roles-and-permissions"></a>事件中心角色和權限

對於初始公開預覽，您可以只加入 Azure AD 帳戶和服務主體的事件中樞命名空間的 「 擁有者 」 或 「 著作人 」 角色。 此作業授與身分識別完全控制權的命名空間中的所有實體。 變更命名空間拓撲的管理作業將會一開始僅支援透過 Azure 資源管理而不是透過原生事件中心 REST 管理介面。 此支援也表示，.NET Framework 用戶端[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager)物件不能與 Azure AD 帳戶。  

## <a name="use-event-hubs-with-an-azure-ad-domain-user-account"></a>使用事件中心的 Azure AD 網域使用者帳戶

下節說明的步驟，才能建立及執行範例應用程式，以提示互動式的 azure AD 使用者能夠登入、 如何授與事件中心的存取權給該使用者帳戶，以及如何使用該識別來存取事件中心。 

本簡介說明簡單的主控台應用程式，[是 Github 上的這段程式碼](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Rbac/EventHubsSenderReceiverRbac/)

### <a name="create-an-active-directory-user-account"></a>建立 Active Directory 使用者帳戶

第一個步驟是選擇性的。 每個 Azure 訂用帳戶會自動搭配 Azure Active Directory 租用戶，如果您有 Azure 訂用帳戶的存取，已註冊您的使用者帳戶。 這表示您可以只使用您的帳戶。 

如果您仍然想要建立特定帳戶來進行這個案例中，[請遵循下列步驟](../automation/automation-create-aduser-account.md)。 您必須在 Azure Active Directory 租用戶，但可能更大的企業案例的案例中建立帳戶的權限。

### <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間

下一步[建立事件中樞命名空間](event-hubs-create.md)中其中一個 Azure 區域事件中心預覽支援 RBAC:**美國東部**，**美國東部 2**，或**西歐**. 

命名空間建立之後，瀏覽至其**存取控制 (IAM)**頁面在入口網站，然後再按一下**新增**將 Azure AD 使用者帳戶新增至擁有者角色。 如果您使用您自己的使用者帳戶，而且您建立命名空間，您已擁有者角色中。 若要將不同的帳戶加入至角色中，搜尋中的 web 應用程式的名稱**新增權限**面板**選取**欄位，，然後按一下 項目。 然後按一下 [儲存] 。
 
![](./media/event-hubs-role-based-access-control/rbac1.PNG)

使用者帳戶現在可以存取的事件中樞命名空間，而到事件中心您先前建立的。
 
### <a name="register-the-application"></a>註冊應用程式

執行範例應用程式之前，在 Azure AD 中註冊它，並核准允許應用程式存取事件中心自行同意提示。 

由於範例應用程式是主控台應用程式，您必須註冊原生應用程式，並加入 API 的權限**Microsoft.EventHub**至 [必要的權限] 集合。 原生應用程式也需要**重新導向 URI**會做為識別項，Azure AD 中的 URI 不需要網路目的地。 使用`http://eventhubs.microsoft.com`對於此範例中，因為已經範例程式碼會使用該 URI。

詳細的註冊步驟說明，請參閱[本教學課程](../active-directory/develop/active-directory-integrating-applications.md)。 請依照下列步驟來註冊**原生**應用程式，然後依照指示將新增更新**Microsoft.EventHub** API，以必要的權限。 當您遵循的步驟，請記下**TenantId**和**ApplicationId**，將會需要這些值來執行應用程式。

### <a name="run-the-app"></a>執行應用程式

您可以執行範例之前，請編輯 App.config 檔案並根據您的案例中，設定下列值：

- `tenantId`： 設定為**TenantId**值。
- `clientId`： 設定為**ApplicationId**值。 
- `clientSecret`： 如果您想要使用用戶端密碼登入，則會建立在 Azure AD 中。 此外，而不是原生應用程式中使用的 web 應用程式或應用程式開發介面。 此外，請加入待測應用程式**存取控制 (IAM)**您先前建立的命名空間中。
- `eventHubNamespaceFQDN`： 設定為新建立的事件中樞命名空間; 的完整 DNS 名稱例如， `example.servicebus.windows.net`。
- `eventHubName`： 設定為您建立事件中樞的名稱。
- 在先前步驟中的應用程式中指定的重新導向 URI。
 
當您執行主控台應用程式時，提示您選取的案例。按一下**互動式的使用者登入**輸入其數字，然後按 ENTER 鍵。 應用程式顯示 [登入] 視窗，會要求您同意，以存取事件中樞，然後再使用服務使用的登入身分識別傳送/接收的案例中執行。

## <a name="next-steps"></a>後續步驟

如需事件中樞的詳細資訊，請造訪下列連結：

* 開始使用[事件中樞教學課程](event-hubs-dotnet-standard-getstarted-send.md)
* [事件中樞常見問題集](event-hubs-faq.md)
* [事件中樞價格詳細資料](https://azure.microsoft.com/pricing/details/event-hubs/)
* [使用事件中樞的完整範例應用程式](https://github.com/Azure/azure-event-hubs/tree/master/samples)