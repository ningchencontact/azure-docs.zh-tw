---
title: 針對 Azure Stack 上的 Kubernetes 部署進行疑難排解 | Microsoft Docs
description: 了解如何針對 Azure Stack 上的 Kubernetes 部署進行疑難排解。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabvrigg
ms.date: 03/20/2019
ms.reviewer: waltero
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: 01a9405c98160149782ab2cf248f64818d631dde
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293782"
---
# <a name="troubleshoot-your-kubernetes-deployment-to-azure-stack"></a>針對 Azure Stack 上的 Kubernetes 部署進行疑難排解

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

> [!Note]  
> Azure Stack 上的 Kubernetes 處於預覽狀態。 預覽版目前不支援 Azure Stack 中斷連線的案例。

下列文章探討 Kubernetes 叢集的疑難排解方法。 您可以檢閱部署警示，並根據部署所需元素來檢閱部署狀態。 您可能需要從 Azure Stack 或裝載 Kubernetes 的 Linux VM 收集部署記錄。 您也可能需要與 Azure Stack 系統管理員合作，從系統管理端點擷取記錄。

## <a name="overview-of-deployment"></a>部署概觀

在針對叢集開始進行疑難排解之前，您可以先檢閱 Azure Stack Kubernetes 叢集部署程序。 此部署會使用 Azure Resource Manager 解決方案範本來建立 VM，並為叢集安裝 ACS 引擎。

### <a name="deployment-workflow"></a>部署工作流程

下圖顯示一般的叢集部署程序。

![部署 Kubernetes 程序](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>部署步驟

1. 從 Marketplace 項目收集輸入參數。

    輸入要用於設定 Kubernetes 叢集的值，包括：
    -  **使用者名稱**：Linux 虛擬機器的使用者名稱，而這些虛擬機器屬於 Kubernetes 叢集和 DVM。
    -  **SSH 公開金鑰**：用來對建立為 Kubernetes 叢集和 DVM 一部分的所有 Linux 機器進行授權的金鑰。
    -  **服務主體**：Kubernetes Azure 雲端提供者所使用的識別碼。 當您建立服務主體時，用戶端識別碼已識別為應用程式識別碼。 
    -  **用戶端密碼**：您在建立服務主體時所建立的金鑰。

2. 建立部署 VM 和自訂指令碼擴充功能。
    -  使用 Marketplace Linux 映像 **Ubuntu Server 16.04-LTS** 來建立部署 Linux VM。
    -  從 Marketplace 下載並執行自訂指令碼擴充功能。 此指令碼為 **Custom Script for Linux 2.0**。
    -  執行 DVM 自訂指令碼。 指令碼會執行下列工作：
        1. 從 Azure Resource Manager 中繼資料端點取得資源庫端點。
        2. 從 Azure Resource Manager 中繼資料端點取得 Active Directory 資源識別碼。
        3. 載入 ACS 引擎的 API 模型。
        4. 將 ACS 引擎部署到 Kubernetes 叢集，並將 Azure Stack 雲端設定檔儲存到 `/etc/kubernetes/azurestackcloud.json`。
3. 建立主要 VM。

4. 下載並執行自訂指令碼擴充功能。

5. 執行主要指令碼。

    指令碼會執行下列工作：
    - 安裝 etcd、Docker 和 Kubernetes 資源，例如 kubelet。 etcd 是分散式金鑰值存放區，可供您跨電腦叢集來儲存儲存。 Docker 支援簡易的作業系統層級虛擬化 (稱為容器)。 Kubelet 是在每個 Kubernetes 節點上執行的節點代理程式。
    - 設定 **etcd** 服務。
    - 設定 **kubelet** 服務。
    - 啟動 kubelet。 此工作涉及下列步驟：
        1. 啟動 API 服務。
        2. 啟動控制器服務。
        3. 啟動排程器服務。
6. 建立代理程式 VM。

7. 下載並執行自訂指令碼擴充功能。

7. 執行代理程式指令碼。 代理程式自訂指令碼會執行下列工作：
    - 安裝 **etcd**。
    - 設定 **kubelet** 服務。
    - 加入 Kubernetes 叢集。

## <a name="steps-for-troubleshooting"></a>疑難排解步驟

您可以在支援 Kubernetes 叢集的 VM 上收集記錄。 您也可以檢閱部署記錄。 您可能也需要詢問 Azure Stack 系統管理員來確認您需要使用的 Azure Stack 版本，並從 Azure Stack 取得與部署相關的記錄。

1. 檢閱[部署狀態](#review-deployment-status)，並從 Kubernetes 叢集中的主要節點[擷取記錄](#get-logs-from-a-vm)。
2. 務必使用最新版的 Azure Stack。 如果您不確定您使用的是哪一個版本，請連絡您的 Azure Stack 管理員。
3.  檢閱 VM 建立檔案。 您可能會有下列問題：  
    - 公開金鑰可能無效。 檢閱您建立的金鑰。  
    - 建立 VM 時可能觸發了內部錯誤，或觸發了建立錯誤。 有數個因素可能會造成錯誤，包括 Azure Stack 訂用帳戶的容量限制。
    - 確定 VM 的完整網域名稱 (FQDN) 是以重複的前置詞開頭。
4.  如果 VM **沒問題**，則請評估 DVM。 如果 DVM 有錯誤訊息：

    - 公開金鑰可能無效。 檢閱您建立的金鑰。  
    - 您必須連絡 Azure Stack 系統管理員，以使用特殊權限端點擷取 Azure Stack 的記錄。 如需詳細資訊，請參閱 [Azure Stack 診斷工具](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics)。
5. 如果您有任何關於部署的問題，可以在 [Azure Stack 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中張貼您的問題，或查看是否有人已回答過此問題。 

## <a name="review-deployment-status"></a>檢閱部署狀態

當您部署 Kubernetes 叢集時，可以檢閱部署狀態以檢查是否有任何問題。

1. 開啟 [Azure Stack 入口網站](https://portal.local.azurestack.external)。
2. 選取 [資源群組]，然後選取部署 Kubernetes 叢集時所使用之資源群組的名稱。
3. 選取 [部署]，然後選取 [部署名稱]。

    ![疑難排解](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  參閱 [疑難排解] 視窗。 所部署的每個資源都會提供下列資訊：
    
    | 屬性 | 說明 |
    | ----     | ----        |
    | 資源 | 資源名稱。 |
    | 類型 | 資源提供者和資源類型。 |
    | 狀態 | 項目的狀態。 |
    | TimeStamp | 時間的 UTC 時間戳記。 |
    | 作業詳細資料 | 作業詳細資料，例如作業所涉及的資源提供者、資源端點和資源的名稱。 |

    每個項目都有綠色或紅色的狀態圖示。

## <a name="review-deployment-logs"></a>檢閱部署記錄

如果 Azure Stack 入口網站未提供足夠的資訊來供您進行疑難排解或克服部署失敗，則下一個步驟是深入了解叢集記錄檔。 若要手動擷取部署記錄檔，您通常需要連接到叢集的其中一個主要虛擬機器。 更簡單的替代方法是下載並執行 Azure Stack 團隊提供的下列 [Bash 指令碼](https://aka.ms/AzsK8sLogCollectorScript)。 此指令碼會連線到 DVM 和叢集的虛擬機器，收集相關的系統和叢集記錄檔，並將它們下載回您的工作站。

### <a name="prerequisites"></a>必要條件

您需要在用來管理 Azure Stack 的電腦上具有 Bash 提示字元。 在 Windows 電腦上，您可以通過安裝 [Git for Windows](https://git-scm.com/downloads) 來取得 Bash 提示字元。 安裝完成後，在開始功能表中尋找 _Git Bash_。

### <a name="retrieving-the-logs"></a>擷取記錄檔

請遵循下列步驟來收集和下載叢集記錄檔：

1. 開啟 Bash 提示字元。 從 Windows 電腦，開啟 _Git Bash_ 或執行：`C:\Program Files\Git\git-bash.exe`。

2. 透過在 Bash 提示字元中執行下列命令來下載記錄檔收集器指令碼：

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. 尋找指令碼所需的資訊並執行它：

    | 參數           | 說明                                                                                                      | 範例                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host      | DVM 的公用 IP 或完整網域名稱 (FQDN)。 虛擬機器名稱以 `vmd-` 開頭。 | IP：192.168.102.38<br>DNS: vmd-myk8s.local.cloudapp.azurestack.external |
    | -h, --help  | 列印命令使用方式。 | |
    | -i, --identity-file | 建立 Kubernetes 叢集時，RSA 私密金鑰檔案會傳遞至 marketplace 項目。 需要遠端存取 Kubernetes 節點。 | C:\data\id_rsa.pem (Putty)<br>~/.ssh/id_rsa (SSH)
    | -m, --master-host   | Kubernetes 主要節點的公用 IP 或完整網域名稱 (FQDN)。 虛擬機器名稱以 `k8s-master-` 開頭。 | IP：192.168.102.37<br>FQDN：k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --user          | 建立 Kubernetes 叢集時，使用者名稱會傳遞至 marketplace 項目。 需要遠端存取 Kubernetes 節點 | azureuser (預設值) |


   當您新增參數值時，您的命令可能看起來可能像這樣：

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. 幾分鐘後，指令碼會將收集的記錄檔輸出到名為 `KubernetesLogs_{{time-stamp}}` 的目錄。 在那裡，您可以找到屬於該叢集的每個虛擬機器的目錄。

    記錄檔收集器指令碼也會在記錄檔中尋找錯誤，並在找到已知的問題時包含疑難排解步驟。 請確定您執行的是最新版本的指令碼，以增加尋找已知的問題的機率。

> [!Note]  
> 查看此 GitHub [存放庫](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) \(英文\)，以深入了解有關記錄檔收集器指令碼的詳細資訊。

## <a name="next-steps"></a>後續步驟

[將 Kubernetes 部署至 Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[將 Kubernetes 叢集新增至 Marketplace (適用於 Azure Stack 操作員)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
