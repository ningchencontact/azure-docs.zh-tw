---
title: 如何在 Azure Dev Spaces 中使用私用 Helm 存放庫
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: 使用 Azure 開發人員空間中的私用 Helm 存放庫。
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 容器, Helm
manager: gwallace
ms.openlocfilehash: d0f2cb739a502d614ac329eef1fc57b2fb15cb38
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014512"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>在 Azure Dev Spaces 中使用私用 Helm 存放庫

[Helm][helm]是 Kuberentes 的套件管理員。 Helm 會使用[圖表][helm-chart]格式來封裝相依性。 Helm 圖表儲存在可以是公用或私用的存放庫中。 Azure Dev Spaces 只會在執行您的應用程式時, 從公用存放庫中抓取 Helm 的圖表。 如果 Helm 存放庫是私用或 Azure Dev Spaces 無法存取它, 您可以將圖表從該存放庫直接新增至您的應用程式。 直接新增圖表可讓 Azure Dev Spaces 執行您的應用程式, 而不需要存取私用 Helm 存放庫。

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>將私用 Helm 存放庫新增至本機電腦

使用[helm][helm-repo-add]儲存機制新增和 helm 存放庫[更新][helm-repo-update], 從您的本機電腦存取私人 helm 儲存機制。

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>將圖表新增至您的應用程式

流覽至您專案的目錄, 然後`azds prep`執行。

```cmd
azds prep --public
```

在應用程式的圖表目錄中, 使用您的圖表建立[yaml][helm-requirements]檔案。 例如, 如果您的應用程式命名為*app1*, 您會建立*圖表/app1/需求。 yaml*。

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

流覽至您應用程式的 [圖表] 目錄, 並使用 helm 相依性[更新][helm-dependency-update]來更新應用程式的 helm 相依性, 並從私人存放庫下載圖表。

```cmd
helm dependency update
```

確認具有*tgz*檔案的*圖表*子目錄已新增至應用程式的圖表目錄。 例如,*圖表/app1/圖表/mychart-0.1.0. tgz*。

您的私用 Helm 存放庫中的圖表已下載並新增至您的專案。 移除*yaml*檔案, 讓 Dev Spaces 不會嘗試更新此相依性。

## <a name="run-your-application"></a>執行您的應用程式

流覽至專案的根目錄並執行`azds up` , 以確認您的應用程式在您的開發人員空間中成功執行。

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>後續步驟

深入瞭解[Helm 及其運作方式][helm]。

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/developing_charts/
[helm-dependency-update]: https://helm.sh/docs/helm/#helm-dependency-update
[helm-repo-add]: https://helm.sh/docs/helm/#helm-repo-add
[helm-repo-update]: https://helm.sh/docs/helm/#helm-repo-update
[helm-requirements]: https://helm.sh/docs/developing_charts/#chart-dependencies