---
title: Azure Stack 中的儲存體帳戶 | Microsoft Docs
description: 了解如何建立 Azure Stack 儲存體帳戶。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/18/2019
ms.author: mabrigg
ms.openlocfilehash: c3b1c0e6ed0850e6346d385dc3e86afdcb78fe39
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478530"
---
# <a name="storage-accounts-in-azure-stack"></a>Azure Stack 中的儲存體帳戶
儲存體帳戶包含 Blob 與資料表服務，且儲存體資料物件的命名空間不得重複。 根據預設，您帳戶中的資料只有身為儲存體帳戶擁有者的您可以使用。

1. 在 Azure Stack POC 電腦上，以[系統管理員](azure-stack-connect-azure-stack.md)身分登入 `https://adminportal.local.azurestack.external`，然後按一下 [+ 建立資源] > [資料 + 儲存體] > [儲存體帳戶]。

   ![](media/azure-stack-provision-storage-account/image01.png)
2. 在 [建立儲存體帳戶] 刀鋒視窗中，輸入儲存體帳戶的名稱。 建立新的 [資源群組]，或選取現有的資源群組，然後按一下 [建立]，以建立儲存體帳戶。

   ![](media/azure-stack-provision-storage-account/image02.png)
3. 若要查看您的新儲存體帳戶，請按一下 [所有資源]，然後搜尋儲存體帳戶並按一下其名稱。

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>後續步驟
[使用 Azure 資源管理員範本](user/azure-stack-arm-templates.md)

[深入了解 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)

[下載 Azure Stack 與 Azure 一致的儲存體驗證指南](https://aka.ms/azurestacktp1doc) \(英文\)
