---
title: 如何在使用 Azure 開發人員空間時管理祕密
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: 在 Azure 上使用容器和微服務快速進行 Kubernetes 開發
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 容器
ms.openlocfilehash: b184f72dfbbfe093443ab8a9b79bafbece3a3d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790182"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>如何在使用 Azure 開發人員空間時管理祕密

您的服務可能需要特定密碼、連接字串和其他祕密，例如資料庫或其他安全的 Azure 服務。 藉由在組態檔中設定這些祕密的值，您可以使用這些值作為程式碼中的環境變數。  請務必小心處理，以避免洩漏祕密的安全性。

Azure Dev Spaces 提供兩個建議、簡化的選項，將秘密儲存在由 Azure Dev Spaces 用戶端工具所產生的 Helm 圖表中：在 `values.dev.yaml` 檔案中，並直接內嵌在 `azds.yaml`中。 不建議將秘密儲存在 `values.yaml`中。 在這篇文章中定義的用戶端工具所產生的兩個方法中，如果您建立自己的 Helm 圖表，則可以直接使用 Helm 圖表來管理和儲存秘密。

## <a name="method-1-valuesdevyaml"></a>方法 1：values.dev.yaml
1. 透過可使用 Azure 開發人員空間的專案來開啟 VS Code。
2. 在與現有_azds_相同的資料夾中，新增名為_yaml_的檔案，並定義您的秘密金鑰和值，如下列範例所示：

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds. yaml_已經參考_yaml_檔案（如果存在的話）。 如果您偏好使用不同的檔案名，請更新 [install] （安裝值）區段：

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. 修改您的服務程式碼，以便將這些祕密當作環境變數來參考，如下列範例所示：

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. 以這些變更來更新叢集中執行的服務。 在命令列上執行命令：

    ```
    azds up
    ```
 
6. (選擇性) 從命令列中，確認這些祕密已建立：

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. 請確認您已將 _values.dev.yaml_ 新增至 _.gitignore_ 檔案，以防止從原始檔控制中認可祕密。
 
 
## <a name="method-2-azdsyaml"></a>方法2： azds. yaml
1.  在 _azds.yaml_ 中，從 yaml 的 configurations/develop/install 區段下設定祕密。 雖然您可以直接在該處輸入祕密值，但不建議您這樣做，因為 _azds.yaml_ 會簽入原始檔控制。 相反地，請使用 "$PLACEHOLDER" 語法來新增預留位置。

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT"
                host: "$REDIS_HOST"
                key: "$REDIS_KEY"
    ```
     
2.  在 _azds.yaml_ 所在的資料夾中建立 _.env_ 檔案。 使標準的「索引鍵=值」標記法來輸入祕密。 請不要將 _.env_ 檔案認可至原始檔控制。 （若要從 git 型版本控制系統中的原始檔控制中省略，請將它新增至 _.gitignore_檔案。）下列範例會顯示一個_env_檔案：

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  修改您服務的原始程式碼，以在程式碼中參考這些祕密，如下列範例所示：

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  以這些變更來更新叢集中執行的服務。 在命令列上執行命令：

    ```
    azds up
    ```

4.  (選擇性) 從 kubectl 檢視祕密：

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="passing-secrets-as-build-arguments"></a>將秘密傳遞為組建引數

先前的章節示範如何傳遞秘密，以在容器執行時間使用。 您也可以使用 `azds.yaml`，在容器組建時間（例如私人 NuGet 的密碼）傳遞秘密。

在 `azds.yaml`中，使用 `<variable name>: ${secret.<secret name>.<secret key>}` 語法，在 configuration *. build. args*中設定組建時間秘密。 例如：

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

在上述範例中， *mynugetsecret*是現有的秘密，而*pattoken*是現有的金鑰。

>[!NOTE]
> 密碼名稱和金鑰可能包含 `.` 字元。 當傳遞秘密做為組建引數時，請使用 `\` 來將 `.` escape。 例如，若要傳遞名為*foo*的秘密與*token*的金鑰： `MYTOKEN: ${secret.foo\.bar.token}`。 此外，也可以使用前置詞和後置文字來評估秘密。 例如： `MYURL: eus-${secret.foo\.bar.token}-version1` 。 此外，父系和祖系空間中可用的秘密可以做為組建引數傳遞。

在您的 Dockerfile 中，使用*ARG*指示詞來取用密碼，然後在 Dockerfile 中稍後使用該相同的變數。 例如：

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

以這些變更來更新叢集中執行的服務。 在命令列上執行命令：

```
azds up
```

## <a name="next-steps"></a>後續步驟

現在，您可以透過這些方法安全地連線到資料庫、Azure Cache for Redis 或存取安全的 Azure 服務。
 
