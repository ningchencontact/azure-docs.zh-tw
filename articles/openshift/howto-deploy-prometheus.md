---
title: 部署獨立 Prometheus 執行個體中的 Azure Red Hat OpenShift 叢集 |Microsoft Docs
description: 在 Azure Red Hat OpenShift 叢集，以監視您的應用程式計量的 Prometheus 執行個體。
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus aro、 openshift、 計量、 red hat
ms.openlocfilehash: a9748932a72106413677b21fe0efd1f69fb02e47
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827023"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>部署獨立 Prometheus 執行個體中的 Azure Red Hat OpenShift 叢集

本文說明如何設定獨立的 Prometheus 執行個體使用的 Azure Red Hat OpenShift 叢集服務探索。

> [!NOTE]
> Azure Red Hat OpenShift 叢集的客戶系統管理員存取並非必要。

目標設定：

- 一個專案 （prometheus-專案），其中包含 Prometheus 和 Alertmanager。
- 兩個專案 （應用程式 project1 和 project2 應用程式），其中包含要監視的應用程式。

您將準備一些 Prometheus 組態檔在本機。 建立新的資料夾來儲存它們。 組態檔會儲存在叢集中為機密資料，以防祕密權杖將稍後新增至叢集。

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>使用 OC 工具登入叢集

1. 開啟網頁瀏覽器，然後移到 web 主控台，您的叢集 (https://openshift 。*隨機識別碼*。*區域*。 azmosa.io)。
2. 利用您的 Azure 認證登入。
3. 在右上角，選取您的使用者名稱，然後選取**複製 [登入] 命令**。
4. 將您的使用者名稱貼到您將使用的終端機中。

> [!NOTE]
> 如果您已登入正確的叢集，請執行`oc whoami -c`命令。

## <a name="prepare-the-projects"></a>準備專案

若要建立專案，請執行下列命令：
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> 您可以使用`-n`或是`--namespace`參數或選取使用中的專案，藉由執行`oc project`命令。

## <a name="prepare-the-prometheus-configuration-file"></a>準備 Prometheus 組態檔
輸入下列內容建立 prometheus.yml 檔案：
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
建立祕密呼叫化唯讀記憶體，藉由輸入下列設定：
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Prometheus.yml 檔案是基本的 Prometheus 組態檔。 它會設定間隔，並會自動探索設定在三個專案 （prometheus 專案，應用程式 project1，應用程式 project2）。 在先前的組態檔中，自動探索的端點會透過 HTTP 擷取未經驗證。

如需有關擷取端點的詳細資訊，請參閱[Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)。


## <a name="prepare-the-alertmanager-config-file"></a>準備 Alertmanager 組態檔
輸入下列內容建立 alertmanager.yml 檔案：
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
建立祕密呼叫化唯讀記憶體警示，輸入下列設定：
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml 是警示管理員設定檔。

> [!NOTE]
> 若要確認前兩個步驟，請執行`oc get secret -n prometheus-project`命令。

## <a name="start-prometheus-and-alertmanager"></a>啟動 Prometheus 和 Alertmanager
移至[openshift/原始存放庫](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus)並下載[prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml)範本。 輸入下列設定，將範本套用到 prometheus 專案：
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus standalone.yaml 檔案是 OpenShift 範本。 它會使用 oauth-proxy 之前它並 Alertmanager 執行個體，也會保護使用 oauth proxy 建立 Prometheus 執行個體。 此範本中，oauth proxy 已設定為允許任何使用者都可以 「 取得 」 prometheus 專案命名空間 (請參閱`-openshift-sar`旗標)。

> [!NOTE]
> 若要確認化唯讀記憶體 StatefulSet 是否相等的預期和目前的數字複本，執行`oc get statefulset -n prometheus-project`命令。 若要檢查專案中的所有資源，請執行`oc get all -n prometheus-project`命令。

## <a name="add-permissions-to-allow-service-discovery"></a>新增權限以允許服務探索

輸入下列內容建立 prometheus sdrole.yml 檔案：
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
若要將範本套用至您想要讓服務探索的所有專案中，執行下列命令：
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
若要從本身收集計量的 Prometheus，套用 prometheus 專案中的權限。

> [!NOTE]
> 若要確認角色和 RoleBinding 已正確建立，請執行`oc get role`和`oc get rolebinding`命令。

## <a name="optional-deploy-example-application"></a>選用：部署範例應用程式

所有項目正常運作，但沒有計量來源。 移至 Prometheus URL (https://prom-prometheus-project.apps.*隨機識別碼*.*區域*.azmosa.io/)。 您可以使用下列命令來找到它：

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> 請記得將 https:// 前置詞新增至主機名稱的開頭。

**狀態 > 服務探索**頁面會顯示 0/0 作用中的目標。

若要部署範例應用程式，它會公開基本的 Python 計量 /metrics 端點下，執行下列命令：
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
新的應用程式應該會顯示為有效的目標，在服務探索 頁面上，在部署後的 30 秒內。

如需詳細資訊，請選取**狀態** > **目標**。

> [!NOTE]
> 針對每個成功依照的目標，Prometheus，請將最新的計量的資料點來進行。 選取  **Prometheus**左上角中，輸入**向上**作為運算式，然後選取**Execute**。

## <a name="next-steps"></a>後續步驟

您可以加入自訂的 Prometheus 檢測您的應用程式。 Prometheus 用戶端程式庫，可簡化 Prometheus 計量準備，可供使用不同的程式設計語言。

如需詳細資訊，請參閱下列 GitHub 程式庫：

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
