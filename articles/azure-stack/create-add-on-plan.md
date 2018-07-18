---
title: 在本文中，您會了解如何更新 Azure Stack 供應項目和方案 | Microsoft Docs
description: 本文說明如何檢視及修改現有的 Azure Stack 供應項目和方案。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a84148a3ac31d51ff30cebffab00e5fec8fdaa87
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236915"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack 附加方案
身為 Azure Stack 操作人員，您可以為您的使用者建立包含所需服務和適用配額的方案，供其訂閱。 這些[*基本方案*](azure-stack-create-plan.md)包含了要提供給您使用者的核心服務，而每個供應項目都只能有一項基本方案。 若您需要修改供應項目，可以使用「附加方案」，讓您可以修改方案來一開始基本方案所提供的擴充電腦、儲存體或網路配額。 

雖然在某些情況下，將所有項目結合在單一方案中可能會是最佳的做法；仍建議您先訂閱基本方案，再使用附加方案來提供其他服務。 例如，您可能會決定在基本方案中提供 IaaS 服務，而將所有 PaaS 服務當作附加方案。 方案也可用來在 Azure Stack 環境中控制資源耗用量。 例如，若您想讓使用者注意資源使用量，可使用相對較小的基本方案 (取決於所需的服務)；當使用者達到容量上限，系統會根據指派給他們的方案，提醒他們已耗用了配置給他們的資源。 接下來，使用者可以選取可用的附加方案來獲得額外的資源。 

> [!NOTE]
> 使用者將附加方案新增到現有供應項目的訂用帳戶時，最多可能需要一小時額外的資源才會出現。 

## <a name="create-an-add-on-plan"></a>建立附加方案
您可以修改現有的供應項目來建立附加方案：

1. 以雲端管理員的身分登入 Azure Stack 管理員入口網站。
2. 依照[建立新的基本方案](azure-stack-create-plan.md)時所使用的相同步驟來建立新方案，提供先前未提供的服務。 在此範例中，金鑰保存庫 (Microsoft.KeyVault) 服務將會包含在新方案中。
3. 在系統管理員入口網站中，按一下 [供應項目]，然後選取要以附加方案來更新的供應項目。

   ![](media/create-add-on-plan/1.PNG)

4.  請捲動到供應項目屬性的底部，並選取 [附加方案]。 按一下 [新增] 。
   
    ![](media/create-add-on-plan/2.PNG)

5. 選取要新增的方案。 在此範例中，方案稱為「**金鑰保存庫方案**」，請按一下 [選取]，將方案新增到供應項目中。 您應該會收到通知，告訴您方案已成功新增到供應項目中。
   
    ![](media/create-add-on-plan/3.PNG)

6. 請檢閱供應項目所隨附的附加方案清單，確認有列出新的附加方案。
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>後續步驟

  [建立供應項目](azure-stack-create-offer.md)