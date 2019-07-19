---
title: 使用 Azure Migrate Server 評估來建立評量 |Microsoft Docs
description: 說明如何使用 Azure Migrate Server 評估工具來建立評量
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229098"
---
# <a name="create-an-assessment"></a>建立評估

本文說明如何使用 Azure Migrate 來建立內部部署 VMware Vm 或 Hyper-v Vm 的評量:伺服器評估。

[Azure Migrate](migrate-services-overview.md)可協助您遷移至 Azure。 Azure Migrate 提供集中式的中樞, 以追蹤將內部部署基礎結構、應用程式和資料移轉至 Azure 的探索、評估和移動。 中樞提供適用于評估和遷移的 Azure 工具, 以及協力廠商獨立軟體廠商 (ISV) 產品。 

## <a name="before-you-start"></a>開始之前

- 請確定您已[建立](how-to-add-tool-first-time.md)Azure Migrate 專案。
- 如果您已經建立專案, 請確定您已[新增](how-to-assess.md)Azure Migrate:伺服器評估工具。
- 若要建立評量, 您需要設定適用于[VMware](how-to-set-up-appliance-vmware.md)或[hyper-v](how-to-set-up-appliance-hyper-v.md)的 Azure Migrate 設備。 設備會探索內部部署機器, 然後將中繼資料和效能資料傳送至 Azure Migrate:伺服器評估。 [深入了解](migrate-appliance.md)。


## <a name="assessment-overview"></a>評量概觀
您可以使用 Azure Migrate 來建立兩種類型的評量:伺服器評估。

**評量** | **詳細資料** | **資料**
--- | --- | ---
**以效能為基礎** | 以收集的效能資料為基礎的評量 | **建議的 VM 大小**:根據 CPU 和記憶體使用量資料。<br/><br/> **建議的磁片類型 (標準或 premium 受控磁片)** :根據內部部署磁片的 IOPS 和輸送量。
**作為內部部署** | 根據內部部署調整大小的評量。 | **建議的 VM 大小**:根據內部部署 VM 大小<br/><br> **建議的磁片類型**:根據您為評量選取的儲存體類型設定。

[深入瞭解](concepts-assessment-calculation.md)評量。

## <a name="run-an-assessment"></a>執行評估

執行評估, 如下所示:

1. 瞭解建立評量的[最佳做法](best-practices-assessment.md)。
2. 在 [**伺服器**] **索引標籤的 [Azure Migrate:伺服器評估** ] 磚中, 按一下 [**評估**]。

    ![評估](./media/how-to-create-assessment/assess.png)

2. 在 [**評估伺服器**] 中, 指定評估的名稱。
3. 按一下 [檢視全部]  來檢閱評估屬性。

    ![評量屬性](./media/how-to-create-assessment//view-all.png)

3. 在 [**選取或建立群組**] 中  , 選取 [新建], 並指定組名。 群組會收集一或多個 Vm 一起進行評估。
4. 在 **[將電腦新增至群組**] 中, 選取要新增至群組的 vm。
5. 按一下 [**建立評**量] 以建立群組, 並執行評量。

    ![建立評估](./media/how-to-create-assessment//assessment-create.png)

6. 建立評量之後, 請在 [**伺服器** >  **] Azure Migrate 中查看它:伺服器評定**  > **評**量。
7. 按一下 [匯出評估]  ，將其下載為 Excel 檔案。



## <a name="review-an-assessment"></a>審查評量

評量描述:

- **Azure 準備就緒**:Vm 是否適合遷移至 Azure。
- **每月成本預估**:在 Azure 中執行 Vm 的預估每月計算和儲存體成本。
- **每月儲存成本預估**:遷移之後磁片儲存體的預估成本。

### <a name="view-an-assessment"></a>查看評量

1. 在 [**遷移目標** >  **伺服器**] 中, **按一下 Azure Migrate 中的 [**評**量]:伺服器評估**。
2. 在 [**評**量] 中, 按一下評量加以開啟。

    ![評定摘要](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>審查 Azure 準備就緒

1. 在 [ **azure 準備就緒**] 中, 確認 vm 是否已準備好遷移至 Azure。
2. 檢查 VM 狀態:
    - **準備好進行 Azure**:Azure Migrate 建議評估中 vm 的 VM 大小和成本估計值。
    - **備妥條件**:顯示問題和建議的補救措施。
    - **尚未準備好進行 Azure**:顯示問題和建議的補救措施。
    - **未知的就緒**:當 Azure Migrate 因數據可用性問題而無法評估準備就緒時使用。

2. 按一下 [ **Azure 就緒**狀態]。 您可以查看 VM 的就緒程度詳細資料, 並向下切入以查看 VM 詳細資料, 包括計算、儲存體和網路設定。



### <a name="review-cost-details"></a>審查成本詳細資料

此視圖會顯示在 Azure 中執行 Vm 的預估計算和儲存成本。

1. 檢查每月計算和儲存成本。 系統會針對評估群組中的所有 Vm 匯總成本。

    - 成本預估是以機器的大小建議和其磁片和屬性為基礎。
    - 系統會顯示計算和儲存體的每月預估成本。
    - 成本估計適用于執行內部部署 Vm 作為 IaaS Vm。 Azure Migrate Server 評估並不考慮 PaaS 或 SaaS 成本。

2. 您可以查看每月儲存成本預估。 此視圖會顯示已評估群組的匯總儲存體成本, 並分割不同類型的儲存體磁片。
3. 您可以向下切入以查看特定 Vm 的詳細資料。


### <a name="review-confidence-rating"></a>檢閱信賴評等

當您執行以效能為基礎的評量時, 會將信賴評等指派給評量。

![信賴評等](./media/how-to-create-assessment/confidence-rating.png)

- 已獎勵從1顆星 (最低) 到5顆星 (最高) 的評等。
- 信賴評等可協助您預估評量所提供的大小建議的可靠性。
- 信賴評等是以計算評量所需的資料點可用性為基礎。

評量的信賴評等如下所示。

**資料點可用性** | **信賴評等**
--- | ---
0%-20% | 1 顆星
21%-40% | 2 顆星
41%-60% | 3 顆星
61%-80% | 4 顆星
81%-100% | 5 顆星




## <a name="next-steps"></a>後續步驟

- 瞭解如何使用相依性[對應](how-to-create-group-machine-dependencies.md)來建立高度信賴群組。
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
