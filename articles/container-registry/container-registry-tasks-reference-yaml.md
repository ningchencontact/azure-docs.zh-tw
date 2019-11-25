---
title: YAML reference - ACR Tasks
description: 適用於以 YAML 為「ACR 工作」定義工作的參考，包括工作屬性、步驟類型、步驟屬性及內建變數。
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: a27f55d08a7ed5d7bf3360030eabefc4b7720b82
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454643"
---
# <a name="acr-tasks-reference-yaml"></a>ACR 工作參考：YAML

「ACR 工作」中的多步驟工作定義提供一個著重於建置、測試及修補容器並以容器為中心的計算原始物件。 This article covers the commands, parameters, properties, and syntax for the YAML files that define your multi-step tasks.

此文章包含適用於為「ACR 工作」建立多步驟工作 YAML 檔案的參考。 如果您想要「ACR 工作」的簡介，請參閱 [ACR 工作概觀](container-registry-tasks-overview.md)。

## <a name="acr-taskyaml-file-format"></a>acr-task.yaml 檔案格式

「ACR 工作」支援採用標準 YAML 語法的多步驟工作宣告。 You define a task's steps in a YAML file. You can then run the task manually by passing the file to the [az acr run][az-acr-run] command. Or, use the file to create a task with [az acr task create][az-acr-task-create] that's triggered automatically on a Git commit or base image update. 雖然此文章以 `acr-task.yaml` 作為包含步驟的檔案，但「ACR 工作」支援任何使用[支援的副檔名](#supported-task-filename-extensions)的有效檔案名稱。

最上層 `acr-task.yaml` 原始物件是**工作屬性** **步驟類型** 及 **步驟屬性**：

* [工作屬性](#task-properties)會套用至整個工作執行的所有步驟。 There are several global task properties, including:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [工作步驟類型](#task-step-types)代表可在工作中執行的動作類型。 步驟類型有三種：
  * `build`
  * `push`
  * `cmd`
* [工作步驟屬性](#task-step-properties)是套用至個別步驟的參數。 步驟屬性有數個，包括：
  * `startDelay`
  * `timeout`
  * `when`
  * ...等等。

接下來是 `acr-task.yaml` 檔案的基底格式 (包括一些常見的步驟屬性)。 雖然這並未完整呈現所有可用的步驟屬性或步驟類型使用方式，但提供一個基本檔案格式的快速概觀。

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>支援的工作副檔名

「ACR 工作」保留了數個將視為工作檔案來處理的副檔名，包括 `.yaml`。 所有「未」包含在下列清單中的副檔名都會被「ACR 工作」視為 Dockerfile：.yaml、.yml、.toml、.json、.sh、.bash、.zsh、.ps1、.ps、.cmd、.bat、.ts、.js、.php、.py、.rb、.lua

YAML 是「ACR 工作」目前唯一支援的檔案格式。 其他副檔名則保留供可能的未來支援使用。

## <a name="run-the-sample-tasks"></a>執行範例工作

此文章接下來的各節中會參考數個範例工作檔案。 The sample tasks are in a public GitHub repository, [Azure-Samples/acr-tasks][acr-tasks]. You can run them with the Azure CLI command [az acr run][az-acr-run]. 範例命令會類似於：

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

範例命令的格式會假設您已在 Azure CLI 中設定預設登錄，因此它們會省略 `--registry` 參數。 To configure a default registry, use the [az configure][az-configure] command with the `--defaults` parameter, which accepts an `acr=REGISTRY_NAME` value.

例如，為 Azure CLI 設定一個名為 "myregistry" 的預設登錄：

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>工作屬性

Task properties typically appear at the top of an `acr-task.yaml` file, and are global properties that apply throughout the full execution of the task steps. 這些全域屬性中有些可在個別步驟中被覆寫。

| 屬性 | Type | 選用 | 描述 | 支援覆寫 | 預設值 |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | 是 | 「ACR 工作」服務所剖析的 `acr-task.yaml` 檔案版本。 在「ACR 工作」努力維持回溯相容性的同時，此值則可讓「ACR 工作」在已定義的版本內維持相容性。 If unspecified, defaults to the latest version. | 否 | None |
| `stepTimeout` | 整數 (秒) | 是 | 步驟的可執行秒數上限。 If the property is specified on a task, it sets the default `timeout` property of all the steps. If the `timeout` property is specified on a step, it overrides the property provided by the task. | 是 | 600 (10 分鐘) |
| `workingDirectory` | string | 是 | The working directory of the container during runtime. If the property is specified on a task, it sets the default `workingDirectory` property of all the steps. If specified on a step, it overrides the property provided by the task. | 是 | `$HOME` |
| `env` | [字串, 字串, ...] | 是 |  Array of strings in `key=value` format that define the environment variables for the task. If the property is specified on a task, it sets the default `env` property of all the steps. If specified on a step, it overrides any environment variables inherited from the task. | None |
| `secrets` | [secret, secret, ...] | 是 | Array of [secret](#secret) objects. | None |
| `networks` | [network, network, ...] | 是 | Array of [network](#network) objects. | None |

### <a name="secret"></a>secret

The secret object has the following properties.

| 屬性 | Type | 選用 | 描述 | 預設值 |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | 否 | The identifier of the secret. | None |
| `keyvault` | string | 是 | The Azure Key Vault Secret URL. | None |
| `clientID` | string | 是 | The client ID of the [user-assigned managed identity](container-registry-tasks-authentication-managed-identity.md) for Azure resources. | None |

### <a name="network"></a>網路

The network object has the following properties.

| 屬性 | Type | 選用 | 描述 | 預設值 |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | 否 | 網路的名稱。 | None |
| `driver` | string | 是 | The driver to manage the network. | None |
| `ipv6` | bool | 是 | Whether IPv6 networking is enabled. | `false` |
| `skipCreation` | bool | 是 | Whether to skip network creation. | `false` |
| `isDefault` | bool | 是 | Whether the network is a default network provided with Azure Container Registry | `false` |

## <a name="task-step-types"></a>工作步驟類型

「ACR 工作」支援三種步驟類型。 每一種步驟類型都支援數個屬性，詳述於每一種步驟類型的小節中。

| 步驟類型 | 描述 |
| --------- | ----------- |
| [`build`](#build) | 使用熟悉的 `docker build` 語法來建置容器映像。 |
| [`push`](#push) | 執行新建置或重新標記之映像的 `docker push` 以推送至容器登錄。 支援 Azure Container Registry、其他私人登錄，以及公用 Docker Hub。 |
| [`cmd`](#cmd) | 以命令方式執行容器，並將參數傳遞給容器的 `[ENTRYPOINT]`。 The `cmd` step type supports parameters like `env`, `detach`, and other familiar `docker run` command options, enabling unit and functional testing with concurrent container execution. |

## <a name="build"></a>build

建置容器映像。 `build` 步驟類型代表一種在雲端中以第一級原始物件形式執行 `docker build` 的多租用戶、安全方法。

### <a name="syntax-build"></a>語法：build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

`build` 步驟類型支援下表中的參數。 `build` 步驟類型也支援 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令的所有建置選項，例如以 `--build-arg` 設定建置時間變數。

| 參數 | 描述 | 選用 |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | 定義所建置映像的完整 `image:tag`。<br /><br />由於映像可能會用於內部工作驗證 (例如功能測試)，因此並非所有映像都需要 `push` 來推送至登錄。 不過，若要在工作執行內將某個映像執行個體化，則該映像確實需要一個可供參考的名稱。<br /><br />Unlike `az acr build`, running ACR Tasks doesn't provide default push behavior. 使用「ACR 工作」時，預設案例會能夠建置、驗證，然後推送映像。 如需了解如何視需要推送所建置的映像，請參閱 [push](#push)。 | 是 |
| `-f` &#124; `--file` | 指定傳遞給 `docker build` 的 Dockerfile。 如果未指定，則會假設使用內容根目錄中的預設 Dockerfile。 To specify a Dockerfile, pass the filename relative to the root of the context. | 是 |
| `context` | 傳遞給 `docker build` 的根目錄。 每個工作的根目錄都會設定為共用的 [workingDirectory](#task-step-properties)，並且包含相關 Git 複製目錄的根目錄。 | 否 |

### <a name="properties-build"></a>屬性：build

`build` 步驟類型支援下列屬性。 Find details of these properties in the [Task step properties](#task-step-properties) section of this article.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | 選用 |
| `disableWorkingDirectoryOverride` | bool | 選用 |
| `entryPoint` | string | 選用 |
| `env` | [字串, 字串, ...] | 選用 |
| `expose` | [字串, 字串, ...] | 選用 |
| `id` | string | 選用 |
| `ignoreErrors` | bool | 選用 |
| `isolation` | string | 選用 |
| `keep` | bool | 選用 |
| `network` | object | 選用 |
| `ports` | [字串, 字串, ...] | 選用 |
| `pull` | bool | 選用 |
| `repeat` | int | 選用 |
| `retries` | int | 選用 |
| `retryDelay` | 整數 (秒) | 選用 |
| `secret` | object | 選用 |
| `startDelay` | 整數 (秒) | 選用 |
| `timeout` | 整數 (秒) | 選用 |
| `when` | [字串, 字串, ...] | 選用 |
| `workingDirectory` | string | 選用 |

### <a name="examples-build"></a>範例：build

#### <a name="build-image---context-in-root"></a>建置映像 - 根目錄中的內容

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>建置映像 - 子目錄中的內容

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

將一或多個已建置或重新標記的映像推送至登錄。 支援推送至私人登錄 (例如 Azure Container Registry) 或公用 Docker Hub。

### <a name="syntax-push"></a>語法：push

`push` 步驟類型支援映像集合。 YAML 集合語法支援內嵌和巢狀格式。 推送單一映像通常是以內嵌語法表示：

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

為了提升可讀性，推送多個映像時，請使用巢狀語法：

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>屬性：push

`push` 步驟類型支援下列屬性。 Find details of these properties in the [Task step properties](#task-step-properties) section of this article.

| | | |
| -------- | ---- | -------- |
| `env` | [字串, 字串, ...] | 選用 |
| `id` | string | 選用 |
| `ignoreErrors` | bool | 選用 |
| `startDelay` | 整數 (秒) | 選用 |
| `timeout` | 整數 (秒) | 選用 |
| `when` | [字串, 字串, ...] | 選用 |

### <a name="examples-push"></a>範例：push

#### <a name="push-multiple-images"></a>推送多個映像

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>建置、推送及執行

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

`cmd` 步驟類型會執行容器。

### <a name="syntax-cmd"></a>語法：cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>屬性：cmd

`cmd` 步驟類型支援下列屬性：

| | | |
| -------- | ---- | -------- |
| `detach` | bool | 選用 |
| `disableWorkingDirectoryOverride` | bool | 選用 |
| `entryPoint` | string | 選用 |
| `env` | [字串, 字串, ...] | 選用 |
| `expose` | [字串, 字串, ...] | 選用 |
| `id` | string | 選用 |
| `ignoreErrors` | bool | 選用 |
| `isolation` | string | 選用 |
| `keep` | bool | 選用 |
| `network` | object | 選用 |
| `ports` | [字串, 字串, ...] | 選用 |
| `pull` | bool | 選用 |
| `repeat` | int | 選用 |
| `retries` | int | 選用 |
| `retryDelay` | 整數 (秒) | 選用 |
| `secret` | object | 選用 |
| `startDelay` | 整數 (秒) | 選用 |
| `timeout` | 整數 (秒) | 選用 |
| `when` | [字串, 字串, ...] | 選用 |
| `workingDirectory` | string | 選用 |

您可以在此文章的[工作步驟屬性](#task-step-properties)一節中，找到這些屬性的詳細資料。

### <a name="examples-cmd"></a>範例：cmd

#### <a name="run-hello-world-image"></a>執行 hello-world 映像

此命令會執行 `hello-world.yaml` 工作檔案以參考 Docker Hub 上的 [hello-world](https://hub.docker.com/_/hello-world/) 映像。

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>執行 bash 映像並回應 "hello world"

此命令會執行 `bash-echo.yaml` 工作檔案以參考 Docker Hub 上的 [bash](https://hub.docker.com/_/bash/) 映像。

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>執行特定 bash 映像標記

若要執行特定的映像版本，請在 `cmd`中指定標記。

此命令會執行 `bash-echo-3.yaml` 工作檔案以參考 Docker Hub 上的 [bash:3.0](https://hub.docker.com/_/bash/) 映像。

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>執行自訂映像

`cmd` 步驟類型會使用標準 `docker run` 格式來參考映像。 映像的開頭如果不是登錄，系統會假設其產生自 docker.io。 上述範例可同樣表示為：

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

By using the standard `docker run` image reference convention, `cmd` can run images from any private registry or the public Docker Hub. 如果您要參考「ACR 工作」執行所在之相同登錄中的映像，則不需要指定任何登錄認證。

* Run an image that's from an Azure container registry. The following example assumes you have a registry named `myregistry`, and a custom image `myimage:mytag`.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalize the registry reference with a Run variable or alias

    Instead of hard-coding your registry name in an `acr-task.yaml` file, you can make it more portable by using a [Run variable](#run-variables) or [alias](#aliases). The `Run.Registry` variable or `$Registry` alias expands at runtime to the name of the registry in which the task is executing.

    For example, to generalize the preceding task so that it works in any Azure container registry, reference the $Registry variable in the image name:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>工作步驟屬性

每個步驟類型都支援數個適用於其類型的屬性。 下表定義所有可用的步驟屬性。 並非所有步驟類型都支援所有屬性。 若要查看每個步驟類型可使用這當中哪些屬性，請參閱 [cmd](#cmd)、[build](#build)及 [push](#push) 步驟類型參考小節。

| 屬性 | Type | 選用 | 描述 | 預設值 |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | 是 | 執行時是否應將容器中斷連結。 | `false` |
| `disableWorkingDirectoryOverride` | bool | 是 | Whether to disable `workingDirectory` override functionality. Use this in combination with `workingDirectory` to have complete control over the container's working directory. | `false` |
| `entryPoint` | string | 是 | 覆寫步驟容器的 `[ENTRYPOINT]`。 | None |
| `env` | [字串, 字串, ...] | 是 | `key=value` 格式的字串陣列，用來定義步驟的環境變數。 | None |
| `expose` | [字串, 字串, ...] | 是 | Array of ports that are exposed from the container. |  None |
| [`id`](#example-id) | string | 是 | 可唯一識別工作內的步驟。 工作內的其他步驟可以參考步驟的 `id`，例如使用 `when`進行相依性檢查。<br /><br />`id` 同時也是執行中容器的名稱。 舉例來說，在工作內其他容器中執行的程序可以參考 `id` 作為其 DNS 主機名稱，或藉由 docker logs [id] 來存取它。 | `acb_step_%d`, where `%d` is the 0-based index of the step top-down in the YAML file |
| `ignoreErrors` | bool | 是 | Whether to mark the step as successful regardless of whether an error occurred during container execution. | `false` |
| `isolation` | string | 是 | The isolation level of the container. | `default` |
| `keep` | bool | 是 | 在執行後是否應保留步驟的容器。 | `false` |
| `network` | object | 是 | Identifies a network in which the container runs. | None |
| `ports` | [字串, 字串, ...] | 是 | Array of ports that are published from the container to the host. |  None |
| `pull` | bool | 是 | Whether to force a pull of the container before executing it to prevent any caching behavior. | `false` |
| `privileged` | bool | 是 | Whether to run the container in privileged mode. | `false` |
| `repeat` | int | 是 | The number of retries to repeat the execution of a container. | 0 |
| `retries` | int | 是 | The number of retries to attempt if a container fails its execution. A retry is only attempted if a container's exit code is non-zero. | 0 |
| `retryDelay` | 整數 (秒) | 是 | The delay in seconds between retries of a container's execution. | 0 |
| `secret` | object | 是 | Identifies an Azure Key Vault secret or [managed identity for Azure resources](container-registry-tasks-authentication-managed-identity.md). | None |
| `startDelay` | 整數 (秒) | 是 | Number of seconds to delay a container's execution. | 0 |
| `timeout` | 整數 (秒) | 是 | 終止步驟前可允許步驟執行的秒數上限。 | 600 |
| [`when`](#example-when) | [字串, 字串, ...] | 是 | 設定步驟與工作內一或多個其他步驟的相依性。 | None |
| `user` | string | 是 | The user name or UID of a container | None |
| `workingDirectory` | string | 是 | 設定步驟的工作目錄。 「ACR 工作」預設會建立根目錄作為工作目錄。 不過，如果您的組建含有數個步驟，則可藉由指定相同的工作目錄，讓較前面的步驟與較後面的步驟共用成品。 | `$HOME` |

### <a name="examples-task-step-properties"></a>範例：工作步驟屬性

#### <a name="example-id"></a>範例：id

建置兩個映像，將功能測試映像執行個體化。 每個步驟都會由唯一 `id` 識別，工作中的其他步驟會在其 `when` 屬性中參考此 id。

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>範例：when

`when` 屬性會指定步驟與工作內其他步驟的相依性。 它支援兩個參數值：

* `when: ["-"]` - 表示與其他步驟沒有相依性。 指定 `when: ["-"]` 的步驟將立即開始執行，並可啟用並行步驟執行。
* `when: ["id1", "id2"]` - 表示步驟與 `id` 為 "id1" 和 `id` 為 "id2" 的步驟相依。 此步驟必須等到 "id1" 和 "id2" 步驟都完成後，才會執行。

如果步驟中未指定 `when`，則該步驟會取決於 `acr-task.yaml` 檔案中的前一個步驟是否完成。

不含 `when` 的循序步驟執行：

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

含有 `when` 的循序步驟執行：

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Parallel images build:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

平行映像建置和相依測試：

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Run 變數

「ACR 工作」包含一組預設變數，可供工作步驟在執行時使用。 使用 `{{.Run.VariableName}}` 格式即可存取這些變數，其中 `VariableName` 是下列其中之一：

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

The variable names are generally self-explanatory. Details follows for commonly used variables. As of YAML version `v1.1.0`, you can use an abbreviated, predefined [task alias](#aliases) in place of most run variables. For example, in place of `{{.Run.Registry}}`, use the `$Registry` alias.

### <a name="runid"></a>Run.ID

Each Run, through `az acr run`, or trigger based execution of tasks created through `az acr task create`, has a unique ID. 此識別碼代表目前正在執行的「執行」。

通常用來唯一標記某個映像：

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

登錄的完整伺服器名稱。 通常用來一般參考工作執行所在的登錄。

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run.RegistryName

The name of the container registry. Typically used in task steps that don't require a fully qualified server name, for example, `cmd` steps that run Azure CLI commands on registries.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

執行開始的目前 UTC 時間。

### <a name="runcommit"></a>Run.Commit

For a task triggered by a commit to a GitHub repository, the commit identifier.

### <a name="runbranch"></a>Run.Branch

For a task triggered by a commit to a GitHub repository, the branch name.

## <a name="aliases"></a>別名

As of `v1.1.0`, ACR Tasks supports aliases that are available to task steps when they execute. Aliases are similar in concept to aliases (command shortcuts) supported in bash and some other command shells. 

With an alias, you can launch any command or group of commands (including options and filenames) by entering a single word.

ACR Tasks supports several predefined aliases and also custom aliases you create.

### <a name="predefined-aliases"></a>Predefined aliases

The following task aliases are available to use in place of [run variables](#run-variables):

| Alias | Run variable |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

In task steps, precede an alias with the `$` directive, as in this example:

```yaml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Image aliases

Each of the following aliases points to a stable image in Microsoft Container Registry (MCR). You can refer to each of them in the `cmd` section of a Task file without using a directive.

| Alias | 影像 |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:d0725bc` |
| `bash` | `mcr.microsoft.com/acr/bash:d0725bc` |
| `curl` | `mcr.microsoft.com/acr/curl:d0725bc` |

The following example task uses several aliases to [purge](container-registry-auto-purge.md) image tags older than 7 days in the repo `samples/hello-world` in the run registry:

```yaml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Custom alias

Define a custom alias in your YAML file and use it as shown in the following example. An alias can contain only alphanumeric characters. The default directive to expand an alias is the `$` character.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

You can link to a remote or local YAML file for custom alias definitions. The following example links to a YAML file in Azure blob storage:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>後續步驟

如需多步驟工作的概觀，請參閱[執行 ACR 工作中的多步驟建置、測試及修補工作](container-registry-tasks-multi-step.md)。

如需了解單一步驟建置，請參閱 [ACR 工作概觀](container-registry-tasks-overview.md)。



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
