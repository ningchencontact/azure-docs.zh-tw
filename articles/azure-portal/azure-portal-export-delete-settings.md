---
title: 匯出或刪除 Azure 入口網站設定 | Microsoft Docs
description: 了解如何匯出或刪除使用者設定、 私用儀表板和在 Azure 入口網站中的自訂設定。
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: kfollis
ms.openlocfilehash: fde7ffbaa3ef4d47eea48302a99948932aeb4f00
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361948"
---
# <a name="export-or-delete-user-settings"></a>匯出或刪除使用者設定

您可以在 Azure 入口網站中使用的設定和功能，來建立自訂的體驗。 您的自訂設定的相關資訊會儲存在 Azure 中。 您可以匯出或刪除下列的使用者資料：

* 在 Azure 入口網站中的私人儀表板
* 使用者設定，例如最愛的訂用帳戶或目錄和最後一個登入的目錄
* 佈景主題和其他自訂的入口網站設定

它是個不錯的主意，匯出和刪除它們之前，先檢閱您的設定。 重新建立儀表板，或重做的自訂設定可能很費時。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>匯出或刪除入口網站設定

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在入口網站的標頭，選取**設定**。

    ![顯示入口網站設定齒輪圖示的螢幕擷取畫面](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. 選取 [匯出所有設定] 或 [刪除所有設定和私人儀表板]。

    ![螢幕擷取畫面顯示在入口網站匯出及刪除設定](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      下表描述這些動作。

      |  動作 | 描述 |
      | --- | --- |
      | **匯出所有設定** | 建立包含您的使用者設定，例如您的色彩佈景主題、 我的最愛，以及私人儀表板的.json 檔案。|
      | **刪除所有設定和私人儀表板** | 刪除所有連結至私人儀表板和其他入口網站所做的自訂設定。 |

> [!NOTE]
> 由於使用者設定的動態本質和資料損毀的風險，您無法從.json 檔案匯入設定。
>
>


## <a name="next-steps"></a>後續步驟

* [建立和共用 Azure 儀表板](azure-portal-dashboard-share-access.md)
* [新增、移除及排序我的最愛](azure-portal-add-remove-sort-favorites.md)
