---
title: 針對常見的 Azure Kubernetes Service 問題進行疑難排解
description: 了解在使用 Azure Kubernetes Service (AKS) 時，如何針對常見問題進行疑難排解並加以解決
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: f0b0ff3ff4ac742a7e850798c736eb31098f66e8
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966389"
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

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>我無法使用 kubectl 記錄取得記錄，或無法連線到 API 伺服器。 我收到 「 伺服器發生錯誤： 錯誤撥號後端： 撥接 tcp...」。 我該怎麼辦？

請確定未修改預設網路安全性群組，且連接埠 22 已開啟至 API 伺服器的連線。 檢查是否`tunnelfront`pod 中執行*kube 系統*命名空間使用`kubectl get pods --namespace kube-system`命令。 若非如此，請強制刪除該 Pod，而它會重新啟動。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>我正嘗試升級或調整，卻收到「訊息：不允許變更 'ImageReference' 屬性」錯誤。 如何修正此問題？

您可能會因為修改了 AKS 叢集內代理程式節點中的標記而收到此錯誤。 修改和刪除 MC_* 資源群組中資源的標記和其他屬性可能會導致非預期的結果。 在 AKS 叢集中修改 MC_* 群組下的資源會破壞服務層級目標 (SLO)。

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>我收到錯誤，我的叢集處於失敗狀態，並修正之前升級或調整將會無法運作

*從重新導向到這個疑難排解的協助 https://aka.ms/aks-cluster-failed*

當叢集有多種原因進入失敗的狀態時，就會發生此錯誤。 請遵循下列步驟來解決您的叢集失敗狀態，再重試先前失敗的作業：

1. 直到叢集共`failed`狀態，`upgrade`和`scale`作業會失敗。 常見的根本問題和解決方式包括：
    * 使用自動調整規模**不足，無法計算 (CRP) 配額**。 若要解決，第一次調整您的叢集回到穩定的目標狀態在配額內。 請遵循這些[步驟來要求的計算配額增加](../azure-supportability/resource-manager-core-quotas-request.md)之前嘗試相應增加一次除了初始的配額限制。
    * 調整叢集的進階網路功能及**不足的子網路 （網路） 資源**。 若要解決，第一次調整您的叢集回到穩定的目標狀態在配額內。 然後依照[這些步驟來要求資源配額增加](../azure-resource-manager/resource-manager-quota-errors.md#solution)之前嘗試相應增加一次除了初始的配額限制。
2. 一旦解決升級失敗的根本原因，您的叢集應該處於成功狀態。 狀態為已成功驗證後，請再試一次原始作業。

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>我收到錯誤時嘗試升級或小數位數指出我的叢集目前正在執行升級或升級失敗

*從重新導向到這個疑難排解的協助 https://aka.ms/aks-pending-upgrade*

發生作用中的升級作業，或升級已嘗試，但後續失敗時，叢集作業會受到限制。 若要診斷問題執行`az aks show -g myResourceGroup -n myAKSCluster -o table`擷取在叢集上的詳細的狀態。 根據結果：

* 如果叢集正在主動升級，等到作業會終止。 如果成功，重試先前失敗的作業。
* 如果叢集已經無法升級，請遵循上述步驟

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>可以我我將叢集移至不同的訂用帳戶或訂用帳戶與我的叢集，以新的租用戶？

如果您已將 AKS 叢集移至不同的訂用帳戶擁有新的租用戶的訂用帳戶的叢集，叢集將會遺失由於遺失的角色指派和服務主體權限的功能。 **AKS 不支援跨訂用帳戶或租用戶移動叢集**因為這個條件約束。

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>我收到錯誤，嘗試使用需要虛擬機器擴展集的功能

*Aka.ms/aks vmss-啟用從導向此疑難排解的協助*

您可能會收到錯誤指出您的 AKS 叢集不在虛擬機器擴展集，如下列範例：

**AgentPool 'agentpool' 已設定自動調整為已啟用，但不是虛擬機器擴展集上**

若要使用功能，例如叢集中自動調整程式或多個節點集區，AKS 叢集必須建立使用虛擬機器擴展集。 如果您嘗試使用取決於虛擬機器擴展集的功能，而且您為目標的一般、 非虛擬機器擴展集 AKS 叢集，則會傳回錯誤。 虛擬機器擴展集支援目前為預覽狀態，AKS 中。

請遵循*在您開始之前*預覽正確註冊虛擬機器擴展集功能適當的文件中的步驟，並建立 AKS 叢集：

* [使用叢集中自動調整程式](cluster-autoscaler.md)
* [建立及使用多個節點的集區](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>AKS 資源和參數，都會強制執行哪些命名的限制？

*Aka.ms/aks-命名-規則導向此疑難排解的協助*

命名限制的實作方式在 Azure 平台和 AKS 中。 如果資源名稱或參數會中斷這些限制的其中一個，會傳回錯誤，會要求您提供不同的輸入。 適用於下列的一般命名指導方針：

* AKS *MC_* 資源群組名稱和資源名稱，結合了資源群組名稱。 自動產生的語法`MC_resourceGroupName_resourceName_AzureRegion`必須不超過 80 個字元。 如有需要減少您的資源群組名稱或 AKS 叢集名稱的長度。
* *DnsPrefix*必須開頭和結尾為英數字元值。 有效字元包括英數字元值和連字號 （-）。 *DnsPrefix*不能包含特殊字元像是句號 （.）。

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>我嘗試建立、 更新、 調整、 刪除或不允許作業，因為另一個作業正在進行中的叢集升級時收到錯誤。

*此疑難排解的協助是從 aka.ms/aks-暫止的作業重新導向到*

先前的作業仍在進行中時，叢集作業會受到限制。 若要擷取您的叢集的詳細的狀態，請使用`az aks show -g myResourceGroup -n myAKSCluster -o table`命令。 視需要使用您自己的資源群組和 AKS 叢集名稱。

根據叢集狀態的輸出：

* 如果叢集不是任何佈建狀態*Succeeded*或是*失敗*，等到作業 (*升級 / 更新 / 建立 / 調整 / 刪除 / 移轉*) 會終止。 當先前的作業完成時，重試一次最新的叢集作業。

* 如果叢集升級失敗，請依照下列所述的步驟[我收到我的叢集處於失敗狀態的錯誤，而且升級或直到修正調整將無法運作](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)。