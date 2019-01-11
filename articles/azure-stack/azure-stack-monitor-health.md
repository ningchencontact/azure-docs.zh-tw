---
title: 在 Azure Stack 中監視健康情況和警示 | Microsoft Docs
description: 了解如何在 Azure Stack 中監視健康情況和警示。
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
ms.date: 12/18/2018
ms.author: mabrigg
ms.openlocfilehash: 9a925a01cae75124dc56b0c2bc5cc931a6e04100
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721609"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>在 Azure Stack 中監視健康情況和警示

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

Azure Stack 包含基礎結構監視功能，可協助您檢視 Azure Stack 區域的健康情況和警示。 [區域管理] 圖格預設會釘選在「預設提供者訂用帳戶」的系統管理員入口網站上，當中會列出 Azure Stack 的所有已部署區域。 此圖格會顯示每個區域的作用中重要和警告警示數目。 此圖格是您進入 Azure Stack 健康情況和警示功能的進入點。

![[區域管理] 圖格](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack"></a>了解 Azure Stack 中的健康情況

健康情況資源提供者會管理健康情況和警示。 在 Azure Stack 部署和設定期間，Azure Stack 基礎結構元件會向健康情況資源提供者註冊。 這項註冊讓每個元件的健康情況和警示得以顯示。 在 Azure Stack 中健康情況是個簡單的概念。 如果元件的已註冊執行個體有警示存在，該元件的健康情況狀態會反映出最差的作用中警示嚴重性：警告或重要。

## <a name="alert-severity-definition"></a>警示嚴重性定義

在 Azure Stack 中，只會以兩種嚴重性引發警示：**警告**和**嚴重**。

- **警告**  
  操作員可用排程方式來解決警告警示。 警示通常不會影響使用者工作負載。

- **Critical**  
  操作員應該緊急解決嚴重警示。 這些是目前影響或即將影響 Azure Stack 使用者的問題。


## <a name="view-and-manage-component-health-state"></a>檢視和管理元件健康情況狀態

您可在系統管理員入口網站上，也可以透過 REST API 和 PowerShell，來檢視元件的健康情況狀態。

若要在入口網站中檢視健康情況狀態，請在 [區域管理] 磚中按一下您想要檢視的區域。 您可以檢視基礎結構角色和資源提供者的健康情況狀態。

![基礎結構角色的清單](media/azure-stack-monitor-health/image2.png)

您可以按一下資源提供者或基礎結構角色，以檢視更詳細的資訊。

> [!WARNING]  
> 如果您按一下基礎結構角色，然後按一下角色執行個體，就會看到 [啟動]、[重新啟動]或 [關機]選項。 當您對整合系統套用更新時，請勿使用這些動作。 此外，也**請勿**在「Azure Stack 開發套件」環境中使用這些選項。 這些選項是僅針對每一基礎結構角色有多個角色執行個體的整合式系統環境而設計的。 重新啟動開發套件中的角色執行個體 (特別是 AzS-Xrp01) 會導致系統不穩定。 如需疑難排解協助，請將您的問題張貼到 [Azure Stack 論壇](https://aka.ms/azurestackforum)。
>

## <a name="view-alerts"></a>檢視警示

可直接從 [區域管理] 刀鋒視窗檢視每個 Azure Stack 區域的作用中警示清單。 預設設定下的第一個磚是 [警示] 磚，其中會顯示該區域的重要和警告警示摘要。 就像這個刀鋒視窗中的其他磚一樣，您可以將 [警示] 磚釘選到儀表板上以便快速存取。

![會顯示警告的 [警示] 磚](media/azure-stack-monitor-health/image3.png)

您可以藉由選取 [警示] 圖格的上半部，瀏覽至區域的所有作用中警示清單。 如果您選取磚內的 [重要] 或 [警告] 明細項目，就會瀏覽至警示的篩選清單 (重要或警告)。 

[警示] 刀鋒視窗支援依狀態 (作用中或已關閉) 和嚴重性 (重要或警告) 進行篩選。 預設檢視會顯示所有作用中的警示。 所有已關閉的警示會在七天後從系統中移除。

![依重要或警告狀態進行篩選的篩選器](media/azure-stack-monitor-health/alert-view.png)

[檢視 API] 動作會顯示用來產生清單檢視的 REST API。 這個動作可讓您快速熟悉可用來查詢警示的 REST API 語法。 您可用自動化方式使用此 API，或與您現有的資料中心監視、報告及票證解決方案整合。

您可以按一下特定警示來檢視警示詳細資料。 警示詳細資料會顯示與警示相關的所有欄位，並可讓您快速瀏覽至受影響的元件和警示來源。 例如，如果其中一個基礎結構角色執行個體離線或無法存取，就會發生以下警示。  

![[警示詳細資料] 刀鋒視窗](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>修復警示

在某些警示中，您可以選取 [修復]。

已選取時，**修復**動作會執行警示特定的步驟，以嘗試解決問題。 選取之後，**修復**動作的狀態就會顯示為入口網站通知。

![修復進行中](media/azure-stack-monitor-health/repair-in-progress.png)

**修復**動作會回報成功完成或失敗，以完成相同入口網站通知刀鋒視窗中的動作。  如果警示的「修復」動作失敗，您可以從警示詳細資料中重新執行**修復**動作。 如果警示的「修復」動作成功完成，請**不要**重新執行**修復**動作。

![修復成功完成](media/azure-stack-monitor-health/repair-completed.png)

基礎結構角色執行個體回到線上之後，會自動關閉此警示。 許多 (但並非每一個) 警示都會在相關問題解決時自動關閉。 警示如果有提供 [修復] 動作按鈕，當 Azure Stack 解決問題時，將會自動關閉。  針對所有其他警示，在您執行補救步驟之後，請選取 [關閉警示]。 如果問題仍然存在，Azure Stack 會產生新警示。 如果您將問題解決，警示就會保持關閉，而無須進行任何其他步驟。

## <a name="next-steps"></a>後續步驟

[在 Azure Stack 中管理更新](azure-stack-updates.md)

[Azure Stack 中的區域管理](azure-stack-region-management.md)
