---
title: 在本文中，您會了解如何更新 Azure Stack 供應項目和方案 | Microsoft Docs
description: 本文說明如何檢視及修改現有的 Azure Stack 供應項目和方案。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 00bb17eadfee32e9b0d006ac76bb8e1cd614f13e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780383"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack 附加方案

如果您是 Azure Stack 操作員，當您想要提供其他服務，或將「電腦」、「儲存體」或「網路」配額擴充超過基底方案最初提供的配額時，可以建立附加方案來修改[基本方案](azure-stack-create-plan.md)。 附加方案可修改基底方案，而且是使用者可選擇訂閱的選用擴充功能。

有些時候，在單一方案中結合所有一切是最佳做法。 有些時候，您可以有一個基底計劃，然後使用附加方案來提供其他服務。 例如，您可能會決定在基本方案中提供 IaaS 服務，而將所有 PaaS 服務當作附加方案。

使用附加方案的另一個原因是要協助監控資源使用狀況。 若要這樣做，您可以從包含相對較小配額的基底方案著手 (取決於所需的服務)。 然後，當使用者達到容量時，他們會收到警示，告知他們已耗用以其指派的方案為基礎的資源配置。 接下來，使用者可以選取附加方案，以提供額外的資源。

> [!NOTE]
> 當您不想要使用附加方案來擴充配額時，您也可選擇[編輯配額的原始組態](azure-stack-quota-types.md#edit-a-quota)。

當您將附加方案新增到現有供應項目的訂用帳戶時，最多可能需要一小時額外的資源才會出現。

您可以修改現有的供應項目來建立附加方案。

## <a name="create-an-add-on-plan-1902-and-later"></a>建立附加方案 (1902 和更新版本)

1. 以雲端管理員的身分登入 Azure Stack 管理員入口網站。
2. 依照[建立新的基本方案](azure-stack-create-plan.md)時所使用的相同步驟來建立新方案，提供先前未提供的服務。
3. 在系統管理員入口網站中，按一下 [供應項目]，然後選取要以附加方案來更新的供應項目。

   ![建立附加方案](media/create-add-on-plan/add-on1.png)

4. 請捲動到供應項目屬性的底部，並選取 [附加方案]。 按一下 [新增] 。

    ![建立附加方案](media/create-add-on-plan/add-on2.png)

5. 選取要新增的方案。 在此範例中，方案稱為 **20-storageaccounts**。 選取此方案之後，按一下 [選取] 將方案新增至供應項目。 您應該會收到通知，告訴您方案已成功新增到供應項目中。

    ![建立附加方案](media/create-add-on-plan/add-on3.png)

6. 請檢閱供應項目所隨附的附加方案清單，確認有列出新的附加方案。

    [![建立附加方案](media/create-add-on-plan/add-on4.png "建立附加方案")](media/create-add-on-plan/add-on4lg.png#lightbox)

## <a name="create-an-add-on-plan-1901-and-earlier"></a>建立附加方案 (1901 和之前的版本)

1. 以雲端管理員的身分登入 Azure Stack 管理員入口網站。
2. 依照[建立新的基本方案](azure-stack-create-plan.md)時所使用的相同步驟來建立新方案，提供先前未提供的服務。 在此範例中，金鑰保存庫 (**Microsoft.KeyVault**) 服務將會包含在新方案中。
3. 在系統管理員入口網站中，按一下 [供應項目]，然後選取要以附加方案來更新的供應項目。

   ![建立附加方案](media/create-add-on-plan/1.PNG)

4. 請捲動到供應項目屬性的底部，並選取 [附加方案]。 按一下 [新增] 。

    ![建立附加方案](media/create-add-on-plan/2.PNG)

5. 選取要新增的方案。 在此範例中，方案稱為**保存庫方案**。 選取此方案之後，按一下 [選取] 將方案新增至供應項目。 您應該會收到通知，告訴您方案已成功新增到供應項目中。

    ![建立附加方案](media/create-add-on-plan/3.PNG)

6. 請檢閱供應項目所隨附的附加方案清單，確認有列出新的附加方案。

    ![建立附加方案](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>後續步驟

* [建立供應項目](azure-stack-create-offer.md)