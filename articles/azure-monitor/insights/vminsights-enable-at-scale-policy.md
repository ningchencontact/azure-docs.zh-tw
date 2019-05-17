---
title: 使用 Azure 原則的 Vm 中啟用 Azure 監視器 |Microsoft Docs
description: 本文說明如何啟用 Azure 監視器適用於 Vm 的多個 Azure 虛擬機器或虛擬機器擴展集使用 Azure 原則。
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
ms.openlocfilehash: 9664ad5272ffeb55bd85e3d4c4f4b70d05e97702
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524141"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-policy"></a>使用 Azure 原則的 vm （預覽） 中啟用 Azure 監視器

這篇文章說明如何為 Azure 虛擬機器或使用 Azure 原則的虛擬機器擴展集 Vm （預覽） 啟用 Azure 監視器。 在此程序結束時，您將會順利設定啟用 Log Analytics 和相依性代理程式，並找出不符合規範的虛擬機器。

若要探索、 管理及啟用所有您的 Azure 虛擬機器或虛擬機器擴展集 vm 的 Azure 監視器，您可以使用 Azure 原則或 Azure PowerShell。 Azure 原則是建議的方法，因為您可以管理原則定義，以有效地管理您的訂用帳戶，以確保一致的合規性，並自動啟用新佈建 Vm。 這些原則定義：

* 部署 Log Analytics 代理程式和 Dependency Agent。
* 報告合規性結果。
* 補救不相容的 VM。

如果您想要使用 Azure PowerShell 或 Azure Resource Manager 範本完成這項作業，請參閱[使用 Azure PowerShell 或 Resource Manager 範本啟用](vminsights-enable-at-scale-powershell.md)。 

## <a name="manage-policy-coverage-feature-overview"></a>管理原則涵蓋範圍功能概觀

原先的體驗與 Azure 原則來管理和部署 Vm 的 Azure 監視器的原則定義是從 Azure 原則以獨佔方式執行。 具有**管理的原則涵蓋範圍**功能，它可讓它更簡單且更輕鬆地探索、 管理及啟用大規模**啟用 Vm 的 Azure 監視器**計劃，其中包含原則定義先前所述。 您可以從這項新功能**開始**Vm 的 Azure 監視器中的索引標籤上，選取**管理原則涵蓋範圍**。 它會開啟 [Vm 原則涵蓋範圍] 頁面。 

![從開始到 Vm 索引標籤的 azure 監視器](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

從這裡您可以檢查和管理您的管理群組和訂用帳戶，計劃的涵蓋範圍，以及了解幾個 Vm 存在於每個管理群組和訂用帳戶以及其合規性狀態。   

![Vm 管理原則頁面的 azure 監視器](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

這項資訊是有用的計劃和執行 Azure 監視您控管的案例，適用於 Vm 從一個集中位置。 雖然 Azure 原則提供相容性 檢視，當原則/計畫指派至範圍，與此新的頁面，您可以探索原則/計畫未指派的位置，並將它指派在就地。 所有動作 （指派、 檢視、 編輯） 直接加入 Azure 原則重新導向。 Vm 原則涵蓋範圍 頁面的 azure 監視器 」 是一種擴充和整合的體驗，只有 initiative**啟用 Vm 的 Azure 監視器**。 

從這個頁面您也可以設定您的 Log Analytics 工作區的 Azure 監視器的 Vm，便會執行下列：

- 安裝安裝服務對應及基礎結構深入解析解決方案
- 可讓效能圖表、 活頁簿，和您的自訂記錄檔查詢和警示所使用的作業系統效能計數器。

![適用於 Vm 的 azure 監視器設定的工作區](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

此選項未與任何原則的動作，並且可提供簡單的方式，來滿足[必要條件](vminsights-enable-overview.md)所需的 Vm 啟用 Azure 監視器。  

### <a name="what-information-is-available-on-this-page"></a>哪些資訊是可由此頁面取得？
下表提供原則的涵蓋範圍 頁面中會顯示哪些資訊以及如何解譯它的細目。

| 函式 | 說明 | 
|----------|-------------| 
| **範圍** | 管理群組和訂用帳戶或繼承的權限能夠向下切入透過管理群組階層。|
| **角色** | 您的角色的範圍中，可能是讀取器擁有者或參與者。 在某些情況下，它可能會出現空白，表示您可能必須存取訂用帳戶，但不是到管理群組其所屬項目。 判斷您所見的資料和指派計劃原則 （擁有者）、 編輯，或檢視合規性方面，您可以執行的動作的索引鍵後，即其他資料行中的資訊會因您的角色。 |
| **Vm 數總計** | 在該範圍內的 Vm 數目。 管理群組，它是 Vm 的訂用帳戶和/或子管理群組之下巢狀的總和。 |
| **指派的涵蓋範圍** | Vm 計劃/原則所涵蓋的百分比。 |
| **指派狀態** | 在本專欄中，您可以找到狀態的相關資訊，您的原則 / 計畫指派。 |
| **相容的 Vm** | 原則/initiative 相容的 Vm 數目。 Initiative**啟用 Vm 的 Azure 監視器**這是具有 Log Analytics 代理程式和相依性代理程式的 Vm 數目。 在某些情況下，它可能會出現空白，因為沒有指派，或沒有 Vm 或沒有足夠的權限。 在 合規性狀態下，會提供資訊。 |
| **合規性** | 整體的合規性數目是之所有不同資源的總和除以相容的不同資源的總和。 |
| **合規性狀態** | 您的原則的合規性狀態的詳細資訊 / 計畫指派。|

當您指派 initiative/原則時，所列的範圍或子集，就可能會是指派中選取的範圍。 比方說，您可能已建立的指派訂用帳戶 （原則的範圍），而不是管理群組 （涵蓋範圍的範圍）。 在此案例中的值**指派涵蓋範圍**表示原則 （或計劃） 中的 Vm 範圍除以涵蓋範圍的範圍中的 Vm。 在其他情況下，您可能會有一些 Vm、 資源群組或訂用帳戶從排除原則範圍。 如果值為空白，則表示可能是原則/計畫不存在，或您沒有權限 （使用指派狀態提供資訊）。

## <a name="enable-using-azure-policy"></a>使用 Azure 原則啟用

若要在您的租用戶中，使用 Azure 原則啟用適用於 VM 的 Azure 監視器：

- 將計畫指派給範圍：管理群組、訂用帳戶或資源群組
- 檢閱和補救合規性結果

如需有關指派 Azure 原則的詳細資訊，請參閱 [Azure 原則概觀](../../governance/policy/overview.md#policy-assignment)，並在繼續進行之前，檢閱[管理群組概觀](../../governance/management-groups/index.md)。

### <a name="policies-for-azure-vms"></a>Azure Vm 的原則

下表列出 Azure VM 的原則定義：

|名稱 |說明 |Type |
|-----|------------|-----|
|[預覽]：啟用適用於 VM 的 Azure 監視器 |在指定的範圍 (管理群組、訂用帳戶或資源群組) 中啟用適用於虛擬機器 (VM) 的 Azure 監視器。 請使用 Log Analytics 工作區作為參數。 |方案 |
|[預覽]：稽核 Dependency Agent 部署 – 未列出的 VM 映像 (OS) |如果 VM 映像 (OS) 未在清單中定義且未安裝代理程式，請將 VM 報告為不相容。 |原則 |
|[預覽]：稽核 Log Analytics 代理程式部署 – 未列出的 VM 映像 (OS) |如果 VM 映像 (OS) 未在清單中定義且未安裝代理程式，請將 VM 報告為不相容。 |原則 |
|[預覽]：部署適用於 Linux VM 的 Dependency Agent |如果 VM 映像 (OS) 未在清單中定義且未安裝代理程式，請部署適用於 Linux VM 的 Dependency Agent。 |原則 |
|[預覽]：部署適用於 Windows VM 的 Dependency Agent |如果 VM 映像 (OS) 未在清單中定義且未安裝代理程式，請部署適用於 Windows VM 的 Dependency Agent。 |原則 |
|[預覽]：部署適用於 Linux VM 的 Log Analytics 代理程式 |如果 VM 映像 (OS) 未在清單中定義且未安裝代理程式，請部署適用於 Linux VM 的 Log Analytics 代理程式。 |原則 |
|[預覽]：部署適用於 Windows VM 的 Log Analytics 代理程式 |如果 VM 映像 (OS) 未在清單中定義且未安裝代理程式，請部署適用於 Windows VM 的 Log Analytics 代理程式。 |原則 |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>原則的 Azure 虛擬機器擴展集

下表中列出的 Azure 虛擬機器擴展集的原則定義：

|名稱 |說明 |Type |
|-----|------------|-----|
|[預覽]：啟用 VM 擴展集 (VMSS) 的 Azure 監視器 |啟用 Azure 監視器 （管理群組、 訂用帳戶或資源群組） 的指定範圍中的虛擬機器擴展集。 請使用 Log Analytics 工作區作為參數。 注意： 如果您的擴展集 upgradePolicy 設定為手動，您必須透過呼叫升級，擴充功能套用至集合中的所有 Vm。 在 CLI 中，這會是 az vmss update-執行個體。 |方案 |
|[預覽]：稽核在 VMSS – VM 映像 (OS) 未列出的相依性代理程式部署 |報表的虛擬機器擴展集不符合規範清單和代理程式中未定義的 VM 映像 (OS) 如果未安裝。 |原則 |
|[預覽]：稽核在 VMSS – VM 映像 (OS) 未列出的 Log Analytics 代理程式部署 |報表的虛擬機器擴展集不符合規範清單和代理程式中未定義的 VM 映像 (OS) 如果未安裝。 |原則 |
|[預覽]：部署 Linux VM 擴展集 (VMSS) 的相依性代理程式 |部署適用於 Linux 虛擬機器擴展集，如果 VM 映像 (OS) 定義在清單中，而且未安裝代理程式的相依性代理程式。 |原則 |
|[預覽]：部署 Windows VM 擴展集 (VMSS) 的相依性代理程式 |部署相依性代理程式的 Windows 虛擬機器擴展集，如果 VM 映像 (OS) 定義在清單中，而且未安裝代理程式。 |原則 |
|[預覽]：為 Linux VM 擴展集 (VMSS) 部署 Log Analytics 代理程式 |適用於 Linux 虛擬機器擴展集，如果 VM 映像 (OS) 定義在清單中，而且未安裝代理程式部署 Log Analytics 代理程式。 |原則 |
|[預覽]：為 Windows VM 擴展集 (VMSS) 部署 Log Analytics 代理程式 |部署 Log Analytics 代理程式的 Windows 虛擬機器擴展集，如果 VM 映像 (OS) 定義在清單中，而且未安裝代理程式。 |原則 |

獨立原則 (未包含在計畫中) 如下所述：

|名稱 |說明 |Type |
|-----|------------|-----|
|[預覽]：稽核適用於 VM 的 Log Analytics 工作區 - 報告不相符 |如果 VM 未登入在原則/計畫指派中指定的 Log Analytics 工作區，請將其報告為不相容。 |原則 |

### <a name="assign-the-azure-monitor-initiative"></a>指派 Azure 監視器計畫
若要從 Vm 原則涵蓋範圍] 頁面的 [Azure 監視器中建立原則指派，請執行下列步驟。 若要了解如何完成這些步驟，請參閱 [從 Azure 入口網站建立原則指派](../../governance/policy/assign-policy-portal.md)。

當您指派 initiative/原則時，在指派中選取的範圍可能是此處所列的範圍或子集。 比方說，您可能已建立訂用帳戶 （原則的範圍） 的指派，而不管理群組 （涵蓋範圍的範圍） 中案例的涵蓋範圍 %表示原則 （或計劃） 中的 Vm 範圍除以涵蓋範圍的範圍中的 Vm。 在某些其他情況下，您可能會有一些 Vm Rg 或訂用帳戶從排除原則範圍。  萬一它是空白，它會指出其中一個原則 / 計劃不存在或您沒有權限 （指派狀態中所提供的資訊）  

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 Azure 入口網站中，選取 [監視]。 

3. 選擇 [Insights] 區段中的 [虛擬機器 (預覽)]。
 
4. 選取 **開始**索引標籤，然後在頁面上選取**管理原則涵蓋範圍**。

5. 從資料表中，選取管理群組或訂用帳戶，然後選取**範圍**依序按一下省略符號 （...）。    在我們的範例中，範圍會將原則指派限制為只對某個虛擬機器群組強制執行。

6. 在上**Azure 原則指派**頁面就會預先填入計劃**啟用 Vm 的 Azure 監視器**。 
    **指派名稱**方塊會自動填入方案的名稱，但您可以加以變更。 您也可以新增選擇性的描述。 [指派者] 方塊會根據登入的使用者自動填入，而且此值是選擇性的。

7. (選擇性) 若要從範圍中移除一或多個資源，請選取 [排除項目]。

8. 在支援之區域的 [Log Analytics 工作區] 下拉式清單中，選取一個工作區。

   > [!NOTE]
   > 如果工作區超出指派的範圍之外，請將 [Log Analytics 參與者] 權限授與原則指派的主體識別碼。 否則，您可能會看到部署失敗，例如：`The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`若要授與存取權，請檢閱[如何手動設定受控識別](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)。
   > 
   >  系統會選取 [受控識別] 核取方塊，因為要指派的計畫包含具有 *deployIfNotExists* 效果的原則。
    
9. 在 [管理身分識別位置] 下拉式清單中，選取適當的區域。

10. 選取 [指派]。

一旦您建立的指派，請指派涵蓋範圍、 指派狀態、 合規性 Vm 和合規性狀態，以反映所做的變更將會更新 Vm 原則涵蓋範圍] 頁面的 [Azure 監視器。 

下列矩陣對應每個可能的合規性計劃的狀態。  

| 合規性狀態 | 說明 | 
|------------------|-------------|
| **符合規範** | 在範圍內的所有 Vm 都都 「 Log Analytics 和相依性代理程式部署給他們。|
| **不符合規範** | 並非所有的 Vm，在範圍中具有 Log Analytics 和相依性代理程式部署給他們，而且可能需要修復。|
| **未啟動** | 已新增新的指派。 |
| **Lock** | 您沒有足夠的權限到管理群組。<sup>1</sup> | 
| 空白 | 沒有指派的原則。 | 

<sup>1</sup>如果您沒有管理群組的存取權，要求提供存取權或檢視合規性和管理指派透過子管理群組或訂用帳戶擁有者。 

下表對應計劃的每個可能的指派狀態。

| 指派狀態 | 說明 | 
|------------------|-------------|
| 「成功」 | 在範圍內的所有 Vm 都都 「 Log Analytics 和相依性代理程式部署給他們。|
| **警告** | 訂用帳戶不在管理群組中。|
| **未啟動** | 已新增新的指派。 |
| **Lock** | 您沒有足夠的權限到管理群組。<sup>1</sup> | 
| 空白 | 沒有 Vm 存在，或不指派原則。 | 
| **動作** | 指派原則或編輯指派 | 

<sup>1</sup>如果您沒有管理群組的存取權，要求提供存取權或檢視合規性和管理指派透過子管理群組或訂用帳戶擁有者。 

## <a name="review-and-remediate-the-compliance-results"></a>檢閱和補救合規性結果

下列範例會為 Azure 虛擬機器，但也適用於虛擬機器擴展集。 您可以閱讀[識別非合規性結果](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)，了解如何檢閱合規性結果。 在 Azure 監視器中的 Vm 原則涵蓋範圍 頁面，從資料表中，選取管理群組或訂用帳戶，然後選取**檢視合規性**依序按一下省略符號 （...）。   

![Azure VM 的原則合規性](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

根據計畫包含的原則結果，VM 在下列案例中會回報為不符合規範：

* 未部署 Log Analytics 或 Dependency Agent。  
    此對於包含現有 VM 的範圍來說是典型案例。 若要解決此問題，請在不符合規範的原則上[建立補救工作](../../governance/policy/how-to/remediate-resources.md)，以部署必要的代理程式。  
    - [預覽]: Deploy Dependency Agent for Linux VMs
    - [預覽]: Deploy Dependency Agent for Windows VMs
    - [預覽]: Deploy Log Analytics Agent for Linux VMs
    - [預覽]: Deploy Log Analytics Agent for Windows VMs

* 在原則定義中找不到 VM 映像 (OS)。  
    部署原則的準則僅包含從已知 Azure VM 映像部署的 VM。 請參閱文件以了解 VM OS 是否受到支援。 如果不受支援，請複製部署原則，並加以更新/修改，使映像符合規範。  
    - [預覽]：稽核 Dependency Agent 部署 – 未列出的 VM 映像 (OS)
    - [預覽]：稽核 Log Analytics 代理程式部署 – 未列出的 VM 映像 (OS)

* VM 未登入指定的 Log Analytics 工作區。  
    計畫範圍內的部分 VM 可以登入未在原則指派中指定的 Log Analytics 工作區。 此原則是一個工具，可找出哪些 VM 要回報至不符合規範的工作區。  
    - [預覽]: Audit Log Analytics Workspace for VM - Report Mismatch

## <a name="edit-initiative-assignment"></a>編輯方案指派

任何時候指派給管理群組或訂用帳戶的計劃之後，您可以編輯它，來修改下列屬性：

- 指派名稱
- 說明
- 指派者
- Log Analytics 工作區
- 例外狀況

## <a name="next-steps"></a>後續步驟

現在您的虛擬機器啟用監視，這項資訊是使用適用於 Vm 的 Azure 監視器可用於分析。 若要了解如何使用健康情況功能，請參閱[檢視適用於 VM 的 Azure 監視器健康情況](vminsights-health.md)。 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。 若要找出 VM 效能的瓶頸和整體使用率，請參閱[檢視 Azure VM 效能](vminsights-performance.md)，或者，若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。