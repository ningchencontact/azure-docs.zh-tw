---
title: 針對常見的 Azure Kubernetes Service 問題進行疑難排解
description: 了解在使用 Azure Kubernetes Service (AKS) 時，如何針對常見問題進行疑難排解並加以解決
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: fd3d1c464c6f2d4cbecd715db0689581ca141769
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2018
ms.locfileid: "53654065"
---
# <a name="aks-troubleshooting"></a>AKS 疑難排解

當您建立或管理 Azure Kubernetes Service (AKS) 叢集時，可能偶爾會遇到問題。 本文將詳細說明一些常見問題與疑難排解步驟。

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>我通常可以在哪裡找到針對 Kubernetes 進行偵錯問題的相關資訊？

嘗試[針對 Kubernetes 叢集進行疑難排解的官方指南](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)。
此外，也有 Microsoft 工程師所發佈的[疑難排解指南](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)，該指南可用來針對 Pod、節點、叢集和其他功能進行疑難排解。

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>我在建立或升級期間收到「已超出配額」錯誤。 我該怎麼辦？ 

您需要[要求核心](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>對於 AKS，每個節點的最大 Pod 數設定為何？

如果您在 Azure 入口網站中部署 AKS 叢集，則每個節點的最大 Pod 數設定會預設為 30。
如果您在 Azure CLI 中部署 AKS 叢集，則每個節點的最大 Pod 數設定會預設為 110。 (確定您使用最新版的 Azure CLI)。 在 `az aks create` 命令中使用 `–-max-pods` 旗標，即可變更此預設設定。

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>當我使用進階網路設定部署 AKS 叢集時，收到 insufficientSubnetSize 錯誤。 我該怎麼辦？

在 AKS 建立期間網路功能的自訂 Azure 虛擬網路選項中，Azure 容器網路介面 (CNI) 用於 IP 位址管理 (IPAM)。 AKS 叢集中的節點數可以是介於 1 到 100 之間的任何數字。 根據前一節，子網路大小應大於節點數與每個節點最大 Pod 數的乘積。 關聯性的表達方式如下：子網路大小 > 叢集中的節點數 * 每個節點的最大 Pod。

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>我的 Pod 會在 CrashLoopBackOff 模式中停滯。 我該怎麼辦？

有各種不同的原因可能會讓 Pod 在該模式中停滯。 您可以查看：

* Pod 本身 (使用 `kubectl describe pod <pod-name>`)。
* 記錄 (使用`kubectl log <pod-name>`)。

如需有關如何針對 Pod 問題進行疑難排解的詳細資訊，請參閱[偵錯應用程式](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)。

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>我正嘗試在現有叢集上啟用 RBAC。 如何執行該作業？

很遺憾，目前不支援在現有叢集上啟用角色型存取控制 (RBAC)。 您必須明確地建立新的叢集。 如果您使用 CLI，預設會啟用 RBAC。 如果您使用 AKS 入口網站，可在建立工作流程中使用切換按鈕來啟用 RBAC。

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>我已使用 Azure CLI 搭配預設值或 Azure 入口網站來建立已啟用 RBAC 的叢集，但現在於 Kubernetes 儀表板上看到許多警告。 該儀表板一直可正常運作，而且未產生任何警告。 我該怎麼辦？

儀表板上警告的原因是因為根據預設，現在會透過 RBAC 啟用叢集並已停用對它的存取權。 這種方法通常是良好的做法，因為預設向叢集的所有使用者公開該儀表板可能會導致安全性威脅。 如果您仍然想要啟用該儀表板，請依照[這篇部落格文章](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/) \(英文\) 中的步驟執行。

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>我無法連線到儀表板。 我該怎麼辦？

存取叢集外部服務的最簡單方法是執行 `kubectl proxy`，它會將傳送至 localhost 連接埠 8001 的要求 Proxy 到 Kubernetes API 伺服器。 API 伺服器可以從該處 Proxy 到您的服務：`http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`。

如果沒有看到 Kubernetes 儀表板，請檢查 `kube-proxy` Pod 是否正在 `kube-system` 命名空間中執行。 如果它並未處於執行中狀態，請刪除該 Pod，而它會重新啟動。

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>我無法使用 kubectl 記錄取得記錄，或無法連線到 API 伺服器。 我收到「伺服器錯誤：撥接後端時發生錯誤: 撥接 tcp...」 我該怎麼辦？

請確定並未修改預設的網路安全性群組 (NSG)，並已針對與 API 伺服器的連線開啟連接埠 22。 檢查 `tunnelfront` Pod 是否正在 `kube-system` 命名空間中執行。 若非如此，請強制刪除該 Pod，而它會重新啟動。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-problem"></a>我正嘗試升級或調整，卻收到「訊息：不允許變更 'ImageReference' 屬性」錯誤。  如何修正此問題？

您可能會因為修改了 AKS 叢集內代理程式節點中的標記而收到此錯誤。 修改和刪除 MC_* 資源群組中資源的標記和其他屬性可能會導致非預期的結果。 在 AKS 叢集中修改 MC_* 群組下的資源會破壞服務層級目標 (SLO)。

## <a name="how-do-i-renew-the-service-principal-secret-on-my-aks-cluster"></a>如何更新 AKS 叢集上的服務主體密碼？

根據預設，建立 AKS 叢集時包含的服務主體到期時間為期一年。 當到期日接近時，您可以重設認證，將服務主體再延長一段時間。

以下範例會執行下列步驟：

1. 使用 [az aks show](/cli/azure/aks#az-aks-show) 命令，取得叢集的服務主體識別碼。
1. 使用 [az ad sp credential list](/cli/azure/ad/sp/credential#az-ad-sp-credential-list) 列出服務主體用戶端祕密。
1. 使用 [az ad sp credential-reset](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) 命令，將服務主體再延長一年。 服務主體用戶端祕密必須維持不變，AKS 叢集才能正確執行。

```azurecli
# Get the service principal ID of your AKS cluster.
sp_id=$(az aks show -g myResourceGroup -n myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)

# Get the existing service principal client secret.
key_secret=$(az ad sp credential list --id $sp_id --query [].keyId -o tsv)

# Reset the credentials for your AKS service principal and extend for one year.
az ad sp credential reset \
    --name $sp_id \
    --password $key_secret \
    --years 1
```
