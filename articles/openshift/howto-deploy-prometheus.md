---
title: 部署在 Azure 的 Red Hat OpenShift 的叢集中的獨立 Prometheus |Microsoft Docs
description: 以下是如何在 Azure 的 Red Hat OpenShift 叢集來監視您的應用程式計量上建立的 Prometheus 執行個體。
author: Makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus aro、 openshift、 計量、 red hat
ms.openlocfilehash: e66658151361edd43f61d398274c88c047928028
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342631"
---
# <a name="deploy-a-standalone-prometheus-in-an-azure-red-hat-openshift-cluster"></a>部署在 Azure 的 Red Hat OpenShift 的叢集中的獨立 Prometheus

本指南將說明如何使用 Azure 的 Red Hat OpenShift 叢集中的服務探索設定獨立的 Prometheus。  不需要在叢集中的 「 客戶系統管理員 」 存取。

目標設定如下所示：

- 一個專案 （prometheus-專案），其中包含 Prometheus 和 Alertmanager
- 兩個專案 （應用程式 project1 和 project2 應用程式），其中包含要監視的應用程式

您將準備一些 Prometheus 組態檔在本機。 建立新的資料夾來儲存它們。
為密碼將這些組態檔儲存在叢集中，如果稍後祕密權杖新增到。

## <a name="step-1-sign-in-to-the-cluster-using-the-oc-tool"></a>步驟 1：登入到叢集時使用`oc`工具
使用網頁瀏覽器，瀏覽至您的叢集的 web 主控台 (https://openshift 。*隨機識別碼*。*區域*。 azmosa.io)。
利用您的 Azure 認證登入。
按一下右上方中的使用者名稱，然後選取 [複製登入的命令]。 請將它貼到您將使用的終端機。

如果您已登入具有正確的叢集，您可以確認`oc whoami -c`命令。

## <a name="step-2-prepare-the-projects"></a>步驟 2：準備專案

建立專案。
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> 您可以使用`-n`或是`--namespace`參數或使用中專案的選取`oc project`命令

## <a name="step-3-prepare-prometheus-config"></a>步驟 3：準備 Prometheus 組態
建立名為 prometheus.yml 使用下列內容的檔案。
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
建立名為"化唯讀記憶體 」 設定的密碼。
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

以上所列的檔案是基本的 Prometheus 組態檔。
它會設定間隔，並會自動探索設定在三個專案 （prometheus 專案，應用程式 project1，應用程式 project2）。
在此範例中，自動探索端點會透過 HTTP 擷取，而不需要驗證。
如需有關擷取端點的詳細資訊，請參閱 https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config 。


## <a name="step-4-prepare-alertmanager-config"></a>步驟 4：準備 Alertmanager 組態
建立名為 alertmanager.yml 使用下列內容的檔案。
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
建立名為 [化唯讀記憶體-警示] 設定的密碼。
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

警示管理員設定檔上面所列的檔案。

> [!NOTE]
> 您可以確認與前的兩個步驟 `oc get secret -n prometheus-project`

## <a name="step-5-start-prometheus-and-alertmanager"></a>步驟 5：啟動 Prometheus 和 Alertmanager
下載[prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml)範本，從[openshift/原始存放庫](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus)並將它套用 prometheus 專案中
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus standalone.yaml 檔案是 OpenShift 範本，這將會使用它，並使用 oauth proxy 也保護 Alertmanager 執行個體前面 oauth proxy 建立的 Prometheus 執行個體。  此範本中 oauth proxy 已設定為允許任何使用者都可以 「 取得 」 的 「 prometheus 專案 」 命名空間 (請參閱`-openshift-sar`旗標)。

> [!NOTE]
> 您可以確認是否有等於"化唯讀記憶體"StatefulSet*預期*並*目前*數字的複本`oc get statefulset -n prometheus-project`命令。
> 您也可以檢查專案內的所有資源`oc get all -n prometheus-project`。

## <a name="step-6-add-permissions-to-allow-service-discovery"></a>步驟 6：新增權限以允許服務探索
使用下列內容建立 prometheus sdrole.yml。
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus Service Discovery Role
    description: |
      A role and rolebinding adding permissions required to perform Service Discovery in a given project.
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
將範本套用到所有您想要讓服務探索的專案。
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
如果您也想要能夠從本身收集計量的 Prometheus，請記得太套用 prometheus 專案中的權限。

> [!NOTE]
> 您可以確認是否 RoleBinding 與角色已建立正確`oc get role`和`oc get rolebinding`分別命令

## <a name="optional-deploy-example-application"></a>選用：部署範例應用程式
所有項目正常運作，但沒有計量來源。 移至 Prometheus URL (https://prom-prometheus-project.apps 。*隨機識別碼*。*區域*.azmosa.io/)，可使用下列命令。
```
oc get route prom -n prometheus-project
```
請務必與 https:// 的主機名稱的前置詞。

**狀態 > 服務探索**頁面會顯示 0/0 作用中的目標。

若要部署範例應用程式，/metrics 端點下的公開基本的 python 度量，請執行下列命令。
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
在部署之後，新的應用程式會顯示為 30 秒內的服務探索 頁面上的有效目標。 您可以取得更多詳細資料**狀態 > 目標**頁面。

> [!NOTE]
> 每個成功依照目標為 Prometheus 會在 「 啟動 」 計量新增 datapoint。 按一下  **Prometheus**在左上角並輸入 「 向上 」 作為運算式，然後按一下**Execute**。

## <a name="next-steps"></a>後續步驟

您可以加入自訂的 Prometheus 檢測您的應用程式。

Prometheus 用戶端程式庫，可簡化準備 Prometheus 計量，可供使用不同的程式設計語言。

 - Java https://github.com/prometheus/client_java
 - Python https://github.com/prometheus/client_python
 - 移至 https://github.com/prometheus/client_golang
 - Ruby https://github.com/prometheus/client_ruby
