---
title: 包含檔案
description: 包含針對機密用戶端案例登陸頁面 （Web 應用程式，Web API 中的 精靈） 的檔案
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 9ee7422b372993d60c629524eb036b9678e5776c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121838"
---
## <a name="registration-of-secrets-or-certificates"></a>密碼或憑證的註冊

例如對於任何機密用戶端應用程式中，您需要註冊密碼或憑證。 您可以透過互動式的體驗中註冊您的應用程式祕密[Azure 入口網站](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)，或使用命令列工具 （例如 PowerShell)

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>使用應用程式註冊入口網站註冊用戶端祕密

用戶端認證的管理動作會在**憑證與祕密**應用程式頁面：

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- 應用程式祕密 （也是具名的用戶端祕密） 是 Azure AD 中，機密用戶端應用程式註冊期間所產生的。 當您選取時，會發生這個層代**新的用戶端祕密**。 此時，您必須複製祕密的字串使用剪貼簿中您的應用程式，然後再選取**儲存**。 這個字串不會再顯示。
- 在應用程式註冊中使用上傳的憑證**上傳憑證**按鈕

如需詳細資訊，請參閱[快速入門：設定用戶端應用程式以存取 web Api |將認證新增至您的應用程式](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="registering-client-secrets-using-powershell"></a>使用 PowerShell 註冊的用戶端祕密

或者，您也可以使用命令列工具與 Azure AD 註冊您的應用程式。 [作用中-目錄-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)範例示範如何使用 Azure AD 應用程式註冊的應用程式密碼或憑證：

- 如需有關如何註冊應用程式祕密的詳細資訊，請參閱[AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- 如需有關如何使用應用程式註冊憑證的詳細資訊，請參閱[AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)