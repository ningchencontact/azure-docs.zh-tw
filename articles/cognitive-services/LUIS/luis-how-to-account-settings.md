---
title: 管理帳戶和金鑰
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 帳戶的兩個重要資訊為使用者帳戶和撰寫金鑰。 登入資訊被管理 account.microsoft.com。 您撰寫的金鑰管理從 LUIS 入口網站設定 頁面。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: d7d63ad642ab2d3b336e15dcca606077762ceb9d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116617"
---
# <a name="manage-account-and-authoring-key"></a>管理帳戶和撰寫金鑰

LUIS 帳戶的兩個重要資訊為使用者帳戶和撰寫金鑰。 您的登入資訊是在 [account.microsoft.com](https://account.microsoft.com) 進行管理的。 您撰寫的金鑰管理從[LUIS](luis-reference-regions.md)入口網站**設定**頁面。

## <a name="authoring-key"></a>撰寫金鑰

單一的特定地區的此編寫索引鍵，在**設定**頁面上，可讓您撰寫從所有應用程式[LUIS](luis-reference-regions.md)入口網站，以及[編寫 Api](https://aka.ms/luis-authoring-api)。 為了方便起見，允許撰寫金鑰每個月建立[有限](luis-boundaries.md)數量的端點查詢。

[![LUIS 設定頁面](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

撰寫金鑰是用於您所擁有的任何應用程式，以及任何您被列為共同作業者的應用程式。

## <a name="authoring-key-regions"></a>撰寫金鑰區域

不同的[撰寫區域](luis-reference-regions.md#publishing-regions)有自己特有的撰寫金鑰。 這種金鑰無法在不同的區域中使用。

## <a name="reset-authoring-key"></a>重設撰寫金鑰

如果您的撰寫金鑰已被盜用，請重設金鑰。 在 所有應用程式重設金鑰[LUIS](luis-reference-regions.md)入口網站。 如果您透過撰寫 API 來撰寫您的應用程式，則需要將 `Ocp-Apim-Subscription-Key` 的值變更成新的金鑰。

## <a name="delete-account"></a>刪除帳戶

如需刪除帳戶時會刪除哪些資料的相關資訊，請參閱[資料儲存和移除](luis-concept-data-storage.md#accounts)。

## <a name="next-steps"></a>後續步驟

深入了解您的[撰寫金鑰](luis-concept-keys.md#authoring-key)。

