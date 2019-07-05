---
title: 基準原則需要 MFA 的服務管理 （預覽）-Azure Active Directory
description: 適用於 Azure Resource Manager 時要求 MFA 的條件式存取原則
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 203b752f9da67ebf60e373fe7ce0893b4fd7fcb5
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560967"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>基準原則：需要 MFA 才能進行服務管理 （預覽）

您可能會在組織中使用各種 Azure 服務。 這些服務可透過 Azure Resource Manager API 進行管理：

* Azure 入口網站
* Azure PowerShell
* Azure CLI

使用 Azure Resource Manager 來管理您的服務是具有高度權限的動作。 Azure Resource Manager 可以改變整個租用戶的組態，例如服務設定和訂用帳戶計費。 容易受到攻擊，例如網路釣魚和密碼的噴灑各種單一要素驗證。 因此，務必確認想要存取 Azure Resource Manager 與更新組態，需要多重要素驗證後才允許存取使用者的身分識別。

**服務管理要求 MFA**已[基準原則](concept-baseline-protection.md)，將會為任何使用者存取 Azure 入口網站、 Azure PowerShell 或 Azure CLI 來要求 MFA。 此原則適用於存取 Azure Resource Manager，不論，如果它們是系統管理員的所有使用者。

一旦租用戶中啟用此原則，將會受到質疑登入 Azure 管理資源的所有使用者使用 multi-factor authentication。 如果使用者未註冊 mfa，使用者必須註冊才能繼續使用 Microsoft Authenticator 應用程式。

若要執行互動式登入使用[Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)，使用[Connect AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet。

```PowerShell
Connect-AzAccount
```

執行時，此 Cmdlet 會出示權杖字串。 若要登入，請複製這個字串並將它貼至 [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)  瀏覽器中。 PowerShell 工作階段會進行驗證以便連線至 Azure。

若要執行互動式登入使用[Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)，請執行 [az 登入](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令。

```azurecli
az login
```

如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則，您需要開啟瀏覽器頁面，並遵循之後瀏覽至輸入授權碼的命令列上的指示 [https://aka.ms/devicelogin ](https://aka.ms/devicelogin)瀏覽器中。 之後，使用您在瀏覽器中的帳戶認證登入。

## <a name="deployment-considerations"></a>部署考量

因為**要求 MFA 服務管理**原則套用到所有的 Azure Resource Manager 使用者，需要進行以確保順利部署的幾個考量。 這些考量包括用來識別使用者與 Azure ad 中無法或不應該執行 MFA，以及應用程式與您組織所使用的用戶端不支援新式驗證的服務主體。

## <a name="enable-the-baseline-policy"></a>啟用基準原則

原則**基準原則：服務管理 （預覽） 要求 MFA**隨附預先設定，然後會出現在頂端當您瀏覽至 Azure 入口網站中的 [條件式存取] 刀鋒視窗。

若要啟用此原則，並保護您的系統管理員：

1. 登入 **Azure 入口網站** 為全域管理員、 安全性系統管理員或條件式存取系統管理員。
1. 瀏覽至**Azure Active Directory** > **條件式存取**。
1. 在原則清單中，選取 **基準原則：服務管理 （預覽） 要求 MFA**。
1. 設定**啟用原則**要**立即使用原則**。
1. 按一下  **儲存**。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱

* [條件式存取基準保護原則](concept-baseline-protection.md)
* [可保護身分識別基礎結構的五個步驟](../../security/azure-ad-secure-steps.md)
* [什麼是 Azure Active Directory 中的條件式存取？](overview.md)