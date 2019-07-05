---
title: 健康狀態檢查-Azure Container Registry 作為錯誤參考
description: 錯誤代碼和可能的解決方案，藉由執行 az acr 檢查健全狀況診斷命令在 Azure Container Registry 中找到的問題
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: fc29b27cbb7eea983140c59529d981ad95c27ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555107"
---
# <a name="health-check-error-reference"></a>健全狀況檢查錯誤參考

以下是所傳回的錯誤碼的詳細[az acr 檢查健全狀況][az-acr-check-health]命令。 針對每個錯誤，會列出可能的解決方案。

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

此錯誤表示找不到適用於 CLI 的 Docker 用戶端。 如此一來，不會執行下列額外的檢查： 尋找 Docker 版本，評估 Docker 精靈的狀態，並執行 Docker pull 命令。

*可能的解決方案*:安裝 Docker 用戶端;將 Docker 路徑新增至 系統變數。

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

此錯誤表示 Docker daemon 狀態無法使用，或，它無法使用 CLI。 如此一來，Docker 作業 (例如`docker login`和`docker pull`) 無法透過 CLI 使用。

*可能的解決方案*:重新啟動 Docker 精靈，或驗證正確安裝。

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

此錯誤表示 CLI 程式無法執行命令`docker --version`。

*可能的解決方案*:請嘗試以手動方式執行命令，請確定您擁有最新的 CLI 版本，並調查的錯誤訊息。

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

此錯誤表示 CLI 無法範例映像提取至您的環境。

*可能的解決方案*:驗證提取映像所需的所有元件皆正常都執行。

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

此錯誤表示找不到該 Helm 用戶端，cli，防止其他 Helm 作業。

*可能的解決方案*:請確認 Helm 用戶端已安裝的而且它的路徑加入系統環境變數。

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

此錯誤表示 CLI 程式無法判斷安裝的 Helm 版本。 就可能發生此 Azure CLI 版本 (或如果 Helm 版本) 正在使用已過時。

*可能的解決方案*:更新為最新的 Azure CLI 版本，或以建議的 Helm 版本;以手動方式執行命令，並調查的錯誤訊息。

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

此錯誤表示指定的登錄登入伺服器的 DNS 的 ping，但沒有回應，這表示它是無法使用。 這表示某些連線問題。 或者，登錄可能已不存在，使用者可能沒有權限在登錄中 （若要正確地擷取其登入伺服器），或目標登錄是在不同的雲端，Azure CLI 中使用。

*可能的解決方案*:驗證連線;確認登錄中的拼字正確，且該登錄存在;請確認使用者具有權限，在其上和登錄的雲端是使用 Azure CLI 中的相同。

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

此錯誤表示指定的登錄的挑戰端點回應 403 禁止 HTTP 狀態。 此錯誤表示，使用者不必存取登錄中，最有可能因為虛擬網路組態。

*可能的解決方案*:移除虛擬網路規則，或目前的用戶端 IP 位址加入允許清單。

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

此錯誤表示目標登錄的挑戰端點未發出一項挑戰。

*可能的解決方案*:一段時間後再試。 如果錯誤持續發生，請開啟發生問題 https://aka.ms/acr/issues 。

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

此錯誤表示目標登錄的挑戰端點發出一項挑戰，但是登錄不支援 Azure Active Directory 驗證。

*可能的解決方案*:請嘗試不同的方法來進行驗證，例如，使用系統管理員認證。 如果使用者需要使用 Azure Active Directory 進行驗證，請開啟發生問題 https://aka.ms/acr/issues 。

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

此錯誤表示，登錄登入伺服器未回應以重新整理權杖，因此目標登錄的存取被拒。 如果使用者沒有登錄的適當權限，或使用者認證，Azure cli 已過時，可能會發生此錯誤。

*可能的解決方案*:確認使用者是否有登錄; 上正確的權限執行`az login`重新整理權限、 權杖和認證。

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

此錯誤表示登錄登入伺服器未使用存取權杖，回應，讓目標登錄存取被拒。 如果使用者沒有登錄的適當權限，或使用者認證，Azure cli 已過時，可能會發生此錯誤。

*可能的解決方案*:確認使用者是否有登錄; 上正確的權限執行`az login`重新整理權限、 權杖和認證。

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

此錯誤表示 CLI 找不到指定的登錄中的登入伺服器，沒有預設值後置詞找不到目前的雲端。 如果登錄不存在，如果使用者沒有正確的權限在登錄中，如果登錄的雲端和目前的 Azure CLI 雲端不相符，或 Azure CLI 版本已過時，可能會發生此錯誤。

*可能的解決方案*:檢查拼字正確，以及登錄存在;請確認該使用者在登錄中，具有權限，以及登錄和 CLI 環境的雲端相符;更新至最新版的 Azure CLI。

## <a name="next-steps"></a>後續步驟

若要檢查的登錄庫健康情況的選項，請參閱[檢查 Azure container registry 的健全狀況](container-registry-check-health.md)。

請參閱[常見問題集](container-registry-faq.md)常見問題集和其他關於 Azure Container Registry 的已知的問題。





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
