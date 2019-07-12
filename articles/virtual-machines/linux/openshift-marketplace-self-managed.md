---
title: 部署 OpenShift 容器平台自我管理的 Marketplace 供應項目在 Azure 中 |Microsoft Docs
description: 部署 OpenShift 容器平台自我管理的 Marketplace 供應項目在 Azure 中。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 9b981924dcaf715dd1d05d452b756a40b63f8dac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233091"
---
# <a name="configure-prerequisites"></a>設定必要條件

使用之前的 Marketplace 供應項目部署在 Azure 中的自我管理的 OpenShift 容器平台叢集，必須設定幾個先決條件。  讀取[OpenShift 必要條件](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-prerequisites)文章的指示來建立 ssh 金鑰 （而不需要複雜密碼）、 Azure 金鑰保存庫、 金鑰保存庫密碼和服務主體。

 
## <a name="deploy-using-the-marketplace-offer"></a>使用 Marketplace 供應項目進行部署

自我管理 OpenShift 容器平台將叢集部署到 Azure 的最簡單方式是使用[Azure Marketplace 供應項目](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)。

此選項是最簡單的但是它也受到限制自訂功能。 部署 OpenShift 容器平台 3.11.82 Marketplace 供應項目，並包含下列設定選項：

- **主要節點**：具有可設定執行個體類型的三 (3) 個主要節點。
- **基礎節點**：具有可設定執行個體類型的三 (3) 個基礎節點。
- **節點**：節點數目 （介於 1 到 9） 和執行個體類型皆可設定的。
- **磁碟類型**：使用受控磁碟。
- **網路**：支援新的或現有的網路和自訂的 CIDR 範圍。
- **CNS**：可以啟用 CNS。
- **計量**：您可以啟用 hawkular 計量。
- **記錄**：您可以啟用 EFK 記錄。
- **Azure 雲端提供者**：預設啟用，可以停用。

在 Azure 入口網站的左上方，按一下**建立資源**、 在搜尋方塊中輸入 'openshift 容器平台' 並按下 enter 鍵。

   ![新的資源搜尋](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

[結果] 頁面會開啟**Red Hat OpenShift 容器平台 Self-Managed**清單中。 

   ![新的資源搜尋結果](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

按一下 供應項目，以檢視詳細資料的供應項目。 若要部署此供應項目，按一下**建立**。 會出現在 UI 中，輸入必要的參數。 第一個畫面是**基本概念**刀鋒視窗。

   ![供應項目的 [標題] 頁面](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**基本概念**

若要取得任何輸入參數的說明，請暫留***我***參數名稱旁邊。

輸入的輸入參數的值，然後按一下**確定**。

| 輸入的參數 | 參數描述 |
|-----------------------|-----------------|
| VM 系統管理員使用者名稱 | 若要在所有 VM 執行個體上建立的系統管理員使用者 |
| SSH 公開金鑰系統管理使用者 | 用來登入 VM 的 SSH 公用金鑰不能有複雜密碼 |
| Subscription | 若要將叢集部署到 azure 訂用帳戶 |
| 資源群組 | 建立新的資源群組，或選取現有的空白資源群組的叢集資源 |
| Location | 若要將叢集部署到 azure 區域 |

   ![提供基本概念刀鋒視窗](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**基礎結構設定**

輸入的輸入參數的值，然後按一下**確定**。

| 輸入的參數 | 參數描述 |
|-----------------------|-----------------|
| OCP 叢集名稱前置詞 | 叢集用來設定所有節點的主機名稱的前置詞。 介於 1 到 20 個字元 |
| 主要節點大小 | 接受預設 VM 大小，或按一下**變更大小**選取不同的 VM 大小。  選取適當的工作負載的 VM 大小 |
| 基礎結構節點大小 | 接受預設 VM 大小，或按一下**變更大小**選取不同的 VM 大小。  選取適當的工作負載的 VM 大小 |
| 應用程式節點數目 | 接受預設 VM 大小，或按一下**變更大小**選取不同的 VM 大小。  選取適當的工作負載的 VM 大小 |
| 應用程式的節點大小 | 接受預設 VM 大小，或按一下**變更大小**選取不同的 VM 大小。  選取適當的工作負載的 VM 大小 |
| 防禦主機大小 | 接受預設 VM 大小，或按一下**變更大小**選取不同的 VM 大小。  選取適當的工作負載的 VM 大小 |
| 新的或現有的虛擬網路 | 建立新的 vNet （預設值），或使用現有的 vNet |
| 選擇預設 CIDR 設定或自訂 IP 範圍 (CIDR) | 接受預設的 CIDR 範圍或選取**自訂 IP 範圍**和輸入自訂的 CIDR 資訊。  預設設定會建立具有 vNet 10.0.0.0/14，10.1.0.0/16，基礎結構的主要子網路的 CIDR 10.2.0.0/16，子網路和計算和 cn 10.3.0.0/16 子網路 |
| Key Vault 資源群組名稱 | 包含金鑰保存庫的資源群組名稱 |
| Key Vault 名稱 | 包含密碼與金鑰保存庫名稱 ssh 私密金鑰。  只有英數字元及虛線允許的以及介於 3 到 24 個字元之間 |
| 祕密名稱 | 包含的祕密名稱 ssh 私密金鑰。  允許只有英數字元和連字號 |

   ![供應項目基礎結構 刀鋒視窗](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**變更大小**

若要選取不同的 VM 大小，請按一下***變更大小***。  VM 選取範圍視窗隨即開啟。  選取您想要然後按一下 VM 大小**選取**。

   ![選取 VM 大小](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**現有的虛擬網路**

| 輸入的參數 | 參數描述 |
|-----------------------|-----------------|
| 現有的虛擬網路名稱 | 現有的 vNet 的名稱。 |
| 主要節點的子網路名稱 | 主要節點的現有子網路的名稱。  必須包含至少 16 個 IP 位址，並遵循 RFC 1918 |
| 子網路名稱的基礎結構節點 | 名稱的現有基礎結構節點的子網路。  必須包含至少 32 個 IP 位址，並遵循 RFC 1918 |
| 計算 和 cn 節點的子網路名稱 | 計算 和 cn 節點的現有子網路的名稱。  必須包含至少 32 個 IP 位址，並遵循 RFC 1918 |
| 現有的虛擬網路的資源群組 | 包含現有的 vNet 的資源群組名稱 |

   ![提供基礎結構存在的 vnet](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**自訂的 IP 範圍**

| 輸入的參數 | 參數描述 |
|-----------------------|-----------------|
| 虛擬網路位址範圍 | Vnet 自訂 CIDR |
| 包含主要節點的子網路的位址範圍 | 主要子網路的自訂 CIDR |
| 包含基礎結構節點的子網路的位址範圍 | 基礎結構子網路的自訂 CIDR |
| 包含計算] 和 [cn 節點的子網路位址範圍 | 自訂的計算] 和 [cn 節點的 CIDR |

   ![提供基礎結構自訂的 IP 範圍](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift 容器平台**

輸入的輸入參數值，然後按一下  **確定**

| 輸入的參數 | 參數描述 |
|-----------------------|-----------------|
| OpenShift 系統管理員使用者密碼 | 初始的 OpenShift 使用者密碼。  此使用者也會是叢集系統管理員 |
| 確認 OpenShift 系統管理員使用者密碼 | 重新輸入 OpenShift 系統管理員使用者密碼 |
| Red Hat 訂用帳戶管理員使用者名稱 | 使用者名稱，以存取您的 Red Hat 訂用帳戶或組織識別碼。  此認證用來註冊您的訂用帳戶的 RHEL 執行個體，並不會儲存由 Microsoft 或 Red Hat |
| Red Hat 訂用帳戶管理員使用者密碼 | 若要存取您的 Red Hat 訂用帳戶或啟用金鑰的密碼。  此認證用來註冊您的訂用帳戶的 RHEL 執行個體，並不會儲存由 Microsoft 或 Red Hat |
| Red Hat Subscription Manager OpenShift 集區識別碼 | 包含 OpenShift 容器平台權利的集區識別碼。 請確定您有足夠的權利的 OpenShift 容器平台的叢集安裝 |
| 訊息代理程式的 Red Hat Subscription Manager OpenShift 集區識別碼 / 主要節點 | 集區包含 OpenShift 容器平台權利的 Broker 識別碼 / 主要節點。 請確定您有足夠的權利的 OpenShift 容器平台的叢集安裝。 如果不使用訊息代理程式 / 主要集區識別碼中，輸入應用程式節點的集區識別碼 |
| 設定 Azure 雲端提供者 | 將 OpenShift 設定為使用 Azure 雲端提供者。 如果使用 Azure 磁碟的永續性磁碟區連接所需。  預設為 [是] |
| Azure AD 服務主體用戶端識別碼 GUID | Azure AD 服務主體用戶端識別碼的 GUID-也稱為 AppID。 當設定 Azure 雲端提供者設定為，才需要 **[是]** |
| Azure AD 服務主體用戶端識別碼的密碼 | Azure AD 服務主體用戶端識別碼密碼。 當設定 Azure 雲端提供者設定為，才需要 **[是]** |
 
   ![供應項目 OpenShift 刀鋒視窗](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**其他設定**

[其他設定] 刀鋒視窗可讓 glusterfs 儲存體、 CN 組態記錄、 度量和路由器的子網域。  預設不會安裝上述任何選項，並使用 nip.io 路由器子網域為基於測試目的。 啟用 CN，會使用三個額外的附加磁碟將裝載 glusterfs pod 安裝三個額外的計算節點。  

輸入的輸入參數值，然後按一下  **確定**

| 輸入的參數 | 參數描述 |
|-----------------------|-----------------|
| 設定容器的原生儲存體 (CN) | 安裝的 CN 中的 OpenShift 叢集，並啟用做為儲存體。 將成為預設值，如果已停用 Azure 提供者 |
| 設定叢集記錄 | 在叢集中安裝 EFK 記錄功能。  調整基礎結構的適當節點主機 EFK pod 時，大小 |
| 設定叢集計量 | OpenShift 叢集安裝 Hawkular 計量。  調整基礎結構的適當節點主機 Hawkular 計量 pod 時，大小 |
| 預設路由器的子網域 | 選取用於測試或生產環境中輸入您自己的子網域的自訂 nipio |
 
   ![提供額外的刀鋒視窗](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**其他設定-額外的參數**

| 輸入的參數 | 參數描述 |
|-----------------------|-----------------|
| (CN)節點大小 | 接受預設節點大小，或選取**變更大小**選取新的 VM 大小 |
| 輸入您的自訂子網域 | 要用於將 OpenShift 叢集上的路由器透過應用程式公開的自訂路由網域。  請務必建立適當的萬用字元 DNS 項目] |
 
   ![提供額外的 cn 安裝](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**摘要**

在這個階段，來檢查核心配額不足，無法部署為叢集選取的 Vm 總數，就會發生驗證。  檢閱所輸入的所有參數。  如果可接受輸入，請按一下**確定**以繼續。

   ![供應項目摘要刀鋒視窗](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**購買**

確認 [購買] 頁面上的連絡資訊，然後按一下**採購**接受使用規定，並開始部署 OpenShift 容器平台叢集。

   ![供應項目購買的刀鋒視窗](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>連線到 OpenShift 叢集

部署完成時，請從部署的輸出區段中擷取連線。 藉由連線至 OpenShift 主控台與您的瀏覽器**OpenShift 主控台 URL**。 您也可以防禦主機來透過 ssh 連線。 在以下範例中，管理員使用者名稱是 clusteradmin，而防禦公用 IP DNS FQDN 是 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com：

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清除資源

當不再需要資源時，您可以使用 [az group delete](/cli/azure/group) 命令，移除資源群組、OpenShift 叢集和所有相關資源。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>後續步驟

- [部署後工作](./openshift-post-deployment.md)
- [Azure 中的 OpenShift 部署疑難排解](./openshift-troubleshooting.md)
- [開始使用 OpenShift 容器平台](https://docs.openshift.com/container-platform) \(英文\)

