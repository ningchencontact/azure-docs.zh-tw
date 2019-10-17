---
title: Service Fabric 受控識別總覽 |Microsoft Docs
description: 本文概述受控識別。
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: edce98e6babb676ee72f1d254b929e557332dd75
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333128"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Service Fabric 應用程式的受控識別（預覽）

建置雲端應用程式常見的難題是如何管理程式碼中的認證，以向雲端服務進行驗證。 保護認證安全是很重要的工作，因為它們永遠不會出現在開發人員工作站上，而且不會簽入原始檔控制。 Azure Active Directory （Azure AD）中適用于 Azure 資源的受控識別功能可解決此問題。 此功能會在 Azure AD 中，為 Azure 服務提供自動管理的身分識別。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要您程式碼中的任何認證。

適用于 azure 資源的受控識別功能可免費使用 Azure 訂用帳戶的 Azure AD。 沒有任何額外成本。

> [!NOTE]
> 適用于 Azure 資源的受控識別是先前稱為受控服務識別（MSI）之服務的新名稱。

## <a name="terminology"></a>詞彙

下列詞彙適用于 Azure 資源的受控識別檔集：

- **用戶端識別碼**-在其初始布建期間系結至應用程式和服務主體的 Azure AD 所產生的唯一識別碼（也請參閱[應用程式識別碼](/azure/active-directory/develop/developer-glossary#application-id-client-id)）。

- **主體識別碼**-受控識別之服務主體物件的物件識別碼，用來授與 Azure 資源的角色型存取權。

- **服務主體**-Azure Active Directory 物件，代表指定租使用者中 AAD 應用程式的投影（也請參閱[服務主體](../active-directory/develop/developer-glossary.md#service-principal-object)）。


## <a name="about-managed-identities-in-azure"></a>關於 Azure 中的受控識別

- [Azure 中的受控識別（MI）類型](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [系統指派的受控識別在 Azure 中的運作方式](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [使用者定義的受控識別（MI）在 Azure 中的運作方式](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric 應用程式的支援案例

只有在 Azure 中部署的 Service Fabric 叢集才支援 Service Fabric 的受控識別，且僅適用于部署為 Azure 資源的應用程式;未部署為 Azure 資源的應用程式無法指派身分識別。 在概念上，支援 Azure Service Fabric 叢集中的受控識別是由兩個階段所組成：

1. 將一個或多個受控識別指派給應用程式資源;應用程式可能會分別指派單一系統指派的身分識別，以及（或）高達32的使用者指派身分識別。

2. 在應用程式的定義中，將指派給應用程式的其中一個身分識別對應至組成應用程式的任何個別服務。

應用程式的系統指派身分識別對該應用程式而言是唯一的;使用者指派的身分識別是一種獨立的資源，可以指派給多個應用程式。 在應用程式內，單一身分識別（不論是系統指派或使用者指派）可以指派給應用程式的多個服務，但每個個別服務只能指派一個身分識別。 最後，必須明確地將身分識別指派給服務，才能存取這項功能。 實際上，應用程式的身分識別對應到其構成服務允許應用程式內隔離，而服務可能只會使用對應到它的身分識別（如果未明確指派，則不會有任何一項）。  

預覽版本支援的案例清單如下所示：

   - 部署新的應用程式，其中包含一或多個服務，以及一或多個指派的身分識別

   - 將一個或多個受控識別指派給現有的應用程式，以便存取 Azure 資源;應用程式必須已部署為 Azure 資源本身


下列案例不受支援或不建議使用;請注意，這些動作可能不會被封鎖，但可能會導致您的應用程式中斷：

   - 移除或變更指派給應用程式的身分識別;如果您必須進行變更，請先提交個別的部署，以加入新的身分識別指派，然後再移除先前指派的身分。 從現有的應用程式移除身分識別可能會產生不想要的效果，包括讓應用程式處於無法升級的狀態。 如果需要移除身分識別，則可以放心地刪除應用程式;請注意，這會刪除與應用程式相關聯的系統指派身分識別（如果有定義的話），並會移除指派給應用程式之使用者指派身分識別的任何關聯。

   - 受控識別的 SF 支援目前未整合到[azureservicetokenprovider 會](../key-vault/service-to-service-authentication.md);受控識別功能的預覽期間結束時，將會達成此整合。

>
> [!NOTE]
>
> 這項功能目前為預覽狀態;因此，它可能會受到經常的變更，而且可能不適合生產環境部署。

## <a name="next-steps"></a>後續步驟
* [使用受控識別支援來部署新的 Azure Service Fabric 叢集](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [在現有的 Azure Service Fabric 叢集中啟用受控識別支援](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [使用系統指派的受控識別來部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用使用者指派的受控識別來部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [從服務程式代碼運用 Service Fabric 應用程式的受控識別](./how-to-managed-identity-service-fabric-app-code.md)
* [將其他 Azure 資源的存取權授與 Azure Service Fabric 應用程式](./how-to-grant-access-other-resources.md)
