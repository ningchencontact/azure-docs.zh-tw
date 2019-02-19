---
title: 向 Azure 地圖服務驗證 | Microsoft Docs
description: 進行驗證以使用 Azure 地圖服務。
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b97c303433eb8fadcda51257d37447f052ce4a3b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118795"
---
# <a name="authentication-with-azure-maps"></a>向 Azure 地圖服務驗證

Azure 地圖服務支援兩種用來驗證要求的方式。 共用金鑰或 Azure Active Directory (Azure AD) 可提供不同方法來對傳送至 Azure 地圖服務的每個要求進行授權。 本文的目的是要說明這兩種驗證方法，以協助指導您的驗證實作。

## <a name="shared-key-authentication"></a>共用金鑰驗證

共用金鑰驗證需依靠將 Azure 地圖服務帳戶所產生的金鑰連同每個要求，傳遞至 Azure 地圖服務。  建立 Azure 地圖服務帳戶時會產生兩個金鑰。  每個 Azure 地圖服務要求都需要將訂用帳戶金鑰新增為 URL 的參數。

> [!Tip]
> 建議您定期重新產生金鑰。 您會收到兩個金鑰，以便使用一個金鑰維持連線，同時重新產生另一個金鑰。 在重新產生金鑰時，必須對所有存取此帳戶的應用程式進行更新，使其使用新的金鑰。

若要檢視金鑰，請參閱[驗證詳細資料](https://aka.ms/amauthdetails)。

## <a name="authentication-with-azure-active-directory-preview"></a>使用 Azure Active Directory 進行驗證 (預覽)

Azure 地圖服務現在提供 [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) 整合，以驗證 Azure 地圖服務的要求。  Azure AD 會提供以識別為基礎的驗證 (包括[角色型存取控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview))，以授與使用者或應用程式層級的 Azure 地圖服務資源存取權。 本文的目的是要協助您了解 Azure 地圖服務 Azure AD 整合的概念和元件。

## <a name="authentication-with-oauth-access-tokens"></a>使用 OAuth 存取權杖進行驗證

針對與包含 Azure 地圖服務帳戶的 Azure 訂用帳戶相關聯的 Azure AD 租用戶，Azure 地圖服務可接受 **OAuth 2.0** 存取權杖。  Azure 地圖服務可接受下列項目的權杖：

* Azure AD 使用者 
* 使用的是使用者所委派權限的第三方應用程式
* 適用於 Azure 資源的受控識別

Azure 地圖服務會為每個 Azure 地圖服務帳戶產生 `unique identifier (client ID)`。  用戶端識別碼與其他參數結合時，便可藉由指定下列值從 Azure AD 要求權杖：

```
https://login.microsoftonline.com
```
如需如何為 Azure 地圖服務設定 Azure AD 和要求權杖的詳細資訊，請參閱[如何管理驗證](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

如需如何從 Azure AD 要求權杖的一般資訊，請參閱 [Azure Active Directory 中的驗證基本概念](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。

## <a name="requesting-azure-map-resources-with-oauth-tokens"></a>使用 OAuth 權杖要求 Azure 地圖服務資源

從 Azure AD 取得權杖後，便可將設定了下列兩個必要要求標頭的要求傳送至 Azure 地圖服務：

| 要求標頭    |    值    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Authorization     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` 是以 Azure 地圖服務帳戶為基礎的 GUID，會顯示在 Azure 地圖服務的 [驗證] 頁面上

以下是使用 OAuth 權杖的 Azure 地圖服務路由要求範例：

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

若要檢視用戶端識別碼，請參閱[驗證詳細資料](https://aka.ms/amauthdetails)。

## <a name="control-access-with-role-based-access-control-rbac"></a>使用角色型存取控制 (RBAC) 來控制存取

Azure AD 有一項重要功能，可透過 RBAC 來控制受保護資源的存取。 在建立了 Azure 地圖服務帳戶，且在 Azure AD 租用戶內註冊 Azure 地圖服務 Azure AD 應用程式後，您現在可以在 Azure 地圖服務帳戶的入口網站頁面中，設定使用者、應用程式或 Azure 資源的 RBAC。 

Azure 地圖服務目前可透過 Azure 資源的受控識別，來支援個別 Azure AD 使用者、應用程式或 Azure 服務的讀取存取控制。

![概念](./media/azure-maps-authentication/concept.png)

若要檢視 RBAC 設定，請參閱[如何設定 Azure 地圖服務的 RBAC](https://aka.ms/amrbac)。

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 資源和 Azure 地圖服務的受控識別

[Azure 資源的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)可為 Azure 服務 (Azure 應用程式服務、Azure Functions、虛擬機器等等) 提供自動的受控識別，以便獲得授權來存取 Azure 地圖服務。  

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何向 Azure AD 和 Azure 地圖服務驗證應用程式，請參閱[如何管理驗證](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

* 若要深入了解如何驗證 Azure 地圖服務、地圖控制項和 Azure AD，請參閱 [Azure AD 和 Azure 地圖服務的地圖控制項](https://aka.ms/amaadmc)。