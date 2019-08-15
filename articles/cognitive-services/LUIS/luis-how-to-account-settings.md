---
title: 管理帳戶和金鑰-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 帳戶的兩個重要資訊為使用者帳戶和撰寫金鑰。 您的登入資訊是在 account.microsoft.com 管理。 您的撰寫金鑰是從 LUIS 入口網站的 [設定] 頁面進行管理。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 11a2040e674a0ee38fbce8b21097f058fe603da2
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881937"
---
# <a name="manage-account-and-authoring-key"></a>管理帳戶和撰寫金鑰

LUIS 帳戶的兩個重要資訊為使用者帳戶和撰寫金鑰。 您的登入資訊是在 [account.microsoft.com](https://account.microsoft.com) 進行管理的。 您的撰寫金鑰是從[LUIS](luis-reference-regions.md)入口網站的 [**設定**] 頁面進行管理。

## <a name="authoring-key"></a>撰寫金鑰

[**設定**] 頁面上這個單一的區域專屬撰寫金鑰, 可讓您從[LUIS](luis-reference-regions.md)入口網站以及[撰寫 api](https://go.microsoft.com/fwlink/?linkid=2092087)來撰寫所有應用程式。 為了方便起見，允許撰寫金鑰每個月建立[有限](luis-boundaries.md)數量的端點查詢。

[![LUIS 設定頁面](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

撰寫金鑰是用於您所擁有的任何應用程式，以及任何您被列為共同作業者的應用程式。

## <a name="authoring-key-regions"></a>撰寫金鑰區域

不同的[撰寫區域](luis-reference-regions.md#publishing-regions)有自己特有的撰寫金鑰。 這種金鑰無法在不同的區域中使用。

## <a name="reset-authoring-key"></a>重設撰寫金鑰

如果您的撰寫金鑰已被盜用，請重設金鑰。 金鑰會在[LUIS](luis-reference-regions.md)入口網站中的所有應用程式上重設。 如果您透過撰寫 API 來撰寫您的應用程式，則需要將 `Ocp-Apim-Subscription-Key` 的值變更成新的金鑰。

## <a name="delete-account"></a>刪除帳戶

如需刪除帳戶時會刪除哪些資料的相關資訊，請參閱[資料儲存和移除](luis-concept-data-storage.md#accounts)。

## <a name="next-steps"></a>後續步驟

深入了解您的[撰寫金鑰](luis-concept-keys.md#authoring-key)。

