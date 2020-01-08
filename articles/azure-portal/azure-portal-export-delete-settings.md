---
title: 匯出或刪除 Azure 入口網站設定 | Microsoft Docs
description: 瞭解如何匯出或刪除您的使用者設定、私人儀表板，以及 Azure 入口網站中的自訂設定。
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: f033af37985077f4d8df9d541b55764df0c75eda
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640171"
---
# <a name="export-or-delete-user-settings"></a>匯出或刪除使用者設定

您可以使用 Azure 入口網站中的設定和功能來建立自訂體驗。 您的自訂設定的相關資訊會儲存在 Azure 中。 您可以匯出或刪除下列使用者資料：

* Azure 入口網站中的私用儀表板
* 使用者設定，例如我的最愛訂閱或目錄，以及最後一個登入的目錄
* 主題和其他自訂入口網站設定

在刪除您的設定之前，最好先匯出並加以檢查。 重建儀表板或重做自訂設定可能相當耗時。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>匯出或刪除您的入口網站設定

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在入口網站的標頭中，選取 [**設定**]。

    ![顯示入口網站設定齒輪圖示的螢幕擷取畫面](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. 選取 [匯出所有設定] 或 [刪除所有設定和私人儀表板]。

    ![顯示入口網站匯出和刪除設定的螢幕擷取畫面](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      下表描述這些動作。

      | 行動 | 說明 |
      | --- | --- |
      | **匯出所有設定** | 建立 json 檔案，其中包含您的使用者設定，例如您的色彩主題、我的最愛和私人儀表板。|
      | **刪除所有設定和私人儀表板** | 刪除私人儀表板的所有連結，以及您在入口網站中所做的其他自訂設定。 |

> [!NOTE]
> 由於使用者設定的動態性質和資料損毀的風險，因此您無法從 json 檔案匯入設定。
>
>


## <a name="next-steps"></a>後續步驟

* [建立和共用 Azure 儀表板](azure-portal-dashboard-share-access.md)
* [新增、移除和排序我的最愛](azure-portal-add-remove-sort-favorites.md)
