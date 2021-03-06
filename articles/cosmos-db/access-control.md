---
title: 在 Azure Cosmos DB 中設定存取控制 | Microsoft Docs
description: 了解如何在 Azure Cosmos DB 中設定存取控制。
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: rafats
ms.openlocfilehash: 0d4595bcf8a9f009dce928d3f3cbc442328ec39b
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2018
ms.locfileid: "40037971"
---
# <a name="access-control-in-azure-cosmos-db"></a>Azure Cosmos DB 中的存取控制

若要將 Azure Cosmos DB 帳戶讀者存取權新增至您的使用者帳戶，請在 Azure 入口網站執行下列步驟，以取得訂用帳戶擁有者。

1. 開啟 Azure 入口網站，選取您的 Azure Cosmos DB 帳戶。
2. 按一下 [存取控制 (IAM)] 索引標籤，然後按一下 [+ 新增]。
3. 在 [新增權限] 窗格的 [角色] 方塊中，選取 [Cosmos DB 帳戶讀者角色]。
4. 在 [存取權指派對象為] 方塊中，選取 [Azure AD 使用者、群組或應用程式]。
5. 選取目錄中您要為其授與存取權的使用者、群組或應用程式。  您可以依顯示名稱、電子郵件地址或物件識別碼來搜尋目錄。
    選取的使用者、群組或應用程式會出現在選取的成員清單中。
6. 按一下 [檔案] 。

實體現在已可讀取 Azure Cosmos DB 資源。
