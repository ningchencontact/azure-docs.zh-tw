---
title: 在 Azure 中的管理解決方案 | Microsoft Docs
description: Azure 中的管理解決方案是邏輯、視覺效果和資料擷取規則的集合，可提供針對特定問題領域進行計量的樞紐分析。  本文提供有關安裝及使用管理解決方案的資訊。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: bwren
ms.openlocfilehash: 1e22aab85976fcab8ec270bdea1b8988b4d3bfe7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
---
# <a name="management-solutions-in-azure"></a>在 Azure 中的管理解決方案
管理解決方案會運用 Azure 中的服務，提供特定應用程式或服務的額外作業見解。 本文提供 Azure 中管理解決方案的簡短概觀，以及使用和安裝它們的詳細資料。

管理解決方案通常會將資訊收集到 Log Analytics 中，並提供記錄搜尋和檢視以分析所收集的資料。 它們也會運用 Azure 自動化等其他服務，執行應用程式或服務相關的動作。

您可以針對您使用的任何應用程式和服務，將管理解決方案新增至您的 Azure 訂用帳戶。 這些管理解決方案通常免費提供，但是會收集可能造成使用費用的資料。 除了 [Microsoft 所提供的解決方案](../operations-management-suite/operations-management-suite-solutions-creating.md)，合作夥伴和客戶可以建立要用於其自己的環境中或可透過社群供您客戶使用的管理解決方案。

## <a name="using-management-solutions"></a>使用管理解決方案
每個 Log Analytics 工作區的 [概觀] 頁面會針對工作區中安裝的每個解決方案顯示一個圖格。 按一下解決方案的圖格，以開啟其檢視，其中包含所收集資料的更詳細分析。

![概觀](media/monitoring-solutions/overview.png)

管理解決方案可以包含多種的 Azure 資源，而您可以檢視解決方案隨附的任何資源，就像任何其他資源一樣。 例如，工作區中 [已儲存的搜尋] 隨附解決方案中包含的任何記錄搜尋。 當您在 Log Analytics 中執行特定分析時，可以使用這些搜尋。

## <a name="list-installed-management-solutions"></a>列出已安裝的管理解決方案 
使用下列程序，列出您的訂用帳戶中已安裝的管理解決方案。

1. 登入 Azure 管理入口網站。
2. 在左側窗格中選取 [所有服務]。
3. 向下捲動至 [解決方案]，或在 [篩選] 對話方塊中輸入 solutions。
4. 隨即列出您所有工作區中安裝的解決方案。 解決方案的名稱後面接著其安裝所在的 Log Analytics 工作區名稱。
1. 使用畫面頂端的下拉式方塊，依據訂用帳戶或資源群組進行篩選。


![列出所有解決方案](media/monitoring-solutions/list-solutions-all.png)

按一下解決方案名稱，以開啟其摘要頁面。 此頁面會顯示解決方案中包含的任何 Log Analytics 檢視，並且為解決方案本身及其工作區提供不同的選項。 使用以上其中一個程序來檢視解決方案的摘要頁面，以列出解決方案，然後按一下解決方案的名稱。

![解決方案屬性](media/monitoring-solutions/solution-properties.png)


## <a name="find-management-solutions"></a>尋找管理解決方案
您可以瀏覽及安裝 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace)中來自 Microsoft 和夥伴的管理解決方案。 執行[管理解決方案搜尋](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions)來篩選管理解決方案，然後按一下任何項目以獲得更多詳細資料。

![Marketplace](media/monitoring-solutions/marketplace.png)

## <a name="install-a-management-solution"></a>安裝管理解決方案

### <a name="install-a-management-solution-from-the-azure-marketplace"></a>從 Azure Marketplace 安裝管理解決方案
您可以使用下列任何方法，找出管理解決方案並開始進行安裝。

- 在 [Azure Marketplace](#find-management-solutions) 中的管理解決方案上，按一下 [立即取得]。
- 從[訂用帳戶的解決方案清單](#list-installed-management-solutions)，按一下 [新增]。 在 [管理解決方案] 的右邊，按一下 [更多資訊]。 找出您想要的管理解決方案，然後按一下 [建立]。
- 在 Azure 入口網站中，選取 [建立資源] > [監視 + 管理] > [查看全部]。 在 [管理解決方案] 的右邊，按一下 [更多資訊]。 找出您想要的管理解決方案，然後按一下 [建立]。

當安裝程序開始時，系統會提示您提供因每個解決方案而異的必要組態。 無論如何，您都需要選取將會安裝解決方案及收集其資料的 Log Analytics 工作區。 您可能也需要[指定自動化帳戶](#log-analytics-workspace-and-automation-account) (如果解決方案要求)。

### <a name="install-a-solution-from-the-community"></a>從社群安裝解決方案
社群成員可以將管理解決方案提交至 Azure 快速入門範本。 您可以直接安裝這些解決方案，或下載其範本以便稍後安裝。

1. 依照 [Log Analytics 工作區和自動化帳戶](#log-analytics-workspace-and-automation-account)中所述的程序來連結工作區和帳戶。
2. 移至 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。 
3. 搜尋您感興趣的解決方案。
4. 選取結果中的解決方案，以檢視其詳細資料。
5. 按一下 [部署至 Azure] 按鈕。
6. 除了解決方案中任何參數的值以外，系統還會提示您提供資源群組和位置等資訊。
7. 按一下 [購買] 以安裝解決方案。


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics 工作區和自動化帳戶
所有管理解決方案都需要有 [Log Analytics 工作區](../log-analytics/log-analytics-manage-access.md)，才能儲存解決方案所收集的資料以及裝載其記錄搜尋和檢視。 有些解決方案也需要有 [OMS 工作區](../automation/automation-security-overview.md#automation-account-overview)來容納 Runbook 和相關資源。 工作區和帳戶必須符合下列需求。

* 每次安裝解決方案時，只能使用一個 Log Analytics 工作區和一個自動化帳戶。 您可以將解決方案個別安裝於多個工作區中。
* 如果解決方案需要自動化帳戶，則 Log Analytics 工作區和自動化帳戶必須彼此連結。 Log Analytics 工作區只能連結到一個自動化帳戶，而自動化帳戶只能連結到一個 Log Analytics 工作區。
* 若要連結，Log Analytics 工作區和自動化帳戶必須位於相同的資源群組與區域中。 例外狀況是位於美國東部區域的工作區，以及位於美國東部 2 的自動化帳戶。

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>建立 Log Analytics 工作區與自動化帳戶之間的連結
指定 Log Analytics 工作區和自動化帳戶的方式，取決於解決方案的安裝方法。

* 當您透過 Azure Marketplace 安裝解決方案時，系統會提示您提供工作區和自動化帳戶。 如果它們尚未連結，則會建立它們之間的連結。
* 對於 Azure Marketplace 外部的解決方案，您必須在安裝解決方案之前，先連結 Log Analytics 工作區和自動化帳戶。 若要這麼做，請選取 Azure Marketplace 中的任何解決方案，並選取 Log Analytics 工作區和自動化帳戶。 您不必實際安裝解決方案，因為在選取 Log Analytics 工作區和自動化帳戶時，就會立即建立連結。 一旦建立連結之後，您即可將該 Log Analytics 工作區和自動化帳戶用於任何解決方案。

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>驗證 Log Analytics 工作區與自動化帳戶之間的連結
您可以使用下列程序，驗證 Log Analytics 工作區與自動化帳戶之間的連結。

1. 在 Azure 入口網站中選取自動化帳戶。
1. 捲動到功能表的 [相關資源] 區段。
1. 如果已啟用 [工作區] 設定，則會將此帳戶連結到 Log Analytics 工作區。 您可以按一下 [工作區] 來檢視工作區的詳細資料。

## <a name="remove-a-management-solution"></a>移除管理解決方案
若要移除已安裝的解決方案，請在[已安裝的解決方案清單](#list-installed-management-solutions)中找到它。 按一下解決方案名稱，以開啟其摘要頁面，然後按一下 [刪除]。




## <a name="next-steps"></a>後續步驟
* 取得 [Microsoft 所提供的管理解決方案清單](monitoring-solutions-inventory.md)。
* 了解如何[建立查詢](../log-analytics/log-analytics-log-searches.md)，以分析管理解決方案所收集的資料。

