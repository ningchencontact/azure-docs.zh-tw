---
title: 使用 Azure Kubernetes Service (AKS) 中的 pod 安全性原則
description: 了解如何控制使用 PodSecurityPolicy Azure Kubernetes Service (AKS) 中的 pod 許可
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: iainfou
ms.openlocfilehash: 7ce311ab9c554481f64c6c9be40e2018893a0966
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61027370"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>預覽-保護您的叢集使用 Azure Kubernetes Service (AKS) 中的 pod 安全性原則

若要改善您的 AKS 叢集的安全性，您可以限制哪些 pod 可以排程。 要求的資源不允許的 pod 不 AKS 叢集中執行。 您可以定義此使用 pod 安全性原則的存取權。 這篇文章會示範如何使用 pod 安全性原則來限制 AKS 中的 pod 部署。

> [!IMPORTANT]
> AKS 预览功能是自助服务和可以选择加入的功能。 提供预览是为了从我们的社区收集反馈和 bug。 但是，Azure 技术支持部门不为其提供支持。 如果你创建一个群集，或者将这些功能添加到现有群集，则除非该功能不再为预览版并升级为公开发布版 (GA)，否则该群集不会获得支持。
>
> 如果遇到预览版功能的问题，请[在 AKS GitHub 存储库中提交问题][aks-github]，并在 Bug 标题中填写预览版功能的名称。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您需要 Azure CLI 2.0.61 版或更新版本安裝並設定。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

AKS 叢集中會更新以啟用使用 pod 安全性原則*aks 預覽*CLI 擴充功能。 安裝*aks 預覽*Azure CLI 擴充功能使用[az 延伸模組加入][ az-extension-add]命令，如下列範例所示：

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> 如果您先前已安裝*aks 預覽*延伸模組，安裝任何可用更新使用`az extension update --name aks-preview`命令。

### <a name="register-pod-security-policy-feature-provider"></a>註冊 pod 安全性原則功能提供者

若要建立或更新的 AKS 叢集，以將 pod 安全性原則，請先啟用您訂用帳戶的功能旗標。 若要註冊*PodSecurityPolicyPreview*功能旗標，請使用[az 功能註冊][ az-feature-register]命令，在下列範例所示：

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

狀態需要幾分鐘的時間才會顯示「已註冊」。 您可以使用 [az feature list][az-feature-list] 命令檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

準備就緒時，使用 [az provider register][az-provider-register] 命令重新整理 *Microsoft.ContainerService* 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Pod 的安全性原則概觀

在 Kubernetes 叢集中的許可控制站用來建立資源時，攔截對 API 伺服器的要求。 許可控制站即可*驗證*資源要求對一組規則，或*變動*来變更部署參數的資源。

*PodSecurityPolicy*已驗證的 pod 規格的許可控制站符合您定義的需求。 這些需求可能會限制使用特殊權限的容器、 特定類型的儲存體，或使用者或群組可以執行容器的存取。 當您嘗試部署的資源，其中的 pod 規格不符合在 pod 的安全性原則中所述的需求時，則會拒絕要求。 這項功能來控制哪些 pod 可以排定在 AKS 叢集可防止一些可能的安全性弱點或特殊權限提升。

當您啟用在 AKS 叢集中的 pod 安全性原則時，會套用某些預設原則。 這些預設原則會提供可排程來定義什麼 pod 的立即可用的體驗。 不過，叢集使用者可能會遇到問題，直到您可以定義您自己的原則部署的 pod。 建議方法是：

* 建立 AKS 叢集
* 定義您自己的 pod 安全性原則
* 啟用 pod 的安全性原則功能

若要顯示方式的預設原則會限制 pod 部署，請在本文中我們先啟用 pod 的安全性原則功能，然後再建立自訂原則。

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>啟用 AKS 叢集上的 pod 安全性原則

您可以啟用或停用使用的 pod 安全性原則[更新 az aks] [ az-aks-update]命令。 下列範例啟用 pod 上的安全性原則的叢集名稱*myAKSCluster*資源群組中名為*myResourceGroup*。

> [!NOTE]
> 真實世界的使用，請勿啟用 pod 安全性原則直到您已定義您自己自訂的原則。 在本文中，您必須讓 pod 安全性原則的第一個步驟，以查看如何預設原則會限制 pod 部署。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>AKS 的預設原則

當您啟用 pod 安全性原則時，AKS 建立兩個名為的預設原則*特殊權限*並*限制*。 不要編輯或移除這些預設原則。 相反地，建立您自己的原則定義您想要的設定來控制。 讓我們率先一睹這些預設原則是它們如何影響 pod 部署。

若要檢視可用的原則，請使用[kubectl get psp] [ kubectl-get]命令，如下列範例所示。 做為預設值的一部分*受限*原則，使用者會被拒絕*PRIV*用於特殊權限的 pod 升級，以及使用者*MustRunAsNonRoot*。

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*限制*pod 安全性原則會套用至任何已驗證的使用者，在 AKS 叢集中。 這項指派是由 ClusterRoles 和 ClusterRoleBindings 控制。 使用[kubectl get clusterrolebindings] [ kubectl-get]命令，並搜尋*預設： 限制：* 繫結：

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

下列的扼要輸出所示*psp： 受限*ClusterRole 指派給任何*system： 驗證*使用者。 這項功能會提供基本層級的限制，而不需要您自己所定義的原則。

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:restricted
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

請務必了解這些預設原則與使用者對排程的 pod，然後再開始建立您自己的 pod 安全性原則的要求之間的互動方式。 在下一步 的幾節中，讓我們來排程一些 pod，以查看這些預設原則的運作。

## <a name="create-a-test-user-in-an-aks-cluster"></a>在 AKS 叢集中建立的測試使用者

根據預設，當您使用[az aks get-credentials 來取得認證][ az-aks-get-credentials]命令*管理員*AKS 叢集的認證，並加入您`kubectl`組態。系統管理員使用者會略過強制執行的 pod 安全性原則。 如果您使用 Azure Active Directory 整合以用於您的 AKS 叢集，您可以查看動作中的原則強制執行登入非系統管理員使用者的認證。 在本文中，讓我們建立在 AKS 叢集中可供您的測試使用者帳戶。

建立名為範例命名空間*psp aks*測試使用的資源[kubectl 建立命名空間][ kubectl-create]命令。 接著，建立名為的服務帳戶*nonadmin 使用者*使用[kubectl 建立 serviceaccount] [ kubectl-create]命令：

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

接下來，建立針對 RoleBinding *nonadmin 使用者*若要在命名空間中使用執行基本動作[kubectl 建立 rolebinding] [ kubectl-create]命令：

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>建立系統管理員和非系統管理員使用者的別名命令

若要反白顯示一般的系統管理使用者使用時之間的差異`kubectl`，並在先前步驟中，建立的非系統管理使用者建立兩個命令列別名：

* **Kubectl admin**別名的一般系統管理使用者，而且範圍設定為*psp aks*命名空間。
* **Kubectl nonadminuser**別名為*nonadmin 使用者*上一個步驟中，建立及範圍設定為*psp aks*命名空間。

建立這些兩個別名，如下列命令所示：

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>測試的特殊權限的 pod 建立

讓我們先測試排定的安全性內容的 pod 時，會發生什麼事`privileged: true`。 此安全性內容呈報 pod 的權限。 上一節示範預設 AKS pod 安全性原則*限制*原則應該拒絕此要求。

建立名為`nginx-privileged.yaml`並貼上下列 YAML 資訊清單：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

建立使用 pod [kubectl 套用][ kubectl-apply]命令並指定您的 YAML 資訊清單的名稱：

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

無法排程的 pod 失敗，如下列範例輸出所示：

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Pod 不會達到排程的階段，因此沒有任何刪除之前，您將移動的資源。

## <a name="test-creation-of-an-unprivileged-pod"></a>建立測試的無特殊權限的 pod

在上述範例中，pod 規格會要求特殊權限的擴大。 預設會遭到拒絕此要求*限制*pod 的安全性原則，因此 pod 便會排程。 我們來試試執行這個相同的 NGINX pod，沒有權限擴大要求。

建立名為`nginx-unprivileged.yaml`並貼上下列 YAML 資訊清單：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

建立使用 pod [kubectl 套用][ kubectl-apply]命令並指定您的 YAML 資訊清單的名稱：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Kubernetes 排程器會接受 pod 要求。 不過，如果您查看的 pod 使用狀態`kubectl get pods`，發生錯誤：

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

使用[kubectl 描述 pod] [ kubectl-describe]命令來查看 pod 的事件。 下列扼要的範例會顯示容器和映像需要根權限，即使我們並未要求它們：

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

Nginx 的容器映像即使我們並未要求任何特殊權限的存取，必須建立連接埠的繫結*80*。 若要繫結連接埠*1024年*和以下版本，*根*會要求使用者。 當啟動，請嘗試 pod*限制*pod 安全性原則會拒絕此要求。

此範例會顯示作用中建立的 AKS 預設 pod 的安全性原則，並限制使用者可以執行的動作。 務必了解這些預設原則，原則的行為，您可能不會預期要拒絕的基本 NGINX pod。

您移至下一個步驟之前，先刪除使用您建立這個測試 pod [kubectl 刪除 pod] [ kubectl-delete]命令：

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>建立測試的 pod，以與特定使用者內容

在上述範例中，容器映像會自動嘗試使用 NGINX 繫結連接埠 80 的根。 此要求遭到拒絕的預設*限制*pod 的安全性原則，因此無法啟動的 pod。 讓我們現在使用嘗試執行這個相同的 NGINX pod 特定使用者內容，例如`runAsUser: 2000`。

建立名為`nginx-unprivileged-nonroot.yaml`並貼上下列 YAML 資訊清單：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

建立使用 pod [kubectl 套用][ kubectl-apply]命令並指定您的 YAML 資訊清單的名稱：

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Kubernetes 排程器會接受 pod 要求。 不過，如果您查看的 pod 使用狀態`kubectl get pods`，沒有比前一個範例不同的錯誤：

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

使用[kubectl 描述 pod] [ kubectl-describe]命令來查看 pod 的事件。 下列扼要的範例顯示的 pod 事件：

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

事件會指出已建立容器，並啟動。 沒有任何明顯關於 pod 處於失敗狀態。 讓我們看看使用 pod 記錄[kubectl 記錄][ kubectl-logs]命令：

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

下列範例記錄輸出會提供指示，NGINX 組態本身內，沒有權限錯誤時，服務會嘗試啟動。 此錯誤一次被因為需要繫結連接埠 80。 雖然 pod 規格會定義一般使用者帳戶，此使用者帳戶不是不足以在 OS 層級 NGINX 服務啟動並繫結至受限制的連接埠。

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

同樣地，務必了解預設 pod 安全性原則的行為。 這個錯誤是有點難追蹤，並同樣地，您可能不會預期要拒絕的基本 NGINX pod。

您移至下一個步驟之前，先刪除使用您建立這個測試 pod [kubectl 刪除 pod] [ kubectl-delete]命令：

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>建立自訂的 pod 的安全性原則

既然您已了解預設 pod 安全性原則的行為，讓我們可讓*nonadmin 使用者*成功排程 pod。

讓我們建立一個原則來拒絕要求特殊權限的存取的 pod。 其他選項，例如*runAsUser* ，或允許*磁碟區*，沒有明確限制。 這種類型的原則拒絕要求，以進行特殊權限的存取，但否則可讓叢集執行要求的 pod。

建立名為`psp-deny-privileged.yaml`並貼上下列 YAML 資訊清單：

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

建立原則使用[kubectl 套用][ kubectl-apply]命令並指定您的 YAML 資訊清單的名稱：

```console
kubectl apply -f psp-deny-privileged.yaml
```

若要檢視可用的原則，請使用[kubectl get psp] [ kubectl-get]命令，如下列範例所示。 比較*psp 拒絕 privileged*原則，預設值*限制*已強制執行先前的範例來建立 pod 中的原則。 只使用*PRIV*擴大規模會拒絕您的原則。 沒有任何使用者或群組的限制*psp 拒絕 privileged*原則。

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>允許使用自訂的 pod 安全性原則的使用者帳戶

在上一個步驟中，您建立的 pod 安全性原則，以拒絕 pod 該特殊權限的要求存取權。 若要允許使用的原則，您建立*角色*或是*ClusterRole*。 然後，將使用這些角色的其中一個關聯*RoleBinding*或是*ClusterRoleBinding*。

此範例中，建立可讓您 ClusterRole*使用* *psp 拒絕 privileged*在上一個步驟中建立的原則。 建立名為`psp-deny-privileged-clusterrole.yaml`並貼上下列 YAML 資訊清單：

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

建立使用 ClusterRole [kubectl 套用][ kubectl-apply]命令並指定您的 YAML 資訊清單的名稱：

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

現在建立要使用在上一個步驟中建立 ClusterRole ClusterRoleBinding。 建立名為`psp-deny-privileged-clusterrolebinding.yaml`並貼上下列 YAML 資訊清單：

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

建立使用 ClusterRoleBinding [kubectl 套用][ kubectl-apply]命令並指定您的 YAML 資訊清單的名稱：

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 在這篇文章的第一個步驟中，已啟用 AKS 叢集上的 pod 的安全性原則功能。 建議的做法是只讓 pod 的安全性原則功能，您已定義您自己的原則。 這是您想在其中啟用 pod 安全性原則功能的階段。 已定義一或多個自訂的原則，並已與這些原則相關聯的使用者帳戶。 現在您可以安全地 pod 安全性原則功能，並由預設原則所造成的問題降至最低。

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>測試無特殊權限的 pod 建立一次

套用您自訂的 pod 的安全性原則和使用者帳戶以使用原則的繫結，讓我們嘗試再次建立無特殊權限的 pod。 使用相同`nginx-privileged.yaml`來建立 pod 的使用資訊清單[kubectl 套用][ kubectl-apply]命令：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

已成功排程 pod。 當您核取狀態的 pod using [kubectl get pods] [ kubectl-get]命令，是 pod*執行*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

此範例會示範如何建立自訂的 pod 定義不同的使用者或群組在 AKS 叢集中的存取權的安全性原則。 預設 AKS 原則提供的嚴格控制哪些 pod 上的可以執行，因此建立您自己自訂的原則，然後正確定義您所需要的限制。

刪除使用無特殊權限的 NGINX pod [kubectl 刪除][ kubectl-delete]命令並指定您的 YAML 資訊清單的名稱：

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>清除資源

若要停用 pod 安全性原則，請使用[更新 az aks] [ az-aks-update]命令一次。 下列範例會停用 pod 上的安全性原則的叢集名稱*myAKSCluster*資源群組中名為*myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

接下來，刪除 ClusterRole 和 ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

網路原則使用刪除[kubectl 刪除][ kubectl-delete]命令並指定您的 YAML 資訊清單的名稱：

```console
kubectl delete -f psp-deny-privileged.yaml
```

最後，刪除*psp aks*命名空間：

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>後續步驟

這篇文章會示範如何建立 pod 安全性原則，以避免使用特殊權限的存取。 有許多功能，可以強制的原則，例如磁碟區或 RunAs 使用者的類型。 如需有關可用之選項的詳細資訊，請參閱 < [Kubernetes pod 的安全性原則參考文件][kubernetes-policy-reference]。

如需有關如何限制 pod 的網路流量的詳細資訊，請參閱[保護在 AKS 中使用網路原則的 pod 之間的流量][network-policies]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference
[aks-github]: https://github.com/azure/aks/issues

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
