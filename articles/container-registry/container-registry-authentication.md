---
title: 向 Azure Container Registry 進行驗證 | Microsoft Docs
description: Azure Container Registry 的驗證選項，包括使用 Azure Active Directory 身分識別來登入、使用服務主體，以及使用選擇性的管理員認證。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a55cba27c676b283a4da490f05dd6fc672e10d49
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032380"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>向私用 Docker 容器登錄進行驗證

向 Azure Container Registry 進行驗證的方式有數種，每一種方式都適用於一或多個登錄使用案例。

建議的方式包括直接透過[個別登](#individual-login-with-azure-ad)入向登錄進行驗證, 或者您的應用程式和容器協調器可以使用 Azure Active Directory (Azure AD) 服務執行自動或「無周邊」驗證[主體](#service-principal)。

## <a name="individual-login-with-azure-ad"></a>使用 Azure AD 進行個人登入

直接與您的登錄搭配運作時 (例如將映像拖曳至開發工作站或從開發工作站推送映像)，請在 [Azure CLI](/cli/azure/install-azure-cli) 中使用 [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) 命令來進行驗證：

```azurecli
az acr login --name <acrName>
```

當您使用 `az acr login` 來進行登入時，CLI 會使用您執行 [az login](/cli/azure/reference-index#az-login) 時所建立的權杖，以順暢地向登錄驗證您的工作階段。 在您以此方式登入之後，系統會快取您的認證，而您工作階段中的後續 `docker` 命令不會要求提供使用者名稱或密碼。 

為了進行登錄存取，`az acr login` 所用的驗證權杖有效期為 1 小時，所以我們建議您在執行 `docker` 命令前，一律先登入登錄。 如果您的權杖過期，您可以再次使用 `az acr login` 命令進行重新驗證來重新整理該權杖。 

使用 `az acr login` 搭配 Azure 身分識別可提供[角色型存取](../role-based-access-control/role-assignments-portal.md)功能。 在某些情況下，您可能會想在 Azure AD 中，使用自己的個別身分識別登入登錄庫。 針對跨服務案例，或針對您不想管理個別存取權的工作群組處理其需求時，也可以使用 [Azure 資源的受控識別](container-registry-authentication-managed-identity.md)登入。

## <a name="service-principal"></a>服務主體

如果您將[服務主體](../active-directory/develop/app-objects-and-service-principals.md)指派給登錄，應用程式或服務便可以使用它來進行遠端控制驗證。 服務主體可允許對登錄進行[角色型存取](../role-based-access-control/role-assignments-portal.md)，而您可以將多個服務主體指派給登錄。 多個服務主體可讓您為不同的應用程式定義不同的存取權。

容器登錄的可用角色包括：

* **AcrPull**：提取

* **AcrPush**：提取和推送

* **擁有者**：提取、推送，以及指派角色給其他使用者

如需角色的完整清單，請參閱 [Azure Container Registry 角色和權限](container-registry-roles.md)。

若要讓 CLI 腳本建立用來向 Azure container registry 進行驗證的服務主體, 以及使用服務主體的指引, 請參閱使用[服務主體 Azure Container Registry 驗證](container-registry-auth-service-principal.md)。

## <a name="admin-account"></a>系統管理員帳戶

每個容器登錄都包含一個管理使用者帳戶，且預設為停用。 您可以在 Azure 入口網站中或藉由使用 Azure CLI 或其他 Azure 工具，啟用此管理使用者並管理其認證。

> [!IMPORTANT]
> 管理帳戶是專為讓單一使用者存取登錄而設計，主要用於測試。 我們不建議在多個使用者之間共用系統管理員帳號憑證。 所有使用管理帳戶進行驗證的使用者會顯示為單一使用者，此使用者具備登錄的推送和提取存取權。 變更或停用此帳戶時，會將所有使用其認證之使用者的登錄存取權都停用。 針對遠端控制案例的使用者和服務主體，建議使用個人身分識別。
>

管理帳戶隨附兩個密碼，兩個密碼都可以重新產生。 兩個密碼可讓您在重新產生其中一個密碼時，使用另一個密碼來維持與登錄的連線。 如果已啟用管理帳戶，即可在系統提示時將使用者名稱和其中一個密碼傳遞給 `docker login` 命令，向登錄進行基本驗證。 例如:

```
docker login myregistry.azurecr.io 
```

如需管理登入認證的最佳作法, 請參閱[docker login](https://docs.docker.com/engine/reference/commandline/login/)命令參考。

若要為現有的登錄啟用管理使用者，您可以在 Azure CLI 中使用 [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) 命令的 `--admin-enabled` 參數：

```azurecli
az acr update -n <acrName> --admin-enabled true
```

您可以在 Azure 入口網站中啟用管理使用者，方法是瀏覽至您的登錄，選取 [設定]底下的 [存取金鑰]，然後選取 [管理使用者]底下的 [啟用]。

![Azure 入口網站中的啟用管理使用者 UI][auth-portal-01]

## <a name="next-steps"></a>後續步驟

* [使用 Azure CLI 推送您的第一個映像](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
