---
title: 建立原則指派，以識別 Azure 環境中的不相容資源 | Microsoft Docs
description: 這篇文章會引導您逐步完成建立原則定義來識別不相容的資源。
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/18/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 6bb9eddb6a663e1f230c9c46835661ad20c02cfd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>建立原則指派，以識別 Azure 環境中的不相容資源
了解 Azure 中合規性的第一個步驟是識別您資源的狀態。 本快速入門會逐步引導您完成程序來建立原則指派，以識別出未使用受控磁碟的虛擬機器。

在此程序結束時，您將會成功識別出未使用受控磁碟的虛擬機器。 它們不符合原則指派的規範。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-a-policy-assignment"></a>建立原則指派

在本快速入門中，您會建立一個原則指派，並且指派「稽核沒有受控磁碟的虛擬機器」原則定義。

1. 選取 Azure 原則分頁左窗格上的 [指派]。
2. 從 [指派] 窗格頂端選取 [指派原則]。

   ![指派原則定義](media/assign-policy-definition/select-assign-policy.png)

3. 在 [指派原則] 分頁上，按一下 [原則] 欄位旁的![原則定義按鈕](media/assign-policy-definition/definitions-button.png)，以開啟可用定義的清單。

   ![開啟可用原則定義](media/assign-policy-definition/open-policy-definitions.png)

   Azure 原則隨附您可以使用的內建原則定義。 內建原則定義如下：

   - 強制執行標籤和其值
   - 套用標籤和其值
   - 需要 SQL Server 12.0 版

    如需所有可用內建原則的完整清單，請參閱[原則範本](json-samples.md)。

4. 搜尋您的原則定義以尋找 *Audit VMs that do not use managed disks* (稽核未使用受控磁碟的 VM) 定義。 按一下該原則，然後按一下 [選取]。

   ![尋找正確的原則定義](media/assign-policy-definition/select-available-definition.png)

5. 為原則指派提供顯示**名稱**。 在此案例中，我們將使用 *Audit VMs that do not use managed disks* (稽核未使用受控磁碟的 VM)。 您也可以新增選擇性的 [描述]。 此描述可用來提供詳細資料，說明原則指派如何識別出未使用受控磁碟的所有虛擬機器。
6. 將定價層變更為**標準**，以確保原則套用至現有的資源。

   Azure 原則有兩個定價層 – 免費和標準。 使用免費層次，您只能在未來的資源上強制執行原則，而使用標準層，您也能在現有資源上強制執行這些原則，以更加了解相容性狀態。 若要深入了解定價，請參閱 [Azure 原則定價](https://azure.microsoft.com/pricing/details/azure-policy/)。

7. 選取您想要套用原則的 [範圍]。  範圍會決定在哪些資源或資源群組上強制執行原則指派。 範圍從訂用帳戶到資源群組。
8. 選取您先前註冊的訂用帳戶 (或資源群組)。 在此範例中，會使用 **Azure Analytics Capacity Dev** 訂用帳戶，但是您的選項可能有所不同。 按一下 [選取] 。

   ![尋找正確的原則定義](media/assign-policy-definition/assign-policy.png)

9. 目前將 [排除] 留空，然後按一下 [指派]。

您現在可以識別不相容的資源，以了解環境的相容性狀態。

## <a name="identify-non-compliant-resources"></a>識別不相容的資源

選取左窗格上的 [相容性]，然後搜尋您建立的原則指派。

![原則相容性](media/assign-policy-definition/policy-compliance.png)

如果有任何現有資源不符合這個新指派的規範，它們會顯示在 [不符合規範的資源] 下。

根據現有資源評估條件，而且結果為 true 時，這些資源都會標示為不符合原則規範。 上述範例映像會顯示不符合規範的資源。 下表顯示不同的原則動作如何與結果合規性狀態的條件評估搭配使用。 雖然您在 Azure 入口網站中沒有看到評估邏輯，但是會顯示合規性狀態結果。 合規性狀態結果是符合規範和不符合規範其中之一。

| **資源狀態** | **Action** | **原則評估** | **合規性狀態** |
| --- | --- | --- | --- |
| exists | 拒絕、稽核、附加\*、DeployIfNotExist\*、AuditIfNotExist\* | True | 不符合規範 |
| exists | 拒絕、稽核、附加\*、DeployIfNotExist\*、AuditIfNotExist\* | False | 相容 |
| 新增 | 稽核、AuditIfNotExist\* | True | 不符合規範 |
| 新增 | 稽核、AuditIfNotExist\* | False | 相容 |

\* 附加、DeployIfNotExist 和 AuditIfNotExist 動作需要 IF 陳述式為 TRUE。 這些動作也需要存在條件為 FALSE，以呈現不符合規範。 若為 TRUE，IF 條件會觸發相關資源的存在條件評估。
## <a name="clean-up-resources"></a>清除資源

此集合中的其他指南是以本快速入門為基礎。 如果您打算繼續進行後續的教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源。
1. 選取左窗格中的 [指派]。
2. 搜尋您建立的指派，然後以滑鼠右鍵按一下它。

   ![刪除指派](media/assign-policy-definition/delete-assignment.png)

3.  選取 [刪除指派]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您會將原則定義指派至範圍。 原則定義可確保範圍中的所有資源都符合規範，並且識別哪些資源不符合。

若要深入了解指派原則，以確保所建立的**未來**資源是相容的，請繼續進行教學課程：

> [!div class="nextstepaction"]
> [建立及管理原則](./create-manage-policy.md)
