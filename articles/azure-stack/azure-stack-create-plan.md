---
title: 在 Azure Stack 中建立方案 | Microsoft Docs
description: 身為雲端系統管理員，您可以建立可供訂閱者佈建虛擬機器的方案。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: a2ac138228b7b54f486acb0f42975748136a8da7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759464"
---
# <a name="create-a-plan-in-azure-stack"></a>在 Azure Stack 中建立方案

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

[方案](azure-stack-key-features.md)是一或多項服務及其配額的組合。 身為提供者的您可以為使用者製作方案。 使用者接著即可訂閱您的供應項目，以使用其中的方案、服務和配額。 此範例說明如何建立一個包含計算、網路及儲存體資源提供者的方案。 此方案可讓訂閱者佈建虛擬機器。

## <a name="create-a-plan-1902-and-later"></a>建立方案 (1902 和更新版本)

1. 登入 [Azure Stack 系統管理員入口網站](https://adminportal.local.azurestack.external)。

2. 若要建立使用者可訂閱的方案和供應項目，請依序選取 [+ 建立資源]、[供應項目 + 方案] 和 [方案]。
  
   ![選取方案](media/azure-stack-create-plan/select-plan.png)

3. 隨即會出現索引標籤式的使用者介面，以供您指定方案名稱、新增服務，以及為每個選取的服務定義配額。 最重要的是，在決定要建立供應項目之前，您可以先檢閱其詳細資料。

   在 [新增方案] 的 [基本資料] 索引標籤下，輸入 [顯示名稱] 與 [資源名稱]。 顯示名稱是操作員所能看見的方案易記名稱。 請注意，在系統管理員入口網站中，只有操作員才能夠看見方案詳細資料。

   ![指定詳細資料](media/azure-stack-create-plan/plan-name.png)

4. 建立新的 [資源群組]或選取現有的資源群組，以作為方案的容器。

   ![指定資源群組](media/azure-stack-create-plan/resource-group.png)

5. 選取 [服務] 索引標籤，然後選取 [Microsoft.Compute]、[Microsoft.Network] 及 [Microsoft.Storage]。
  
   ![選取服務](media/azure-stack-create-plan/services.png)

6. 選取 [配額] 索引標籤。在 [Microsoft.Storage] 旁，從下拉式方塊中選擇任一預設配額，或選取 [新建] 以建立自訂配額。
  
   ![配額](media/azure-stack-create-plan/quotas.png)

7. 如果您要建立新的配額，請輸入配額的 [名稱]，然後指定配額值。 選取 [確定] 以建立配額。

   ![新增配額](media/azure-stack-create-plan/new-quota.png)

8. 重複步驟 6 和 7，以建立並指派 [Microsoft.Network] 和 [Microsoft.Compute] 的配額。 當三項服務全都有指派的配額時，它們看起來就和下一個範例一樣。

   ![完成配額指派](media/azure-stack-create-plan/all-quotas-assigned.png)

9. 選取 [檢閱 + 建立] 來檢閱方案。 檢閱所有的值和配額，以確保其正確無誤。 請注意每個服務/配額配對左邊的展開箭號。 有新的功能可讓您在選取的方案中逐一展開配額，以檢視方案中各項配額的詳細資料，並回頭進行任何必要的編輯。

   ![建立方案](media/azure-stack-create-plan/create.png)

10. 當您準備好時，選取 [建立] 以建立方案。

11. 若要查看新方案，請選取 [方案]，然後搜尋該方案並選取其名稱。 如果您的資源清單很長，請使用 [搜尋] 依名稱找出您的方案。

## <a name="create-a-plan-1901-and-earlier"></a>建立方案 (1901 和之前的版本)

1. 登入 [Azure Stack 系統管理員入口網站](https://adminportal.local.azurestack.external)。

2. 若要建立使用者可訂閱的方案和供應項目，請依序選取 [+ 建立資源]、[供應項目 + 方案] 和 [方案]。
  
   ![選取方案](media/azure-stack-create-plan/select-plan1901.png)

3. 在 [新增方案] 下，輸入 [顯示名稱] 與 [資源名稱]。 顯示名稱是使用者所能看見的方案易記名稱。 只有系統管理員可以看到資源名稱，他會使用此名稱將方案當作 Azure Resource Manager 資源來使用。

   ![指定詳細資料](media/azure-stack-create-plan/plan-name1901.png)

4. 建立新的 [資源群組]或選取現有的資源群組，以作為方案的容器。

   ![指定資源群組](media/azure-stack-create-plan/resource-group1901.png)

5. 選取 [服務]，然後選取 [Microsoft.Compute]、[Microsoft.Network] 及 [Microsoft.Storage]。 接著，選擇 [儲存] 以儲存組態。 核取方塊會在滑鼠停留在每個選項時出現。
  
   ![選取服務](media/azure-stack-create-plan/services1901.png)

6. 選取 [配額]、[Microsoft.Storage (本機)]，然後選擇預設配額或選取 [建立新的配額] 來建立自訂配額。
  
   ![配額](media/azure-stack-create-plan/quotas1901.png)

7. 如果您要建立新配額，請輸入配額的 [名稱] > 指定配額值 > 選取 [確定]。 [建立配額] 對話方塊隨即關閉。

   ![新增配額](media/azure-stack-create-plan/new-quota1901.png)

   然後選取您所建立的新配額。 選取配額進行指派並關閉選取對話方塊。
  
   ![指派配額](media/azure-stack-create-plan/assign-quota1901.png)

8. 重複步驟 6 和 7，以建立並指派 **Microsoft.Network (本機)** 和 **Microsoft.Compute (本機)** 的配額。 當三項服務全都有指派的配額時，它們看起來就和下一個範例一樣。

   ![完成配額指派](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. 在 [配額] 下，選擇 [確定]，然後在 [新增方案] 下，選擇 [建立] 來建立方案。

    ![建立方案](media/azure-stack-create-plan/create1901.png)

10. 若要查看新方案，請選取 [所有資源]，然後搜尋該方案並選取其名稱。 如果您的資源清單很長，請使用 [搜尋] 依名稱找出您的方案。

    ![檢閱方案](media/azure-stack-create-plan/plan-overview1901.png)

## <a name="next-steps"></a>後續步驟

* [建立供應項目](azure-stack-create-offer.md)
