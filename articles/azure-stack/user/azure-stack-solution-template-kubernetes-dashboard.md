---
title: 存取 Azure Stack 中的 Kubernetes 儀表板 | Microsoft Docs
description: 了解如何存取 Azure Stack 中的 Kubernetes 儀表板
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: 76af6d6585d52eee99548f69c92bd414068fa28d
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259205"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>存取 Azure Stack 中的 Kubernetes 儀表板 

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件* 
> [!Note]   
> Azure Stack 上的 Kubernetes 處於預覽狀態。 預覽版目前不支援 Azure Stack 中斷連線的案例。 

Kubernetes 包含 Web 儀表板，您可以用來執行基本的管理作業。 此儀表板可讓您檢視您應用程式的基本健全狀況狀態和計量、建立和部署服務，以及編輯現有的應用程式。 這篇文章會示範如何在 Azure Stack 上設定 Kubernetes 儀表板。

## <a name="prerequisites-for-kubernetes-dashboard"></a>Kubernetes 儀表板的必要條件

* Azure Stack Kubernetes 叢集

    您需要將一個 Kubernetes 叢集部署到 Azure Stack。 如需詳細資訊，請參閱[部署 Kubernetes](azure-stack-solution-template-kubernetes-deploy.md)。

* SSH 用戶端

    您將需要 SSH 用戶端來安全地連線到您在叢集中的主要節點。 如果您使用的是 Windows，則可以使用 [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm)。 您將需要部署 Kubernetes 叢集時使用的私密金鑰。

* FTP (PSCP)

    您可能還需要支援 SSH 和 SSH 檔案傳輸通訊協定的 FTP 用戶端，以將憑證從主要節點傳送到 Azure Stack 管理電腦。 您可以使用 [FileZilla](https://filezilla-project.org/download.php?type=client)。 您將需要部署 Kubernetes 叢集時使用的私密金鑰。

## <a name="overview-of-steps-to-enable-dashboard"></a>啟用儀表板的步驟概觀

1.  從叢集中的主要節點匯出 Kubernetes 憑證。 
2.  將憑證匯入到 Azure Stack 管理電腦。
2.  開啟 Kubernetes Web 儀表板。 

## <a name="export-certificate-from-the-master"></a>從主要節點匯出憑證 

您可以從叢集中的主要節點擷取儀表板的 URL。

1. 從 Azure Stack 儀表板取得叢集主要節點的公用 IP 位址和使用者名稱。 若要取得此資訊︰

    - 登入 [Azure Stack 入口網站](https://portal.local.azurestack.external/)
    - 選擇 [所有服務] > [所有資源]。 在叢集資源群組中尋找主要節點。 主要節點名稱為 `k8s-master-<sequence-of-numbers>`。 

2. 在入口網站中開啟主要節點。 複製**公用 IP** 位址。 按一下 [連線]，在 [使用 VM 本機帳戶登入] 方塊中取得您的使用者名稱。 這與您在建立叢集時設定的使用者名稱相同。 使用公用 IP 位址而不是 [連線] 刀鋒視窗中列出的私人 IP 位址。

3.  開啟 SSH 用戶端來連線至主要叢集。 如果您正在使用 Windows，可以使用 [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) 來建立連線。 您將使用主要節點的公用 IP 位址 (也就是使用者名稱)，並新增您建立叢集時使用的私密金鑰。

4.  當終端機連線時，鍵入 `kubectl` 以開啟 Kubernetes 命令列用戶端。

5. 執行以下命令：

    ```Bash   
    kubectl cluster-info 
    ``` 
    尋找儀表板的 URL。 例如：`https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  擷取自我簽署的憑證，並將其轉換為 PFX 格式。 執行以下命令：

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  取得 **kube-system** 命名空間中的祕密清單。 執行以下命令：

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    請記下 kubernetes-dashboard-token-\<XXXXX> 值。 

8.  取得權杖，並將它儲存。 使用上一個步驟中的祕密值更新 `kubernetes-dashboard-token-<####>`。

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>匯入憑證

1. 開啟 Filezilla 並連線到主要節點。 您將需要：

    - 主要節點的公用 IP
    - 使用者名稱
    - 私人祕密
    - 使用 **SFTP - SSH 檔案傳輸通訊協定**

2. 將 `/etc/kubernetes/certs/client.pfx` 和 `/etc/kubernetes/certs/ca.crt` 複製到 Azure Stack 管理電腦。

3. 記下檔案的所在位置。 使用位置更新指令碼，然後使用提高權限的提示開啟 PowerShell。 執行更新後的指令碼：  

    ```PowerShell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>開啟 Kubernetes 儀表板 

1. 停用網頁瀏覽器上的快顯封鎖程式。

2. 將瀏覽器指向執行命令 `kubectl cluster-info` 時記下的 URL。 例如：https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy 
3. 選取用戶端憑證。
4. 輸入權杖。 
5. 重新連線到主要節點上的 bash 命令列，然後授與 `kubernetes-dashboard` 權限。 執行以下命令：

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    該指令碼會提供 `kubernetes-dashboard` 雲端系統管理員權限。 如需詳細資訊，請參閱[對於已啟用 RBAC 的叢集](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)。

您可以使用儀表板。 如需 Kubernetes 儀表板的詳細資訊，請參閱 [Kubernetes Web UI 儀表板](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Azure Stack Kubernetes 儀表板](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>後續步驟 

[將 Kubernetes 部署至 Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)  

[將 Kubernetes 叢集新增至 Marketplace (適用於 Azure Stack 操作員)](../azure-stack-solution-template-kubernetes-cluster-add.md)  

[Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
