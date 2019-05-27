---
title: 如何在使用 Azure 開發人員空間時管理祕密
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: 在 Azure 上使用容器和微服務快速進行 Kubernetes 開發
keywords: Docker、Kubernetes、Azure、AKS、Azure Container Service、容器
ms.openlocfilehash: 8ee50289083b12b7b2abd3b9ece2c8de345df9fe
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851423"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>如何在使用 Azure 開發人員空間時管理祕密

您的服務可能需要特定密碼、連接字串和其他祕密，例如資料庫或其他安全的 Azure 服務。 藉由在組態檔中設定這些祕密的值，您可以使用這些值作為程式碼中的環境變數。  請務必小心處理，以避免洩漏祕密的安全性。

Azure 開發人員空間提供兩個儲存秘密的建議選項：儲存在 values.dev.yaml 檔案中和直接內嵌在 azds.yaml 中。 不建議將祕密儲存在 values.yaml 中。
 
## <a name="method-1-valuesdevyaml"></a>方法 1：values.dev.yaml
1. 透過可使用 Azure 開發人員空間的專案來開啟 VS Code。
2. 新增名為的檔案_values.dev.yaml_現有的相同資料夾中_azds.yaml_並定義您的祕密金鑰和值，如下列範例所示：

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds.yaml_已經參考_values.dev.yaml_檔案若有的話。 如果您偏好不同的檔案名稱，更新 install.values 區段：

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
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>方法 2：直接內嵌在 azds.yaml 中
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
     
2.  在 _azds.yaml_ 所在的資料夾中建立 _.env_ 檔案。 使標準的「索引鍵=值」標記法來輸入祕密。 請不要將 _.env_ 檔案認可至原始檔控制。 (若要從 git 版控制系統的原始檔控制中移除，請將該檔案新增至 _.gitignore_ 檔案。)_.env_ 檔案如下列範例所示：

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

## <a name="next-steps"></a>後續步驟

現在，您可以透過這些方法安全地連線到資料庫、Azure Cache for Redis 或存取安全的 Azure 服務。
 
