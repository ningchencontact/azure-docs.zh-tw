---
title: Azure Stack 快速入門 - 建立 Windows 虛擬機器
description: Azure Stack 快速入門 - 使用入口網站來建立 Windows VM
services: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 5776fc472483018eb2c9e4f8962d0b1e8bce8081
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32155576"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>快速入門：使用 Azure Stack 入口網站建立 Windows Server 虛擬機器

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

您可以使用 Azure Stack 入口網站建立 Windows Server 2016 虛擬機器。 請遵循本文中的步驟建立和使用虛擬機器。

## <a name="sign-in-to-the-azure-stack-portal"></a>登入 Azure Stack 入口網站

登入 Azure Stack 入口網站。 Azure Stack 入口網站的位址依您所連線的 Azure Stack 產品而有所不同：

* 如需 Azure Stack 開發套件 (ASDK)，請前往：https://portal.local.azurestack.external。
* 針對 Azure Stack 整合系統，請移至您 Azure Stack 操作員所提供的 URL。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 按一下 [+ 新增] > [計算] > [Windows Server 2016 Datacenter Eval] > [建立]。 如果您沒有看到 [Windows Server 2016 Datacenter Eval] 項目，請連絡您的 Azure Stack 操作員。 如[將 Windows Server 2016 VM 映像新增到 Azure Stack 市集](../azure-stack-add-default-image.md)文章中所述，請要求他們將它新增至市集。

    ![在入口網站中建立 Windows 虛擬機器的步驟](media/azure-stack-quick-windows-portal/image01.png)
2. 在 [基本] 中，輸入 [名稱]、[使用者名稱] 與 [密碼]。 選擇 [訂用帳戶] 。 建立 [資源群組] 或選取現有的資源群組，選取 [位置]，然後按一下 [確定]。

    ![設定基本設定](media/azure-stack-quick-windows-portal/image02.png)
3. 在 [選擇大小] 下，按一下 [D1 標準] > [選取]。
    ![選擇虛擬機器的大小](media/azure-stack-quick-windows-portal/image03.png)
4. 在 [設定] 下，接受預設值並按一下 [確定]。
    ![設定虛擬機器設定](media/azure-stack-quick-windows-portal/image04.png)
5. 在 [摘要] 下，按一下 [確定]，以建立虛擬機器。
    ![檢視摘要及建立虛擬機器](media/azure-stack-quick-windows-portal/image05.png)
6. 若要查看您的新虛擬機器，請按一下 [所有資源]，搜尋虛擬機器名稱，然後在搜尋結果中按一下其名稱。
    ![查看虛擬機器](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>清除資源

當您使用完虛擬機器時，請刪除虛擬機器及其資源。 若要這樣做，請在虛擬機器頁面上選取資源群組，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在這個快速入門中，您已部署基本 Windows Server 虛擬機器。 若要深入了解 Azure Stack 虛擬機器，請繼續移至 [Azure Stack 中虛擬機器的考量](azure-stack-vm-considerations.md)。
