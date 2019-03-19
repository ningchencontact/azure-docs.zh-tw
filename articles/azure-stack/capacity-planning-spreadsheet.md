---
title: Azure Stack 的容量規劃試算表 | Microsoft Docs
description: 了解 Azure Stack 部署的容量規劃試算表。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 459398f5f40ae5fb28dfa4ac73b94aecca7304eb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099411"
---
# <a name="azure-stack-capacity-planner"></a>Azure Stack Capacity Planner
Azure Stack Capacity Planner 為用於 Azure Stack 資源容量規劃的試算表。 此容量規劃工具可讓您設計各種運算資源的配置，進而了解這些配置如何與選取之硬體供應項目相應。 以下提供使用 Azure Stack Calculator 的詳細指示。

## <a name="worksheet-descriptions"></a>工作表說明
下列為包含於 Azure Stack Capacity Planner 試算表之工作表的簡短摘要，可從 [https://aka.ms/azstackcapacityplanner](https://aka.ms/azstackcapacityplanner) 下載：

|索引標籤名稱|說明|
|-----|-----|
|版本免責聲明|計算機、版本號碼及發行日期之用途的簡要概觀。|
|範例的指示|提供使用 Azure Stack Capacity Planner 的詳細指示。|
|DefinedSolutionSKUs|包含最多五個硬體定義的多欄資料表。 此工作表中的項目均為範例。 用意在於使用者可能會變更詳細資料，以符合正在考量使用或購買的系統組態。|
|DefineByVMFootprint|透過建立各種 VM 大小和數量的集合，找出適當的硬體 SKU。|
|DefineByWorkloadFootprint|透過建立 Azure Stack 工作負載的集合，尋找適當的硬體 SKU。|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKU 指示
此工作表包含最多五個硬體定義範例。 變更詳細資料，以符合正在考量使用或購買的系統組態。

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>由經授權之硬體合作夥伴提供的硬體選取項目
Azure Stack 提供作為解決方案合作夥伴所安裝之軟體的整合系統。 這些解決方案合作夥伴將擁有屬於本身之 Azure Stack 容量規劃工具的授權版本，且這些工具應用於決定解決方案的容量。

### <a name="multiple-ways-to-model-computing-resources"></a>多種建立運算資源的方式
用於在 Azure Stack Planner 建立資源的基本建置區塊，為各種不同大小的 Azure Stack 虛擬機器 (VM)。 這些 VM 的大小範圍介於最小的「Basic 0」與目前最大的 VM「Standard_Fsv2」。 根據您的需求，不同 VM 的各種數量可以使用此工具的兩種不同方式，來用於建立運算資源配置。

1. 規劃工具的使用者會選取特定的硬體供應項目，進而了解哪些不同資源的組合可與此硬體資源相應。 

2. 規劃工具的使用者會建立特定的 VM 配置組合，進而使 Azure Stack Calculator 顯示哪些可用的硬體 SKU 可支援此 VM 組態。

此工具提供配置 VM 資源的兩種方法；一種為 VM 資源配置的單一集合，另一種為最多六個不同工作負載組態的集合。 每個工作負載組態可包含不同可用之 VM 資源的配置。 以下提供建立與使用各配置模型的詳細資訊。 包含於非背景陰影資料格，或此工作表之 SKU 下拉式清單內的唯一值，應加以修改。 陰影資料格內所作之變更可能會中斷資源的計算。


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint 指示
若要建立使用不同大小和數量之 VM 的單一集合建立模型，請選取 [DefineByVMFootprint] 索引標籤，並遵循此步驟順序。

1. 在此工作表的右上角中，使用所提供之下拉式清單方塊控制項，以選取您想安裝於各硬體系統 (SKU) 的初始伺服器數量 (介於 4 與 16 之間)。 此伺服器數量可在模型化程序期間隨時修改，以了解此數量如何影響資源配置模型的整體可用資源。
2. 如果您想針對一個特定硬體組態建立各種 VM 資源配置，請找出頁面右上角之「目前的 SKU」標籤正下方的藍色下拉式清單方塊。 下拉此清單方塊，然後選取您需要的硬體 SKU。
3. 您現在已準備好開始將各種大小的 VM 新增置模型。 若要包含特定的 VM 類型，請在該 VM 項目左側的藍色外框方塊內輸入數量值。

   > [!NOTE]
   > VM 儲存體總計是指 VM 資料磁碟的總容量 (支援的磁碟數量 * 單一磁碟的最大容量 (1 TB))。 根據我們已填入 [可用儲存體組態] 資料表中的組態指標，您可以選擇想要的每個 Azure Stack VM 儲存體資源層級。 不過，請注意，您可以視需要新增或變更「可用儲存體組態」資料表。<br><br>各 VM 會以初始指派的本機暫存儲存體啟動。 若要反映精簡佈建的暫存儲存體，您可針對下拉式選單的任何項目變更本機暫存數量 (包括允許之暫存儲存體數量的上限)。

4. 隨著新增 VM，您將會看到顯示變更之可用 SKU 資源的圖表。 如此可讓您了解模型化程序期間，新增各種 VM 大小和數量的影響。 另一個檢視變更之影響的方法為，查看列於可用 VM 清單正下方的「已耗用與仍可用」數字。 這些數字會根據目前選取的硬體 SKU 反映估計值。
5. 建立 VM 集合後，按一下「目前的 SKU」標籤正下方之頁面右上角所顯示的「建議的 SKU」按鈕，即可找到建議的硬體 SKU。 使用此按鈕，您就可修改 VM 組態，並了解哪個硬體支援各組態。


## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint 指示
若要使用 Azure Stack 工作負載集合建立模型，請選取「DefineByWorkloadFootprint」索引標籤，並遵循此步驟順序。 Azure Stack 工作負載為透過可用的 VM 資源建立。   

> [!TIP]
> 若要變更 Azure Stack VM 的儲存體大小，請檢視上一節步驟三中的附註。

1. 在此頁面的右上角中，使用所提供之下拉式清單方塊控制項，以選取您想安裝於各硬體系統 (SKU) 的初始伺服器數量 (介於 4 與 16 之間)。
2. 如果您想針對一個特定硬體組態建立各種 VM 資源配置，請找出頁面右上角之「目前的 SKU」標籤正下方的藍色下拉式清單方塊。 下拉此清單方塊，然後選取您需要的硬體 SKU。
3. 根據上節 DefineByVMFootprint 指示步驟三所述，針對 DefineByVMFootprint 頁面上各個所需的 Azure Stack VM，選取適當的儲存體大小。 每個 VM 的儲存體大小均定義於 DefineByVMFootprint 表。
4. 從 DefineByWorkloadFootprint 頁面的左上角開始，透過輸入包含於該工作負載內的各 VM 類型數量，針對最多六個不同的工作負載類型建立組態。 透過在該工作負載名稱正下方的欄位，輸入數值即可完成。 工作負載名稱可修改，以反映將由此特定組態所支援的工作負載類型。
5. 透過在「數量」標籤正下方該欄位底部輸入數值，即可將各工作負載類型的特定數量納入。
6. 建立工作負載類型和數量後，按一下「目前的 SKU」標籤正下方頁面右上角所顯示的「建議的 SKU」按鈕，將會產生最小的 SKU 與足夠的資源，以支援所要顯示的該整體工作負載組態。
7. 透過針對硬體 SKU 所選取的伺服器數量進行修改，或變更工作負載組態內的 VM 配置或數量，即可完成進一步模組化。 相關聯的圖將會出現即時回饋，顯示變更如何影響整體的資源耗用量。
8. 當您對於變更感到滿意後，再次按一下「建議的 SKU」按鈕，將會顯示針對新組態所建議的 SKU。


## <a name="next-steps"></a>後續步驟
瞭解 [Azure Stack 的資料中心整合考量](azure-stack-datacenter-integration.md)
