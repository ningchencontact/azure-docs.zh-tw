---
title: Azure 資料共用預覽的角色和需求
description: 深入瞭解在 Azure 資料共用預覽版中，資料提供者和資料取用者共用資料的存取控制角色和需求。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: c0841f6386440776c6ea719f9932a53cada9d9c4
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166381"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Azure 資料共用預覽的角色和需求

本文說明使用 Azure 資料共用預覽來共用資料時所需的角色，以及使用 Azure 資料共用預覽來接受和接收資料。 

## <a name="roles-and-requirements"></a>角色和需求

Azure 資料共用會使用 Azure 服務的受控識別（先前稱為 Msi）來驗證基礎儲存體帳戶，以便讀取資料提供者所共用的資料，以及接收當做資料取用者共用的資料。 因此，資料提供者與資料取用者之間不會交換認證。 

受控服務識別需要獲得基礎儲存體帳戶的存取權。 Azure 資料共用服務會使用 Azure 資料共用資源的受控服務識別來讀取和寫入資料。 Azure 資料共用的使用者必須能夠針對其共用資料的儲存體帳戶，建立受控服務識別的角色指派。 建立角色指派的許可權存在於**擁有**者角色、使用者存取系統管理員角色，或已指派 Microsoft 授權/角色指派/寫入權限的自訂角色中。 

如果您不是有問題之儲存體帳戶的擁有者，而且無法自行為 Azure 資料共用資源的受控識別建立角色指派，您可以要求 Azure 系統管理員代表您建立角色指派。 

以下是指派給資料共用資源管理的身分識別之角色的摘要：

| |  |  |
|---|---|---|
|**儲存類型**|**Data Provider 來源儲存體帳戶**|**資料取用者目標儲存體帳戶**|
|Azure Blob 儲存體| 儲存體 Blob 資料讀取器 | 儲存體 Blob 資料參與者
|Azure Data Lake Gen1 | 擁有者 | 不支援
|Azure Data Lake Gen2 | 儲存體 Blob 資料讀取器 | 儲存體 Blob 資料參與者
|
### <a name="data-providers"></a>資料提供者 
若要將資料集新增至 Azure 資料共用，必須將資料提供者資料共用資源受控識別新增至儲存體 Blob 資料讀取器角色。 如果使用者是透過 Azure 新增資料集，而且是儲存體帳戶的擁有者，或是已指派 Microsoft 授權/角色指派/寫入權限的自訂角色成員，Azure 資料共用服務就會自動完成此作業。 

或者，使用者可以讓 Azure 系統管理員以手動方式將資料共用資源受控識別新增至儲存體 Blob 資料讀取器角色。 系統管理員手動建立此角色指派，將必須是儲存體帳戶的擁有者，或擁有自訂角色指派。 這適用于從 Azure 儲存體或 Azure Data Lake Gen2 共用的資料。 

如果從 Azure Data Lake Gen1 共用資料，則必須對擁有者角色指派角色。 

若要為資料共用資源的受控識別建立角色指派，請遵循下列步驟：

1. 流覽至儲存體帳戶。
1. 選取 [存取控制 (IAM)]。
1. 選取 [新增角色指派]。
1. 在 [*角色*] 下，選取 [*儲存體 Blob 資料讀取器*]。
1. 在 [*選取*] 下，輸入 Azure 資料共用帳戶的名稱。
1. 按一下 [儲存]。

### <a name="data-consumers"></a>資料取用者
若要接收資料，必須將資料取用者資料共用資源受控識別新增至儲存體 Blob 資料參與者角色。 若要讓 Azure 資料共用服務能夠寫入儲存體帳戶，必須要有此角色。 如果使用者是透過 Azure 新增資料集，而且是儲存體帳戶的擁有者，或為已指派 Microsoft 授權/角色指派/寫入權限的自訂角色成員，Azure 資料共用服務就會自動完成此作業。 

或者，使用者可以讓 Azure 系統管理員以手動方式將資料共用資源受控識別新增至儲存體 Blob 資料參與者角色。 系統管理員手動建立此角色指派，將必須是儲存體帳戶的擁有者，或擁有自訂角色指派。 請注意，這適用于 Azure 儲存體或 Azure Data Lake Gen2 共用的資料。 不支援將資料接收到 Azure Data Lake Gen1。 

若要手動為資料共用資源的受控識別建立角色指派，請遵循下列步驟：

1. 流覽至儲存體帳戶。
1. 選取 [存取控制 (IAM)]。
1. 選取 [新增角色指派]。
1. 在 [*角色*] 下，選取 [*儲存體 Blob 資料參與者*]。 
1. 在 [*選取*] 下，輸入 Azure 資料共用帳戶的名稱。
1. 按一下 [儲存]。

如果您要使用 REST Api 共用資料，您必須將中的資料共用帳戶新增至適當的角色，以手動建立這些角色指派。 

若要深入瞭解如何新增角色指派，請參閱[此檔，](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)其中概述如何將角色指派新增至 Azure 資源。 

## <a name="resource-provider-registration"></a>資源提供者註冊 

接受 Azure 資料共用邀請時，您必須手動將 DataShare 資源提供者註冊到您的訂用帳戶。 請遵循下列步驟，在您的 Azure 訂用帳戶中註冊 DataShare 資源提供者。 

1. 在 Azure 入口網站中，流覽至 訂用帳戶 **。**
1. 選取您要用於 Azure 資料共用的訂用帳戶。
1. 按一下 [**資源提供者**]。
1. 搜尋 DataShare。
1. 按一下 [註冊]。

## <a name="next-steps"></a>後續步驟

- 深入了解 Azure 中的角色 - [了解角色定義](../role-based-access-control/role-definitions.md)

