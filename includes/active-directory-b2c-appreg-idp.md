---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: 3b6f8ef5f2ec9f77dcba9c006fdd28b5ec668033
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315034"
---
與 Azure AD B2C 的通訊會透過您在 B2C 租使用者中註冊的應用程式進行。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選取包含您 Azure AD B2C 租使用者的目錄。
1. 在左側功能表中，選取 [ **Azure AD B2C**]。 或者，選取 [**所有服務**]，然後搜尋並選取 [ **Azure AD B2C**]。
1. 選取 [應用程式]，然後選取 [新增]。
1. 輸入應用程式的名稱。 例如， *testapp1*。
1. 針對 [ **Web 應用程式/WEB API**]，選取 **[是]** 。
1. 針對 [**回復 URL**]，輸入`https://jwt.ms`
1. 選取 [建立]。
