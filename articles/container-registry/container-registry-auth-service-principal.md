---
title: 使用服務主體進行 Azure Container Registry 驗證
description: 使用 Azure Active Directory 服務主體，提供您私人容器登錄中映像的存取權。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: 5d8904b5906adbdab68989b3a5cf9c3975c23533
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347058"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>使用服務主體進行 Azure Container Registry 驗證

您可以使用 Azure Active Directory (Azure AD) 服務主體，來提供容器登錄的容器映像 `docker push` 和 `pull` 存取權。 藉由使用服務主體，您可以提供「無周邊」服務和應用程式的存取權。

## <a name="what-is-a-service-principal"></a>何謂服務主體？

Azure AD *服務主體*提供您訂用帳戶內 Azure 資源的存取權。 您可以將服務主體視為服務的使用者身分識別，其中「服務」是需要存取資源的任何應用程式、服務或平台。 您可以設定一個服務主體，並將其存取權限範圍限制為您指定的那些資源。 然後，請設定應用程式或服務，以使用服務主體的認證來存取那些資源。

在 Azure Container Registry 的內容中，您可以建立一個 Azure AD 服務主體，並提供您在 Azure 中之私人登錄的提取、發送和提取，或其他權限。 如需完整清單，請參閱 [Azure Container Registry 角色和權限](container-registry-roles.md)。

## <a name="why-use-a-service-principal"></a>為何要使用服務主體？

藉由使用 Azure AD 服務主體，您可以提供對您私人容器登錄之有限範圍的存取權。 您可以針對每個應用程式或服務建立不同的服務主體，每個對您的登錄均具備量身訂做的存取權限。 此外，由於您可以避免在服務和應用程式之間共用認證，因此您可以替換認證，或只撤銷您所選擇之主體服務 (以及應用程式) 的存取權。

例如，儘管您的建置系統可以使用已為其提供 `push` 和 `pull` 存取權的服務主體，但您的 Web 應用程式可以使用僅為其提供映像 `pull` 存取權的服務主體。 如果應用程式的開發轉手，您可以替換其服務主體認證，而不會影響該建置系統。

## <a name="when-to-use-a-service-principal"></a>何時使用服務主體

您應該使用服務主體來提供**無周邊案例**中的登錄存取權。 也就是，任何必須以自動化或其他自動方式發送或提取容器映像的應用程式、服務或指令碼。

針對登錄的個別存取權 (例如，當您手動將容器映像提取至您的開發工作站時)，您應該改用您自己的 [Azure AD 身分識別](container-registry-authentication.md#individual-login-with-azure-ad)進行登錄存取 (例如，使用 [az acr login][az-acr-login])。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>範例指令碼

您可以找到前述適用於 GitHub 上的 Azure CLI 的範例指令碼，以及適用於 Azure PowerShell 的版本：

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>後續步驟

一旦為服務主體授與容器登錄的存取權之後，您就可以在應用程式和服務中使用它的認證進行無周邊登錄互動。 您可以使用服務主體認證，從使用 Azure 容器登錄進行驗證的任何 Azure 服務。 範例包括：

* [從 Azure Kubernetes Service 對 Azure Container Registry 進行驗證 (AKS)](container-registry-auth-aks.md)
* [使用來自 Azure 容器執行個體 (ACI) 的 Azure Container Registry 進行驗證](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
