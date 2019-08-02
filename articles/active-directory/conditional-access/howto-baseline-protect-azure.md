---
title: 基準原則需要 MFA 以進行服務管理 (預覽)-Azure Active Directory
description: Azure Resource Manager 需要 MFA 的條件式存取原則
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
ms.openlocfilehash: aab2aa4415345747a0e87b90ef0a7ee770ef3465
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608132"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>基準原則:服務管理需要 MFA (預覽)

您的組織中可能會使用各種不同的 Azure 服務。 這些服務可以透過 Azure Resource Manager API 來管理:

* Azure 入口網站
* Azure PowerShell
* Azure CLI

使用 Azure Resource Manager 來管理您的服務是具有高許可權的動作。 Azure Resource Manager 可以改變整個租使用者的設定, 例如服務設定和訂用帳戶計費。 單一要素驗證容易遭受各種攻擊, 例如網路釣魚和密碼噴灑。 因此, 請務必確認想要存取 Azure Resource Manager 和更新設定的使用者身分識別, 方法是要求多重要素驗證, 然後才允許存取。

**服務管理需要 mfa**是[基準原則](concept-baseline-protection.md), 會要求任何存取 Azure 入口網站、Azure PowerShell 或 AZURE CLI 的使用者進行 mfa。 此原則適用于所有存取 Azure Resource Manager 的使用者, 而不論其是否為系統管理員。

在租使用者中啟用此原則之後, 所有登入 Azure 管理資源的使用者都會受到多重要素驗證的挑戰。 如果使用者未註冊 MFA, 使用者將需要使用 Microsoft Authenticator 應用程式進行註冊, 才能繼續進行。

若要使用[Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)執行互動式登入, 請使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)  Cmdlet。

```PowerShell
Connect-AzAccount
```

執行時，此 Cmdlet 會出示權杖字串。 若要登入, 請複製這個字串, 並 [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)將它  貼入瀏覽器中的。 PowerShell 工作階段會進行驗證以便連線至 Azure。

若要使用[Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)執行互動式登入, 請執行 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)  命令。

```azurecli
az login
```

如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則, 您需要開啟瀏覽器頁面, 並遵循命令列上的指示, 在瀏覽器中流覽至 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)之後, 輸入授權碼。 之後, 請在瀏覽器中使用您的帳號憑證登入。

## <a name="deployment-considerations"></a>部署考量

由於「**要求服務管理**原則的 MFA」適用于所有 Azure Resource Manager 使用者, 因此需要進行幾項考慮以確保部署順利。 這些考慮包括識別 Azure AD 中無法或不應執行 MFA 的使用者和服務原則, 以及您的組織使用不支援新式驗證的應用程式和用戶端。

## <a name="enable-the-baseline-policy"></a>啟用基準原則

原則**基準原則:[要求 MFA for service management (預覽** )] 已預先設定, 當您流覽至 Azure 入口網站中的 [條件式存取] 分頁時, 會顯示在頂端。

若要啟用此原則並保護您的系統管理員:

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入 **Azure 入口網站** 。
1. 流覽至**Azure Active Directory**  > **條件式存取**。
1. 在原則清單中, 選取 **[基準原則]:需要 MFA 以進行服務管理 (預覽**)。
1. 將 [**啟用原則**] 設定為**立即使用原則**。
1. 按一下 [ **儲存**]。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱：

* [條件式存取基準保護原則](concept-baseline-protection.md)
* [可保護身分識別基礎結構的五個步驟](../../security/fundamentals/steps-secure-identity.md)
* [什麼是 Azure Active Directory 中的條件式存取？](overview.md)