---
title: "Azure 管理解決方案 | Microsoft Docs"
description: "管理解決方案包含 Azure 中客戶可新增至其 Log Analytics 工作區的已封裝管理案例。  本文提供如何自訂客戶和合作夥伴所建立之解決方案的詳細資訊。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d46b869815fef44a8137bb5121133a1c0140ca30
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2018
---
# <a name="working-with-management-solutions-in-azure-preview"></a>使用 Azure 中的管理解決方案 (預覽)
> [!NOTE]
> 這是 Azure 中管理解決方案 (目前處於預覽狀態) 的預備文件。    
> 
> 

管理解決方案包含客戶可新增至其 Azure 環境的已封裝管理案例。  除了 [Microsoft 所提供的解決方案](../log-analytics/log-analytics-add-solutions.md)，合作夥伴和客戶可以建立要用於其自己的環境中或可透過社群供您客戶使用的管理解決方案。

## <a name="finding-and-installing-management-solutions"></a>尋找及安裝管理解決方案
有多種方法可尋找及安裝下列各節中所述的管理解決方案。

### <a name="azure-marketplace"></a>Azure Marketplace
可以從 Azure 入口網站中的 Azure Marketplace 安裝 Microsoft 和信任的夥伴所提供的管理解決方案。

1. 登入 Azure 管理入口網站。
2. 在左側窗格中選取 [所有服務]。
3. 向下捲動至 [解決方案]，或在 [篩選] 對話方塊中輸入 solutions。
4. 按一下 [+新增] 按鈕。
5. 藉由瀏覽、按一下 [篩選] 按鈕，或在 [搜尋所有項目] 方塊中輸入資料，以搜尋您感興趣的解決方案。
6. 按一下 Marketplace 項目，以檢視其詳細資訊。
7. 按一下 [建立] 以開啟 [新增方案] 窗格。
8. 除了解決方案中任何參數的值以外，系統還會提示您輸入所需的資訊，例如 [Log Analytics 工作區和自動化帳戶](#log-analytics-workspace-and-automation-account)。
9. 按一下 [建立] 以安裝解決方案。

### <a name="oms-portal"></a>OMS 入口網站
可以從 OMS 入口網站中的「方案庫」安裝 Microsoft 所提供的管理解決方案。

1. 登入 OMS 入口網站。
2. 按一下 [方案庫] 圖格。
3. 在 [OMS 方案庫] 頁面中，您可以了解每個可用的方案。 按一下要新增之解決方案的名稱。
4. 在所選解決方案的頁面中，該解決方案的詳細資料會顯示於此。 按一下 [新增] 。
5. 新增解決方案之後，該解決方案的磚會出現在入口網站的 [概觀] 頁面上。待 Log Analytics 處理完資料後，您便可開始使用該解決方案。

### <a name="azure-quickstart-templates"></a>Azure 快速入門範本
社群成員可以將管理解決方案提交至 Azure 快速入門範本。  您可以下載這些範本以供稍後安裝，或檢查這些範本以了解如何[建立自己的解決方案](#creating-a-solution)。

1. 依照 [Log Analytics 工作區和自動化帳戶](#log-analytics-workspace-and-automation-account)中所述的程序來連結工作區和帳戶。
2. 移至 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。  
3. 搜尋您感興趣的解決方案。
4. 選取結果中的解決方案，以檢視其詳細資料。
5. 按一下 [部署至 Azure] 按鈕。
6. 除了解決方案中任何參數的值以外，系統還會提示您提供資源群組和位置等資訊。
7. 按一下 [購買] 以安裝解決方案。

### <a name="deploy-azure-resource-manager-template"></a>部署 Azure Resource Manager 範本
您從社群取得的解決方案或您[自己建立](#creating-a-solution)的解決方案會當作 Resource Manager 範本實作，因此您可以使用任何[部署範本](../azure-resource-manager/resource-group-template-deploy-portal.md)的標準方法。  請注意，在安裝解決方案前，您必須建立並連結 [Log Analytics 工作區和自動化帳戶](#log-analytics-workspace-and-automation-account)。

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics 工作區和自動化帳戶
大部分的管理解決方案都需要 [Log Analytics 工作區](../log-analytics/log-analytics-manage-access.md)來包含檢視，以及[自動化帳戶](../automation/automation-security-overview.md#automation-account-overview)來包含 Runbook 和相關資源。 工作區和帳戶必須符合下列需求。

* 一個解決方案只能使用一個 Log Analytics 工作區和一個自動化帳戶。  
* 解決方案所用的 Log Analytics 工作區和自動化帳戶必須彼此連結。 Log Analytics 工作區只能連結到一個自動化帳戶，而自動化帳戶只能連結到一個 Log Analytics 工作區。
* 若要連結，Log Analytics 工作區和自動化帳戶必須位於相同的資源群組與區域中。  例外狀況是位於美國東部區域的工作區，以及位於美國東部 2 的自動化帳戶。

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>建立 Log Analytics 工作區與自動化帳戶之間的連結
指定 Log Analytics 工作區和自動化帳戶的方式，取決於解決方案的安裝方法。

* 當您透過 OMS 入口網站安裝 Microsoft 解決方案時，它會安裝在目前的工作區中，因此不需要自動化帳戶。
* 當您透過 Azure Marketplace 安裝解決方案時，系統會提示您提供工作區和自動化帳戶，並為您在它們之間建立連結。  
* 對於 Azure Marketplace 外部的解決方案，您必須在安裝解決方案之前，先連結 Log Analytics 工作區和自動化帳戶。  若要這麼做，請選取 Azure Marketplace 中的任何解決方案，並選取 Log Analytics 工作區和自動化帳戶。  您不必實際安裝解決方案，因為在選取 Log Analytics 工作區和自動化帳戶時，就會立即建立連結。  一旦建立連結之後，您即可將該 Log Analytics 工作區和自動化帳戶用於任何解決方案。 

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>驗證 Log Analytics 工作區與自動化帳戶之間的連結
您可以使用下列程序，驗證 Log Analytics 工作區與自動化帳戶之間的連結。

1. 在 Azure 入口網站中選取自動化帳戶。
2. 如果已啟用功能表 [相關資源] 區段中的 [工作區] 設定，則會將此帳戶連接到 Log Analytics 工作區。  您可以按一下 [工作區] 來檢視工作區的詳細資料。

## <a name="listing-management-solutions"></a>列出管理解決方案
使用下列程序，檢視連結至您的 Azure 訂用帳戶的工作區中的管理解決方案。

1. 登入 Azure 管理入口網站。
2. 在左側窗格中選取 [所有服務]。
3. 向下捲動至 [解決方案]，或在 [篩選] 對話方塊中輸入 solutions。
4. 將會列出安裝在您所有工作區中的解決方案。

請注意，您只可以檢視使用 OMS 入口網站在目前工作區中安裝的 Microsoft 解決方案。

## <a name="removing-a-management-solution"></a>移除管理解決方案
移除管理解決方案時，也會移除解決方案中的所有資源。  

1. 使用[列出解決方案](#listing-solutions)中的程序，在 Azure 入口網站中尋找解決方案。
2. 選取您想要移除的解決方案。
3. 按一下 [刪除]  按鈕。

## <a name="creating-a-management-solution"></a>建立管理解決方案
[在 Operations Management Suite (OMS) 中建立解決方案](operations-management-suite-solutions-creating.md)提供有關建立管理解決方案的完整指引。 

## <a name="next-steps"></a>後續步驟
* 搜尋 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates)不同 Resource Manager 範本的範例。
* 建立自己的[管理解決方案](operations-management-suite-solutions-creating.md)。

