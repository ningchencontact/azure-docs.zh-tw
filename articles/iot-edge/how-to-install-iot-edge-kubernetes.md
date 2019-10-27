---
title: 如何在 Kubernetes 上安裝 IoT Edge |Microsoft Docs
description: 瞭解如何使用本機開發叢集環境，在 Kubernetes 上安裝 IoT Edge
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a453779ffe4ae20acf55510d0ac9f9483763af21
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964825"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>如何在 Kubernetes 上安裝 IoT Edge （預覽）

IoT Edge 可以使用它來與 Kubernetes 整合，以作為彈性、高可用性的基礎結構層。 它會向 Kubernetes API 伺服器註冊 IoT Edge 的*自訂資源定義*（.crd）。 此外，它還提供一個*操作員*（IoT Edge 代理程式），以協調雲端管理的預期狀態與本機叢集狀態。 

模組存留期是由 Kubernetes 排程器所管理，此排程器會維護模組可用性並選擇其位置。 IoT Edge 會管理在最上層執行的邊緣應用程式平臺，並持續協調 IoT 中樞中指定的所需狀態與邊緣叢集上的狀態。 Edge 應用程式模型仍然是以 IoT Edge 模組和路由為基礎的熟悉模型。 IoT Edge agent 操作員會執行*自動*轉譯至 Kubernetes 原生結構（例如 pod、部署、服務等）。

以下是高階架構圖：

![kubernetes 的架構](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Edge 部署的每個元件都是以裝置特定的 Kubernetes 命名空間為範圍，因此可以在多個邊緣裝置及其部署之間共用相同的叢集資源。

>[!NOTE]
>Kubernetes 上的 IoT Edge 處於[公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)狀態。

## <a name="install-locally-for-a-quick-test-environment"></a>在本機安裝以進行快速測試環境

### <a name="prerequisites"></a>必要條件

* Kubernetes 1.10 或更新版本。 如果您沒有現有的叢集設定，可以針對本機叢集環境使用[Minikube](https://kubernetes.io/docs/setup/minikube/) 。 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide)，Kubernetes 套件管理員。

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) ，用於與叢集進行互動和互動。

### <a name="setup-steps"></a>設定步驟

1. 開始**Minikube**

    ``` shell
    minikube start
    ```

1. 初始化叢集中的**Helm**伺服器元件（*tiller*）

    ``` shell
    helm init
    ```

1. 新增 IoT Edge 存放庫並更新 helm 安裝

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [建立 IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)、[註冊 IoT Edge 裝置](how-to-register-device.md)，並記下其連接字串。

1. 在您的叢集中安裝 iot 和 IoT Edge 代理程式

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

    在叢集命名空間底下，您會看到一個 IoT Edge 裝置，遵循慣例*msiot-\<iothub-name >-\<edgedevice-name >* 。 IoT Edge 代理程式和 iot pod 應該在此命名空間中啟動並執行。

1. 使用快速入門的[部署模組](quickstart-linux.md#deploy-a-module)一節中的步驟，新增模擬的溫度感應器模組。 IoT Edge 模組管理是從 IoT 中樞入口網站完成，就像任何其他 IoT Edge 裝置一樣。 不建議透過 Kubernetes 工具對模組設定進行本機變更，因為它們可能會遭到覆寫。

1. 在幾秒內，重新整理儀表板中 edge 裝置命名空間下的**pod 頁面，** 會列出 IoT Edge 中樞和模擬感應器 pod，如同使用 IoT Edge hub pod 將資料內嵌至 IoT 中樞。

## <a name="clean-up-resources"></a>清除資源

若要移除 edge 部署所建立的所有資源，請使用下列命令搭配上一節的步驟5中使用的名稱。

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>後續步驟

### <a name="deploy-as-a-highly-available-edge-gateway"></a>部署為高可用性 edge 閘道 

Kubernetes 叢集中的邊緣裝置可用來作為下游裝置的 IoT 閘道。 您可以將它設定為可復原節點失敗，因此可為邊緣部署提供高可用性。 請參閱這[份詳細的逐步](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes)解說，以在此案例中使用 IoT Edge。