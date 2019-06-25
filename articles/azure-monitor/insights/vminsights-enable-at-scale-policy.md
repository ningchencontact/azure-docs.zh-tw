---
title: 使用 Azure 原則來啟用虛擬機器的 Azure 監視器 |Microsoft Docs
description: 本文說明如何為 Vm 啟用多個 Azure 虛擬機器的 Azure 監視器，或使用 Azure 原則的虛擬機器擴展集。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: fda79a7ea361a6b44798d18b79ffd763055087a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122642"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>使用 Azure 原則的 Vm （預覽） 來啟用 Azure 監視器

這篇文章說明如何啟用 Azure 監視器的 Vm （預覽） 適用於 Azure 的虛擬機器或虛擬機器擴展集使用 Azure 原則。 在此程序結束時，您將會順利設定啟用相依性代理程式與 Log Analytics 並找出不相容的虛擬機器。

若要探索、 管理及啟用所有您的 Azure 虛擬機器或虛擬機器擴展集 vm 的 Azure 監視器，您可以使用 Azure 原則或 Azure PowerShell。 Azure 原則是因為您可以管理原則定義，以有效地管理您的訂用帳戶，以確保一致的合規性，並自動啟用新佈建 Vm，我們建議的方法。 這些原則定義：

* 部署 Log Analytics 代理程式和 Dependency Agent。
* 報告合規性結果。
* 補救不相容的 vm。

如果您想要完成這些工作，使用 Azure PowerShell 或 Azure Resource Manager 範本，請參閱[啟用使用 Azure PowerShell 或 Azure Resource Manager 範本的 Vm （預覽） 的 Azure 監視器](vminsights-enable-at-scale-powershell.md)。

## <a name="manage-policy-coverage-feature-overview"></a>管理原則涵蓋範圍功能概觀

一開始，Azure 原則來管理和部署 Vm 的 Azure 監視器的原則定義的體驗已完成從 Azure 原則以獨佔方式。 管理原則涵蓋範圍功能會讓它更簡單且更輕鬆地探索、 管理及啟用大規模**啟用 Vm 的 Azure 監視器**計劃，其中包含先前所述的原則定義。 您可以從這項新功能**開始**適用於 Vm 的 Azure 監視器中的索引標籤。 選取 **管理的原則涵蓋範圍**來開啟**Vm 原則涵蓋範圍的 Azure 監視器**頁面。

![從開始到 Vm 索引標籤的 azure 監視器](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

從這裡開始，您可以檢查，及管理您的管理群組和訂用帳戶計劃的涵蓋範圍。 您可以了解幾個 Vm 存在於每個管理群組和訂用帳戶以及其合規性狀態。

![Vm 管理原則頁面的 azure 監視器](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

這項資訊是可協助您規劃及執行 Azure 監視您控管的案例，適用於 Vm 從一個集中位置。 雖然 Azure 原則提供相容性 檢視，在範圍內指派原則或方案時，這個新的頁面，您可以探索其中原則或方案未指派，並將它指派在位置。 所有動作，都例如指派、 檢視及直接編輯重新導向至 Azure 原則。 **Vm 原則涵蓋範圍的 Azure 監視器**頁面是一種擴充和整合的體驗，只有 initiative**啟用 Vm 的 Azure 監視器**。

從這個頁面上，您也可以設定您的 Log Analytics 工作區的 Azure 監視器的 Vm，其中：

- 安裝的安裝服務對應 」 和 「 基礎結構深入解析解決方案。
- 可讓效能圖表、 活頁簿，和您的自訂記錄檔查詢和警示所使用的作業系統效能計數器。

![適用於 Vm 的 azure 監視器設定的工作區](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

此選項未與任何原則的動作。 它可提供簡單的方式，來滿足[必要條件](vminsights-enable-overview.md)所需的 Vm 啟用 Azure 監視器。  

### <a name="what-information-is-available-on-this-page"></a>哪些資訊是可由此頁面取得？
下表提供原則的涵蓋範圍 頁面，以及如何解譯它所展示的資訊的細目。

| 函式 | 描述 | 
|----------|-------------| 
| **範圍** | 管理群組和訂用帳戶或管理群組階層中向下鑽研的能力繼承存取權。|
| **角色** | 您的範圍，可能是讀取器、 擁有者或參與者的角色。 在某些情況下，它可能會出現空白，表示您可能必須存取的訂用帳戶，但不是到管理群組其所屬項目。 其他資料行中的資訊會根據您的角色而有所不同。 角色會負責判斷您所見的資料和指派原則或計劃 （擁有者）、 編輯，或檢視合規性方面，您可以執行的動作的索引鍵。 |
| **Vm 數總計** | 在該範圍內的 Vm 數目。 管理群組，它是 Vm 的訂用帳戶或子管理群組之下巢狀的總和。 |
| **指派的涵蓋範圍** | 係由其原則或方案的 Vm 的百分比。 |
| **指派狀態** | 您的原則或方案作業的狀態資訊。 |
| **相容的 Vm** | 符合原則或方案的 Vm 數目。 Initiative**啟用 Vm 的 Azure 監視器**，這是具有 Log Analytics 代理程式和相依性代理程式的 Vm 數目。 在某些情況下，它可能會出現空白因為沒有指派、 沒有 Vm 或沒有足夠的權限。 資訊不提供**合規性狀態**。 |
| **合規性** | 整體的合規性數目是不同的資源符合分割之所有不同資源的總和的總和。 |
| **合規性狀態** | 在您的原則或方案指派的合規性狀態的資訊。|

當您指派原則或方案時，所列的範圍或子集，就可能會是指派中選取的範圍。 比方說，您可能已建立的指派訂用帳戶 （原則的範圍），而不是管理群組 （涵蓋範圍的範圍）。 在此案例中的值**指派涵蓋範圍**指出除以涵蓋範圍的範圍中的虛擬機器的計畫範圍的原則中的 Vm。 在其他情況下，您可能會有一些 Vm、 資源群組或訂用帳戶從排除原則範圍。 如果此值為空白，表示可能是原則或方案不存在，或您沒有權限。 資訊不提供**指派狀態**。

## <a name="enable-by-using-azure-policy"></a>使用 Azure 原則啟用

若要在您的租用戶中，使用 Azure 原則啟用適用於 VM 的 Azure 監視器：

- 指派給範圍的計劃： 管理群組、 訂用帳戶或資源群組。
- 檢閱並補救合規性結果。

如需有關指派 Azure 原則的詳細資訊，請參閱 [Azure 原則概觀](../../governance/policy/overview.md#policy-assignment)，並在繼續進行之前，檢閱[管理群組概觀](../../governance/management-groups/index.md)。

### <a name="policies-for-azure-vms"></a>Azure Vm 的原則

Azure VM 的原則定義詳列於下表。

|Name |描述 |類型 |
|-----|------------|-----|
|[預覽]：啟用適用於 VM 的 Azure 監視器 |啟用 Azure 監視器中的虛擬機器指定的範圍 （管理群組、 訂用帳戶或資源群組）。 請使用 Log Analytics 工作區作為參數。 |方案 |
|[預覽]：稽核相依性代理程式部署 – VM 映像 (OS) 未列出 |如果清單中未定義的 VM 映像 (OS)，而且未安裝代理程式會回報為不相容的 Vm。 |原則 |
|[預覽]：Audit Log Analytics 代理程式部署 – VM 映像 (OS) 未列出 |如果清單中未定義的 VM 映像 (OS)，而且未安裝代理程式會回報為不相容的 Vm。 |原則 |
|[預覽]：部署適用於 Linux Vm 的相依性代理程式 |如果 VM 映像 (OS) 定義在清單中，而且未安裝代理程式，請部署適用於 Linux Vm 的相依性代理程式。 |原則 |
|[預覽]：部署適用於 Windows Vm 的相依性代理程式 |如果 VM 映像 (OS) 定義在清單中，而且未安裝代理程式，請部署適用於 Windows Vm 的相依性代理程式。 |原則 |
|[預覽]：部署適用於 Linux Vm 的 Log Analytics 代理程式 |如果 VM 映像 (OS) 定義在清單中，而且未安裝代理程式，請部署適用於 Linux Vm 的 Log Analytics 代理程式。 |原則 |
|[預覽]：部署適用於 Windows Vm 的 Log Analytics 代理程式 |如果 VM 映像 (OS) 定義在清單中，而且未安裝代理程式，請部署適用於 Windows Vm 的 Log Analytics 代理程式。 |原則 |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>原則的 Azure 虛擬機器擴展集

下表詳列的 Azure 虛擬機器擴展集的原則定義。

|Name |描述 |類型 |
|-----|------------|-----|
|[預覽]：啟用虛擬機器擴展集的 Azure 監視器 |啟用 Azure 監視器 （管理群組、 訂用帳戶或資源群組） 的指定範圍中的虛擬機器擴展集。 請使用 Log Analytics 工作區作為參數。 注意：如果擴展集的升級原則設定為手動，呼叫升級擴充功能套用至集合中的所有 Vm。 在 CLI 中，這是 az vmss update-執行個體。 |方案 |
|[預覽]：稽核相依性代理程式部署在虛擬機器擴展集 – VM 映像 (OS) 未列出 |報告虛擬機器擴展集為不相容，如果清單中未定義的 VM 映像 (OS)，而且未安裝代理程式。 |原則 |
|[預覽]：Audit Log Analytics 代理程式部署在虛擬機器擴展集 – VM 映像 (OS) 未列出 |報告虛擬機器擴展集為不相容，如果清單中未定義的 VM 映像 (OS)，而且未安裝代理程式。 |原則 |
|[預覽]：部署 Linux 虛擬機器擴展集的相依性代理程式 |部署適用於 Linux 虛擬機器擴展集，如果 VM 映像 (OS) 定義在清單中，而且未安裝代理程式的相依性代理程式。 |原則 |
|[預覽]：部署 Windows 虛擬機器擴展集的相依性代理程式 |部署的 Windows 虛擬機器擴展集，如果 VM 映像 (OS) 定義在清單中，而且未安裝代理程式的相依性代理程式。 |原則 |
|[預覽]：部署 Linux 虛擬機器擴展集的 Log Analytics 代理程式 |部署適用於 Linux 虛擬機器擴展集，如果 VM 映像 (OS) 定義在清單中，而且未安裝代理程式的 Log Analytics 代理程式。 |原則 |
|[預覽]：部署 Windows 虛擬機器擴展集的 Log Analytics 代理程式 |部署的 Windows 虛擬機器擴展集，如果 VM 映像 (OS) 定義在清單中，而且未安裝代理程式的 Log Analytics 代理程式。 |原則 |

獨立原則 (未包含在計畫中) 如下所述：

|Name |描述 |類型 |
|-----|------------|-----|
|[預覽]：Audit Log Analytics 工作區的 VM – 報告不相符 |如果他們未記錄到原則或方案指派中指定的 Log Analytics 工作區，報告為不相容 Vm。 |原則 |

### <a name="assign-the-azure-monitor-initiative"></a>指派 Azure 監視器計畫
若要建立原則指派，從**Vm 原則涵蓋範圍的 Azure 監視器**頁面上，請遵循下列步驟。 若要了解如何完成這些步驟，請參閱 [從 Azure 入口網站建立原則指派](../../governance/policy/assign-policy-portal.md)。

當您指派原則或方案時，在指派中選取的範圍可能是此處所列的範圍或子集。 比方說，您可能已建立的指派訂用帳戶 （原則的範圍），而不管理群組 （涵蓋範圍的範圍）。 在此情況下，涵蓋範圍的百分比表示的原則或方案的範圍中的 Vm 除以涵蓋範圍中的 Vm。 在其他情況下，您可能會有一些 Vm，或資源群組或訂用帳戶從排除原則範圍。 如果是空白的表示可能是原則或方案並不存在，或者您沒有權限。 資訊不提供**指派狀態**。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 Azure 入口網站中，選取 [監視]  。 

3. 選擇 [Insights]  區段中的 [虛擬機器 (預覽)]  。
 
4. 選取 [**開始**] 索引標籤。在頁面上，選取**管理的原則涵蓋範圍**。

5. 從資料表中，選取管理群組或訂用帳戶。 選取 **範圍**藉由選取省略符號 （...）。在範例中，範圍會限制原則指派給群組的強制執行的虛擬機器。

6. 在  **Azure 原則指派**頁面上，它已預先填入之開發案**啟用 Vm 的 Azure 監視器**。 
    **指派名稱**方塊會自動填入方案的名稱，但您可以加以變更。 您也可以新增選擇性的描述。 **指派**根據登入者自動填入方塊。 此為選用值。

7. (選擇性) 若要從範圍中移除一或多個資源，請選取 [排除項目]  。

8. 在支援之區域的 [Log Analytics 工作區]  下拉式清單中，選取一個工作區。

   > [!NOTE]
   > 如果工作區超出指派的範圍之外，請將 [Log Analytics 參與者]  權限授與原則指派的主體識別碼。 如果您不這樣做，您可能會看到部署失敗，例如`The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`若要授與存取權，請檢閱[如何手動設定受管理的身分識別](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)。
   > 
   >  **受控身分識別**因為指派的計劃中包含的原則，選取核取方塊*deployIfNotExists*效果。
    
9. 在 [管理身分識別位置]  下拉式清單中，選取適當的區域。

10. 選取 [指派]  。

您可以建立指派之後, **Vm 原則涵蓋範圍的 Azure 監視器**頁面上更新**指派涵蓋範圍**，**指派狀態**，**相容Vm**，並**合規性狀態**以反映所做的變更。 

下列矩陣對應每個可能的合規性計劃的狀態。  

| 合規性狀態 | 描述 | 
|------------------|-------------|
| **符合規範** | 在範圍內的所有 Vm 都都 「 Log Analytics 和相依性代理程式部署給他們。|
| **不符合規範** | 並非所有的 Vm，在範圍中具有 Log Analytics 和相依性代理程式部署給他們，而且可能需要修復。|
| **未啟動** | 已新增新的指派。 |
| **Lock** | 您沒有足夠的權限到管理群組。<sup>1</sup> | 
| 空白  | 沒有原則指派。 | 

<sup>1</sup>如果您沒有管理群組的存取權，要求以存取擁有者。 或者，檢視合規性及管理透過子管理群組或訂用帳戶的指派。 

下表對應計劃的每個可能的指派狀態。

| 指派狀態 | 描述 | 
|------------------|-------------|
| 「成功」  | 在範圍內的所有 Vm 都都 「 Log Analytics 和相依性代理程式部署給他們。|
| **警告** | 訂用帳戶未在管理群組。|
| **未啟動** | 已新增新的指派。 |
| **Lock** | 您沒有足夠的權限到管理群組。<sup>1</sup> | 
| 空白  | 沒有 Vm 存在，或未指派的原則。 | 
| **Action** | 指派原則或編輯指派。 | 

<sup>1</sup>如果您沒有管理群組的存取權，要求以存取擁有者。 或者，檢視合規性及管理透過子管理群組或訂用帳戶的指派。

## <a name="review-and-remediate-the-compliance-results"></a>檢閱和補救合規性結果

下列範例是針對 Azure VM，但它也適用於虛擬機器擴展集。 若要了解如何檢閱合規性結果，請參閱[識別不符合規範結果](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)。 在  **Vm 原則涵蓋範圍的 Azure 監視器**頁面上，從資料表中選取 管理群組或訂用帳戶。 選取 **檢視合規性**藉由選取省略符號 （...）。   

![Azure VM 的原則合規性](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

根據包含計劃的原則的結果，Vm 會回報為不符合規範，在下列情況：

* Log Analytics 代理程式 」 或 「 相依性代理程式就不會部署。  
    此對於包含現有 VM 的範圍來說是典型案例。 若要減少這種情況，將部署必要的代理程式所[建立補救工作](../../governance/policy/how-to/remediate-resources.md)不符合規範的原則。  
    - [預覽]: Deploy Dependency agent for Linux VMs
    - [預覽]: Deploy Dependency agent for Windows VMs
    - [預覽]: Deploy Log Analytics agent for Linux VMs
    - [預覽]: Deploy Log Analytics agent for Windows VMs

* VM 映像 (OS) 不被識別原則定義中。  
    部署原則的準則僅包含從已知 Azure VM 映像部署的 VM。 請參閱文件以了解 VM OS 是否受到支援。 如果不受支援，請複製部署原則，並加以更新/修改，使映像符合規範。  
    - [預覽]：稽核相依性代理程式部署 – VM 映像 (OS) 未列出
    - [預覽]：Audit Log Analytics 代理程式部署 – VM 映像 (OS) 未列出

* VM 未登入指定的 Log Analytics 工作區。  
    計畫範圍內的部分 VM 可以登入未在原則指派中指定的 Log Analytics 工作區。 此原則是以找出哪些 Vm 要回報不符合規範的工作區的工具。  
    - [預覽]: Audit Log Analytics workspace for VM – Report mismatch

## <a name="edit-an-initiative-assignment"></a>編輯方案指派

在任何時候您將方案指派給管理群組或訂用帳戶之後, 您可以編輯它，來修改下列屬性：

- 作業的名稱
- 描述
- 指派
- Log Analytics 工作區
- 例外狀況

## <a name="next-steps"></a>後續步驟

現在您的虛擬機器啟用監視，這項資訊是使用適用於 Vm 的 Azure 監視器可用於分析。 

- 若要了解如何使用健全狀況的功能，請參閱[Vm 的健全狀況檢視 Azure 監視器](vminsights-health.md)。 
- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。 
- 若要找出瓶頸並與您的 VM 效能的整體使用率，請參閱[檢視 Azure 虛擬機器效能](vminsights-performance.md)。 
- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。
