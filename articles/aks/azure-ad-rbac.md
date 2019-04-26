---
title: 使用 RBAC 和 Azure Kubernetes 服務中的 Azure AD 的控制叢集資源
description: 了解如何使用 Azure Active Directory 群組成員資格，來限制 Azure Kubernetes Service (AKS) 中使用角色型存取控制 (RBAC) 的叢集資源的存取
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: e974c47d1dfb04f66b622c64a7143d00de87c4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467539"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>在 Azure Kubernetes Service 中使用角色型存取控制與 Azure Active Directory 身分識別的叢集資源的控制存取

Azure Kubernetes Service (AKS) 可以設定為使用 Azure Active Directory (AD) 進行使用者驗證。 在此組態中，您可登入使用 Azure AD 驗證權杖的 AKS 叢集。 您也可以設定 Kubernetes 角色型存取控制 (RBAC) 來限制存取叢集資源會根據使用者的身分識別或群組成員資格。

這篇文章會示範如何使用 Azure AD 群組成員資格來控制命名空間的存取權，以及叢集使用 AKS 叢集中的 Kubernetes RBAC 的資源。 例如，群組和使用者會建立在 Azure AD 中，然後在 AKS 叢集中，授與適當的權限，來建立及檢視資源會建立角色和 RoleBindings。

## <a name="before-you-begin"></a>開始之前

本文假設您有現有的 AKS 叢集與 Azure AD 的整合已啟用。 如果您需要 AKS 叢集，請參閱[整合 Azure Active Directory 與 AKS][azure-ad-aks-cli]。

您需要 Azure CLI 2.0.61 版或更新版本安裝並設定。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="create-demo-groups-in-azure-ad"></a>在 Azure AD 中建立示範群組

在本文中，讓我們建立兩個可用來顯示如何 Kubernetes RBAC 與 Azure AD 中控制叢集資源的存取權的使用者角色。 使用下列兩個範例角色：

* **應用程式開發人員**
    * 名為的使用者*aksdev*屬於*appdev*群組。
* **網站可靠性工程師**
    * 名為的使用者*akssre*屬於*opssre*群組。

在生產環境中，您可以使用現有的使用者和群組內的 Azure AD 租用戶。

首先，取得您 AKS 叢集使用的資源識別碼[az aks show] [ az-aks-show]命令。 將資源識別碼指派給名為的變數*AKS_ID*以便在其他的命令中參考它。

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Azure AD 中建立第一個範例群組使用的應用程式開發人員[az ad 群組建立][ az-ad-group-create]命令。 下列範例會建立名為的群組*appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

現在，建立的 Azure 角色指派*appdev*群組使用[az 角色指派建立][ az-role-assignment-create]命令。 此指派可讓使用任何成員的群組`kubectl`方式授與使用者互動與 AKS 叢集*Azure Kubernetes 服務叢集使用者角色*。

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> 如果您收到錯誤，例如`Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`，等候幾秒鐘，讓 Azure AD 群組物件識別碼，以透過目錄傳播，然後再次嘗試`az role assignment create`命令一次。

建立第二個範例群組、 SREs 這一項名為*opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

同樣地，建立 Azure 角色指派，以授與群組的成員*Azure Kubernetes 服務叢集使用者角色*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>在 Azure AD 中建立示範使用者

與 Azure AD 中建立我們的應用程式開發人員和 SREs 的兩個範例群組現在可讓建立兩個範例使用者。 若要測試在本文結尾處的 RBAC 整合，您可登入這些帳戶的 AKS 叢集。

在 Azure ad 中建立第一個使用者帳戶[az ad 使用者建立][ az-ad-user-create]命令。

下列範例會建立顯示名稱的使用者*AKS Dev*和 使用者主體名稱 (UPN) `aksdev@contoso.com`。 更新包含已驗證的網域，您的 Azure AD 租用戶的 UPN (取代*contoso.com*使用您自己的網域)，並提供您自己的安全`--password`認證：

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

現在將使用者新增至*appdev*群組中先前的區段使用建立[az ad 群組成員加入][ az-ad-group-member-add]命令：

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

建立第二個使用者帳戶。 下列範例會建立顯示名稱的使用者*AKS SRE*和 使用者主體名稱 (UPN) `akssre@contoso.com`。 同樣地，更新您的 Azure AD 租用戶包含已驗證的網域 UPN (取代*contoso.com*使用您自己的網域)，並提供您自己的安全`--password`認證：

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>建立 AKS 叢集資源的應用程式開發人員

現在建立的 Azure AD 群組和使用者。 Azure 角色指派所建立的群組成員能夠連線到 AKS 叢集的一般使用者身分。 現在，我們將設定 AKS 叢集，以允許這些不同的群組存取特定資源。

首先，取得叢集使用的系統管理員認證[az aks get-credentials 來取得認證][ az-aks-get-credentials]命令。 您可以在其中一個下列各節，取得一般*使用者*叢集以查看 Azure AD 驗證的認證運作中的流程。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

建立 AKS 叢集使用的命名空間[kubectl 建立命名空間][ kubectl-create]命令。 下列範例會建立命名空間名稱*開發人員*:

```console
kubectl create namespace dev
```

在 Kubernetes 中，*角色*定義的權限授與，以及*RoleBindings*將它們套用至所需的使用者或群組。 這些指派可以套用至指定的命名空間或在整個叢集中套用。 如需詳細資訊，請參閱[使用 RBAC 授權][rbac-authorization]。

首先，建立的角色*開發人員*命名空間。 此角色授與命名空間的完整權限。 在生產環境中，您可以指定不同的使用者或群組更細微的權限。

建立名為`role-dev-namespace.yaml`並貼上下列 YAML 資訊清單：

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

建立角色使用[kubectl 套用][ kubectl-apply]命令並指定您的 YAML 資訊清單的檔案名稱：

```console
kubectl apply -f role-dev-namespace.yaml
```

接下來，取得的資源識別碼*appdev*群組使用[az ad 群組顯示][ az-ad-group-show]命令。 此群組會設定為下一個步驟中 RoleBinding 的主旨。

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

現在，建立針對 RoleBinding *appdev*来用於命名空間存取先前建立的角色群組。 建立名為 `rolebinding-dev-namespace.yaml` 的檔案，並貼上下列 YAML 資訊清單。 在最後一行中，取代*groupObjectId*前一個命令的群組物件識別碼輸出：

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

建立使用 RoleBinding [kubectl 套用][ kubectl-apply]命令並指定您的 YAML 資訊清單的檔案名稱：

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>為 SREs 建立 AKS 叢集資源

現在，重複上述步驟來建立命名空間、 角色和 RoleBinding SREs。

首先，建立的命名空間*sre*使用[kubectl 建立命名空間][ kubectl-create]命令：

```console
kubectl create namespace sre
```

建立名為`role-sre-namespace.yaml`並貼上下列 YAML 資訊清單：

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

建立角色使用[kubectl 套用][ kubectl-apply]命令並指定您的 YAML 資訊清單的檔案名稱：

```console
kubectl apply -f role-sre-namespace.yaml
```

取得的資源識別碼*opssre*群組使用[az ad 群組顯示][ az-ad-group-show]命令：

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

建立針對 RoleBinding *opssre*来用於命名空間存取先前建立的角色群組。 建立名為 `rolebinding-sre-namespace.yaml` 的檔案，並貼上下列 YAML 資訊清單。 在最後一行中，取代*groupObjectId*前一個命令的群組物件識別碼輸出：

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

建立使用 RoleBinding [kubectl 套用][ kubectl-apply]命令並指定您的 YAML 資訊清單的檔案名稱：

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>使用 Azure AD 身分識別的叢集資源進行互動

現在，當您建立和管理 AKS 叢集中的資源，讓我們來測試的預期權限的工作。 在這些範例中，您可以排程並檢視使用者的指派命名空間中的 pod。 然後，您嘗試指派的命名空間之外的排程和檢視 pod。

首先，重設*kubeconfig*內容中使用[az aks get-credentials 來取得認證][ az-aks-get-credentials]命令。 在上一節中，您將使用叢集系統管理員認證的內容。 系統管理員使用者會略過 Azure AD 登入提示。 不含`--admin`參數，在使用者內容會套用所需的所有要求都必須使用 Azure AD 進行驗證。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

基本的 NGINX pod 使用的排程[執行 kubectl] [ kubectl-run]命令*dev*命名空間：

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

登入提示字元中，為輸入的認證為您自己`appdev@contoso.com`本文開頭所建立的帳戶。 一旦您成功登入，帳戶語彙基元會快取，供未來`kubectl`命令。 已成功排程 NGINX，如下列範例輸出所示：

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

現在，使用[kubectl get pods] [ kubectl-get]命令來檢視中的 pod *dev*命名空間。

```console
kubectl get pods --namespace dev
```

下列範例輸出所示，NGINX pod 已順利*執行*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>建立和檢視指派的命名空間外部的叢集資源

現在試著檢視之外的 pod*開發人員*命名空間。 使用[kubectl get pods] [ kubectl-get]命令同樣地，若要查看這次`--all-namespaces`，如下所示：

```console
kubectl get pods --all-namespaces
```

使用者的群組成員資格沒有 Kubernetes 角色，可讓此動作，如下列範例輸出所示：

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

同樣地，在嘗試這類排程在不同的命名空間中的 pod *sre*命名空間。 使用者的群組成員資格未對齊的 Kubernetes 角色和 RoleBinding 授與這些權限，如下列範例輸出所示：

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>測試 SRE 存取 AKS 叢集資源

若要確認，我們的 Azure AD 群組成員資格和 Kubernetes RBAC 可正確運作之間不同的使用者和群組，請嘗試先前的命令時的登入身分*opssre*使用者。

重設*kubeconfig*內容中使用[az aks get-credentials 來取得認證][ az-aks-get-credentials]清除先前快取的驗證權杖的命令*aksdev*使用者：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

嘗試在指派的排程和檢視 pod *sre*命名空間。 出現提示時，使用登入您自己`opssre@contoso.com`本文開頭所建立的認證：

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

下列範例輸出所示，您就可以成功建立，並檢視 pod:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

現在，嘗試檢視或排程指派 SRE 命名空間之外的 pod:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

這些`kubectl`命令失敗，如下列範例輸出所示。 若要建立的權限或其他命名空間中的 manager 資源，避免授與使用者的群組成員資格和 Kubernetes 角色和 RoleBindings:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>清除資源

在本文中，您會建立 AKS 叢集和使用者的資源和 Azure AD 中的群組。 若要清除所有這些資源，請執行下列命令：

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>後續步驟

如需如何保護 Kubernetes 叢集的詳細資訊，請參閱[AKS 存取和身分識別選項)][rbac-authorization]。

如需身分識別和資源控制的最佳作法，請參閱[AKS 中驗證和授權的最佳做法][operator-best-practices-identity]。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
