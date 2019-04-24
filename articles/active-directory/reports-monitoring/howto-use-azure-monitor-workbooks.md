---
title: 使用 Azure 監視器的活頁簿，針對 Azure Active Directory 報告 |Microsoft Docs
description: 了解如何使用 Azure 監視器活頁簿做為 Azure Active Directory 報告
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2c9b3d0ef110fea0629af345a71d0d7b7cce7313
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287138"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>作法：使用 Azure 監視器的活頁簿做為 Azure Active Directory 報告

您想要：

- 了解影響您[條件式存取原則](../conditional-access/overview.md)上您的使用者登入體驗？

- 疑難排解登入失敗，以取得較佳的組織登入健全狀況檢視，以及快速解決問題？

- 知道誰在使用舊版驗證登入您的環境？ 藉由[封鎖舊版驗證](../conditional-access/block-legacy-authentication.md)，您可以改善您的租用戶保護。


[Azure 監視器活頁簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)將文字、 Analytics 查詢，Azure 計量，以及參數結合成豐富的互動式報表。 Azure Active Directory 為您提供協助您找到上述問題的答案來監視活頁簿。

本文：

- 假設您已熟悉如何[使用 Azure 監視器的活頁簿中建立互動式報表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。

- 說明如何使用 Azure 監視器活頁簿，關於監視，以及在回答上述問題。
 


## <a name="prerequisites"></a>必要條件

若要使用此功能，您必須要有：

- Azure Active Directory 租用戶，與 premium (P1 / 「 進階 P2) 授權。 了解如何[取得進階授權](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)。

- A [Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

## <a name="access-workbooks"></a>存取活頁簿 

若要存取活頁簿：

1. 登入您[Azure 入口網站](https://portal.azure.com)。

2. 在左側導覽列上，按一下 [Azure Active Directory]。

3. 在 **監視**區段中，按一下**Insights**。 

    ![深入解析](./media/howto-use-azure-monitor-workbooks/41.png)

4. 按一下報表或範本，或按一下**開啟**工具列中。 

    ![資源庫](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>登入分析

若要存取登入分析活頁簿，請按一下**登入**中**使用量**一節。 

此活頁簿會顯示下列登入趨勢：

- 所有登入

- 成功

- 等候使用者動作

- 失敗

您可以篩選依每個趨勢：

- 時間範圍

- 應用程式

- 使用者

![資源庫](./media/howto-use-azure-monitor-workbooks/43.png)


針對每個趨勢，您取得細分：

- 位置

    ![資源庫](./media/howto-use-azure-monitor-workbooks/45.png)

- 裝置

    ![資源庫](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>使用傳統驗證的登入次數 


若要存取登入使用[舊有的驗證](../conditional-access/block-legacy-authentication.md)活頁簿中，按一下**登入使用舊版驗證**中**使用量**一節。 

此活頁簿會顯示下列登入趨勢：

- 所有登入

- 成功


您可以篩選依每個趨勢：

- 時間範圍

- 應用程式

- 使用者

- 通訊協定 

![資源庫](./media/howto-use-azure-monitor-workbooks/47.png)


針對每個趨勢，您可以取得的細目-依應用程式和通訊協定。

![資源庫](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>條件式存取的登入 


若要存取的登入[條件式存取原則](../conditional-access/overview.md)活頁簿中，按一下**被條件式存取的登入**中**條件式存取**一節。 

此活頁簿會顯示已停用的登入的趨勢。

您可以篩選依每個趨勢：

- 時間範圍

- 應用程式

- 使用者

![資源庫](./media/howto-use-azure-monitor-workbooks/49.png)


對已停用登入，您可以取得細目依條件式存取狀態。

![條件式存取狀態](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>授與控制項的登入

若要存取的登入[授與控制項](../conditional-access/controls.md)活頁簿中，按一下**授與控制項的登入**中**條件式存取**一節。 

此活頁簿會顯示下列已停用的登入趨勢：

- 需要 MFA
 
- 需要使用規定

- 需要隱私權聲明

- 其他


您可以篩選依每個趨勢：

- 時間範圍

- 應用程式

- 使用者

![資源庫](./media/howto-use-azure-monitor-workbooks/50.png)


針對每個趨勢，您可以取得的細目-依應用程式和通訊協定。

![資源庫](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>登入失敗分析

使用**登入失敗分析**活頁簿，以針對錯誤進行疑難排解：

- 登入
- 條件式存取原則
- 舊有的驗證。 


若要存取登入的條件式存取資料，請按一下**登入使用舊版驗證**中**疑難排解**一節。 

此活頁簿會顯示下列登入趨勢：

- 所有登入

- 成功

- 擱置動作

- 失敗


您可以篩選依每個趨勢：

- 時間範圍

- 應用程式

- 使用者

![資源庫](./media/howto-use-azure-monitor-workbooks/52.png)


若要疑難排解登入，您取得細分：

- 最上層錯誤

    ![資源庫](./media/howto-use-azure-monitor-workbooks/53.png)

- 登入等候使用者動作

    ![資源庫](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>後續步驟

* [使用 Azure 監視器的活頁簿中建立互動式報表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)