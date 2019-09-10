---
title: 針對常見的 Azure Kubernetes Service 問題進行疑難排解
description: 了解在使用 Azure Kubernetes Service (AKS) 時，如何針對常見問題進行疑難排解並加以解決
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 50bb26aa1a29dc8b1454fadec416aceea76405b2
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844254"
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

如果使用 Azure CNI (進階網路)，AKS 會根據每個節點上設定的「最大 Pod 數」預先配置 IP 位址。 AKS 叢集中的節點數目可以是介於 1 到 110 之間的任何數字。 根據每個節點所設定的最大 Pod 數，子網路大小應大於「節點數與每個節點最大 Pod 數的乘積」。 下列基本方程式概述此設計：

子網路大小 > 叢集中的節點數 (將未來的調整需求納入考量) * 每個節點最大 Pod 數。

如需詳細資訊，請參閱[為你的叢集規劃 IP 位址](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)。

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

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>我無法使用 kubectl 記錄取得記錄，或無法連線到 API 伺服器。 我收到「伺服器發生錯誤：撥接後端：撥接 tcp ...」。 我該怎麼辦？

請確定預設的網路安全性群組並未修改，而且埠22和9000已開放給 API 伺服器連接。 `tunnelfront` 使用`kubectl get pods --namespace kube-system`命令，檢查 pod 是否正在*kube 系統*命名空間中執行。 若非如此，請強制刪除該 Pod，而它會重新啟動。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>我正嘗試升級或調整，卻收到「訊息：不允許變更 'ImageReference' 屬性」錯誤。 如何修正此問題？

您可能會因為修改了 AKS 叢集內代理程式節點中的標記而收到此錯誤。 修改和刪除 MC_* 資源群組中資源的標記和其他屬性可能會導致非預期的結果。 在 AKS 叢集中修改 MC_* 群組下的資源會破壞服務層級目標 (SLO)。

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>我收到錯誤，指出我的叢集處於失敗狀態，而且升級或調整規模必須等到修正後才會生效

*此疑難排解協助的導向來源 https://aka.ms/aks-cluster-failed*

當叢集因多種原因而進入失敗狀態時，就會發生此錯誤。 在重試先前失敗的作業之前，請遵循下列步驟來解決叢集失敗狀態：

1. 直到叢集的`upgrade` `scale`狀態不正常，且作業不會成功。 `failed` 常見的根本問題和解決方式包括：
    * 使用**不足的計算（CRP）配額**進行調整。 若要解決此問題，請先將叢集調整回配額內的穩定目標狀態。 然後，請遵循下列[步驟來要求增加計算配額](../azure-supportability/resource-manager-core-quotas-request.md)，然後再嘗試重新擴充超過初始配額限制。
    * 使用 advanced 網路和**不足的子網（網路）資源**來調整叢集。 若要解決此問題，請先將叢集調整回配額內的穩定目標狀態。 然後[，遵循下列步驟來要求增加資源配額，](../azure-resource-manager/resource-manager-quota-errors.md#solution)再嘗試重新相應增加超過初始配額限制。
2. 解決升級失敗的根本原因之後，您的叢集應該會處於成功狀態。 一旦驗證成功狀態之後，請重試原始操作。

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>我在嘗試升級或調整狀態時收到錯誤。我的叢集目前正在升級或升級失敗

*此疑難排解協助的導向來源 https://aka.ms/aks-pending-upgrade*

在具有單一節點集區或具有[多個節點](use-multiple-node-pools.md)集區之叢集的叢集上進行升級和調整作業是互斥的。 您不能同時升級和調整叢集或節點集區。 相反地，每個作業類型必須在目標資源上完成，然後才在該相同資源上進行下一個要求。 如此一來，當作用中的升級或調整作業進行中或已嘗試，且後續失敗時，作業就會受到限制。 

若要協助診斷問題， `az aks show -g myResourceGroup -n myAKSCluster -o table`請執行以取出叢集上的詳細狀態。 根據結果：

* 如果叢集正在主動升級，請等候作業終止。 如果成功，請再次嘗試先前失敗的操作。
* 如果叢集升級失敗，請遵循上一節中所述的步驟。

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>我是否可以將叢集移至不同的訂用帳戶，或將我的訂用帳戶新增至新的租使用者？

如果您已將 AKS 叢集移至不同的訂用帳戶或主控訂用帳戶的叢集至新的租使用者，叢集將會因為遺失角色指派和服務主體許可權而失去功能。 AKS 不支援在訂用帳戶或租使用者**之間移動**叢集，因為此條件約束。

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>我在嘗試使用需要虛擬機器擴展集的功能時收到錯誤

*此疑難排解協助是從 aka.ms/aks-vmss-enablement 導向*

您可能會收到錯誤，指出您的 AKS 叢集不在虛擬機器擴展集上，如下列範例所示：

**AgentPool ' AgentPool ' 已將自動調整設定為 [已啟用]，但不在虛擬機器擴展集**

若要使用叢集自動調整程式或多個節點集區之類的功能，您必須建立使用虛擬機器擴展集的 AKS 叢集。 如果您嘗試使用相依于虛擬機器擴展集的功能，並將目標設為一般非虛擬機器擴展集 AKS 叢集，則會傳回錯誤。 虛擬機器擴展集支援目前在 AKS 中處於預覽狀態。

遵循適當檔中的*開始之前*的步驟，正確地註冊虛擬機器擴展集功能預覽並建立 AKS 叢集：

* [使用叢集自動調整程式](cluster-autoscaler.md)
* [建立和使用多個節點集區](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>AKS 資源和參數會強制執行哪些命名限制？

*此疑難排解協助是從 aka.ms/aks-naming-rules 導向*

命名限制是由 Azure 平臺和 AKS 所執行。 如果資源名稱或參數違反其中一個限制，則會傳回錯誤，要求您提供不同的輸入。 以下是適用的一般命名指導方針：

* AKS *MC_* 資源組名結合了資源組名與資源名稱。 自動產生的`MC_resourceGroupName_resourceName_AzureRegion`語法必須不超過80個字元。 如有需要，請縮短您的資源組名或 AKS 叢集名稱的長度。
* *DnsPrefix*的開頭和結尾都必須是英數位元值。 有效的字元包括英數位元值和連字號（-）。 *DnsPrefix*不能包含特殊字元，例如句號（.）。

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>我在嘗試建立、更新、調整、刪除或升級叢集時收到錯誤，因為另一個作業正在進行中，所以不允許該作業。

*此疑難排解協助是從 aka.ms/aks-pending-operation 導向*

當先前的作業仍在進行中時，叢集作業會受到限制。 若要取得叢集的詳細狀態，請使用`az aks show -g myResourceGroup -n myAKSCluster -o table`命令。 視需要使用您自己的資源群組和 AKS 叢集名稱。

根據叢集狀態的輸出：

* 如果叢集處於 [*成功*] 或 [*失敗*] 以外的任何布建狀態，請等候作業（*升級/更新/建立/調整/刪除/遷移*）終止。 當先前的作業完成時，請重新嘗試最新的叢集操作。

* 如果叢集的升級失敗，請遵循我[收到的錯誤：「我的叢集處於失敗狀態，而且升級或調整無法正常執行，直到修正為止](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)」。

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>我收到錯誤，指出我在嘗試建立新叢集但未傳入現有叢集時，找不到我的服務主體。

建立 AKS 叢集時，它需要服務主體來代表您建立資源。 AKS 提供在叢集建立時建立新的功能，但這需要 Azure Active Directory 在合理的時間內完全傳播新的服務主體，才能讓叢集在建立時成功。 當此傳播時間過長時，叢集將無法建立驗證，因為它找不到可用的服務主體來執行此動作。 

針對此使用下列因應措施：
1. 使用現有的服務主體，其已在不同區域之間傳播，並在叢集建立時存在於傳入 AKS。
2. 如果使用自動化腳本，請在建立服務主體與建立 AKS 叢集之間新增時間延遲。
3. 如果使用 Azure 入口網站，請在建立期間返回叢集設定，然後在幾分鐘後重試驗證頁面。