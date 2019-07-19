---
title: 包含檔案
description: 包含機密用戶端案例登陸頁面 (daemon、Web 應用程式、Web API) 的檔案
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
ms.openlocfilehash: cd37880be6d518105e880b93a0bd748f7c729d88
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286305"
---
## <a name="registration-of-secrets-or-certificates"></a>註冊秘密或憑證

就像任何機密用戶端應用程式一樣, 您必須註冊秘密或憑證。 您可以透過[Azure 入口網站](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)中的互動式體驗, 或使用命令列工具 (例如 PowerShell) 來註冊您的應用程式秘密

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>使用應用程式註冊入口網站註冊用戶端秘密

用戶端認證的管理會出現在應用程式的 [**憑證 & 秘密**] 頁面中:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- 應用程式密碼 (也稱為用戶端密碼) 是在註冊機密用戶端應用程式時, 由 Azure AD 所產生。 當您選取 [**新增用戶端密碼**] 時, 就會發生此產生。 此時, 您必須先複製剪貼簿中的秘密字串, 以便在您的應用程式中使用, 然後再選取 [**儲存**]。 這個字串將不會再顯示。
- 憑證會使用 [**上傳憑證**] 按鈕, 在應用程式註冊中上傳。 Azure AD 僅支援在應用程式上直接註冊的憑證, 且不會遵循憑證鏈。

如需詳細資訊[, 請參閱快速入門:設定用戶端應用程式以存取 web Api |將認證新增至您的應用程式](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="registering-client-secrets-using-powershell"></a>使用 PowerShell 註冊用戶端秘密

或者, 您也可以使用命令列工具, 向 Azure AD 註冊應用程式。 [Active directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)範例示範如何使用 Azure AD 應用程式來註冊應用程式密碼或憑證:

- 如需如何註冊應用程式密碼的詳細資訊, 請參閱[AppCreationScripts/Configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- 如需如何向應用程式註冊憑證的詳細資訊, 請參閱[AppCreationScripts-withCert/Configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)
