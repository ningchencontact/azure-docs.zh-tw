---
title: 如何在 Kubernetes 上安裝 IoT Edge |Microsoft Docs
description: 了解如何使用本機開發叢集環境中的 Kubernetes 上安裝 IoT Edge 上的
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 66aca7be9a2df93d846d7e78bc64c93279afc2d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160691"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>如何安裝 IoT Edge 上 Kubernetes （預覽）

IoT Edge 可以與使用它作為復原、 高可用性的基礎結構層的 Kubernetes 整合。 它會註冊 IoT Edge*自訂資源定義*(CRD) 與 Kubernetes API 伺服器。 此外，還提供*運算子*（IoT Edge 代理程式），來調解雲端管理本機叢集狀態的預期的狀態。 

模組的存留期管理 Kubernetes 排程器，它會維護模組可用性，並選擇其位置。 IoT Edge 管理 edge 應用程式平台上，執行持續重新調整所需的狀態指定 IoT 中樞在 edge 叢集上的狀態。 Edge 應用程式模型仍熟悉 IoT Edge 模組與路由為基礎的模型。 執行 IoT Edge 代理程式操作員*自動*轉譯至 Kubernetes 原生建構 pod、 部署、 服務等等。

以下是高階架構圖表：

![kubernetes arch](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Edge 部署的每個元件的範圍限定到特定裝置，讓您可以共用相同的叢集資源，在多個邊緣裝置和其部署至 Kubernetes 命名空間。

>[!NOTE]
>在 Kubernetes 上的 IoT Edge 處於[公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="install-locally-for-a-quick-test-environment"></a>快速的測試環境的本機安裝

### <a name="prerequisites"></a>必要條件

* Kubernetes 1.10 或更新版本。 如果您沒有現有的叢集設定，您可以使用[Minikube](https://kubernetes.io/docs/setup/minikube/)針對本機叢集環境。 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide)，Kubernetes 的套件管理員。

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)與叢集互動。

### <a name="setup-steps"></a>設定步驟

1. 啟動**Minikube**

    ``` shell
    minikube start
    ```

1. 初始化**Helm**伺服器元件 (*tiller*) 在叢集中

    ``` shell
    helm init
    ```

1. 新增 IoT Edge 存放庫，並更新 helm 安裝

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [建立 IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)，[註冊 IoT Edge 裝置](how-to-register-device-portal.md)，並記下其連接字串。

1. 將 iotedged 和 IoT Edge 代理程式安裝到您的叢集

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. 在瀏覽器中開啟 Kubernetes 儀表板

    ```shell
    minikube dashboard
    ```

    在叢集中的命名空間，您會看到一個 IoT Edge 裝置，依照慣例*msiot-\<iothub 名稱 >-\<edgedevice 名稱 >*。 這個命名空間中應該啟動並執行 IoT Edge 代理程式 」 和 「 iotedged pod。

1. 新增模擬的溫度感應器模組中的步驟[部署模組](quickstart-linux.md#deploy-a-module)快速入門 區段。 IoT Edge 模組管理是完成從 IoT 中樞入口網站，就像任何其他 IoT Edge 裝置。 不建議對 Kubernetes 工具透過模組設定中的本機變更，因為它們可能會覆寫。

1. 幾秒鐘後，重新整理**Pod**邊緣裝置命名空間下的儀表板中的頁面會列出 IoT Edge 中樞，並為執行 IoT Edge 中樞的模擬感應器 pod pod 內嵌資料至 IoT 中樞。

## <a name="clean-up-resources"></a>清除資源

若要移除所有 edge 部署所建立的資源，請使用下列命令上一節的步驟 5 中所使用的名稱。

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>後續步驟

### <a name="deploy-as-a-highly-available-edge-gateway"></a>部署為高可用性的 edge 閘道 

在 Kubernetes 叢集中的 edge 裝置可以作為下游裝置的 IoT 閘道器。 它可以設定為從因此能提供高可用性以 edge 部署的節點失敗中恢復。 請參閱此[詳細的逐步解說](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes)在此案例中使用 IoT Edge。